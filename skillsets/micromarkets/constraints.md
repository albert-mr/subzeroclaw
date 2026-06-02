# Constraints (always on)

These hold no matter which `library/` playbook you've pulled. They're advice, not
enforced here — but **market-service/oracle-service enforce** the load-bearing ones
server-side (a deploy with a bad/unvetted field returns a 400 `refused`), so don't
fight them.

- **Four templates only.** A market must fit `event_by_date`, `account_says`,
  `page_shows`, or `claim_confirmed`. Anything else — private, subjective, or
  unobservable ("will my startup succeed?") — reshape it or say plainly it can't
  be a market.
- **Natural-language outcome labels — never the defaults.** Two outcomes, binary,
  short (they go on the bet buttons), parallel (both phrase the same side). E.g.
  "Will ETH close above $2,050 on May 29?" → ["Yes","No"]; "Will Argentina win the
  final?" → ["Argentina wins","Argentina doesn't win"]. Never ship
  `happened/did_not_happen`.
- **Sources must be public, stable, non-auth, non-ephemeral.** No private groups,
  screenshots, paywalled or login-gated pages.
- **Never auto-rewrite a source.** If the bench says a domain is blocked or only
  reachable via an alternative, surface the swap and get explicit confirmation
  (`POST /drafts/{id}/accept-source`). Unknown sources also need explicit confirmation.
- **Always run source-vetting before you propose** (`library/30-source-vetting.md`).
  Skipping it is the one thing that breaks markets quietly.
- **Resolution date strictly after today (UTC).** Parse what the user said into a
  crisp future date ("June 6" → 2026-06-06, "end of month" → last day). Never
  default to today — a same-day date collapses the betting window.
- **Only the creator** can confirm, cancel, or request early resolution on their
  draft. Once a market is `confirmed`, no one edits it — cancel and re-create.
  Any user can bet on an open market, including from another chat.
- **The silent-refund law.** If the jury's answer doesn't map to one of your two
  outcomes, the market does NOT error — it **silently refunds everyone**. Crisp
  predicate + crisp "what counts" rules + fetchable sources are how you prevent a
  market that quietly gives the money back instead of resolving.

You set the **predicate, the date, and the sources** (subject to the above) and call
the services per `api.md`. You never set or name an amount, an address, a nonce, a
deadline, or a state transition — the services own money, clocks, and chain.
