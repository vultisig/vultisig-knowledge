# vultiserver

**URL:** github.com/vultisig/vultiserver
**Language:** Go (99.3%)
**Purpose:** TSS server for Fast Vaults (auto co-signing)

---

## Overview

Server-side TSS participant enabling 2/2 Fast Vaults. One share on user device, one on Vultiserver. Encrypted shares emailed to users.

---

## Folder Structure

```
vultiserver/
├── api/                      # HTTP handlers
├── cmd/                      # Entry points
├── service/                  # TSS business logic
├── storage/                  # Data persistence
├── vaults/                   # Vault share management
├── relay/                    # Inter-service communication
├── config/                   # Configuration
├── internal/                 # Private packages
└── docker-compose.yml        # Container setup
```

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `cmd/` | Server entry point |
| `api/` | HTTP endpoint handlers |
| `service/` | Keygen, keysign, reshare logic |
| `vaults/` | Vault share storage |

---

## API Endpoints

| Endpoint | Purpose |
|----------|---------|
| `POST /vault/create` | Keygen (create vault) |
| `POST /vault/sign` | Keysign (sign tx) |
| `POST /vault/reshare` | Reshare vault |
| `GET /vault/get/{pubkey}` | Get vault metadata |
| `GET /ping` | Health check |

---

## Architecture

**Two components:**
1. **API Server** - HTTP handlers
2. **TSS Worker** - Performs actual TSS operations

---

## Dependencies

- **Redis** - Session/state management
- **GG20/DKLS** - TSS libraries
- Docker for deployment

---

_Updated: 2026-01-06_
