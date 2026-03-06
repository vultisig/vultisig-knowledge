# Signing Flow (Keysign)

_Source: docs.vultisig.com_
_See also: [mpc-tss-explained.md](./mpc-tss-explained.md) for protocol details, [app-communication.md](./app-communication.md) for relay API_

## Overview

Keysigning is the collaborative process where threshold devices sign transactions. No single device ever has access to the complete private key.

---

## Four Phases

### 1. Session Initiation
- Host device begins cryptographic session
- Sends session metadata to relay server (or local network via mDNS)
- QR code generated with session ID + encrypted chain code
- See [app-communication.md](./app-communication.md) for relay API details

### 2. Device Pairing
- Other devices scan QR code
- Join using embedded session ID and encrypted chain code
- Host monitors until threshold reached (e.g., 2 of 3 devices)
- Timeout: if threshold not met within session window, ceremony aborts

### 3. Signing Ceremony (DKLS23)

The signing ceremony runs the DKLS23 3-round protocol. All communication happens via the relay server (or direct p2p in local mode).

**Round 1 — Commitment:**
- Each device generates a random nonce
- Devices exchange commitments to their secret values via relay
- Nonce generation and sharing combined into single round

**Round 2 — Oblivious Transfer Multiplication:**
- Secure two-party multiplication using Oblivious Transfer (OT)
- Each pair of participants runs OT protocol
- Statistical consistency checks performed
- This replaces GG20's slower Paillier homomorphic encryption

**Round 3 — Signature Completion:**
- Partial signatures computed locally on each device
- Combined into final ECDSA signature
- Output is indistinguishable from a standard single-key ECDSA signature

**Failure scenarios:**
- Device drops mid-round → ceremony fails, must restart from Round 1
- Network timeout → relay messages expire, ceremony aborts
- Threshold not met → cannot proceed (e.g., only 1 of 3 devices online)
- On failure, no partial signature leaks — protocol is UC-secure

### 4. Broadcast
- Initiating device assembles final signature from partial results
- Broadcasts signed transaction to the target blockchain
- Transaction hash shared with other participants
- All devices update local state

---

## Vault-Specific Flows

### Fast Vault (2-of-2)
- Single-device signing UX
- Vultiserver acts as automatic co-signer (second device)
- No QR scanning needed — server auto-joins via API
- Policy engine on Vultiserver enforces spending limits, whitelists, time delays
- See [infrastructure.md](./infrastructure.md) for Vultiserver details

### Secure Vault (2-of-3, 3-of-4)
- Threshold devices must physically participate
- User prepares transaction on initiating device
- Other devices scan QR, verify transaction details
- All participants must approve before ceremony proceeds
- See [vault-lifecycle.md](./vault-lifecycle.md) for vault types

---

## Network Modes

| Mode | Use Case | How | Latency |
|------|----------|-----|---------|
| **Internet** | Devices on different networks | Via relay server | Higher (relay hop) |
| **Local** | Maximum privacy, same network | Via WiFi (mDNS discovery) | Lower (direct p2p) |

Both modes use identical message format and DKLS23 protocol. See [app-communication.md](./app-communication.md) for protocol details.

---

## Transaction Flow (Complete)

```
User initiates tx on Device A
    |
Device A creates session on relay (POST /:sessionID)
    |
QR code generated with sessionID + encrypted chaincode
    |
Device B scans QR, joins session (GET /:sessionID)
    |
Device A uploads tx payload (POST /payload/:hash)
Device B fetches and verifies payload
    |
User approves on Device B
    |
DKLS23 Round 1: Exchange commitments via relay
DKLS23 Round 2: OT multiplication via relay
DKLS23 Round 3: Compute + combine partial signatures
    |
Device A assembles final ECDSA signature
    |
Device A broadcasts to blockchain
    |
Both devices receive tx hash confirmation
```

---

## GG20 vs DKLS23

The codebase is transitioning from GG20 to DKLS23. Some Vultiserver flows may still use GG20.

| Aspect | GG20 | DKLS23 |
|--------|------|--------|
| Rounds | 6 | 3 |
| Speed | Baseline | 5-10x faster |
| Crypto primitive | Paillier (homomorphic) | Oblivious Transfer |
| Vulnerability | Alpha-Rays (Paillier) | None known |

See [mpc-tss-explained.md](./mpc-tss-explained.md) for full protocol comparison.

---

_Updated: 2026-03-05_
