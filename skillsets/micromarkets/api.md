# API — how you call the services (curl)

You have one tool: `shell`. You reach the market services with `curl`. You never touch
chain, keys, or money — the services do. Two services, two env vars:

- `$MARKET_SERVICE_URL` — the front half: draft + deploy. (dev default `http://host.docker.internal:8001`)
- `$ORACLE_SERVICE_URL` — the back half: query live/resolved markets. (dev default `http://host.docker.internal:8000`)
- `$SZC_CREATOR_ID` — the current user's id (the Telegram router supplies it; dev default `1`).

Always send `creator_id` so the service can enforce creator-only actions. Read missing
fields / refusals from the JSON and act on them; never retry a refusal blindly.

## DRAFT + DEPLOY (market-service)

```bash
M="${MARKET_SERVICE_URL:-http://host.docker.internal:8001}"; C="${SZC_CREATOR_ID:-1}"

# start a draft from the user's request -> {draft_id, template_hint, missing_fields}
curl -s "$M/drafts" -H 'content-type: application/json' \
  -d "{\"raw\":\"Will Barça win the Clásico?\",\"creator_id\":$C}"

# fill fields as the user settles them (creator-only). outcomes = exactly 2 distinct labels.
curl -s -X PATCH "$M/drafts/$ID" -H 'content-type: application/json' -d "{
  \"creator_id\":$C, \"title\":\"Will Barça win the Clásico on 2026-06-04?\",
  \"template\":\"event_by_date\", \"resolution_date\":\"2026-06-04\",
  \"outcomes\":[\"Barça wins\",\"Barça doesn't win\"],
  \"rules\":[\"Win at full time per the Wikipedia match report\"]}"

# vet sources -> verdicts (accept auto-persists; swap/unknown/reject need accept-source)
curl -s "$M/drafts/$ID/validate-sources" -H 'content-type: application/json' \
  -d "{\"creator_id\":$C,\"sources\":[\"en.wikipedia.org\"]}"
curl -s "$M/drafts/$ID/accept-source" -H 'content-type: application/json' \
  -d "{\"creator_id\":$C,\"source\":\"aemet.es\",\"choice\":\"accept_unknown\"}"

# preview (only when missing_fields is empty) -> the card to show the user
curl -s "$M/drafts/$ID/preview" -H 'content-type: application/json' -d "{\"creator_id\":$C}"

# DEPLOY on user confirm. Service validates (guards), deploys oracle+market, opens betting,
# locks, hands off to oracle-service. Returns 202 {state:"deploying"}; poll GET for status.
curl -s "$M/drafts/$ID/deploy" -H 'content-type: application/json' -d "{\"creator_id\":$C}"

curl -s "$M/drafts/$ID"                 # one draft (state, missing_fields, oracle_market_id once handed off)
curl -s "$M/drafts?creator_id=$C"       # this user's drafts
curl -s -X DELETE "$M/drafts/$ID" -H 'content-type: application/json' -d "{\"creator_id\":$C}"  # cancel pre-deploy
```

## QUERY live/resolved markets (oracle-service)

```bash
O="${ORACLE_SERVICE_URL:-http://host.docker.internal:8000}"
curl -s "$O/markets"                    # all markets (id, state, outcome, addresses)
curl -s "$O/markets?state=betting_open"
curl -s "$O/markets/$MID"               # full detail incl. outcome + oracle reasoning
curl -s "$O/markets/$MID/resolve" -X POST   # creator early-resolve (won't collapse the window)
```

## Reading responses

- 2xx with JSON: use it. `missing_fields` non-empty → keep filling. `verdicts` → follow `30-source-vetting`.
- 400 `{detail:{refused:[...]}}` from deploy → a guard tripped (bad/missing field, unvetted source,
  past date). Tell the user the reason in plain English and fix it. Don't retry blindly.
- 403 → creator-only action by a non-creator. 404 → wrong id. 409 → wrong state (e.g. editing after deploy).

The services enforce every rule. You advise and drive the conversation; you do not set amounts,
addresses, deadlines, or state transitions.
