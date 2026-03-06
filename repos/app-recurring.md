# app-recurring

**URL:** github.com/vultisig/app-recurring
**Language:** Go (99.8%)
**Purpose:** Dollar Cost Averaging (DCA) plugin for automated recurring crypto swaps

---

## Overview

DCA plugin enabling policy-based, automated recurring cryptocurrency swaps across multiple blockchain networks. Uses distributed key signing via the Vultisig verifier for secure transaction execution. Supports configurable frequencies and rate limiting.

---

## Folder Structure

```
app-recurring/
├── cmd/
│   ├── server/            # REST API for policy management
│   ├── scheduler/         # Background transaction scheduler
│   ├── worker/            # Task execution & key signing
│   └── tx_indexer/        # Blockchain transaction monitoring
├── internal/
│   ├── dca/               # Core DCA logic
│   ├── evm/               # EVM blockchain abstraction
│   └── graceful/          # Shutdown handling
├── deploy/                # Kubernetes manifests
├── .github/workflows/     # CI/CD pipelines
├── Dockerfile
├── go.mod
└── go.sum
```

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `cmd/server/` | API server for DCA policy CRUD |
| `cmd/scheduler/` | Cron-like DCA execution scheduler |
| `cmd/worker/` | Signs and broadcasts transactions |
| `cmd/tx_indexer/` | Monitors transaction confirmations |
| `internal/dca/` | Core DCA business logic |
| `internal/evm/` | EVM chain interactions |
| `deploy/` | K8s deployment configs |

---

## Dependencies

**Core Libraries:**
- `github.com/ethereum/go-ethereum` - Blockchain interactions
- `github.com/hibiken/asynq` - Redis task queue
- `github.com/jackc/pgx/v5` - PostgreSQL driver
- `github.com/labstack/echo/v4` - REST API framework

**Vultisig Ecosystem:**
- `github.com/vultisig/recipes` - Recipe system
- `github.com/vultisig/verifier` - Policy verification
- `github.com/vultisig/go-wrappers` - DKLS cryptography

---

## Supported Networks

**EVM Chains:**
- Ethereum, Arbitrum, Avalanche, BNB Chain
- Base, Blast, Optimism, Polygon

**Other Chains:**
- Bitcoin, Solana, XRP

---

## Features

- Configurable frequencies (minutely to monthly)
- Multi-chain swap support (same-chain only)
- Max 2 transactions per frequency window (rate limiting)
- Distributed key signing for security
- Policy-based access control via verifier

---

_Updated: 2026-01-06_
