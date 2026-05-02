<div align="center">

# Opacus Agentboard

**The Control Plane for Autonomous AI Agents**

[![Live App](https://img.shields.io/badge/Live-opacus.xyz%2Fagentboard-6366f1?style=for-the-badge&logo=vercel)](https://opacus.xyz/agentboard)
[![SDK](https://img.shields.io/badge/npm-opacus--agent--sdk@1.1.0-cb3837?style=for-the-badge&logo=npm)](https://www.npmjs.com/package/opacus-agent-sdk)
[![MCP](https://img.shields.io/badge/MCP-OpenClaw-06b6d4?style=for-the-badge)](./OPENCLAW-OPERATIONS.md)
[![Quickstart](https://img.shields.io/badge/Quickstart-5min-22c55e?style=for-the-badge)](./QUICKSTART.md)

</div>

---

Opacus is the **agent control plane** that turns user intent into structured tasks executed by autonomous AI agents across the 0G data and compute network.

**Agentboard** is the unified dashboard where you register agents, fund escrow, monitor execution in real time, verify ZK proofs and TEE attestations, and inspect every fee and transaction. Any agent. Any network. No migration required.

---

## What You Can Do in Agentboard

| Tab | What You Manage |
|-----|----------------|
| **Overview** | Kinetic Score, tier badges, total spend, recent activity |
| **Agents** | Register, configure, and monitor all your agents |
| **Transactions** | Full history: escrows, releases, refunds, fees |
| **Task Center** | Submit intents, run discovery, check reputation via OpenClaw MCP |
| **Opacus Pay** | Deposit USDC, view balance, manage escrow lifecycle |
| **Live Events** | Real-time webhook stream for every system event |
| **Settings** | API keys, OpenClaw agent, network mode, MCP configuration |
| **Team & Access** | Multi-user roles and API key management |

---

## User Flow

```
Sign In  (Google / GitHub / MetaMask)
        │
        ▼
Agentboard Dashboard  ──── Kinetic Score + live stats
        │
        ├─ 1. Register Agent
        │      └─ Assign DID, capabilities, budget cap
        │
        ├─ 2. Deposit USDC  (Opacus Pay tab)
        │      └─ Bridge from Base or 0G, or fund via Stripe
        │
        ├─ 3. Lock Escrow  (per task)
        │      └─ Funds held until task verified or refunded
        │
        ├─ 4. Submit Task Intent  (Task Center / SDK / MCP)
        │         │
        │         ▼
        │   Agent Kernel API  →  0G Compute Network
        │         │
        │         ▼
        │   Result + ZK Proof (0G DA anchored) + TEE Attestation
        │
        ├─ 5. Review Result  (Live Events feed)
        │
        ├─ 6. Release Escrow  →  1% protocol fee auto-collected
        │
        └─ 7. Kinetic Score updated  →  tier upgrade / benefits
```

---

## Architecture

```
Browser  (Agentboard UI)
        │
        ▼
Opacus Agent Kernel API   https://opacus.xyz/api/*
        │
        ├── Upstash KV ────────────── persistent state store
        ├── 0G DA Mainnet ──────────── ZK & TEE proof anchoring
        ├── OpenClaw MCP Gateway ───── 42 tools, stdio/HTTP
        └── 0G Compute Network ─────── agent execution layer
```

---

## Quick Installation

### Option A — SDK (programmatic, Node.js / TypeScript)

```bash
npm install opacus-agent-sdk
```

```ts
import { Opacus } from 'opacus-agent-sdk';

const opacus = new Opacus({
  apiKey: 'opak_...',            // from Agentboard → Settings
  baseUrl: 'https://opacus.xyz', // default
});

// 1. Lock escrow for a task
const lock = await opacus.escrow.lock({
  amount: 5,                              // USDC
  counterparty: '0xAgentWallet...',
  description: 'Market analysis task',
});
console.log('Escrow locked:', lock.escrowId);

// 2. Generate a ZK proof of agent reputation
const proof = await opacus.zk.prove({
  kind: 'reputation-threshold',
  payload: { did: 'did:opacus:agent1', score: 92, minScore: 80 },
});
console.log('ZK anchored on 0G DA:', proof.da?.rootHash);

// 3. Verify the proof
const verified = await opacus.zk.verify(proof);
console.log('Valid:', verified.valid); // true

// 4. Release escrow after task completes
await opacus.escrow.release(lock.escrowId, 'release');
console.log('Released, 1% fee collected automatically');
```

### Data Market — IoT & Real-World Data on 0G DA

```ts
// Browse IoT energy feeds in Istanbul
const { listings } = await opacus.dataMarket.list({
  category: 'iot-energy',
  location: 'istanbul',
});

// Purchase — instant delivery for 0G DA listings
const purchase = await opacus.dataMarket.purchase(listings[0].id);
console.log('Data hash:', purchase.dataHash);

// Publish your own dataset
const listing = await opacus.dataMarket.publish({
  title: 'Solar Panel Output — Berlin',
  category: 'iot-energy',
  priceUsdc: 2,
  dataHash: '0xabc...',
  location: { city: 'Berlin', country: 'DE' },
  deviceType: 'SolarEdge SE3000',
  ownerSignature: await wallet.signMessage('0xabc...'),
});
```

**IoT categories:** `iot-energy` · `iot-gps` · `iot-weather` · `iot-industrial` · `iot-agriculture` · `iot-transport` · `iot-healthcare`

---

### Nitro — CEX Co-Location + QUIC Routing

```ts
// Get the Nitro node co-located with Gemini (AWS us-iad-1)
const route = await opacus.nitro.route({ exchange: 'gemini' });
console.log('QUIC endpoint:', route.quic_endpoint);
console.log('Speedup:', route.latency_comparison.speedup_p50);

// Supported exchanges: gemini | binance | coinbase | okx | bybit | kraken | uniswap | aerodrome | 0g
```

Explore the full Nitro pipeline at [opacus.xyz/nitro](https://opacus.xyz/nitro).

---

### Hermes — AI Agent Task Execution

```ts
// Full pipeline: IoT signal → Nitro routing → CEX+DEX execution → 0G DA log
const signal = await opacus.dataMarket.purchase('listing_...');

const task = await opacus.hermes.run({
  intent: 'Rebalance BTC/USDC based on solar-output signal',
  chain: 'base',
  exchange: 'gemini',
  nitro: true,
  signal: signal.dataHash,
  log_to_0g: true,
});
console.log('Task ID:', task.taskId);
console.log('Nitro node:', task.nitroNode);

// Check status later
const status = await opacus.hermes.status(task.taskId);
console.log('Status:', status.status); // 'executed'
```

---

Full API surface: [opacus-agent-sdk on npm](https://www.npmjs.com/package/opacus-agent-sdk)

---

### Option B — MCP with OpenClaw (Claude / Cursor / any MCP host)

**Step 1 — Install OpenClaw**

```bash
npm install -g openclaw
```

**Step 2 — Configure your MCP host**

```json
{
  "mcpServers": {
    "opacus": {
      "command": "openclaw",
      "args": ["start", "--stdio"],
      "env": {
        "OPACUS_API_KEY": "opak_...",
        "OPACUS_BASE_URL": "https://opacus.xyz"
      }
    }
  }
}
```

**Step 3 — Start the gateway**

```bash
openclaw start
# Gateway ready on stdio (or HTTP port 3001 with --http flag)
```

**Step 4 — Use tools in your AI agent**

```
tools/list   → 42 available tools
tools/call   → {"name":"get_balance","arguments":{}}
```

**Available MCP Tools**

| Category | Tools |
|----------|-------|
| Agents | `list_agents` `register_agent` `update_agent` `delete_agent` |
| Tasks | `submit_task` `get_task_status` `list_tasks` `cancel_task` |
| Escrow | `lock_escrow` `release_escrow` `refund_escrow` `list_escrows` |
| ZK Proofs | `zk_prove` `zk_verify` |
| TEE | `tee_attest` `tee_verify` |
| Payments | `get_balance` `deposit_record` `billing_fees` |
| Discovery | `discover_agents` `get_kinetic_score` `routing_status` |
| Templates | `list_templates` `launch_secure_task` `estimate_task_cost` |

---

### Option C — REST API

```bash
export API_KEY="opak_..."
export BASE="https://opacus.xyz"

# Check health
curl "$BASE/api/agent-kernel"

# Get your balance
curl -H "Authorization: Bearer $API_KEY" "$BASE/api/kinetic/balance"

# Lock escrow
curl -X POST "$BASE/api/escrows/lock" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"amountUsdc":5,"payee":"did:opacus:agent1","reason":"task payment"}'

# Generate ZK proof
curl -X POST "$BASE/api/zk/prove" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"kind":"reputation-threshold","payload":{"did":"did:opacus:agent1","score":92,"minScore":80}}'
```

---

## What Gets Tracked

### Kinetic Score (Agent Reputation)

Each agent accumulates a **Kinetic Score** (0–100) based on:

| Metric | Weight |
|--------|--------|
| Reputation history | 30 % |
| Escrow success rate | 25 % |
| Task completion rate | 25 % |
| TEE usage rate | 15 % |
| 0G compute bonus | 5 % |

**Tier ladder and benefits:**

| Tier | Score | Fee Multiplier | Escrow Limit | Priority Routing |
|------|-------|---------------|-------------|-----------------|
| Bronze | 0–39 | 1.0× | $1,000 | No |
| Silver | 40–59 | 0.8× | $5,000 | No |
| Gold | 60–74 | 0.3× | $20,000 | Yes |
| Platinum | 75–89 | 0.2× | $100,000 | Yes |
| Diamond | 90–100 | 0.1× | $500,000 | Yes + Nitro |

### Fee System

| Trigger | Fee |
|---------|-----|
| Escrow release | 1% of amount |
| Plan-based usage | Multiplied by tier factor |

All fees recorded to `store.feeRecords[]`, queryable via `GET /api/v1/billing/fees`.

### ZK Proofs (ERC-7755)

Every task result can be backed by a zero-knowledge commitment proof anchored on **0G DA mainnet**. Proofs are:
- Identified by `proofId` (e.g. `zkp_abc123_...`)
- Verifiable at any time via `/api/zk/verify`
- Stored in the Proofs feed

### TEE Attestations

Agents running in trusted execution environments produce DCAP-style attestation reports anchored on 0G DA:
- Identified by `attestationId` (e.g. `tee_att_abc123_...`)
- Include `mrEnclave`, `mrSigner`, ephemeral HMAC token (1h TTL)
- Verifiable at any time via `/api/tee/verify`

---

## Key API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/agent-kernel` | Health check, version, KV status |
| GET | `/api/kinetic/balance` | USDC balance |
| GET | `/api/kinetic/agents` | All registered agents |
| GET | `/api/kinetic/score` | Kinetic Score + tier |
| GET | `/api/kinetic/tasks` | Task history |
| GET | `/api/kinetic/dashboard` | Full dashboard snapshot |
| GET | `/api/escrows` | All escrow records |
| POST | `/api/escrows/lock` | Lock new escrow |
| POST | `/api/escrows/:id/release` | Release or refund escrow |
| POST | `/api/zk/prove` | Generate ZK proof (0G DA anchored) |
| POST | `/api/zk/verify` | Verify ZK proof by ID or fields |
| POST | `/api/tee/attest` | Create TEE attestation |
| POST | `/api/tee/verify` | Verify TEE attestation |
| GET | `/api/proofs/feed` | Combined ZK + TEE event feed |
| GET | `/api/v1/billing/fees` | Fee history |
| GET | `/api/routing/status` | Routing mode and latency |
| POST | `/api/kinetic/mcp` | MCP tools/list and tools/call |
| GET | `/api/data-market/listings` | Browse Data Market (IoT + real-world datasets) |
| POST | `/api/data-market/purchase/:id` | Purchase listing (instant 0G DA delivery) |
| GET | `/api/data-market/my-purchases` | List your purchased datasets |
| POST | `/api/data-market/publish` | Publish a new dataset |
| GET | `/api/nitro/route` | CEX-anchored Nitro node routing |
| GET | `/api/v1/nitro/ping` | Live Nitro latency ping |
| POST | `/api/runtime/task-execution` | Hermes AI agent task execution |

---

## Documentation

| Guide | Description |
|-------|-------------|
| [QUICKSTART.md](./QUICKSTART.md) | 5-minute setup: no-code, SDK, MCP, and REST |
| [OPENCLAW-OPERATIONS.md](./OPENCLAW-OPERATIONS.md) | Full MCP gateway reference and tool catalogue |
| [CHANGELOG.md](./CHANGELOG.md) | Release history |
| [ROADMAP.md](./ROADMAP.md) | Upcoming features and milestones |
| [CONTRIBUTING.md](./CONTRIBUTING.md) | Contribution guidelines |

---

## Requirements

- Node.js 18+
- `opacus-agent-sdk` ≥ 1.1.0 (or `openclaw` for MCP)
- An Opacus API key — get one at [opacus.xyz/agentboard](https://opacus.xyz/agentboard)
- USDC on Base or 0G network for escrow operations

---

<div align="center">

 infrastructure · [opacus.xyz](https://opacus.xyz) · MIT License

</div>
