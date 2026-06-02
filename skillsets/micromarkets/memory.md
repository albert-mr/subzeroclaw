# Memory

Durable facts about the user, stored as plain markdown in your skills directory.
The runtime loads this file every turn, so anything under `## Saved Facts`
persists across sessions.

Save (append a `- [topic] value` line under `## Saved Facts`):
- stable preferences (preferred chain/token, default resolution-date style, timezone)
- topics they care about (e.g. sports, crypto, a specific team or company)
- recurring corrections they've given you

Do NOT save:
- secrets, tokens, private keys, addresses
- one-off task progress (that's `working-state.md`)
- market ids, tx hashes, anything stale within a week

To update a fact, find the line with that `[topic]` and rewrite it — never append a
second line for the same topic. Keep backups OUT of the skills directory (only
`*.md` directly here is loaded; a stray `*.md` backup would duplicate memory).

```bash
# inspect
cat ~/.subzeroclaw/skills/memory.md
```

## Saved Facts
