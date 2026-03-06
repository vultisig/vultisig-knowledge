# recipes

**URL:** github.com/vultisig/recipes
**Language:** Go
**Purpose:** Shared transaction parsing, validation engine, and constraint matching for all plugins

---

## Overview

The recipes library is the **shared foundation** that all plugins and the verifier use to parse, validate, and constrain blockchain transactions. It defines how transactions map to policy rules and provides the constraint engine that enforces what plugins are allowed to do.

**This is NOT example code** — it is critical infrastructure used by every plugin.

---

## Key Concepts

### MetaRules vs Direct Rules

- **MetaRules**: High-level chain-agnostic abstractions (`ethereum.send`, `solana.swap`) that auto-expand to protocol-specific implementations
- **Direct Rules**: Low-level ABI/IDL-mapped rules (`ethereum.erc20.transfer`) for precise control

### Constraint Types

| Type | Description |
|------|-------------|
| FIXED | Exact value required |
| MAX | Maximum allowed |
| MIN | Minimum allowed |
| MAGIC_CONSTANT | System address (treasury, router) resolved at runtime per chain |
| ANY | Accept any value |
| REGEXP | Regular expression match |

### Magic Constants

Predefined system addresses maintained centrally:
- `VULTISIG_TREASURY` — treasury address (chain-aware)
- `THORCHAIN_VAULT` — THORChain router/vault
- `THORCHAIN_ROUTER` — THORChain swap router

---

## Key Packages

```go
"github.com/vultisig/recipes/engine"     // Rule evaluation engine
"github.com/vultisig/recipes/types"      // Rule, Constraint, Policy types (protobuf)
"github.com/vultisig/recipes/sdk/evm"    // EVM transaction building helpers
"github.com/vultisig/recipes/chain"      // Chain implementations + registry
```

---

## Supported Chains

EVM (Ethereum, Polygon, Arbitrum, Base, etc.), UTXO (Bitcoin, Litecoin, Dogecoin, etc.), Solana, Cosmos-based, THORChain, XRPL, and more.

---

## Used By

- **Verifier**: Evaluates transactions against policy rules before TSS signing
- **All plugins**: Define recipe specifications, validate policies
- **app-recurring**: Transaction building via `recipes/sdk/evm`

---

_Updated: 2026-02-09_
