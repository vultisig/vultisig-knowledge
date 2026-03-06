# vultisig-windows

**URL:** github.com/vultisig/vultisig-windows
**Platform:** Windows desktop + browser extension
**Language:** TypeScript (React) + Go (Wails backend)

---

## Overview

Desktop wallet app (Windows) + Chrome browser extension. React/TypeScript frontend with Go backend via Wails v2. Contains the **reference implementation for agent chat** (97 files, 35 tool handlers) — iOS and Android will mirror this.

---

## Folder Structure

```
vultisig-windows/
├── clients/
│   ├── desktop/        → Desktop app (React + Vite, Wails-hosted)
│   └── extension/      → Chrome extension (React)
├── core/               → Shared business logic
│   ├── chain/          → Blockchain integrations (per-chain modules)
│   ├── config/         → Configuration
│   ├── mpc/            → Multi-party computation protocols
│   └── ui/             → Shared UI components
│       └── agent/      → AI agent chat (reference implementation)
│           ├── orchestrator/  → AgentOrchestrator, BackendClient, AuthService
│           ├── tools/
│           │   ├── handlers/  → 35 tool handlers (vault, asset, tx, plugin, policy)
│           │   └── shared/    → Shared tool utilities
│           ├── components/    → Chat UI components
│           └── hooks/         → React hooks
├── lib/                → Pure utilities (no Vultisig dependencies)
├── mediator/           → Relay server (Go)
├── relay/              → Relay service (Go)
├── storage/            → Storage layer (Go + SQLite)
└── main.go             → Wails entry point
```

**Domain layers:**
- `lib/` — Pure, reusable (no Vultisig deps)
- `core/` — Vultisig business logic
- `clients/` — Platform-specific code

Import rule: `clients/` → `core/` → `lib/`. Never the reverse.

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `core/mpc/` | MPC protocol — changes affect signing on ALL platforms |
| `core/ui/agent/` | Reference agent chat impl (73 files, 39 tools) |
| `core/chain/` | Per-chain blockchain integrations |
| `clients/desktop/` | Desktop app entry point |
| `clients/extension/` | Browser extension entry point |
| `mediator/` + `relay/` | Go services for multi-device communication |
| `main.go` | Wails v2 entry point (Go ↔ WebView bridge) |

---

## Key Commands

```bash
yarn dev:desktop       # Desktop dev server
yarn dev:extension     # Extension dev
yarn build:desktop     # Production build (desktop)
yarn build:extension   # Production build (extension)
yarn test              # Unit tests
yarn lint              # Lint
yarn typecheck         # Type check
yarn check:all         # Full quality gate (lint + typecheck + test + knip)
```

---

## Dependencies

- **Wails v2** — Go ↔ WebView bridge
- **TanStack React Query** — Server state management
- **Yarn workspaces** — Monorepo management
- **Vitest** — Testing
- Go services: mediator, relay, storage (test with `go test -race`)

---

## Notes

- This repo is **upstream** for vultisig-sdk — `packages/core/` and `packages/lib/` in the SDK are synced from here
- Agent chat in `core/ui/agent/` is the reference implementation — changes need to be mirrored in iOS/Android
- Go services (mediator, relay) require separate testing with `-race` flag

---

_Updated: 2026-03-05_
