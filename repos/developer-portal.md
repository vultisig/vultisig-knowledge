# developer-portal

**URL:** github.com/vultisig/developer-portal
**Language:** TypeScript (React 18)
**Purpose:** Web dashboard for plugin developers to manage plugins, API keys, teams, and earnings

---

## Overview

React frontend for plugin developers. Wallet-based auth via Vultisig extension. All changes require EIP-712 signatures for audit trail.

**Current status:** Plugin registration is manual — developers contact dev@vultisig.com or Discord.

---

## Features

| Feature | Description |
|---------|-------------|
| Plugin management | View/edit registered plugins (title, description, endpoint) |
| API keys | Create, enable/disable, delete keys (shown once on creation) |
| Team collaboration | RBAC with Admin, Staff, Editor, Viewer roles |
| Magic link invites | 8-hour expiry, single-use team invitations |
| Earnings dashboard | Transaction history, filters by plugin/date/status |
| Kill-switch | Staff-only emergency plugin disable |

---

## Auth

Wallet-based via Vultisig browser extension:
1. Connect wallet → extension returns vault info
2. Sign message → JWT token issued
3. All subsequent requests use `Authorization: Bearer {token}`

Only Fast Vaults can connect.

---

## Tech Stack

React 18, TypeScript, Vite 7, Ant Design v6, styled-components, ethers.js v6, React Query

---

_Updated: 2026-02-09_
