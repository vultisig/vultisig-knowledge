# docs Repository (Comprehensive)

**URL:** github.com/vultisig/docs
**Purpose:** Source for docs.vultisig.com (GitBook-powered)

---

## Folder Structure

```
docs/
├── .gitbook/assets/          # Images and media
├── app-guide/                # User feature docs
│   ├── creating-a-vault/     # Fast/Secure vault creation
│   ├── wallet/               # Sending, swapping
│   ├── defi/                 # Circle, THORChain, Maya, staking
│   └── vault-management/     # Details, backup, reshare, upgrade
├── developer-docs/           # SDK, extension, app store
│   ├── app-store/            # Plugin development
│   └── vultisig-sdk/         # SDK guides
├── getting-started/          # Onboarding
├── help/                     # FAQ, troubleshooting
├── security-technology/      # TSS, DKLS23, protocols
├── vult-token/               # Token docs, airdrop
├── vultisig-ecosystem/       # Extension, web app
├── vultisig-infrastructure/  # Vultiserver, relay
├── SUMMARY.md                # Navigation structure
└── README.md                 # Landing page
```

---

## Key Files

| File | Purpose |
|------|---------|
| `SUMMARY.md` | Defines sidebar navigation for GitBook |
| `README.md` | Homepage content |
| `security-technology/how-dkls23-works.md` | Core TSS protocol explanation |
| `developer-docs/app-store/build-your-app.md` | Plugin development guide |

---

## Content Organization

### User-Facing (app-guide/)
- Vault creation flows (Fast vs Secure)
- Wallet operations (send, swap)
- DeFi integrations (per-chain)
- Vault management (backup, reshare)

### Developer-Facing (developer-docs/)
- App Store plugin development
- SDK implementation
- Extension integration guide

### Technical (security-technology/)
- TSS/DKLS23 deep dives
- Keysign process
- Multi-sig comparison
- Emergency recovery

---

## How to Update

1. Edit markdown files
2. Push to main branch
3. GitBook auto-deploys to docs.vultisig.com

---

## Dependencies

- GitBook (hosting/rendering)
- Assets in `.gitbook/assets/`

---

## Key Entry Points for AI Queries

| Topic | File |
|-------|------|
| "How does DKLS23 work?" | `security-technology/how-dkls23-works.md` |
| "How to create a vault?" | `app-guide/creating-a-vault/` |
| "How to build a plugin?" | `developer-docs/app-store/build-your-app.md` |
| "What is Vultiserver?" | `vultisig-infrastructure/what-is-vultisigner.md` |
| "Token utility?" | `vult-token/vult.md` |

---

_Updated: 2026-01-06_
