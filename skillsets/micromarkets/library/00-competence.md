# Competence self-assessment — the create front door
Use when: intent == CREATE. Run this FIRST, before any template playbook. Re-enter
on any fragile signal (a bench reject, a source that smells like an SPA/paywall/
login-wall, a weak type↔source pairing, or rules too fuzzy to map to two outcomes).

Before you propose a market, run this gut-check. It's how you know which markets you
can do, and how well — so you steer toward the ones that resolve cleanly and are
honest about the ones that don't.

## The three questions (all must be "yes")

1. **Does it fit ONE of the four templates?** event_by_date, account_says,
   page_shows, claim_confirmed. If it fits none — private, subjective, unobservable
   ("will my startup succeed?") — it can't be a market. Say so plainly.
2. **Can an LLM jury resolve it from PUBLIC sources at the date?** Checkable by
   reading public pages, not a guess or a feeling or something only the user sees.
3. **Is the source machine-fetchable at resolve time?** Server-rendered HTML or a
   public JSON endpoint — not a JS SPA or an auth/login wall. Use
   `library/10-research.md` to find and read candidates, `library/30-source-vetting.md`
   to read the bench verdict.

## Route to exactly one template

- event_by_date → `library/20-template-event-by-date.md`
- account_says → `library/21-template-account-says.md`
- page_shows → `library/22-template-page-shows.md`
- claim_confirmed → `library/23-template-claim-confirmed.md`

`mm draft propose` returns a `template_hint`, but that's a regex fallback — you pick
the template from what the user actually wants.

## How well you do each type — be honest

- **event_by_date — STRONG.** Crisp dated event, server-rendered bench-direct
  sources. Steer here whenever the question can be shaped this way.
- **claim_confirmed — STRONG** when "confirmed" is crisp and there are ≥2 reliable
  sources. The jury's cross-source weighing is its sweet spot.
- **page_shows — USABLE.** Great for a server-rendered page or public JSON; weak for
  price/pricing pages and client-rendered SPAs (they read as chrome). A price
  question is usually better reshaped than forced onto a fragile fetch.
- **account_says — WEAK today.** The native Twitter backend isn't wired in, so it
  resolves only through a server-rendered news mirror. A raw `x.com` link is a bad
  resolving source (login/feed forms are hard-rejected; a plain status URL is
  JS-rendered → comes back `unknown` → silently refunds). Steer to a news mirror or
  reshape to claim_confirmed.

## Reshape › disclose › decline (in that order)

- **Reshape** to a stronger type when you honestly can (the canonical move:
  account_says → claim_confirmed, "Will reliable sources confirm @x said Y by DATE?").
- **Disclose** the weakness when it can still be built but is fragile — tell the
  user the resolve path and its risk before they commit.
- **Decline** only when it fits no template at all.

The through-line: when the jury's answer doesn't map to one of your two outcomes,
the market doesn't error — it **silently refunds everyone** (see `constraints.md`).
Every "yes" above protects the user from a market that quietly gives the money back.

## Don't

This skill only judges **fit and resolvability**. Don't decide a source is valid
(that's `30-source-vetting`). Don't force a market into a template to avoid
declining, and don't bury a weakness to keep the draft moving — honesty is the job.
