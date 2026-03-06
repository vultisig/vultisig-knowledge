# Repo Index

_All Vultisig repositories by security tier._

---

## CRITICAL — Crypto Core

Changes require crypto-team review. Every edit is human-reviewed.

| Repo | Language | Description |
|------|----------|-------------|
| [dkls23-rs](./dkls23-rs.md) | Rust | DKLS23 threshold signature implementation. 3-round keygen/sign, post-quantum capable. Audited by Trail of Bits. |
| [ml-dsa-tss](./ml-dsa-tss.md) | Go + Assembly | ML-DSA (FIPS 204) post-quantum threshold signatures. 3 security levels. WASM bindings for browser. Experimental. |
| [go-wrappers](./go-wrappers.md) | Go + C | Go bindings for DKLS and Schnorr libraries. CGo bridge layer. |
| [vultiserver](./vultiserver.md) | Go | Server-side TSS for Fast Vaults (2-of-2 auto co-signing). API server + TSS worker. |

---

## HIGH — Shared Infrastructure

Standard workflow. Crypto-adjacent paths require extra review.

| Repo | Language | Description |
|------|----------|-------------|
| [commondata](./commondata.md) | Protobuf + Swift + Go | Shared data contract for all apps. Source of truth for cross-platform schemas. |
| [vultisig-relay](./vultisig-relay.md) | Go | Message relay for TSS ceremonies. Stateless, Redis-backed routing. |
| [vultisig-sdk](./vultisig-sdk.md) | TypeScript | SDK for MPC wallet operations. Fast Vault + Secure Vault across 40+ chains. |

---

## STANDARD — Applications & Services

Full agent workflow. Normal review process.

### Client Apps

| Repo | Language | Description |
|------|----------|-------------|
| [vultisig-windows](./vultisig-windows.md) | TypeScript + Go | Desktop app + browser extension. Wails v2. Reference impl for agent chat. |
| [vultisig-ios](./vultisig-ios.md) | Swift | iOS + macOS app. SwiftUI MVVM. DKLS23 via C FFI. |
| [vultisig-android](./vultisig-android.md) | Kotlin | Android app. Jetpack Compose. DKLS23 via JNI. |

### AI & Agent

| Repo | Language | Description |
|------|----------|-------------|
| [agent-backend](./agent-backend.md) | Go | AI conversation engine. Claude via OpenRouter, 10 core tools, MCP dynamic tools, SSE streaming. |
| [mcp](./mcp.md) | Go | MCP server. 34+ tools + 8 skills for crypto operations across 29+ chains. Includes Polymarket and Aave V3. |

### Backend Services

| Repo | Language | Description |
|------|----------|-------------|
| [vultisig-go](./vultisig-go.md) | Go | Core Go wallet library. Address derivation, relay communication. |
| [verifier](./verifier.md) | Go | TSS service for vault operations + signing. API server + worker. PostgreSQL + Redis + S3. |
| [plugin-agent](./plugin-agent.md) | Go | Policy enforcement middleware. WebSocket streaming. |
| [feeplugin](./feeplugin.md) | Go | Fee collection service. Per-tx, subscription, per-install models. |
| [vcli](./vcli.md) | Go | CLI for local plugin dev + E2E testing. Requires 5 sibling repos. |
| [airdrop](./airdrop.md) | Go | Airdrop distribution service. |

### Plugin Apps

| Repo | Language | Description |
|------|----------|-------------|
| [recipes](./recipes.md) | TypeScript | TX parsing + constraint engine. MetaRules vs Direct Rules, magic constants. |
| [app-marketplace](./app-marketplace.md) | TypeScript | Plugin marketplace. React 18, Ant Design, postMessage API. |
| [app-recurring](./app-recurring.md) | TypeScript | DCA plugin. 4 binaries: server, scheduler, worker, tx_indexer. |
| [reward-distributor](./reward-distributor.md) | Solidity | VULT reward distribution contract. |

---

## LOW — Public-Facing

Full autonomy. Minimal review required.

| Repo | Language | Description |
|------|----------|-------------|
| [website-prod](./website-prod.md) | TypeScript (Next.js) | Marketing website. Next.js 15 + Tailwind + shadcn/ui. |
| [docs](./docs.md) | TypeScript | Documentation site (docs.vultisig.com). GitBook. |
| [developer-portal](./developer-portal.md) | TypeScript | Developer dashboard. Wallet-based auth, EIP-712 signatures. |

---

## Dependency Graph

```
                    commondata (protobuf schemas)
                   /     |       \
              iOS    Android    Go services
               \       |       /
                vultisig-relay (TSS ceremony routing)
               /       |       \
         vultiserver  verifier  client apps
               \       |       /
              go-wrappers ← dkls23-rs + ml-dsa-tss (Rust crypto)

         vultisig-windows (upstream)
              |
         vultisig-sdk (downstream mirror of core/ + lib/)

         agent-backend → mcp (dynamic tools)
              |
         verifier (auth validation)
```

---

_Updated: 2026-03-05_
