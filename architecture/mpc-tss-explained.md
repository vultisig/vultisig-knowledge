# MPC/TSS Explained

_Source: docs.vultisig.com_
_See also: [signing-flow.md](./signing-flow.md) for keysign details, [vault-lifecycle.md](./vault-lifecycle.md) for keygen/reshare_

## What is TSS?

**Threshold Signature Scheme** - Cryptographic protocol where multiple parties collaborate to sign transactions without any single party possessing the complete private key.

**Key insight:** The private key never exists. Only key shares exist, and they combine via zero-knowledge proofs to produce valid signatures.

---

## Common Misconception

> **"Vultisig stores/has the private key somewhere"**

This is the #1 misconception. **Vultisig does not have any private key, ever.**

- No server holds the key
- No device holds the complete key
- The key literally never exists in any location
- Key shares are mathematically combined during signing without reconstruction

The shares produce a valid signature through cryptographic protocols - the key itself is never assembled.

---

## DKLS23 Protocol

Vultisig uses DKLS23 (Doerner, Kondi, Lee, Shelat, 2023) - the latest advancement in threshold ECDSA.

### Why DKLS23?

| vs GG20 | Improvement |
|---------|-------------|
| Rounds | 6 → 3 rounds |
| Speed | 5-10x faster signing |
| Security | No Paillier vulnerability |
| Compatibility | WebAssembly support |

### Three-Round Signing

**Round 1: Commitment**
- Generate and exchange commitments to secret values
- Nonce generation and sharing combined

**Round 2: Multiplication**
- Secure two-party multiplication via oblivious transfer
- Statistical consistency checks

**Round 3: Signature Completion**
- Compute and combine final signature
- Output indistinguishable from standard ECDSA

### Oblivious Transfer (OT)

Instead of Paillier's homomorphic encryption, DKLS23 uses OT:
- Sender transfers one of two messages without knowing which was selected
- Multiple instances from small number of base OTs
- Eliminates homomorphic operations entirely

---

## TSS vs Multi-Signature

| Factor | TSS | Multi-Sig |
|--------|-----|-----------|
| **Private Key** | Never exists | Multiple keys held |
| **On-Chain** | Single signature | Multiple signatures visible |
| **Redundancy** | Re-share to recover | Must migrate funds |
| **Chain Support** | Multi-chain native | Chain-specific |
| **Flexibility** | Add/remove devices | Fixed once created |
| **Fees** | One signature = lower | Multiple = higher |
| **Privacy** | Looks like normal tx | Exposes structure |

---

## Security Properties

DKLS23 provides:
- UC-security against malicious adversaries
- Dishonest majority tolerance
- No Alpha-Rays Attack (Paillier vulnerability)
- Nonce leakage prevention
- Simpler security proofs

---

_Updated: 2026-01-06_
