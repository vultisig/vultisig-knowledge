# Infrastructure Components

_Source: docs.vultisig.com_
_See also: [app-communication.md](./app-communication.md) for relay API, [signing-flow.md](./signing-flow.md) for Vultiserver in signing_

## Design Philosophy

- **Trustlessness:** Minimal reliance on centralized services
- **Privacy:** No tracking or data collection in-app
- **User Experience:** Infrastructure improvements without security compromise

---

## Vultiserver

**Purpose:** Automatic co-signing for Fast Vaults

### How It Works
- Acts as second signer in 2-of-2 Fast Vault
- Auto-signs when pre-defined policies are met
- User retains full control via policies

### Key Features
- Single-device UX with multi-device security
- Configurable transaction policies
- Spending limits, whitelisted addresses, time delays
- Security protocols modifiable by vault share holders

### Not Custodial
- Vultiserver alone cannot move funds (needs user device)
- User sets all rules
- Open source and auditable

---

## Relay Server

**Purpose:** Encrypted device-to-device communication

### How It Works
- Transmits keygen and keysign messages between devices
- All communication end-to-end encrypted
- Relay cannot access message contents

### Key Features
- Works across different networks
- No message content visible to relay
- Open source: github.com/vultisig/vultisig-relay

### Security Properties
- E2E encryption prevents interception
- Relay is just a message pipe
- Local mode available for max privacy (same network)

---

## Commondata

**Purpose:** Shared data service for Vultisig ecosystem

### Provides
- Chain data (addresses, transactions)
- Token information
- Price feeds
- Shared utilities

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────┐
│                    User Devices                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐          │
│  │   iOS    │  │ Android  │  │ Windows  │          │
│  │   Mac    │  │          │  │          │          │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘          │
│       │             │             │                 │
└───────┼─────────────┼─────────────┼─────────────────┘
        │             │             │
        ▼             ▼             ▼
┌───────────────────────────────────────────────────┐
│              Relay Server (E2E Encrypted)          │
│        Message routing between devices             │
└───────────────────────────────────────────────────┘
        │
        ▼
┌───────────────────────────────────────────────────┐
│              Vultiserver (Fast Vault only)         │
│         Auto co-signing with user policies         │
└───────────────────────────────────────────────────┘
        │
        ▼
┌───────────────────────────────────────────────────┐
│                   Commondata                       │
│          Chain data, tokens, prices                │
└───────────────────────────────────────────────────┘
        │
        ▼
┌───────────────────────────────────────────────────┐
│                  Blockchains                       │
│    BTC, ETH, RUNE, SOL, ATOM, DOGE, etc.          │
└───────────────────────────────────────────────────┘
```

---

_Updated: 2026-01-06_
