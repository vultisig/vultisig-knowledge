# Plugin Ecosystem Architecture

_The complete picture of how Vultisig plugins work, how they're built, and how they relate to each other._

---

## Core Principle: Every Plugin is Independent

**This is the most important architectural concept to understand.**

Each Vultisig plugin is a **completely standalone, independently deployed service**. The `app-recurring` repo (DCA plugin) is the **baseline template** — developers clone or reference it, then build whatever they want.

- Each plugin runs its **own** HTTP server, worker, scheduler, and database
- Each plugin defines its **own** recipe specification — the rules, constraints, supported chains, and parameters
- Each plugin defines its **own** authorization requirements — what users must sign, what permissions apply, what rate limits are enforced
- Users must configure the **correct policy for each specific plugin** they install — there is no universal policy format
- Developers have **total freedom** in business logic: DCA bots, AI trading agents, payroll systems, cross-chain arbitrage, governance automation — anything expressible as blockchain transactions

**Vultisig provides the rails** (TSS signing, policy enforcement via verifier, marketplace distribution, fee collection). **Developers provide the logic.**

This means when interacting with any plugin:
- You MUST understand that plugin's specific recipe schema
- You MUST know its supported operations and constraints
- Policy configuration is plugin-specific, not universal
- The plugin's `GetRecipeSpecification()` defines what's possible
- The plugin's `ValidatePluginPolicy()` enforces what's valid

---

## System Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                        USER (Vultisig Wallet)                        │
│  iOS / Android / macOS / Windows / Browser Extension                 │
└──────────┬───────────────────────────────────────────────────────────┘
           │ Install plugin, sign policy, configure automation
           ▼
┌──────────────────────┐     ┌──────────────────────┐
│     Marketplace      │     │   Developer Portal   │
│  (app-marketplace)   │     │  (developer-portal)  │
│                      │     │                      │
│ • Browse plugins     │     │ • Register plugins   │
│ • Install/uninstall  │     │ • Manage API keys    │
│ • Reviews & ratings  │     │ • Track earnings     │
│ • Policy creation    │     │ • Team management    │
└──────────┬───────────┘     └──────────┬───────────┘
           │                            │
           ▼                            ▼
┌──────────────────────────────────────────────────────────────────────┐
│                         VERIFIER (Vultisig-managed)                  │
│  • Validates transactions against user-signed policies               │
│  • Re-derives signing hashes from raw tx (NEVER trusts plugin)       │
│  • Participates in TSS signing ceremonies                            │
│  • Rate limiting, kill-switch, fee management                        │
└──────────┬───────────────────────────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────────────────────────────────────┐
│            PLUGIN (Developer-built, fully independent)                │
│                                                                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌────────────┐ │
│  │ HTTP Server  │  │   Worker    │  │  Scheduler  │  │ TX Indexer │ │
│  │ /reshare     │  │ Asynq tasks │  │ Cron/event  │  │ On-chain   │ │
│  │ /sign        │  │ TSS signing │  │ triggers    │  │ monitoring │ │
│  │ /policy      │  │ Broadcasting│  │             │  │            │ │
│  └─────────────┘  └─────────────┘  └─────────────┘  └────────────┘ │
└──────────┬───────────────────────────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────────────────────────────────────┐
│                    RECIPES (Shared tx validation library)             │
│  MetaRules (ethereum.send) + Direct Rules (ethereum.erc20.transfer)  │
│  Constraint types: FIXED, MAX, MIN, MAGIC_CONSTANT, ANY, REGEXP     │
└──────────┬───────────────────────────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────────────────────────────────────┐
│                    BLOCKCHAINS (36+ supported)                        │
│  EVM (13) • UTXO (6) • Cosmos (10) • Solana, Polkadot, Sui, etc.   │
└──────────────────────────────────────────────────────────────────────┘
```

---

## How a Plugin Works (End-to-End)

1. **User installs plugin** from Marketplace, configures automation (e.g., "buy $100 ETH weekly")
2. **Policy creation**: Plugin creates a policy — user signs a set of rules defining allowed transactions
3. **TSS reshare**: 4-party reshare (user vault, fast vault server, verifier, plugin) gives plugin a key share
4. **Trigger fires**: Scheduler, event, or HTTP triggers the plugin
5. **Plugin builds tx**: Constructs unsigned transaction based on policy rules
6. **Verifier validates**:
   - Re-derives signing hashes from raw tx bytes (NEVER trusts plugin-provided hashes)
   - Validates tx against policy constraints
   - Checks rate limits, trial status, fees
7. **TSS signing**: Verifier co-signs if compliant
8. **Plugin broadcasts**: Signed tx goes to blockchain
9. **TX indexer monitors**: Tracks on-chain confirmation

---

## Security Architecture

### The Critical Security Mechanism

The Verifier **independently re-derives signing hashes from raw transaction bytes**, completely ignoring what the plugin provides. A malicious plugin cannot trick the system into signing arbitrary data.

### What Plugins Cannot Do

- Sign arbitrary data (hashes are re-derived by verifier)
- Exceed policy rate limits
- Operate outside policy constraints
- Access other vaults
- Bypass the verifier

### Safety Controls

- **Global kill-switch**: Disable all plugin signing
- **Per-plugin kill-switch**: Emergency disable for specific plugin
- **Rate limiting**: MaxTxsPerWindow + RateLimitWindow per policy
- **Trial accounts**: Time-limited access without fee vault
- **Vault encryption**: AES encryption on S3/MinIO storage

---

## Policy & Rules System

Each plugin defines its own rules. Two types:

### MetaRules (High-Level, Auto-Expand)

| MetaRule | Expands To |
|----------|------------|
| `ethereum.send` | `ethereum.eth.transfer` + `ethereum.erc20.transfer` |
| `solana.send` | `solana.system.transfer` + `solana.spl_token.transfer` |
| `ethereum.swap` | 1inch, Uniswap V2/V3 calls |
| `solana.swap` | Jupiter aggregator calls |

### Direct Rules (ABI-Level, Fine-Grained)

```json
{
  "resource": "ethereum.erc20.transfer",
  "effect": "EFFECT_ALLOW",
  "target": { "address": "0xA0b8..." },
  "parameter_constraints": [
    { "parameter_name": "amount", "constraint": { "type": "MAX", "value": "1000000" } }
  ]
}
```

### Constraint Types

FIXED, MAX, MIN, MAGIC_CONSTANT, ANY, REGEXP, UNSPECIFIED

### Magic Constants

System addresses maintained in `vultisig/recipes` — resolved per chain at runtime:
- VULTISIG_TREASURY, THORCHAIN_VAULT, THORCHAIN_ROUTER

---

## Building a Plugin

### Minimum Requirements

1. Expose `/reshare` and `/sign` HTTP endpoints
2. Implement `plugin.Spec` interface:
   - `GetPluginID()` — unique identifier
   - `GetRecipeSpecification()` — define what users can configure
   - `ValidatePluginPolicy()` — validate user configurations
   - `Suggest()` — auto-generate configs from user input
   - `GetSkills()` — markdown describing capabilities (for AI agents)
3. Pass verifier validation on all transactions

### Project Structure (from app-recurring baseline)

```
your-plugin/
├── cmd/
│   ├── server/main.go        # HTTP server (required)
│   └── worker/main.go        # Async worker (required)
├── spec/spec.go              # Your recipe schema + validation
├── trigger/trigger.go        # Your trigger mechanism
├── go.mod
└── plugin-config.yaml        # For marketplace submission
```

### Key Go Packages

```go
"github.com/vultisig/verifier/plugin"        // Server, Spec interface
"github.com/vultisig/verifier/plugin/server"  // Pre-built HTTP server
"github.com/vultisig/verifier/plugin/policy"  // Policy service
"github.com/vultisig/verifier/vault"          // Vault operations
"github.com/vultisig/recipes/engine"          // Tx validation
"github.com/vultisig/recipes/sdk/evm"         // EVM tx building
"github.com/hibiken/asynq"                    // Task queue
```

### Submission

1. Develop + test locally with vcli
2. Prepare `plugin-config.yaml` (id, title, endpoint, category)
3. Join Discord third-party developer section
4. Submit for review (basic sanity + optional "Vultisig Approved" audit)
5. Deploy your own infrastructure

---

## Revenue Model

- **70% to developer, 30% to $VULT treasury**
- Fee types: per-transaction, subscription, per-installation
- Auto-converted to USDC via DEX aggregators

---

## Component Repository Map

| Component | Repo | Deployed By | Purpose |
|-----------|------|-------------|---------|
| Verifier | `verifier` | Vultisig | Policy enforcement, TSS signing |
| Fee Plugin | `feeplugin` | Vultisig | Fee collection, treasury |
| Reference Plugin | `app-recurring` | Developer (template) | DCA automation baseline |
| Recipes | `recipes` | Library | Tx parsing, validation engine |
| Marketplace | `app-marketplace` | Vultisig | User-facing plugin store |
| Developer Portal | `developer-portal` | Vultisig | Dev management dashboard |
| Plugin Agent | `plugin-agent` | Per-deployment | Signing middleware + events |
| SDK | `vultisig-sdk` | Library | TypeScript vault operations |
| CLI | `vcli` | Local dev tool | E2E testing without mobile |

---

## vcli: Local Development

The vcli enables e2e testing without the mobile app:

```
START → IMPORT vault → INSTALL plugin (4-party TSS) →
  [GENERATE policy → ADD → MONITOR] → DELETE → UNINSTALL → STOP
```

Requires 5 sibling repos: `vcli`, `verifier`, `app-recurring`, `recipes`, `go-wrappers`

---

## SDK (`@vultisig/sdk`)

TypeScript SDK for vault operations:

```typescript
import { Vultisig, Chain } from '@vultisig/sdk'
const sdk = new Vultisig()
await sdk.initialize()

const vaultId = await sdk.createFastVault({ name, email, password })
const vault = await sdk.verifyVault(vaultId, code)
const address = await vault.address(Chain.Ethereum)
const balance = await vault.balance(Chain.Bitcoin)
```

Supports: 36 chains, FastVault (2-of-2), SecureVault (N-of-M), signing, swaps, balance checking.

---

_Source: Deep research from local repos + docs.vultisig.com + github.com/vultisig/vcli_
_Created: 2026-02-09_
