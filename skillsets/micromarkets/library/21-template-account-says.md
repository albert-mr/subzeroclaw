# Template: account says
Use when: intent == CREATE and the market is whether a named account/person will
post, tweet, or say something by a date ("@handle", "tweet", "post", "said").

**Be honest up front: this type is weak today.** The natural source —
`x.com`/`twitter.com` — is a login-walled JavaScript app. A plain fetch gets page
chrome, not the post, and the bench's auth-wall gate **hard-rejects** `x.com/i/web`
outright. There's a proven native Twitter backend in the older system, but it is
**not wired into this version**. So right now this market resolves only through a
**server-rendered news mirror** that reported the statement. Tell the user that
before you build it.

## Predicate shape

One account, one statement, one date — concrete enough that a news report would
clearly confirm or deny it. Note the reshape: the subject becomes the **news
mirror**, not the raw tweet.

> **Will Reuters report that @elonmusk announced a Tesla price cut by 2026-06-15?**

If the user only cares about the literal post existing and won't accept a news
mirror, say so and offer to **reshape into `claim_confirmed`** ("Will reliable
sources confirm @x said Y?") — that plays to the jury's strength.

## Outcome labels

A clean two-way split that reads without the title: `said` / `did not say`, or
`posted it` / `didn't post it`. No "maybe", no "deleted later".

## Good sources

Use `library/10-research.md` and `library/30-source-vetting.md`. Type-specific:

- **Best:** a server-rendered news outlet that *quotes the statement* (Reuters, AP,
  BBC, established trade press). The mirror reported it; the jury can read it.
- **Worst case, disclose it:** a raw `x.com` URL. Login/feed forms are hard-rejected
  before the bench; a plain `x.com/<user>/status/<id>` isn't gate-rejected but is
  JS-rendered → `unknown` → **silently refunds** at resolve. Don't propose a raw
  tweet as the resolving source — steer to a mirror.
- A Nitter-style aggregator is unreliable; prefer a real outlet that *covered* it.

## The silent-refund failure mode — say it before building

If the jury's answer doesn't land on exactly `said`/`did_not_say`, the market
**silently refunds everyone**. For this type that's the *likely* path when the only
evidence is the raw tweet. Frame it plainly: "With only a Twitter link this will
probably **refund** rather than resolve. With a news mirror that covered it, it
resolves cleanly." Let the user choose with eyes open.

## Don't

Never claim a direct `x.com` link will resolve, never promise the native Twitter
backend (it's not wired in), and never silently accept a weak source to push the
draft forward — disclose the refund risk and let the user decide.
