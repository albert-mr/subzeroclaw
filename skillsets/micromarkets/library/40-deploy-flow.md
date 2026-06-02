# Deploy flow
Use when: intent == CREATE/DEPLOY — walking a draft from propose through fields to
preview and deploy. All calls are `curl` to market-service (see `api.md`).

Keep `working-state.md` updated as you go so a restart resumes cleanly.

1. **Propose.** `POST $MARKET_SERVICE_URL/drafts {raw, creator_id}` → `draft_id`,
   `template_hint`, `missing_fields`. Record the `draft_id` in `working-state.md`.
2. **Gate it.** `cat library/00-competence.md` — fits one template + jury-resolvable?
   Pull the matching `20–23` template playbook.
3. **Fill `missing_fields` conversationally.** For each settled field:
   `PATCH /drafts/{id} {creator_id, title|template|resolution_date|outcomes|rules}`.
   Use `library/10-research.md` (curl) to find unknown facts/dates yourself.
4. **Sources.** Follow `library/30-source-vetting.md`: `validate-sources` → read verdicts
   → `accept-source` where the tree says. Re-read it whenever sources change. **Don't skip.**
5. **Preview — only when `missing_fields` is empty.** `POST /drafts/{id}/preview` → show
   the returned card to the user with a confirm prompt.
6. **Deploy on confirm.** `POST /drafts/{id}/deploy {creator_id}` → 202 `{state:"deploying"}`.
   The service deploys oracle+market, opens betting, locks, and hands off to oracle-service.
   Send ONE warm acknowledgement. Then `GET /drafts/{id}` to report when it reaches
   `handed_off` (carries `oracle_market_id`). Clear `working-state.md`.
7. **Cancel** (pre-deploy only): `DELETE /drafts/{id} {creator_id}`. After deploy it's
   service-owned (a creator can early-resolve via oracle-service — see `50-query.md`).

## If deploy returns 400 `{detail:{refused:[...]}}`

A guard tripped (missing/bad field, unvetted source, past date). Read the reasons, fix the
cause in plain conversation, re-`PATCH`/re-vet, then deploy again. This is the backstop:
even if you skipped a step, deploy won't ship a broken market.
