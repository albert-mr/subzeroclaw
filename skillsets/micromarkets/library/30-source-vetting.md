# Source vetting
Use when: you validate sources for a draft — `mm sources validate` returns a
swap/unknown/reject verdict, or the user names sources to vet. Re-read this whenever
sources change.

Every source goes through the bench. Run:

```bash
mm sources validate <draft_id> <domain-or-url> [<domain-or-url> ...]
```

It returns a verdict per source: `accept`, `swap_pending_confirmation`, `unknown`,
or `reject`. Read each verdict aloud to the user.

## Decision tree

- **accept** → auto-persisted, nothing to do. Move on.
- **swap_pending_confirmation** → the bench has a better/alternative route. **Always
  ask** the user, then on agreement: `mm sources accept <draft_id> <original> --as swap`.
- **unknown** → the bench doesn't list it. See the shortcut below.
- **reject** → **always ask**; propose alternatives from the bench's suggestions.
  Accept a reject override only with explicit user confirmation:
  `mm sources accept <draft_id> <original> --as reject` (rarely).

## Accept-on-trust shortcut for `unknown`

When the user names a specific source **as part of their market request** ("Sources:
espn.com", "use aemet.es"), treat that as implicit accept-on-trust for `unknown`
verdicts:

1. `mm sources validate <draft_id> <their list>`
2. For each `accept` — done.
3. For each `unknown` — `mm sources accept <draft_id> <src> --as unknown` **without
   re-asking**, then briefly note it: "Accepted `aemet.es` as unknown — the bench
   doesn't list it, so the oracle fetches it directly at resolve."
4. For each `swap`/`reject` — **always** ask. Here the bench is telling you the
   user's choice is probably wrong; don't override silently, don't auto-accept.

If the user names a source **mid-conversation** ("also add bbc.co.uk"), same logic —
they just said it, so it's their stated preference for `unknown`.

The exception: if you fetched the source yourself (`library/10-research.md`) and it's
clearly broken (paywall/login wall, or 404), ask before accepting even if named.

## Don't

Never auto-rewrite a source to a different one. Never accept a `swap`/`reject`
without explicit confirmation. The bench decides validity; you read the verdict and
get the user's call.
