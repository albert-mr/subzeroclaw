# Create flow
Use when: intent == CREATE — walking a draft from propose through missing fields to
preview and confirm.

The choreography. Keep `working-state.md` updated as you go so a restart resumes
cleanly.

1. **Propose.** `mm draft propose "<the user's raw request>"` → returns `draft_id`,
   `template_hint`, and `missing_fields`. Record the draft_id in `working-state.md`.
2. **Gate it.** `cat library/00-competence.md` — confirm it fits one template and a
   jury can resolve it. Pull the matching `20–23` template playbook.
3. **Fill missing_fields conversationally** (not a form). For each field the user
   settles: `mm draft set <draft_id> <field>="<value>"`. Use `library/10-research.md`
   to find unknown facts/dates yourself.
4. **Sources.** For every domain/URL: follow `library/30-source-vetting.md`
   (`mm sources validate` → read verdicts → `mm sources accept` where the tree says).
   Re-read that playbook whenever sources change. **Don't skip this** (constraints).
5. **Preview — only when `missing_fields` is empty.** `mm preview <draft_id>`. Post
   the preview the user with its inline buttons (Confirm / Edit / Cancel).
6. **Confirm.** When the user says "ok / do it / yes", `mm confirm <draft_id>`
   immediately — no extra source, no "are you sure?". Then ONE warm acknowledgement;
   the service deploys the oracle + market and takes over. Clear `working-state.md`.
7. **Cancel** (pre-confirm only): `mm cancel <draft_id>`. After confirm the market is
   worker-owned and cancel is refused (a creator can still `mm resolve` early — see
   `library/60-resolution-and-settlement.md`).

## If a command is refused

`mm` enforces the guards (creator-only, missing fields, source not vetted, bad date,
etc.). If it returns refused, surface the reason in plain English and fix the cause —
don't auto-retry. This is the backstop: even if you skipped a step, `mm confirm`
won't ship a broken market.
