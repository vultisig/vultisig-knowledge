# feeplugin

**URL:** github.com/vultisig/feeplugin
**Language:** Go
**Purpose:** Fee collection, treasury management, and revenue distribution (Vultisig-managed)

---

## Overview

The fee plugin is a **Vultisig-managed** service that handles fee collection from plugin usage, automatic token conversion to USDC via DEX aggregators, and revenue distribution (70% developer / 30% $VULT treasury). It is NOT a third-party plugin — it runs as core infrastructure alongside the verifier.

---

## Fee Types

| Type | Description | Best For |
|------|-------------|----------|
| Per-transaction | Charged per executed operation | Trading bots, swap aggregators |
| Subscription | Recurring monthly/yearly | Premium features, analytics |
| Per-installation | One-time charge on install | Premium tools, license-based |

---

## Key Components

- **FeePlugin**: Main processor — loads fees from verifier, batches, signs ERC20 transfers
- **Spec**: Plugin interface implementation (recipe schema, validation)
- **Worker**: Asynq tasks for fee loading, transaction building, post-tx verification
- **VerifierApi client**: Queries verifier for pending fees, reports collected

---

## Fee Collection Flow

```
Plugin executes tx → Fee recorded in verifier → Fee plugin loads pending fees →
Batches into fee_run → Signs ERC20 USDC transfer → Broadcasts → Confirms → Reports back
```

---

## Dependencies

- Verifier API (fee tracking)
- Ethereum RPC (USDC transfers)
- PostgreSQL (fee records)
- Redis/Asynq (task queue)
- MinIO (vault storage)

---

_Updated: 2026-02-09_
