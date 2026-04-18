# Changelog

All notable changes to Opacus Agentboard are documented in this file.

Format: [Keep a Changelog](https://keepachangelog.com/en/1.0.0/)  
Versioning: [Semantic Versioning](https://semver.org/spec/v2.0.0.html)

---

## [2.9.0] — 2026-05-25

### Added
- `opacus-agent-sdk` v1.0.1 published to npm — full TypeScript SDK for ZK, TEE, Escrow, Tasks, and Agents
- ZK proof response now includes top-level `commitment` field (ERC-7755 compatibility)
- ZK proof `da.daNodes` now correctly reflects the number of 0G DA nodes that received the proof
- TEE attestation verification by `attestationId` (persistent lookup via Upstash KV)
- `/api/proofs/feed` endpoint — combined real-time ZK + TEE event feed
- `/api/routing/status` endpoint — current routing mode and latency
- `/api/routing/mode` endpoint — set routing mode (`fast` / `secure` / `balanced`)
- `/api/deposit/record` endpoint — manual deposit recording with TX hash
- `/api/deposit/history` endpoint — full deposit history per user
- `/api/v1/billing/fees` endpoint — fee history with amounts and triggers

### Fixed
- Escrow fee recording now correctly persists to `store.feeRecords` for billing queries
- ZK prove endpoint no longer throws when 0G DA upload returns fewer nodes than expected
- TEE verify endpoint correctly resolves attestation by ID when stored with timestamp suffix
- Agentboard polling interval reduced from 30s to 8s; `Cache-Control: no-store` header added

### Changed
- Kinetic Score tier thresholds updated: gold now requires 60+ (was 70+)
- MCP gateway updated to 42 tools (added `launch_secure_task`, `estimate_task_cost`, `deposit_history`)

---

## [2.8.0] — 2026-04-10

### Added
- OpenClaw MCP gateway with 42 structured tool definitions
- `openclaw skills reload` command for hot-reloading tool definitions
- Upstash KV as permanent state store (replacing Vercel Blob)
- Bridge system: Base ↔ 0G USDC bridge with `initiateBridge` + `completeBridge` lifecycle
- `/runtime/bridge-status` endpoint for querying 0G bridge completion

### Fixed
- MCP `tools/call` standard compliance (JSON-RPC 2.0 envelope)
- 429 rate limit handling in OpenClaw gateway (exponential back-off)
- DID uniqueness enforcement on agent registration
- Balance aggregation across exec wallet and login wallet

### Changed
- Agent persistence now seed-based (deterministic wallet from env seed)
- Agentboard sidebar: added Team & Access tab

---

## [2.7.0] — 2026-03-15

### Added
- Kinetic Score system (0–100) with 5-tier ladder: bronze / silver / gold / platinum / diamond
- Score breakdown endpoint: reputation, escrow success rate, task completion, TEE usage, 0G compute bonus
- Escrow 1% protocol fee auto-collection on release
- Fee multiplier per tier (diamond: 0.1×, gold: 0.3×)

### Fixed
- ZK prove endpoint no longer returns 500 when 0G DA is slow; graceful timeout with retry hint
- Escrow release correctly validates `active` status before processing

---

## [2.6.0] — 2026-02-28

### Added
- TEE attestation with DCAP-style report, mrEnclave, mrSigner fields
- TEE HMAC token (1-hour TTL) for session-scoped verification
- 0G DA anchoring for both ZK proofs and TEE attestations
- `/api/tee/attest` and `/api/tee/verify` endpoints

---

## [2.5.0] — 2026-02-19

### Added
- ZK proof system with ERC-7755 commitment scheme
- 0G DA mainnet integration for proof anchoring
- `/api/zk/prove` and `/api/zk/verify` endpoints
- Proof kinds: `reputation-threshold`, `task-completion`, `balance-proof`, `identity`

### Changed
- Agent Kernel API migrated to Vercel serverless (from Railway)

---

## [2.4.0] — 2026-02-01

### Added
- Escrow lifecycle: lock → active → released / refunded
- Opacus Pay tab in Agentboard (USDC deposit, balance view, escrow management)
- Live Events tab: real-time webhook stream via Server-Sent Events

### Fixed
- Agent registration DID collision edge case

---

## [2.3.0] — 2026-01-15

### Added
- Agentboard multi-tab UI: Overview, Agents, Transactions, Task Center, Settings
- Google OAuth and MetaMask sign-in
- API key generation and management in Settings

---

## [2.0.0] — 2026-01-01

### Added
- Initial Agentboard UI release
- Agent Kernel API on Vercel
- 0G compute network integration
- Intent parsing → task orchestration → agent execution pipeline
