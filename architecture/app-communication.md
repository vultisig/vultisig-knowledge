# Cross-App Communication (Comprehensive)

_How iOS, Android, Windows, and backend services communicate_
_See also: [signing-flow.md](./signing-flow.md) for keysign flow, [infrastructure.md](./infrastructure.md) for component overview_

---

## Overview

Vultisig apps communicate via two channels:
1. **Relay Server** - Internet-based, E2E encrypted
2. **Local Network** - WiFi/mDNS, same network only

All communication uses **protobuf schemas** defined in `commondata` repo.

---

## Communication Flow

### Keygen (Vault Creation)

```
Device A (Initiator)                 Relay Server                    Device B
     │                                    │                              │
     │──── POST /:sessionID ─────────────>│                              │
     │     (create session)               │                              │
     │                                    │                              │
     │<─── QR code with sessionID ────────│                              │
     │                                    │                              │
     │                                    │<──── GET /:sessionID ────────│
     │                                    │      (join session)          │
     │                                    │                              │
     │──── POST /message/:sessionID ─────>│                              │
     │     (keygen round 1)               │                              │
     │                                    │──── GET /message/:sessionID >│
     │                                    │                              │
     │<─── POST /message/:sessionID ──────│<──── keygen round 1 ─────────│
     │                                    │                              │
     │     [3 DKLS23 rounds]              │     [3 DKLS23 rounds]        │
     │                                    │                              │
     │──── POST /complete/:sessionID ────>│                              │
     │     (keygen complete)              │                              │
```

### Keysign (Transaction Signing)

```
Device A (Initiator)                 Relay Server                    Device B
     │                                    │                              │
     │──── POST /start/:sessionID ───────>│                              │
     │     (start keysign)                │                              │
     │                                    │                              │
     │──── POST /payload/:hash ──────────>│                              │
     │     (transaction payload)          │                              │
     │                                    │                              │
     │                                    │<──── GET /start/:sessionID ──│
     │                                    │<──── GET /payload/:hash ─────│
     │                                    │                              │
     │     [DKLS23 signing rounds]        │     [DKLS23 signing]         │
     │                                    │                              │
     │──── POST /complete/:sessionID/keysign ─>│                         │
     │     (signature complete)           │                              │
```

---

## Relay Server API

**Base URL:** Configurable per environment

### Session Management
| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/:sessionID` | POST | Create session |
| `/:sessionID` | GET | List participants |
| `/:sessionID` | DELETE | Remove session |

### Messaging
| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/message/:sessionID` | POST | Send message to session |
| `/message/:sessionID/:participantID` | GET | Get messages for participant |
| `/message/:sessionID/:participantID/:hash` | DELETE | Delete specific message |

### TSS Coordination
| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/start/:sessionID` | POST/GET | Signal/check session start |
| `/complete/:sessionID` | POST/GET | Signal/check completion |
| `/complete/:sessionID/keysign` | POST/GET | Keysign completion |

### Payload
| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/payload/:hash` | POST | Store payload (tx data) |
| `/payload/:hash` | GET | Retrieve payload |

---

## Commondata (Protobuf Schemas)

**Repo:** github.com/vultisig/commondata

Defines shared data structures used across all apps:

```
commondata/
├── proto/vultisig/           # .proto definitions
├── Sources/swift/vultisig/   # Swift generated code
└── go/vultisig/              # Go generated code
```

### Key Message Types

| Type | Purpose |
|------|---------|
| `KeygenMessage` | Keygen round data |
| `KeysignMessage` | Signing round data |
| `ReshareMessage` | Reshare coordination |
| `VaultShare` | Encrypted vault share |

### Update Flow

1. Edit `.proto` files
2. Run `buf generate`
3. Swift/Go code auto-generated
4. Apps import updated package

---

## Vultiserver Communication

**For Fast Vaults only** - Vultiserver acts as second signer.

```
Mobile App                      Vultiserver
     │                              │
     │──── POST /vault/create ─────>│  (keygen)
     │<─── vault share + email ─────│
     │                              │
     │──── POST /vault/sign ───────>│  (keysign)
     │<─── signature share ─────────│
```

### Vultiserver Endpoints

| Endpoint | Purpose |
|----------|---------|
| `POST /vault/create` | Distributed key generation |
| `POST /vault/sign` | Transaction signing |
| `POST /vault/reshare` | Share redistribution |
| `GET /vault/get/{pubkey}` | Get vault metadata |

---

## Local Network Mode

When devices are on same WiFi:

1. **Discovery:** mDNS broadcast to find peers
2. **Direct Connection:** Peer-to-peer, no relay
3. **Same Protocol:** Uses same message format as relay

**Benefits:**
- Maximum privacy (no external servers)
- Lower latency
- Works offline (no internet needed)

---

## Security Properties

| Property | Implementation |
|----------|----------------|
| **E2E Encryption** | Messages encrypted before relay |
| **Session Isolation** | Each session has unique ID |
| **Message Authentication** | Signed by sender |
| **No Data Retention** | Relay doesn't store decrypted data |

---

## Sequence: Complete Keysign Flow

```
1. User initiates tx on Device A
2. Device A creates session on relay
3. Device A generates QR with sessionID + encrypted chaincode
4. Device B scans QR
5. Device B joins session via relay
6. Device A uploads tx payload
7. Device B fetches tx payload, displays for verification
8. User approves on Device B
9. DKLS23 Round 1: Both devices exchange commitments via relay
10. DKLS23 Round 2: OT multiplication via relay
11. DKLS23 Round 3: Signature completion
12. Device A assembles final signature
13. Device A broadcasts to blockchain
14. Both devices notified of tx hash
```

---

## Known Issues

### Cross-Platform Stability

**Status:** Active pain point

The multi-platform native architecture (iOS, Android, Windows each with native codebases) creates challenges:

| Issue | Impact |
|-------|--------|
| **Async feature rollout** | iOS ships feature before Android, causes keysign failures when devices on different versions |
| **Protocol sync** | All platforms must implement same protocol version exactly |
| **Fee/balance sync** | Different platforms may show different fee estimates or balances |

**Root cause:** Native codebases = better UX per platform, but harder to keep in sync vs single cross-platform codebase.

**Mitigation:** Feature flags, protocol versioning, thorough cross-platform testing before release.

---

_Updated: 2026-01-06_
