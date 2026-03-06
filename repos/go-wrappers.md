# go-wrappers

**URL:** github.com/vultisig/go-wrappers
**Language:** Go (78.6%), C (20.6%)
**Purpose:** Go bindings for cryptographic protocols (DKLS, Schnorr)

---

## Overview

Collection of Go wrapper libraries for cryptographic protocols, specifically focused on distributed key generation and digital signature schemes. Provides Go interfaces to underlying C implementations for threshold signing and blockchain-related operations.

---

## Folder Structure

```
go-wrappers/
├── go-dkls/               # DKLS protocol Go wrapper
├── go-schnorr/            # Schnorr signature wrapper
├── gorules/               # Rule-based logic module
├── includes/              # C header files, shared includes
├── .github/
│   └── workflows/         # CI/CD automation
├── Makefile               # Build automation
├── flake.nix              # Nix environment config
├── flake.lock
├── go.mod
└── go.sum
```

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `go-dkls/` | DKLS TSS protocol bindings |
| `go-schnorr/` | Schnorr signature bindings |
| `gorules/` | Rule engine module |
| `includes/` | C headers and shared code |
| `Makefile` | Build targets |
| `go.mod` | Module dependencies |

---

## Dependencies

**Build:**
- C compiler (CGO enabled)
- Nix (optional, for reproducible builds)
- Make

**Linked Libraries:**
- DKLS23 (Rust -> C -> Go)
- Schnorr implementation

---

## Modules

**go-dkls:**
- Wraps DKLS23 Rust library
- Provides Go API for keygen, sign, reshare
- Used by verifier, app-recurring

**go-schnorr:**
- Schnorr signature scheme bindings
- Used for chains requiring Schnorr (Bitcoin Taproot)

**gorules:**
- Rule evaluation module
- Policy/condition checking

---

## Build Notes

```bash
# Build with CGO
make build

# Nix environment (reproducible)
nix develop

# Run tests
make test
```

---

## Usage

Used internally by other Vultisig Go services:
- `verifier` - TSS signing
- `app-recurring` - DCA transaction signing
- `vultiserver` - Auto co-signer

---

_Updated: 2026-01-06_
