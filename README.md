# Opacus Agentboard

Production-ready web dashboard for creating, managing, and observing autonomous agents on Opacus.

## What this repository contains

- `agentboard.html` — Single-file dashboard UI (auth, agent lifecycle, billing, API key management)
- `QUICKSTART.md` — Fast setup and usage guide

## Live deployment

- Production: https://opacus.xyz/agentboard.html
- API (current tunnel): https://6995-78-186-104-224.ngrok-free.app

## Core capabilities

### 1) Authentication

- Google OAuth (backend code exchange)
- GitHub OAuth (backend code exchange)
- MetaMask login with **mandatory signature** (`personal_sign`)
- Automatic fallback to email login when OAuth is not configured

### 2) Agent lifecycle

- Create agent (`POST /api/agents`)
- List agents (`GET /api/agents`)
- View runtime/metrics (`GET /api/agents/:sessionId/runtime`)
- Delete agent (`DELETE /api/agents/:sessionId`)

### 3) Billing / Payments

- Wallet connect + network checks
- On-chain balance visibility
- Payment flow helpers (lock/release UI actions)
- Settlement and billing table visualization

### 4) Developer tools in UI

- API key generation + local persistence
- Environment switch (sandbox/production)
- Activity feed and operational status indicators

## Runtime architecture

Agentboard is a browser UI that talks to Agent Kernel HTTP API:

- Frontend: static HTML/CSS/JS (`agentboard.html`)
- Backend: Agent Kernel (`/api/*`, `/auth/*`)
- Blockchain integrations: MetaMask + ethers.js

## Important behavior notes

- In production, Agentboard uses the configured production API base URL.
- MetaMask login is non-persistent by design and requires signature each time.
- OAuth errors are surfaced toasts-first and fall back gracefully when needed.

## Environment expectations (backend)

Agentboard depends on these backend endpoints:

- `GET /health`
- `GET /auth/config`
- `POST /auth/google/token`
- `POST /auth/github/token`
- `POST /api/agents`
- `GET /api/agents`
- `GET /api/agents/:sessionId`
- `DELETE /api/agents/:sessionId`

OAuth backend env variables expected:

- `GOOGLE_OAUTH_CLIENT_ID`
- `GOOGLE_OAUTH_CLIENT_SECRET`
- `GITHUB_OAUTH_CLIENT_ID`
- `GITHUB_OAUTH_CLIENT_SECRET`
- `FRONTEND_URL`

## Local development

```bash
# serve static UI
cd website
npx serve . -l 8080

# run backend (agent-kernel)
cd ../agent-kernel
npm install
npm run build
node dist/api-server.js
```

Then open:

- `http://localhost:8080/agentboard.html`

## Deployment notes

- Vercel deploy is used for `opacus.xyz`.
- API currently exposed via ngrok tunnel in this setup.
- For stable production API, migrate from ngrok free tunnel to fixed-host backend.

## Security notes

- Never commit private keys or OAuth secrets.
- Keep `.env.mainnet` private and machine-local.
- Browser wallet signatures are user-mediated and cannot be automated server-side.

## Troubleshooting

### OAuth `invalid_client`

- Verify OAuth client IDs/secrets in backend env
- Verify redirect URIs and origins in Google/GitHub app settings
- Confirm `/auth/config` returns non-empty client IDs

### Stuck on "Please wait"

- Fixed in current version by deterministic modal lifecycle cleanup
- If browser cache is stale, hard refresh (`Cmd+Shift+R`)

### Payment endpoint works but escrow lock fails

- Usually wrong escrow contract for active chain
- Validate chain ID and contract deployment address pair

## License

Internal Opacus project distribution unless otherwise specified.
