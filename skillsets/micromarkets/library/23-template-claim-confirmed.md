# Template: claim confirmed
Use when: intent == CREATE and the market asks whether a CLAIM gets confirmed/
verified/reported by reliable sources before a date ("will it be confirmed",
"verified by", "reported by").

This is the jury's sweet spot. Reach for it when the market is "did the world's
reliable reporting **converge** on a fact by a date?" — a claim several independent
outlets either corroborate or don't. If it's really about one specific page, that's
page-shows; if it's a clean scheduled event with a definitive record, that's
event-by-date. Use claim-confirmed when the answer lives in **the weight of multiple
reports** — exactly what the IntelligentOracle jury is best at. **Strong**, *if* you
do the one job below.

## Predicate shape

A claim, plus what "confirmed" concretely means, plus a date:

> **Will the claim "Acme Corp acquired Beta Inc" be confirmed by reliable sources
> before 2026-06-15?**

Pin the date crisply and early. The claim should be a single checkable proposition,
not a bundle of "and/or" sub-claims.

## Outcome labels

Frame around *confirmation*, not the raw event: `confirmed` / `not confirmed`, or
`reported by multiple outlets` / `unreported or disputed`. Binary and mutually
exclusive.

## Good sources

This template wants **at least two independent, server-rendered news/encyclopedia
domains** so the jury has something to corroborate across — a single source defeats
the point. Reuters + AP + Wikipedia + a major outlet is the canonical shape. Use
`library/10-research.md` and `library/30-source-vetting.md`. More than one direct
source is a quality signal here, not just nice-to-have.

## Silent-refund failure mode — disclose up front

The biggest risk is a **fuzzy definition of "confirmed."** If the rules don't pin
what counts, the jury can return a verdict that matches neither label, and an
unmapped outcome **silently degrades to a full refund**. Make "confirmed" crisp in
the rules, conversationally, before preview. Spell out the bar: e.g. "*Confirmed*
requires **at least two of the listed outlets** to report the acquisition as
completed (not 'in talks', not 'rumored'); a single report or a denial counts as
*not confirmed*." Tell the user: vague criteria mean the market may just refund. A
few seconds nailing the bar is the whole job of this skill.

## Don't

Don't decide source validity (the bench does). Don't set/compute the resolution
date — help the user state the date and the confirmation bar in plain language.
Don't name amounts, addresses, or state transitions.
