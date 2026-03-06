# dkls23-rs

**URL:** github.com/silence-laboratories/dkls23
**Language:** Rust (98.8%)
**Purpose:** Core TSS implementation - threshold ECDSA signing protocol

---

## Overview

Production-ready threshold ECDSA signing protocol with dynamic quorum management developed by Silence Laboratories. Powers Vultisig's distributed signing. Security audited by Trail of Bits (February 2024). Reduces communication rounds from 6 to 3 vs previous protocols (GG18/GG20).

---

## Folder Structure

```
dkls23/
├── crates/
│   ├── msg-relay/         # Message relay functionality
│   └── dkls-metrics/      # Performance metrics
├── docs/                  # Audit reports, research papers
├── examples/
│   ├── keygen/            # Key generation examples
│   ├── sign/              # Signing examples
│   └── refresh/           # Key refresh examples
├── scripts/               # Utility tooling
├── src/
│   ├── keygen/            # Distributed key generation
│   ├── sign/              # Distributed signing
│   ├── proto/             # Protocol definitions
│   ├── setup/             # Setup module
│   ├── key_export.rs      # Key export
│   └── key_import.rs      # Key import
├── Cargo.toml
└── README.md
```

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `src/keygen/` | Distributed Key Generation (DKG) |
| `src/sign/` | Distributed Signature Generation (DSG) |
| `src/key_import.rs` | Import existing keys |
| `src/key_export.rs` | Export key shares |
| `examples/` | Integration examples |
| `crates/msg-relay/` | Network message relay |
| `docs/` | Security audit, papers |

---

## Dependencies

**Cryptographic:**
- ChaCha20Poly1305 (encryption)
- EdDSA/Curve25519 (authentication)
- X25519 (key agreement)
- Oblivious transfer implementations

---

## Core Capabilities

- **DKG:** Distributed Key Generation
- **DSG:** Distributed Signature Generation
- **Key Refresh:** Rotate shares without changing public key
- **Import/Export:** Migrate keys in/out
- **Dynamic Quorum:** Add/remove participants
- **Migration:** From GG20/CMP protocols

---

## Protocol Details

- 3 rounds (vs 6 in GG18/GG20)
- 5-10x faster signing
- Oblivious transfer (not homomorphic encryption)
- Zero-copy message serialization
- Malicious adversary security

---

## Resources

- **Docs:** https://dkls23.silencelaboratories.com/docs/dkls23/
- **Benchmarks:** https://dkls23.silencelaboratories.com/
- **Crate:** https://docs.rs/sl-dkls23

---

_Updated: 2026-01-06_
