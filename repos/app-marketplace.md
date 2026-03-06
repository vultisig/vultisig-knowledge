# app-marketplace

**URL:** github.com/vultisig/app-marketplace
**Language:** TypeScript (97.6%)
**Purpose:** Plugin marketplace web app for Vultisig vault apps and automations

---

## Overview

Decentralized marketplace web application for discovering, installing, and managing Vultisig vault apps. Enables users to browse apps, install via browser extension, manage subscriptions, and create custom automation recipes. Built with React 18, Vite, and Ant Design.

---

## Folder Structure

```
app-marketplace/
├── src/
│   ├── components/        # Reusable UI components
│   ├── pages/             # Route-level page components
│   ├── providers/         # React context providers
│   ├── utils/             # Helper functions and APIs
│   ├── types/             # TypeScript definitions
│   ├── hooks/             # Custom React hooks
│   ├── styles/            # Global styles and themes
│   ├── main.tsx           # Application entry point
│   └── App.tsx            # Root component with routing
├── public/                # Static assets
├── proto/                 # Protocol Buffer definitions
├── vite.config.ts         # Build configuration
├── tsconfig.json          # TypeScript config
├── eslint.config.js       # Linting rules
└── vercel.json            # Deployment config
```

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `src/main.tsx` | Application bootstrap |
| `src/App.tsx` | Root component, routing setup |
| `src/pages/` | Page components (Home, App Details, etc.) |
| `src/providers/` | Context providers (Auth, Theme, etc.) |
| `src/utils/` | API clients, helpers |
| `src/components/` | Shared UI components |
| `proto/` | Protobuf schemas for data exchange |
| `vite.config.ts` | Build and dev server config |

---

## Dependencies

**Core:**
- React 18
- TypeScript
- Vite (build tool)
- Ant Design (UI library)

**State/Data:**
- React Query (TanStack Query)
- React Router v6
- Protocol Buffers

**Integration:**
- WebAssembly (wallet-core)
- postMessage API (extension communication)

---

## Integration Notes

- Communicates with browser extension via postMessage
- Uses protobuf for data serialization with backend
- Requires Vultisig browser extension for full functionality
- Node.js v18+ required

---

_Updated: 2026-01-06_
