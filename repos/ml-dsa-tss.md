# ml-dsa-tss

**URL:** github.com/vultisig/ml-dsa-tss
**Language:** Go (49.0%), Assembly (43.6%)
**Purpose:** Threshold ML-DSA (FIPS 204) post-quantum signatures

---

## Overview

Go implementation of threshold ML-DSA (FIPS 204) cryptographic signatures enabling distributed key generation and multi-party signing. Supports configurable t-of-n threshold schemes (2 <= t <= n <= 6), three security levels, and WebAssembly bindings for browser/Node.js usage.

---

## Folder Structure

```
ml-dsa-tss/
├── protocol/
│   ├── keygen/            # Fresh key generation
│   ├── sign/              # Threshold signing
│   ├── reshare/           # Resharing protocols (R1, R2, R3)
│   ├── keyshare/          # Share serialization/validation
│   ├── mldsa44/           # Security level 44
│   ├── mldsa65/           # Security level 65
│   └── mldsa87/           # Security level 87
├── internal/              # Shared cryptographic internals
├── wasm/                  # Browser/Node.js bindings
├── bench/                 # Performance benchmarks
├── research/              # Parameter research scripts
├── go.mod
└── README.md
```

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `protocol/keygen/` | Distributed key generation |
| `protocol/sign/` | Threshold signature generation |
| `protocol/reshare/` | Key share rotation |
| `protocol/keyshare/` | Share management |
| `protocol/mldsa*/` | Security level implementations |
| `wasm/` | JavaScript/WASM bindings |
| `bench/` | Benchmark suite |

---

## Dependencies

**Go:**
- Standard Go crypto packages
- libp2p (network benchmarking)

**Serialization:**
- CBOR (keyshare serialization)

---

## Security Levels

| Level | Security | Use Case |
|-------|----------|----------|
| ML-DSA-44 | 128-bit | Standard |
| ML-DSA-65 | 192-bit | Enhanced |
| ML-DSA-87 | 256-bit | Maximum |

---

## Core Capabilities

- **Keygen:** Fresh distributed key generation
- **Sign:** Threshold signature generation
- **Reshare:** Key rotation without public key change
- **WASM:** Browser-compatible bindings
- **Configurable:** t-of-n schemes (2 <= t <= n <= 6)

---

## Security Model

- Requires at least t honest parties during protocol
- Clients must run test signatures after resharing
- Message authentication is client responsibility
- Session-based API for all operations

---

## Why ML-DSA?

Post-quantum secure signatures (FIPS 204). Future-proofing against quantum computing threats while maintaining threshold/distributed signing capabilities.

---

## Roadmap Context

**Status:** Most exciting upcoming feature (per Paaao)

Post-quantum MPC signing protocol is a key differentiator in development:
- First MPC wallet with quantum-resistant signatures
- Ahead of competitors on quantum threat timeline
- Positions Vultisig for long-term security leadership

**Strategic value:** When quantum computing becomes a real threat, Vultisig will already have the solution deployed.

---

_Updated: 2026-01-06_
