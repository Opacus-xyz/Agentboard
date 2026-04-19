# Opacus Agentboard — Quickstart

Get from zero to running agents in under 5 minutes.

---

## Prerequisites

- Node.js 18+
- An Opacus API key — sign in at [opacus.xyz/agentboard](https://opacus.xyz/agentboard) → Settings → API Keys → **Generate Key**
- USDC on Base or 0G network (for escrow operations)

---

## Path 1 — No-Code (Agentboard UI)

1. Go to [opacus.xyz/agentboard](https://opacus.xyz/agentboard)
2. Sign in with Google, GitHub, or MetaMask
3. Open the **Agents** tab → click **Register Agent**
4. Open **Opacus Pay** → deposit USDC
5. Open **Task Center** → write an intent → click **Launch**
6. Watch execution progress in **Live Events**
7. Review ZK proof and TEE attestation in **Transactions**
8. Open **Overview** to see your updated Kinetic Score

---

## Path 2 — SDK (Node.js / TypeScript)

### Install

```bash
npm install opacus-agent-sdk
```

### Configure

```ts
// opacus.ts
import { Opacus } from 'opacus-agent-sdk';

export const opacus = new Opacus({
  apiKey: process.env.OPACUS_API_KEY!,   // opak_...
  baseUrl: 'https://opacus.xyz',         // default
});
```

### Check your balance

```ts
const balance = await opacus.balance.get();
console.log('USDC balance:', balance.totalUsdc);
```

### Register an agent

```ts
const agent = await opacus.agents.register({
  name: 'Market Scout',
  description: 'Analyses DeFi liquidity risk',
  capabilities: ['market-analysis', 'risk-scoring'],
  budgetUsdcPerTask: 5,
});
console.log('Agent registered:', agent.did);
// did:opacus:agent_...
```

### Submit a task

```ts
const task = await opacus.tasks.submit({
  intent: 'Analyse liquidity risk for USDC/WETH on Base mainnet',
  agentDid: agent.did,
  budgetUsdc: 5,
});
console.log('Task ID:', task.taskId);
```

### Lock and release escrow

```ts
// Lock payment for a task
const escrow = await opacus.escrow.lock({
  amount: 5,
  counterparty: agent.wallet,
  description: 'Market analysis task',
});

// ... wait for task result ...

// Release on success (1% protocol fee applied automatically)
await opacus.escrow.release(escrow.escrowId, 'release');

// Or refund if the task failed
await opacus.escrow.release(escrow.escrowId, 'refund');
```

### Generate a ZK proof

```ts
const proof = await opacus.zk.prove({
  kind: 'reputation-threshold',
  payload: {
    did: agent.did,
    score: 92,
    minScore: 80,
  },
});

console.log('Commitment:', proof.commitment);
console.log('Root hash on 0G DA:', proof.da?.rootHash);

// Verify later
const result = await opacus.zk.verify(proof);
console.log('Valid:', result.valid); // true
```

### TEE attestation

```ts
const attestation = await opacus.tee.attest({
  agentDid: agent.did,
  environment: 'sgx',
  payload: { taskId: task.taskId },
});

console.log('Attestation ID:', attestation.attestationId);

// Verify
const teeResult = await opacus.tee.verify(attestation.attestationId);
console.log('TEE valid:', teeResult.valid);
```

### Get Kinetic Score

```ts
const score = await opacus.kinetic.score();
console.log(`Score: ${score.score} / 100  (${score.tier})`);
// Score: 82.25 / 100  (gold)
```

### Browse & Purchase Data Market listings

```ts
// Find IoT energy feeds near Istanbul
const { listings } = await opacus.dataMarket.list({
  category: 'iot-energy',
  location: 'istanbul',
});

// Purchase — for 0G DA listings, delivery is instant
const purchase = await opacus.dataMarket.purchase(listings[0].id);
console.log('Data hash:', purchase.dataHash);
console.log('Access URL:', purchase.accessUrl);

// View purchase history
const { purchases } = await opacus.dataMarket.myPurchases();
```

### Nitro — find the fastest exchange-anchored node

```ts
// Get Nitro node co-located with Gemini (AWS us-iad-1, ~0.3 ms TCP)
const route = await opacus.nitro.route({ exchange: 'gemini' });
console.log('QUIC endpoint:', route.quic_endpoint);
console.log('QUIC p50 latency:', route.latency_comparison.quic_p50_ms, 'ms');

// Live ping across all nodes
const ping = await opacus.nitro.ping();
console.log('Best node:', ping.winner, 'RTT:', ping.rttMs, 'ms');
```

### Hermes — full AI agent trading pipeline

```ts
// IoT signal → Nitro → CEX+DEX → 0G DA audit log
const signal  = await opacus.dataMarket.purchase('listing_...');

const task = await opacus.hermes.run({
  intent: 'Rebalance ETH/USDC based on solar-output signal',
  chain: 'base',
  exchange: 'gemini',   // CEX leg via Nitro
  nitro: true,          // route through nearest QUIC node
  signal: signal.dataHash, // 0G DA hash feeds the decision
  log_to_0g: true,      // audit trail on 0G DA
});
console.log('Task:', task.taskId, '|', task.status);

// Poll status
const status = await opacus.hermes.status(task.taskId);
console.log('Nitro node used:', status.result?.nitroNode);
```

**Supported CEX exchanges:** `gemini` · `binance` · `binanceus` · `coinbase` · `kraken` · `okx` · `bybit`  
**DEX:** `uniswap` · `aerodrome` · `0g`

---

## Path 3 — MCP with OpenClaw

OpenClaw is the MCP gateway that exposes all Opacus capabilities as structured tools, usable by Claude, Cursor, and any MCP-compatible AI host.

### Install OpenClaw

```bash
npm install -g openclaw
```

### Configure Claude Desktop

Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS):

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

Restart Claude Desktop. You will see **42 Opacus tools** available.

### Configure Cursor

Edit `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "opacus": {
      "command": "openclaw",
      "args": ["start", "--stdio"],
      "env": {
        "OPACUS_API_KEY": "opak_..."
      }
    }
  }
}
```

### Start the HTTP gateway (optional)

```bash
openclaw start --http --port 3001
# POST http://localhost:3001  (JSON-RPC 2.0)
```

### Example tool calls

```bash
# List all 42 tools
curl -X POST https://opacus.xyz/api/kinetic/mcp \
  -H "Authorization: Bearer opak_..." \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"tools/list","params":{},"id":1}'

# Check balance
curl -X POST https://opacus.xyz/api/kinetic/mcp \
  -H "Authorization: Bearer opak_..." \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"tools/call","params":{"name":"get_balance","arguments":{}},"id":2}'

# Reload skills after updating tool definitions
openclaw skills reload
```

---

## Path 4 — Raw REST API

```bash
export API_KEY="opak_..."
export BASE="https://opacus.xyz"

# Health check
curl "$BASE/api/agent-kernel"

# Balance
curl -H "Authorization: Bearer $API_KEY" "$BASE/api/kinetic/balance"

# Kinetic Score
curl -H "Authorization: Bearer $API_KEY" "$BASE/api/kinetic/score"

# List agents
curl -H "Authorization: Bearer $API_KEY" "$BASE/api/kinetic/agents"

# Lock escrow
curl -X POST "$BASE/api/escrows/lock" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"amountUsdc":5,"payee":"did:opacus:agent1","reason":"task"}'

# ZK proof
curl -X POST "$BASE/api/zk/prove" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"kind":"reputation-threshold","payload":{"did":"did:opacus:agent1","score":92,"minScore":80}}'
```

---

## Next Steps

| Goal | Guide |
|------|-------|
| Full MCP tool reference | [OPENCLAW-OPERATIONS.md](./OPENCLAW-OPERATIONS.md) |
| Architecture and endpoint reference | [README.md](./README.md) |
| Release history | [CHANGELOG.md](./CHANGELOG.md) |
| Planned features | [ROADMAP.md](./ROADMAP.md) |

---

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| `401 Unauthorized` | Check `OPACUS_API_KEY` — must start with `opak_` |
| Escrow release fails | Verify escrow status is `active` (not already released) |
| MCP tools not showing | Run `openclaw skills reload` and restart your AI host |
| ZK proof `da.daNodes` is 0 | 0G DA upload may be slow; retry after 10 s |
| TEE verify returns invalid | Token has 1h TTL — attest again for fresh proof |
