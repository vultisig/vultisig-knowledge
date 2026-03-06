# vultisig-ios

**URL:** github.com/vultisig/vultisig-ios
**Platforms:** iOS + macOS (shared codebase)
**Language:** Swift (95.4%), C (3.5%)

---

## Overview

iOS + macOS wallet app implementing TSS keygen/keysign via DKLS23 protocol. SwiftUI with strict MVVM architecture.

---

## Folder Structure

```
vultisig-ios/
├── VultisigApp/
│   ├── Models/       → Data models (Vault, Coin, Chain, KeyShare)
│   ├── Views/        → SwiftUI views (organized by feature)
│   ├── ViewModels/   → Business logic, state (@Observable)
│   ├── Services/     → API calls, TSS operations, relay communication
│   ├── Crypto/       → C bindings for DKLS23, EdDSA, ECDSA
│   └── Extensions/   → Swift extensions
├── Mediator/         → Inter-component communication layer
└── .github/          → CI/CD workflows
```

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `VultisigApp/` | Main app module |
| `ViewModels/` | Business logic, @Observable + @MainActor |
| `Services/` | TSS operations, relay communication |
| `Crypto/` | C bindings for cryptographic operations — do not modify without review |
| `Mediator/` | Communication layer between components |

---

## Key Commands

```bash
# Build
xcodebuild -scheme VultisigApp -destination 'platform=iOS Simulator,name=iPhone 16'

# Test
xcodebuild test -scheme VultisigApp -destination 'platform=iOS Simulator,name=iPhone 16'

# Lint
swiftlint

# Format
swiftformat .
```

---

## Key Flows

- **Vault creation**: Views → ViewModel → Services → Crypto (C FFI) → Mediator (relay)
- **Transaction signing**: ViewModel builds TX → keysign via TSS → broadcast
- **Agent chat**: Not yet implemented (reference impl in vultisig-windows)

---

## Dependencies

- **Trust Wallet WalletCore** — Chain support
- **commondata** — Protobuf schemas (submodule, do not edit directly)
- Custom C libraries for TSS crypto

---

## Notes

- Mac app shares same codebase (SwiftUI multiplatform)
- C code (3.5%) is cryptographic primitives — C FFI bridge
- MVVM strictly enforced: `@Observable` for ViewModels (iOS 17+), `@MainActor` for all ViewModels
- `guard let` for early exits, no force unwraps
- async/await with structured concurrency

---

_Updated: 2026-03-04_
