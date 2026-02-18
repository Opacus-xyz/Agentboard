# Opacus Agentboard

Professional operations dashboard for running autonomous agents on Opacus.

[![Live App](https://img.shields.io/badge/Live-opacus.xyz%2Fagentboard-6366f1)](https://opacus.xyz/agentboard.html)
[![OpenClaw Docs](https://img.shields.io/badge/OpenClaw-Operational%20Guide-06b6d4)](./OPENCLAW-OPERATIONS.md)
[![Quickstart](https://img.shields.io/badge/Docs-Quickstart-22c55e)](./QUICKSTART.md)

---

## Overview

Agentboard is the production-facing control plane for:

- Authentication and session management
- Agent lifecycle operations
- Payment and billing visibility
- OpenClaw-powered task execution workflows

The UI is optimized for operational reliability, rapid onboarding, and transparent runtime status.

## Key Capabilities

### Authentication

- Google OAuth code exchange
- GitHub OAuth code exchange
- MetaMask login with mandatory `personal_sign`
- Safe fallback behavior when OAuth providers are not configured

### Agent Operations

- Create, list, inspect, and delete agents
- Runtime status visibility
- Activity and metrics panels

### Payments and Billing

- Wallet connection and chain checks
- On-chain USDC balance visibility
- Escrow lock/release helpers
- Settlement-aware billing panels

### OpenClaw Runtime

- Task execution flow with budget/reputation constraints
- Discovery explorer for capability-based matching
- Reputation lookup by DID
- User-owned Anthropic API key support

See the complete guide: [OPENCLAW-OPERATIONS.md](./OPENCLAW-OPERATIONS.md)

## Architecture at a Glance

```text
Agentboard (UI)
	├─ Auth + Agent Ops + Billing + OpenClaw panels
	└─ API calls
				↓
Agent Kernel
	├─ /health
	├─ /auth/*
	├─ /api/agents/*
	└─ /api/openclaw/*
				↓
OpenClaw Runtime + Opacus services
```

## API Dependencies

Required backend endpoints:

- `GET /health`
- `GET /auth/config`
- `POST /auth/google/token`
- `POST /auth/github/token`
- `POST /api/agents`
- `GET /api/agents`
- `GET /api/agents/:sessionId`
- `DELETE /api/agents/:sessionId`
- `GET /api/openclaw/health`
- `POST /api/openclaw/request`

## Environment Requirements

- `GOOGLE_OAUTH_CLIENT_ID`
- `GOOGLE_OAUTH_CLIENT_SECRET`
- `GITHUB_OAUTH_CLIENT_ID`
- `GITHUB_OAUTH_CLIENT_SECRET`
- `FRONTEND_URL`

## Local Development

1. Start backend

```bash
cd agent-kernel
npm install
npm run build
node dist/api-server.js
```

2. Start static frontend

```bash
cd website
npx serve . -l 8080
```

3. Open

- `http://localhost:8080/agentboard.html`

## Production Notes

- Frontend deploys on Vercel (`opacus.xyz`)
- Stable backend hosting should use a fixed production domain
- Avoid long-term operational dependence on temporary tunnels

## Security Guidelines

- Never commit private keys, OAuth secrets, or seed phrases
- Keep environment secrets out of source control
- Treat browser signatures as user-side approvals only
- Rotate API and OAuth credentials regularly

## Troubleshooting

### OAuth `invalid_client`

- Validate provider client id/secret
- Validate redirect URI and allowed origin
- Confirm `/auth/config` returns provider identifiers

### Login stuck on loading

- Verify backend health and auth config endpoints
- Hard refresh (`Cmd+Shift+R`) to clear stale frontend state

### Payment lock/release failures

- Check chain and contract alignment
- Verify escrow addresses for active network

## Project Documentation

- [QUICKSTART.md](./QUICKSTART.md)
- [OPENCLAW-OPERATIONS.md](./OPENCLAW-OPERATIONS.md)
- [ROADMAP.md](./ROADMAP.md)
- [CHANGELOG.md](./CHANGELOG.md)
- [CONTRIBUTING.md](./CONTRIBUTING.md)

## License

Internal Opacus project distribution unless otherwise specified.
