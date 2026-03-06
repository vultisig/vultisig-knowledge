# Vultisig Architecture Overview

_Source: docs.vultisig.com_
_See also: [mpc-tss-explained.md](./mpc-tss-explained.md), [vault-lifecycle.md](./vault-lifecycle.md), [infrastructure.md](./infrastructure.md)_

## What is Vultisig?

Seedless, multi-chain, multi-device crypto vault using Threshold Signature Schemes (TSS). No seed phrases, no single point of failure.

**Key Differentiators:**
- No private key ever exists in one place
- Multi-chain native (30+ chains)
- Built for AI agents from ground up
- Open source, free to use

---

## Core Components

### 1. Client Apps
- **iOS + Mac** (single repo: vultisig-ios)
- **Android** (vultisig-android)
- **Windows** (vultisig-windows)
- **Browser Extension** (WebAssembly-based)

### 2. Backend Infrastructure
| Component | Purpose |
|-----------|---------|
| **Vultiserver** | Auto-cosigning for Fast Vaults |
| **Relay Server** | Encrypted device-to-device communication |
| **Commondata** | Shared data service |

### 3. App Store
- Plugin marketplace for DeFi actions
- Verifier service for app security
- Revenue sharing with developers

---

## Vault Types

### Fast Vault (Hot Wallet)
- 2-of-2: User device + Vultiserver
- Single-device UX with threshold security
- Good for daily transactions, smaller amounts

### Secure Vault (Cold Wallet)
- 2-of-3 or 3-of-4: User devices only
- No third-party involvement
- Best for serious asset protection

---

## TSS Operations

| Operation | Devices Required | Purpose |
|-----------|-----------------|---------|
| **Keygen** | 100% of devices | Create shared public key |
| **Keysign** | 67%+ (threshold) | Sign transactions |
| **Reshare** | Current threshold | Add/remove devices |

---

## Network Modes

- **Internet Mode:** Via relay server (different networks)
- **Local Mode:** Via WiFi/mDNS (same network, max privacy)

---

## Security Model

- Private key never reconstructed during normal operation
- End-to-end encrypted communication
- No tracking or data collection in-app
- Open source for auditability

---

_Updated: 2026-01-06_
