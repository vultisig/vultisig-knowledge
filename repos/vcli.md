# vcli

**URL:** github.com/vultisig/vcli
**Language:** Go
**Purpose:** CLI for local plugin development and end-to-end testing without mobile app

---

## Overview

Command-line interface that replaces the Vultisig mobile app for local development. Enables developers to import vaults, install plugins via 4-party TSS reshare, manage policies, and monitor transactions — all from the terminal.

Solves the version drift problem: the Vultisig stack (vcli, verifier, app-recurring, recipes, go-wrappers) is tightly coupled, and Docker-based development creates compatibility issues, especially on ARM Macs.

---

## Setup Requirements

- Go 1.24+
- Docker (for PostgreSQL, Redis, MinIO only)
- 5 sibling repos: `vcli`, `verifier`, `app-recurring`, `recipes`, `go-wrappers`
- A Fast Vault exported from the Vultisig mobile app

---

## Workflow

```
START → IMPORT → INSTALL → [GENERATE → ADD → MONITOR] → DELETE → UNINSTALL → STOP
```

1. **START**: Launch infrastructure (Docker) + native services
2. **IMPORT**: Import Fast Vault from mobile app export
3. **INSTALL**: 4-party TSS reshare (CLI + Fast Vault Server + Verifier + Plugin)
4. **GENERATE**: Generate policies with auto address derivation
5. **ADD**: Add policies to installed plugins
6. **MONITOR**: Watch execution and tx status
7. **DELETE/UNINSTALL/STOP**: Clean up

---

## Critical Rules

- Follow steps exactly — no skipping or restarting mid-process
- TSS sessions are stateful protocol executions
- Never manually edit databases, move keyshare files, or reuse failed state

---

_Updated: 2026-02-09_
