# MicroMarkets (Hermes) — a SubZeroClaw skillset

The MicroMarkets agent **Hermes** — personality + skills — re-expressed natively in
SubZeroClaw's flat-markdown, shell-first idiom. The agent is a thin conversational brain:
it owns three verbs — **draft, query, deploy** — and `curl`s HTTP services for everything
stateful. It holds no keys and never touches chain.

## Architecture it targets

```
Telegram → Router → subzeroclaw agent (this skillset; draft·query·deploy via curl)
                          │ curl
        market-service (front half)  ──POST /markets──▶  oracle-service (back half)
        draft→validate→deploy→betting→lock                resolve→settle/refund
```

The agent never deploys chain or handles money. `market-service` does draft→deploy→lock;
`oracle-service` does resolve→settle. The agent just talks HTTP to them (see `api.md`).

## Loading model

SubZeroClaw `cat`s every top-level `*.md` every turn and ignores subdirs. So:

```
soul.md          CORE  identity + voice (proactive, one-proposal, warm)
index.md         CORE  router: intent → which library file to cat
constraints.md   CORE  always-on hard rules (services enforce them too)
api.md           CORE  the curl contract: service URLs, endpoints, copy-paste templates
memory.md        CORE  durable user facts
working-state.md CORE  the draft in progress
library/               NOT auto-loaded; agent cats on demand
  00-competence 10-research 20–23 templates 30-source-vetting
  40-deploy-flow 50-query 60-resolution-and-settlement(read-only) owner-self-modification
```

## Use it

Top-level CORE files in the skills dir, `library/` beneath; set the service URLs as env:

```bash
ln -s "$(pwd)/skillsets/micromarkets" ~/.subzeroclaw/skills
export MARKET_SERVICE_URL=http://localhost:8001 ORACLE_SERVICE_URL=http://localhost:8000 SZC_CREATOR_ID=1
```

In the dev playground the agent reaches the services on `host.docker.internal`. In
production the Telegram router supplies `SZC_CREATOR_ID` (the user) and a bearer token.

## Boundary

`api.md` is the only contract the skills depend on. Guards live server-side in
market-service `ingest` + oracle-service `ingest` (the trust boundary): a wrong/hostile
skill can only produce a 400 `refused`, never an ungated source or a bad on-chain market.
Skills advise; the services enforce.
