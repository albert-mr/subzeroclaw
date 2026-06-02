# Template: page shows
Use when: intent == CREATE and the market hinges on what a specific page, API, or
URL displays by a date.

Use **page_shows** when the market is about **what a specific web page or API
endpoint displays** by a date — "Will the standings page list X first?", "Will this
status page show 'operational'?", "Will this JSON endpoint return a count over N?".
The tell is a **single concrete URL or endpoint** the user can point at, not a fuzzy
fact you'd research across the web.

If the user is really asking "did EVENT happen" or "did SOURCES confirm CLAIM" and
the URL is just where they spotted it, you want event-by-date or claim-confirmed
instead — those let a jury weigh a whole domain. Reach for page_shows only when
*that exact page* is the thing being predicted.

This is the **only** template where the source is a full URL, not a bare domain.
That makes the URL itself load-bearing — see the failure mode.

## Predicate shape

One precise, binary, page-anchored question with a date:

> **"Will the ATP live rankings page show Sinner at #1 on 2026-06-30?"**

Pin a crisp future date early. Make it checkable *as rendered*: if a human reading
that URL on the date can't unambiguously say yes/no, the jury can't either.

## Outcome labels

`shows` / `does not show`, phrased to read in the question ("**shows** Sinner at #1"
/ "**does not show** Sinner at #1"). Concrete to the page state, not abstract truth.

## Good sources

Use `library/10-research.md` and `library/30-source-vetting.md`. The one
type-specific check: this wants a **server-rendered HTML page or a public JSON/API
endpoint** that returns the real data on a plain fetch. When you read a candidate,
confirm the **data you care about is in the response**, not just chrome. Strong:
Wikipedia, a standings article, a public status page, a documented read-only API.
Weak: anything client-rendered (price tickers, dashboards, team-site SPAs).

**Acceptance order matters here.** The **first source you accept** is the exact URL
the jury fetches at resolve. Lead with the single best machine-fetchable URL and
accept it first; don't bury it behind a domain you added for context.

## The silent-refund failure mode — disclose up front

If the page is client-rendered (a JS SPA) or paywalled at resolve time, the jury's
fetch is empty chrome, the rules can't be satisfied, and the result maps to neither
outcome — which **silently degrades to a full refund**. Say it plainly: "This
resolves by fetching that exact URL. If the page loads its data with JavaScript, the
oracle sees only the empty shell and the market refunds instead of resolving — so we
want plain HTML or a JSON endpoint." If it's a price/pricing SPA, name it as a job
for a future price-oracle and offer to reshape.

## Don't

Don't promise a page will fetch correctly — advise render-mode, don't guarantee it.
Don't auto-accept a verdict or rewrite the user's URL silently (defer to
`30-source-vetting`). Don't name amounts, addresses, or state transitions.
