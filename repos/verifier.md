# verifier

**URL:** github.com/vultisig/verifier
**Language:** Go (91.5%)
**Purpose:** TSS service for cryptographic vault operations and transaction signing

---

## Overview

Threshold signature scheme (TSS) service enabling cryptographic vault operations. Integrates with Vultisig Plugins to process and sign blockchain transactions while maintaining separation between business logic and cryptographic operations. Uses DKLS-based TSS for distributed signing.

---

## Folder Structure

```
verifier/
├── cmd/
│   ├── verifier/          # API server entry point
│   └── worker/            # Async worker entry point
├── internal/
│   ├── api/               # API handlers
│   ├── services/          # Business logic
│   └── storage/           # Data access layer
├── types/                 # Data structure definitions
├── vault/                 # Vault storage implementations
├── vault_config/          # Vault configuration management
├── config/                # Application configuration
├── plugin/                # Plugin integration code
├── verifier.example.json  # Config example
├── worker.example.json    # Worker config example
├── Dockerfile
├── docker-compose.yml
├── Makefile
└── go.mod
```

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `cmd/verifier/` | Main API server binary |
| `cmd/worker/` | Async worker binary |
| `internal/api/` | HTTP handlers, routes |
| `internal/services/` | Core business logic |
| `vault/` | Vault storage abstraction |
| `plugin/` | Plugin integration layer |
| `types/` | Shared type definitions |
| `config/` | Configuration loading |

---

## Dependencies

**Infrastructure:**
- PostgreSQL 14+
- Redis 6+
- S3 or local storage

**Go Libraries:**
- Standard Go HTTP server
- DKLS cryptographic operations
- Vultisig ecosystem packages

---

## API Surface

- Vault lifecycle (create, retrieve, admin)
- Signing workflows (async with polling)
- Policy enforcement
- Transaction synchronization

**Key endpoint:** `/vault/sign/response/:id` for polling sign results

---

## Architecture Notes

- Separation: API server handles requests, workers handle async signing
- Storage: S3 or local backends for vault data
- Auth: Token-based authentication
- Queue: Redis-backed async task processing

---

_Updated: 2026-01-06_
