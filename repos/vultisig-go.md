# vultisig-go

**URL:** github.com/vultisig/vultisig-go
**Language:** Go (99.1%)
**Purpose:** Core Go library for Vultisig chain operations

---

## Overview

Shared Go library used by backend services. Provides address derivation, chain constants, relay communication, and common utilities. This is a library — not a service.

---

## Folder Structure

```
vultisig-go/
├── cmd/vultisig/             # CLI entry point (dev/testing tool)
├── address/                  # Address generation from public keys (multi-chain)
├── common/                   # Chain constants, string↔chain mapping, shared helpers
├── relay/                    # Relay server client (TSS ceremony communication)
├── types/                    # Shared type definitions
├── internal/                 # Private packages
├── go.mod                    # Dependencies
└── flake.nix                 # Nix environment for reproducible builds
```

---

## Key Packages

| Package | Purpose | Used By |
|---------|---------|---------|
| `address/` | Derive addresses from public keys for supported chains | MCP (`get_address` tool) |
| `common/` | Chain name constants, chain↔string mapping, shared utilities | MCP (UTXO builders, address tool) |
| `relay/` | Client for relay server communication during TSS ceremonies | Backend services |
| `types/` | Shared data structures (vault, key shares, etc.) | Multiple services |

---

## Consumers

| Service | Packages Used |
|---------|--------------|
| **mcp** | `address/`, `common/` — address derivation and UTXO transaction building |

Not used by agent-backend directly (agent-backend talks to MCP which uses vultisig-go).

---

## Notes

- Pure library — no HTTP server, no database, no state
- CLI (`cmd/vultisig/`) is for development and testing only
- Nix environment available for reproducible builds (`nix develop`)
- Chain support: all chains that MCP supports for address derivation

---

_Updated: 2026-03-05_
