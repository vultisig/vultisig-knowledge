# Emergency Recovery

_Source: docs.vultisig.com_

## What is Emergency Recovery?

**Last resort** process to reconstruct private keys from vault shares if Vultisig software becomes unavailable.

**Warning:** One-way operation. Permanently converts TSS vault into single-signature wallet.

---

## When to Use

- Vultisig software completely unavailable
- No other recovery options work
- You have threshold number of vault shares

**Do NOT use** for normal recovery - use reshare instead.

---

## Requirements

- Go (Golang) installed
- Access to threshold number of vault shares (e.g., 2-of-3)

---

## Methods

### 1. Web UI (Recommended)

**URL:** https://share-decoder.vultisig.com

Community-built tool, no dev expertise needed.

**Source:** github.com/vultisig/community-tools/tree/main/recovery-tools/vultisig-share-decoder

### 2. Self-Hosted Web UI

```bash
git clone https://github.com/vultisig/mobile-tss-lib
cd mobile-tss-lib/cmd/recovery-web
make
# Access via browser
```

### 3. CLI

```bash
git clone https://github.com/vultisig/mobile-tss-lib
cd mobile-tss-lib/cmd/recovery-cli
go run main.go
# Follow terminal prompts
```

---

## Supported Chains

| Chain | Supported |
|-------|-----------|
| Bitcoin (BTC) | ✅ |
| Bitcoin Cash (BCH) | ✅ |
| Litecoin (LTC) | ✅ |
| Dogecoin (DOGE) | ✅ |
| Ethereum (ETH) | ✅ |
| THORChain (RUNE) | ✅ |
| MayaChain (MAYA) | ✅ |
| Other chains | ❌ |

---

## Critical Warnings

1. **Vault destroyed:** Once private key generated, no longer a TSS vault
2. **Security downgrade:** Single-sig wallet = single point of failure
3. **Don't use for large amounts:** Move funds to new proper vault immediately
4. **Backup everything:** Before starting, ensure you have all shares backed up

---

_Updated: 2026-01-06_
