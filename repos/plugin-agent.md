# plugin-agent

**URL:** github.com/vultisig/plugin-agent
**Language:** Go
**Purpose:** Policy enforcement middleware with WebSocket event streaming

---

## Overview

Middleware service bridging vault operations with plugin-based transaction processing. Manages plugin policies with ECDSA signature validation, enqueues async TSS tasks, and provides real-time event streaming via WebSocket.

---

## Key Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/vault/reshare` | POST | Queue vault resharing |
| `/vault/sign` | POST | Queue transaction signing |
| `/vault/sign/response/:taskId` | GET | Poll signing result |
| `/plugin/policy` | POST | Create policy (with signature verification) |
| `/plugin/policy` | PUT | Update policy |
| `/plugin/policy/:policyId` | DELETE | Soft-delete policy |
| `/events` | GET (WS) | WebSocket event streaming |
| `/propose` | POST | Propose transaction for signing |
| `/address/derive` | GET | Derive addresses from vault key |

---

## Event Types

- `vault_reshared` — vault key reshare completed
- `vault_deleted` — vault backup removed
- `policy_created` — new policy registered
- `policy_deleted` — policy deactivated

Events support historical replay from `last_seen` timestamp.

---

## Architecture

Two processes: Server (HTTP API) + Worker (Asynq task processor)

Uses: PostgreSQL (policies, events), Redis (queue, sessions), S3/MinIO (vault storage)

---

_Updated: 2026-02-09_
