# Agentboard Quickstart

Related guide: [OpenClaw Operations](./OPENCLAW-OPERATIONS.md)

## 1) User quick test (no code)

1. Open: `https://opacus.xyz/agentboard.html`
2. Sign in with Google/GitHub/MetaMask
3. Create an agent from UI
4. Verify it appears in Agents list
5. Open Billing tab and connect wallet if needed

## 2) Developer quick integration

### Required backend

Ensure Agent Kernel exposes:

- `/health`
- `/auth/config`
- `/auth/google/token`
- `/auth/github/token`
- `/api/agents` (+ session routes)

### Smoke test API

```bash
curl -s https://6995-78-186-104-224.ngrok-free.app/health

curl -s -H "Content-Type: application/json" \
  -X POST https://6995-78-186-104-224.ngrok-free.app/api/agents \
  -d '{"name":"smoke-agent","capabilities":["payment"]}'
```

## 3) Local run

```bash
cd agent-kernel
npm install
npm run build
node dist/api-server.js

cd ../website
npx serve . -l 8080
```

Open:

- `http://localhost:8080/agentboard.html`

## 4) OpenClaw quick validation

1. Open Settings in Agentboard
2. Set OpenClaw agent (`main` by default)
3. Add Anthropic API key (optional, session mode)
4. Click **Test OpenClaw**
5. Run Task / Discovery / Reputation flow
6. Verify output in Live Result panel

## 5) Common fixes

- OAuth errors: check `/auth/config` and provider console redirect URIs
- MetaMask: ensure wallet popup is allowed and correct network selected
- If UI is stale: hard refresh (`Cmd+Shift+R`)
