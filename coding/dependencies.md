# Dependency Version Matrix

_Minimum versions and key dependencies across all Vultisig repos._

---

## Language Runtimes

| Language | Version | Repos |
|----------|---------|-------|
| **Go** | 1.25+ | agent-backend, mcp, vultisig-go, vultiserver, relay, verifier, plugin-agent, feeplugin, vcli |
| **Go** | 1.22+ | commondata (lower — only proto generation) |
| **Node.js** | LTS (v22+) | vultisig-windows, vultisig-sdk |
| **Node.js** | 19+ | website-prod |
| **Rust** | 1.70+ | dkls23-rs |
| **Swift** | 5.9+ | vultisig-ios (SwiftUI, @Observable) |
| **Kotlin** | 1.9+ | vultisig-android (Jetpack Compose) |

---

## Package Managers

| Tool | Version | Repos |
|------|---------|-------|
| **Yarn** | 4.12+ | vultisig-windows, vultisig-sdk, website-prod |
| **buf** | latest | commondata (protobuf generation) |
| **Gradle** | 8.x | vultisig-android |
| **SPM** | built-in | vultisig-ios |
| **Cargo** | latest | dkls23-rs |

---

## Key Frameworks

| Framework | Version | Repos |
|-----------|---------|-------|
| **Next.js** | 16.1 | website-prod |
| **React** | 19.x | vultisig-windows, vultisig-sdk, website-prod, app-marketplace |
| **Wails** | v2 | vultisig-windows |
| **Jetpack Compose** | latest | vultisig-android |
| **SwiftUI** | iOS 17+ | vultisig-ios |
| **Echo** | v4 | agent-backend (HTTP framework) |
| **TanStack React Query** | v5 | vultisig-windows, vultisig-sdk |

---

## Infrastructure

| Service | Version | Used By |
|---------|---------|---------|
| **PostgreSQL** | 14+ | agent-backend, verifier |
| **Redis** | 6+ | agent-backend, vultiserver, relay, verifier |
| **S3/MinIO** | any | verifier, plugin-agent |

---

## Testing Tools

| Tool | Repos |
|------|-------|
| **Vitest** | vultisig-windows, vultisig-sdk |
| **go test -race** | All Go repos |
| **XCTest** | vultisig-ios |
| **JUnit + Gradle** | vultisig-android |
| **knip** (dead code) | vultisig-windows |

---

## Linting

| Tool | Repos |
|------|-------|
| **ESLint** | vultisig-windows, vultisig-sdk, website-prod |
| **SwiftLint** | vultisig-ios |
| **ktlint** | vultisig-android |
| **golangci-lint** | Go repos (where configured) |
| **buf lint** | commondata |

---

## External Services

| Service | Purpose | Used By |
|---------|---------|---------|
| **OpenRouter** | Claude LLM API proxy | agent-backend |
| **CoinGecko** | Token discovery, prices | mcp |
| **1inch** | EVM swap routing | mcp |
| **Jupiter** | Solana swap routing | mcp |
| **Polymarket** | Prediction markets | mcp |
| **Blockchair** | UTXO chain API | mcp |
| **Trust Wallet WalletCore** | Chain support (AAR) | vultisig-android (requires GitHub credentials) |

---

_Updated: 2026-03-05_
