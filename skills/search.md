## Agentic Search

SubZeroClaw has one tool: shell. Use ordinary Unix commands directly. Do not wait for custom adapters.

### Search prior sessions

Session logs live in `~/.subzeroclaw/logs/`.

Prefer ripgrep if installed:

```bash
rg -n -i 'query terms' ~/.subzeroclaw/logs
```

Fallback to grep:

```bash
grep -Rni 'query terms' ~/.subzeroclaw/logs 2>/dev/null
```

Use this before asking the user to repeat old project context.

### Search current public information

Use `curl` against a simple web-search endpoint, or any search CLI installed on the host. Example DuckDuckGo HTML query:

```bash
python3 - <<'PY'
import urllib.parse
q = urllib.parse.quote_plus('query terms')
print('https://html.duckduckgo.com/html/?q=' + q)
PY
curl -L -A 'Mozilla/5.0' 'https://html.duckduckgo.com/html/?q=query+terms'
```

For a known URL, fetch it directly:

```bash
curl -L -A 'Mozilla/5.0' 'https://example.com'
```

If HTML is noisy, strip it with Python. Use `python3 -c` so piped HTML stays on stdin:

```bash
curl -L -s 'https://example.com' | python3 -c 'import re,sys,html; text=sys.stdin.read(); text=re.sub(r"(?is)<(script|style).*?</\1>", " ", text); text=re.sub(r"(?s)<[^>]+>", " ", text); print(html.unescape(re.sub(r"\s+", " ", text)).strip()[:12000])'
```

### Workflow

1. For old project/user context, search logs first.
2. For current facts, search/fetch with shell tools.
3. Summarize findings; do not paste huge raw pages.
4. If a durable fact matters later, append it to memory.
5. If the process becomes reusable, write or update a skill.
