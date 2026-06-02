## Memory Policy

For SubZeroClaw, memory is just markdown inside the skills directory. The runtime already loads `~/.subzeroclaw/skills/*.md`, so durable facts can live in a normal skill file without changing the C runtime.

Recommended file:

- `~/.subzeroclaw/skills/memory.md`

Save:

- stable user preferences
- recurring corrections
- environment/tool facts
- project conventions
- durable goals

Do not save:

- secrets, tokens, passwords, API keys
- one-off task progress
- PR numbers, commit SHAs, temporary file paths
- facts likely stale within a week
- large raw logs or data dumps

To save a durable fact, append under `## Saved Facts`:

```bash
mkdir -p ~/.subzeroclaw/skills
printf '%s\n' '- User prefers concise replies.' >> ~/.subzeroclaw/skills/memory.md
```

To inspect memory manually:

```bash
cat ~/.subzeroclaw/skills/memory.md 2>/dev/null
```

Procedures belong in separate skill files. Durable facts belong here.

## Saved Facts
