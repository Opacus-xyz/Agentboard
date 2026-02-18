# Opacus Agentboard

Production dashboard for creating, managing, and operating autonomous agents on Opacus.

## Live Links

- App: https://opacus.xyz/agentboard.html

## Repository Structure

- agentboard.html — Single-file production dashboard UI
- QUICKSTART.md — Fast setup and first-run checks
- ROADMAP.md — Planned milestones and delivery status
- CHANGELOG.md — Release history and notable updates
- CONTRIBUTING.md — Collaboration and PR expectations

## Product Scope

### Authentication

- Google OAuth code exchange flow
- GitHub OAuth code exchange flow
- MetaMask login with mandatory personal_sign signature
- Automatic fallback flow when OAuth providers are not configured

### Agent Operations

- Create agents
- List and inspect runtime status
- Delete agents
- Observe platform and activity metrics

### Payments and Billing

- Wallet connection and network checks
- On-chain USDC balance visibility
- Payment lock/release helper flows
- Settlement-oriented billing table

### Developer Experience

- API key generation and local persistence
- Environment switching and diagnostics
- Operational status and health indicators

## API Dependencies

Agentboard expects these backend endpoints:

- GET /health
- GET /auth/config
- POST /auth/google/token
- POST /auth/github/token
- POST /api/agents
- GET /api/agents
- GET /api/agents/:sessionId
- DELETE /api/agents/:sessionId

## Required Backend Environment

- GOOGLE_OAUTH_CLIENT_ID
- GOOGLE_OAUTH_CLIENT_SECRET
- GITHUB_OAUTH_CLIENT_ID
- GITHUB_OAUTH_CLIENT_SECRET
- FRONTEND_URL

## Local Run

1) Start backend

- cd agent-kernel
- npm install
- npm run build
- node dist/api-server.js

2) Start static frontend

- cd website
- npx serve . -l 8080

3) Open

- http://localhost:8080/agentboard.html

## Deployment Notes

- Frontend is deployed on Vercel (opacus.xyz)
- Current backend exposure is ngrok-based
- Stable production should use fixed domain API hosting

## Security Notes

- Never commit private keys, OAuth secrets, or wallet seed material
- Keep mainnet env files machine-local and excluded from source control
- Browser signatures are user-side approvals and are not server-automatable

## Troubleshooting

### OAuth invalid_client

- Confirm provider app client id/secret
- Confirm redirect URI and origin registration
- Confirm /auth/config returns non-empty provider ids

### Login stuck on loading

- Fixed in current implementation via deterministic modal cleanup
- Hard refresh if stale browser cache is suspected

### Payment lock issues

- Usually contract/chain mismatch
- Verify escrow address matches active chain deployment

## Progress Visibility

Project progress is maintained in:

- ROADMAP.md
- CHANGELOG.md

## License

Internal Opacus project distribution unless otherwise specified.
