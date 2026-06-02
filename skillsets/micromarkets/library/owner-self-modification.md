# Owner: editing yourself
Use when: the owner asks you to change your behavior, prompt, or skills. (Only the
owner — if you're unsure who the owner is, ask; non-owners get "that's owner-only".)

On SubZeroClaw your skills are plain markdown files in your skills directory, and you
have `shell`. So self-modification is just reading, editing, and committing those
files — no special tool. This composes with the standard `skill-management` habits.

## The loop

1. **Read first.** `cat ~/.subzeroclaw/skills/<file>.md` (or `library/<file>.md`).
2. **Make the smallest targeted change.** Edit the file (heredoc/`tee`/`sed`).
   - Tone/identity → `soul.md`.
   - A rule that must always hold → `constraints.md`.
   - Routing (a new intent or template) → `index.md`.
   - A market type or phase → the matching `library/<n>-*.md`, or add a new
     `library/<name>.md` and add a row to `index.md`.
3. **Confirm before committing.** Show the owner the diff (`git diff`) or the
   proposed new file and ask. Once they confirm, commit immediately — git history is
   the audit log; they don't want to hand-track changes.
   ```bash
   cd <repo> && git add -A && git commit -m "skills: <one-line summary>"
   ```
4. **Verify** the markdown is clean and doesn't conflict with another skill (no two
   files giving contradictory instructions — remember every CORE file loads every
   turn).

## Rules

- Keep backups OUT of the skills directory. A stray `*.md` backup at the top level
  loads into the prompt and duplicates/contradicts guidance. Use git, or `/tmp`.
- Skills are conversational guidance, not authoritative state. Never put a secret,
  a key, or chain-critical config in a skill file.
- New library skills are not auto-loaded — they only take effect once `index.md`
  points to them and you `cat` them. Wire the router when you add one.
