# Vultisig Knowledge

_Shared knowledge base for humans and AI agents working on Vultisig._

---

## What is this?

A curated collection of technical documentation covering Vultisig's architecture, repositories, coding patterns, and operational knowledge. Used as a git submodule in agent workspaces and as a reference for developers.

---

## Quick Navigation

### Start Here

- **[repos/index.md](./repos/index.md)** — All 24 repos by security tier, with descriptions and dependency graph
- **[architecture/overview.md](./architecture/overview.md)** — System architecture overview
- **[coding/patterns.md](./coding/patterns.md)** — Cross-repo naming, error handling, testing, commit conventions
- **[coding/gotchas.md](./coding/gotchas.md)** — Known pitfalls that will waste your time

### Architecture

| Doc | What it covers |
|-----|---------------|
| [overview.md](./architecture/overview.md) | High-level system architecture |
| [mpc-tss-explained.md](./architecture/mpc-tss-explained.md) | MPC/TSS protocol (DKLS23, GG20) — how signing works |
| [vault-lifecycle.md](./architecture/vault-lifecycle.md) | Vault creation, reshare, recovery flows |
| [vault-creation.md](./architecture/vault-creation.md) | Detailed vault creation flow |
| [signing-flow.md](./architecture/signing-flow.md) | Transaction signing flow |
| [reshare.md](./architecture/reshare.md) | Key reshare protocol |
| [infrastructure.md](./architecture/infrastructure.md) | Server and infrastructure overview |
| [app-communication.md](./architecture/app-communication.md) | Multi-device communication via relay |
| [plugin-ecosystem.md](./architecture/plugin-ecosystem.md) | Plugin architecture and fee model |
| [utxo-address-rotation.md](./architecture/utxo-address-rotation.md) | UTXO address rotation spec |

### Repos

Individual repo docs live in [repos/](./repos/). Each covers: overview, folder structure, key entry points, dependencies, and notes.

See **[repos/index.md](./repos/index.md)** for the full list organized by security tier.

### Coding

| Doc | What it covers |
|-----|---------------|
| [patterns.md](./coding/patterns.md) | Naming conventions, error handling, architecture patterns, testing, commits, dependency management |
| [gotchas.md](./coding/gotchas.md) | Cross-repo pitfalls, crypto traps, build issues, infrastructure quirks |
| [dependencies.md](./coding/dependencies.md) | Centralized version matrix: runtimes, frameworks, infrastructure, external services |

### Support

| Doc | What it covers |
|-----|---------------|
| [common-issues.md](./support/common-issues.md) | Frequently encountered problems and solutions |
| [emergency-recovery.md](./support/emergency-recovery.md) | Critical recovery procedures |

---

## For Agents

If you're an AI agent working on a Vultisig repo:

1. Read the repo doc in `repos/` for your target repo
2. Read `coding/gotchas.md` to avoid known pitfalls
3. If working on crypto/TSS code, read `architecture/mpc-tss-explained.md`
4. If working on cross-platform changes, read `repos/index.md` dependency graph

---

_Updated: 2026-03-05_
