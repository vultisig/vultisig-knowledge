# vultisig-relay

**URL:** github.com/vultisig/vultisig-relay
**Language:** Go
**Purpose:** Message relay for TSS ceremonies

---

## Overview

Facilitates encrypted communication between devices during keygen/keysign. Stateless, Redis-backed message routing.

---

## Folder Structure

```
vultisig-relay/
├── cmd/router/               # Entry point
├── config/                   # Configuration
├── contexthelper/            # Context utilities
├── model/                    # Data structures
├── server/                   # HTTP handlers
├── storage/                  # Redis + in-memory backends
├── docker-compose.yml        # Container setup
└── go.mod                    # Dependencies
```

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `cmd/router/` | Main entry point |
| `server/handler.go` | HTTP endpoint handlers |
| `storage/` | Redis abstraction |

---

## API Endpoints

| Endpoint | Purpose |
|----------|---------|
| `POST /:sessionID` | Create session |
| `GET /:sessionID` | List participants |
| `POST /message/:sessionID` | Send message |
| `GET /message/:sessionID/:participantID` | Get messages |
| `POST /start/:sessionID` | Start TSS session |
| `POST /complete/:sessionID` | Complete session |
| `POST /payload/:hash` | Store tx payload |

---

## Dependencies

- **Echo** - HTTP framework
- **Redis** - Message storage
- **Go Cache** - In-memory fallback

---

_Updated: 2026-01-06_
