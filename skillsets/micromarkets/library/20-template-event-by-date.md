# Template: event by date
Use when: intent == CREATE and the market is whether a scheduled/anticipated event
happens by a date — a match, fixture, launch, vote, hearing, release.

This is the bread-and-butter market and your **strongest** one. Reach for it
whenever the question is "did this **thing happen** by a **date**?" and the answer
is just yes-it-happened or no-it-didn't.

## Predicate shape

One crisp binary tied to a specific calendar date:

> **"Will Barça beat Real Madrid on 2026-06-04?"**
> **"Will Apple announce a foldable iPhone by 2026-09-30?"**

A named subject, a single clear thing that either occurs or doesn't, and a date you
can pin down. "Will Barça have a good season?" is not this; "Will Barça win the
Clásico on 2026-06-04?" is.

## Outcome labels

Match the verb in the question, phrase both sides around the event:

> win → ["Barça wins", "Barça doesn't win"]
> announce/launch → ["Yes, announced", "No"]
> happen by a date → ["Yes", "No"]

## Good sources

Sweet spot: bench-direct, **server-rendered** pages — Wikipedia season/event pages,
Reuters/AP/BBC articles, government/election results, a league's wiki article. Use
`library/10-research.md` for the search→read loop and `library/30-source-vetting.md`
for the verdict. Steer away from team/league SPAs that return chrome.

## Disclose up front: two silent-failure traps

Both fail quietly later, not as an error in chat — be honest while drafting:

1. **A fuzzy or non-future date wedges the market.** The date is only hard-checked
   at deploy, after Confirm. A vague ("sometime this summer") or same-day date sails
   past the conversation and the market lands in `failed`. Pin a crisp date strictly
   after today before you propose — "June 6" → 2026-06-06, "end of month" → last day.
2. **A mushy event description silently refunds.** If the event is so ambiguous the
   jury can't return one of your two outcomes, the market refunds everyone. Tighten
   the subject and the bar for "happened" (which competition? does extra time count?).

## Don't

Don't invent the resolution date — parse what they said into a crisp future date and
confirm it back. Don't name an amount, address, or state transition (`mm` owns
those). Don't decide source validity — that's the bench.
