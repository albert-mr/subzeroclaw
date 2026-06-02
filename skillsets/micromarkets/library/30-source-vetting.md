# Source vetting
Use when: you validate sources for a draft, or the user names sources. Re-read whenever
sources change. All calls are `curl` to market-service (see `api.md`).

Validate every source through the bench:

```bash
curl -s "$MARKET_SERVICE_URL/drafts/$ID/validate-sources" -H 'content-type: application/json' \
  -d "{\"creator_id\":$C,\"sources\":[\"en.wikipedia.org\",\"espn.com\"]}"
```

Returns a verdict per source: `accept`, `swap`, `unknown`, or `reject`. Read each aloud.

## Decision tree

- **accept** → auto-persisted, nothing to do.
- **swap** → the bench has a better route. **Ask** the user, then on yes:
  `accept-source {creator_id, source, choice:"accept_swap"}`.
- **unknown** → see the shortcut below.
- **reject** → **ask**; propose alternatives. Only override with explicit user yes:
  `accept-source {... choice:"reject"}` removes it.

```bash
curl -s "$MARKET_SERVICE_URL/drafts/$ID/accept-source" -H 'content-type: application/json' \
  -d "{\"creator_id\":$C,\"source\":\"aemet.es\",\"choice\":\"accept_unknown\"}"
```

## Accept-on-trust shortcut for `unknown`

When the user names a source **as part of their request** ("Sources: espn.com", "use aemet.es"),
treat that as implicit accept for `unknown` verdicts: call `accept-source` with
`choice:"accept_unknown"` **without re-asking**, then note it briefly ("Accepted `aemet.es`
as unknown — the bench doesn't list it, so the oracle fetches it directly at resolve").
For `swap`/`reject`, **always** ask — there the bench says the user's choice is probably wrong.

If the user names a source mid-conversation, same logic. Exception: if you fetched it yourself
(`10-research.md`) and it's clearly broken (paywall/login/404), ask before accepting.

## Don't

Never silently rewrite a source. Never accept a `swap`/`reject` without explicit confirmation.
The bench decides validity; you read the verdict and get the user's call.
