# Query
Use when: intent == QUERY — list markets, read status, or a creator wants to early-resolve.
All calls are `curl` (see `api.md`). You query; you do NOT take bets or run resolution —
those are service-owned (betting happens via the Telegram deposit card; resolution runs in
oracle-service).

## "What markets are open?" / "Show me markets"

```bash
curl -s "$ORACLE_SERVICE_URL/markets"                    # all (id, state, outcome, addresses)
curl -s "$ORACLE_SERVICE_URL/markets?state=betting_open" # filter by state
```
Render a compact list: title/id, state, outcome (if any), resolves-on. Don't dump raw JSON.

## "What's market 42 doing?" / "Did it resolve?"

```bash
curl -s "$ORACLE_SERVICE_URL/markets/42"     # full detail incl. outcome + oracle reasoning
```
Explain the state plainly. For end states / "why refunded", see `60-resolution-and-settlement.md`.

## My in-flight drafts (front half)

```bash
curl -s "$MARKET_SERVICE_URL/drafts?creator_id=$C"   # this user's drafts + their state
curl -s "$MARKET_SERVICE_URL/drafts/$ID"             # one draft (handed_off carries oracle_market_id)
```

## "Resolve market 42 now" (creator only)

```bash
curl -s -X POST "$ORACLE_SERVICE_URL/markets/42/resolve"
```
Brings the resolution deadline to now (never before betting closes). Tell the user the verdict
lands shortly. If it 409s (wrong state), explain — don't retry.

## Not your job

Placing bets / collecting deposits (the Telegram deposit card does that), and driving
resolution/settlement (oracle-service's worker does that). You report status; you don't act on money.
