# MicroMarkets (Hermes) — a SubZeroClaw skillset

This is the MicroMarkets agent **Hermes** — its personality and skills — re-expressed
natively in SubZeroClaw's flat-markdown, shell-first idiom. It's a portable skillset:
point a SubZeroClaw agent at it and you get Hermes's voice and playbooks. It does not
change the C runtime.

Adapted from `micromarkets_v2/hermes/` (system prompt + numbered skills + the
2026-05-29 skills-architecture spec).

## The design in one paragraph

SubZeroClaw `cat`s **every top-level `*.md`** in the skills dir into the system
prompt **every turn**, and ignores subdirectories. Hermes's design is the opposite:
a deterministic bridge loads only the one relevant skill. We reconcile the two
**without touching C**: a small **CORE** (always loaded) plus a **`library/`**
subfolder (never auto-loaded) that the agent pulls on demand with its own `shell`
tool. `index.md` is the router — it reads intent and tells the agent which
`library/<file>.md` to `cat`. This keeps the prompt lean, keeps skills small and
independently editable, and reproduces Hermes's selective loading in SubZeroClaw's
own idiom.

```
soul.md            CORE  identity + voice (proactive, one-proposal, warm)
index.md           CORE  the router: intent -> which library file to cat
constraints.md     CORE  always-on hard rules (the analog of "tools enforce")
memory.md          CORE  durable user facts (## Saved Facts)
working-state.md   CORE  the draft currently in progress
library/                 NOT auto-loaded; agent cats on demand
  00-competence.md  10-research.md
  20-template-event-by-date.md  21-template-account-says.md
  22-template-page-shows.md     23-template-claim-confirmed.md
  30-source-vetting.md  40-create-flow.md
  50-discovery-and-betting.md   60-resolution-and-settlement.md
  owner-self-modification.md
```

## How to use it

The 5 CORE files must be the **top-level** files in the agent's skills dir, with
`library/` as a subdir beneath them:

```bash
# point subzeroclaw's default skills dir at this skillset
ln -s "$(pwd)/skillsets/micromarkets" ~/.subzeroclaw/skills
# or copy: cp -r skillsets/micromarkets/* ~/.subzeroclaw/skills/
```

Then the runtime loads the 5 CORE files; the agent `cat`s `library/...` itself. To
try it in the dev playground, drop the CORE files in `subzeroclaw-dev/skills/` and
the `library/` folder inside it.

## "Skills advise; tools enforce" — the boundary

Hermes's safety law is that guards live in tools/workers and fire regardless of what
a skill says. SubZeroClaw has only `shell`, so we keep the law via an `mm` CLI the
skills call. **Two halves:**

- **Research is native** — `search_web`/`peek_url` become plain `curl` (+ `python3`
  to strip HTML). This works today, no backend. It's what makes the proactive UX
  land. See `library/10-research.md`.
- **Stateful/guarded ops go through `mm`** — `mm` owns state, money, deadlines, and
  chain, and enforces the guards on every call. The skills *advise*; `mm` *enforces*.
  Even if the agent skips a step, `mm confirm` refuses an unvetted source or a bad
  date.

**`mm` is a contract specified here, not built in this skillset** (it's the
integration seam to the existing `micromarkets_v2` MCP tools / workers).

### `mm` CLI contract

| `mm` command | wraps (MCP tool) | guard it enforces |
|---|---|---|
| `mm draft propose "<msg>"` | propose_market_draft | creates draft; returns draft_id, template_hint, missing_fields |
| `mm draft set <id> <field>="<v>"` | update_market_draft | ALLOWED_FIELDS whitelist, EDITABLE_STATES, creator-only |
| `mm draft show <id>` | (read) | — |
| `mm sources validate <id> <src…>` | validate_sources | bench gate (no auto-substitute) |
| `mm sources accept <id> <src> --as swap\|unknown\|reject` | accept_source | explicit creator confirmation |
| `mm preview <id>` | render_draft_preview | missing_fields must be empty |
| `mm confirm <id>` | confirm_market | validate_eight_fields, creator-only, freezes config |
| `mm cancel <id>` | cancel_draft | pre-confirm only |
| `mm markets list [--user <id>] [--scope all\|mine]` | list_open_markets | read-only |
| `mm markets show <id>` | get_market_status | read-only |
| `mm bet <id> <a\|b> --user <id>` | handle_button_press(join) | open-to-anyone; service renders deposit card |
| `mm resolve <id> --user <id>` | request_resolve | creator-only; min-open duration |

(Appeal/cancel-after-deploy exist in the backend too; add rows when wired.)

## What this delivers (vs. a naive port)

- **Natural proactive conversation** — `soul.md` always loaded; real `curl` research.
- **Fast, legible iteration** — small one-job files; edit one, it's live next `cat`;
  `owner-self-modification.md` lets the agent co-author its own skills.
- **More autonomy** — shell-first research; agent pulls skills on demand; only
  `constraints.md` is an always-on rail.
- **Continuity** — `memory.md` + `working-state.md` always loaded.

## Known tradeoff

Selection is **agent-driven**, not bridge-deterministic — the agent *could* skip
pulling `30-source-vetting`. Backstops: the rule is in always-loaded `constraints.md`,
the checklist is embedded in `40-create-flow.md`, and `mm` enforces server-side when
wired. This is weaker than Hermes's deterministic bridge by design — the goal here is
autonomy, and no money/chain is wired in this skillset.
