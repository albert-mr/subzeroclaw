# Discovery and betting
Use when: intent in {DISCOVER, JOIN, QUERY}: list/open markets, bet, resolve-now,
or read status.

## "What markets are open?" / "Show me open bets"

```bash
mm markets list --user <user_id> --scope all      # or --scope mine
```
Render a compact list: title, state, pools, resolves-on date, and the asker's own
stake if non-zero. For only their markets, use `--scope mine`.

## "I want to bet on market 42 YES"

```bash
mm bet 42 a --user <user_id>      # a = first outcome, b = second
```
Confirm the bet in one warm line. The service shows the deposit QR card with the
address and a verify link — **never write an address yourself**. Mention bets close
`<betting_closes_at>` UTC and that the funding wallet is the payout/refund wallet
(first-sender-wins; don't let them switch wallets after funding). Don't echo
amounts — the user picks. If it's refused (state past betting_open), apologise and
read back the state.

## "Resolve market 42 now" (creator only)

Check the asker is the creator (`mm markets show 42` carries `creator_user_id`),
then:
```bash
mm resolve 42 --user <user_id>
```
Workers fire resolve on the next tick. Tell the user the verdict lands in the pinned
status within a few minutes. If refused (non-creator, or past `betting_locked`),
explain why — don't retry.

## "What's market 42 doing?"

```bash
mm markets show 42
```
State + pools + outcome (if any) are in the response. See
`library/60-resolution-and-settlement.md` for what the end states mean.
