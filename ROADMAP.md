# Roadmap

## Current Status Snapshot

- Repository setup: Completed
- Production dashboard HTML: Completed
- Core authentication flows: Completed
- MetaMask signature login: Completed
- Basic payment transfer validation: Completed
- Escrow lock/release on current chain: In Progress

## Milestones

### M1 — Stable Core Dashboard

Status: Completed

- UI foundation
- Agent CRUD flows
- Runtime and status panels
- API health integration

### M2 — Authentication Hardening

Status: Completed

- OAuth configuration checks
- Fallback behavior for missing provider config
- MetaMask signature requirement
- Stuck-loading modal lifecycle fix

### M3 — Payment Reliability

Status: In Progress

- Validate chain/contract mapping for escrow
- Add explicit contract mismatch diagnostics in UI
- Add guided recovery path for unsupported chain

### M4 — Production Backend Stability

Status: Planned

- Replace temporary tunnel API with fixed production host
- Add uptime checks and incident visibility
- Add deployment rollback notes

### M5 — Integration Pack

Status: Planned

- Minimal backend integration examples
- API cookbook snippets
- End-to-end onboarding script for integrators

## Near-Term TODO

- Finalize escrow address mapping per chain
- Add release smoke test script
- Add release checklist for docs + deploy + API checks
