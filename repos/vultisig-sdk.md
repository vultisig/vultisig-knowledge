# vultisig-sdk

**URL:** github.com/vultisig/vultisig-sdk
**Language:** TypeScript (92.8%)
**Purpose:** SDK for integrating MPC wallet functionality into applications

---

## Overview

TypeScript library enabling developers to integrate multi-party computation (MPC) wallet functionality into applications. Supports secure vault creation, address derivation, and transaction signing across 40+ blockchains. Offers both Fast Vault (server-assisted 2-of-2) and Secure Vault (multi-device N-of-M) implementations.

---

## Security Tier

HIGH

---

## Folder Structure

```
vultisig-sdk/
├── packages/
│   ├── sdk/               → Main SDK (EDIT HERE)
│   │   ├── src/
│   │   │   └── Vultisig.ts  → Main SDK class
│   │   └── tests/         → unit, integration, e2e
│   ├── core/              → Upstream (synced from vultisig-windows, DO NOT EDIT)
│   │   ├── chain/         → Blockchain integrations
│   │   ├── config/
│   │   └── mpc/           → MPC protocol
│   └── lib/               → Upstream (synced, DO NOT EDIT)
│       ├── dkls/
│       ├── schnorr/
│       └── utils/
├── clients/
│   └── cli/               → CLI tool
├── examples/              → Browser, Electron examples
├── .config/               → ESLint, TSConfig, Knip, TypeDoc configs
├── .changeset/            → Changelog management
├── package.json           → Workspace root
└── yarn.lock
```

**Critical:** `packages/core/` and `packages/lib/` are mirrors of vultisig-windows. Never edit directly — use `yarn sync-and-copy` to update from upstream.

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `packages/sdk/src/` | Main SDK code — edit here |
| `packages/core/` | Upstream from vultisig-windows — DO NOT EDIT |
| `packages/lib/` | Upstream from vultisig-windows — DO NOT EDIT |
| `clients/cli/` | CLI tool for SDK operations |
| `examples/` | Integration examples |

---

## Key Commands

```bash
yarn                   # Install
yarn build:sdk         # Build SDK
yarn build:sdk-full    # Full build with upstream sync
yarn test              # Unit tests
yarn test:e2e          # E2E (requires vault file)
yarn check:all         # lint + typecheck + test + knip
yarn docs              # Generate API docs
yarn format            # Format
```

---

## Dependencies

**Build:**
- Yarn workspaces (monorepo)
- Rollup (multi-platform builds)
- TypeScript strict mode

**Runtime:**
- WalletCore WASM (address derivation)
- Zod (runtime validation at boundaries)
- Changesets (versioning)

---

## Vault Types

**Fast Vault:**
- Server-assisted 2-of-2 MPC
- Uses VultiServer for instant signing
- Best for: Speed, automation

**Secure Vault:**
- Multi-device N-of-M threshold signing
- Mobile device pairing via QR
- Best for: Maximum security

---

## Features

- Address derivation (40+ chains)
- Vault management (create, import, export)
- Transaction signing
- QR code device pairing
- Cross-platform support

---

## Integration

```typescript
// Basic usage pattern
import { FastVault, SecureVault } from '@vultisig/sdk';

// Fast vault for automation
const fastVault = await FastVault.create(config);

// Secure vault for user custody
const secureVault = await SecureVault.pair(qrCode);
```

---

_Updated: 2026-01-06_
