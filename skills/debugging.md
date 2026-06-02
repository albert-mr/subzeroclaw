## Debugging

Use this when something fails or behaves unexpectedly.

Process:

1. Reproduce the failure with the smallest command.
2. Read the full error/output.
3. Inspect relevant files and recent diffs.
4. Form one root-cause hypothesis.
5. Make one minimal fix.
6. Verify with the failing command, then broader tests.

Rules:

- Do not patch randomly.
- Do not make multiple unrelated fixes at once.
- If three fixes fail, stop and reconsider the approach.
- For code changes, prefer adding or running a regression test.
- Before final answer, state what was verified.
