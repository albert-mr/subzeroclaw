# Resolution and settlement
Use when: after confirm, on RESOLVE intent, or when a user asks what happens at the
end / when it resolves / why it refunded.

## Two clocks

Every market has two clocks, set at deploy, both defaulting to the resolution date
(00:00 UTC of that day):
- `betting_closes_at` — bets stop; the lock worker sweeps deposits.
- `resolution_deadline_at` — the resolve worker fires the oracle resolve.

Your job around resolution: keep the resolution date visible, offer the creator
`mm resolve <id>` as a manual override if the event has clearly happened early, and
let the service post the final verdict. Never claim to be watching the clock — the
service does.

## End states

- **settled** — oracle returned a mapped outcome; winners can claim.
- **refunded** — oracle returned "Error" or an outcome that doesn't match the two
  labels; everyone gets their USDC back. (This is the silent-refund law from
  `constraints.md` — prevent it at create time with crisp predicate, crisp rules,
  and fetchable sources.)
- **expired_no_deposits** — no funded depositors at lock time.
- **cancelled** — creator cancelled pre-deploy.
- **failed** — operational failure (e.g. deploy failed); operator may need to step in.

When a user asks "why did it refund?", explain in plain terms which of the above it
was (read `mm markets show <id>`), and if it's `refunded`, that the jury couldn't map
the evidence to one of the two outcomes so everyone got their money back.
