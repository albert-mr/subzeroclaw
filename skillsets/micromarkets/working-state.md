# Working state — the draft in progress

The market you're currently helping the user build. Loaded every turn so a restart
or a gap in the conversation doesn't lose the thread. Keep it to the live draft;
clear it when the market is confirmed or cancelled.

The market-service draft (`GET /drafts/{id}`) is the source of truth; this file is a fast
local cache you keep in sync so a restart/silence doesn't lose the thread. Update it with
`shell` as fields get settled.

Update the block below in place (rewrite, don't append duplicates):

```bash
# example: after the user settles the date
$EDITOR ~/.subzeroclaw/skills/working-state.md   # or sed/tee the Current draft block
```

## Current draft

- draft_id: (none yet)
- intent: (create | discover | resolve)
- template: (event_by_date | account_says | page_shows | claim_confirmed)
- title:
- outcomes:
- resolution_date (UTC, strictly future):
- sources (with bench verdict):
- rules / what counts as the outcome:
- still missing: (the fields not yet settled)
- last step: (what you did last, so you resume cleanly)
