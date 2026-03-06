# Resharing

_Source: docs.vultisig.com_
_See also: [vault-lifecycle.md](./vault-lifecycle.md) for full lifecycle, [mpc-tss-explained.md](./mpc-tss-explained.md) for protocol details_

## What is Reshare?

Vault configuration update that allows:
- Adding devices (2-of-2 → 2-of-3)
- Removing compromised devices
- Replacing non-responsive devices

**Key benefit:** Same addresses, no fund migration needed.

---

## Requirements

- Current signing threshold must participate
- New devices must be online during reshare

---

## Process

1. Initiate reshare from existing vault
2. All threshold devices participate
3. New vault shares generated for all devices
4. Old shares invalidated

---

## Critical Post-Reshare Actions

**All vault shares change after reshare.**

| Action | Why |
|--------|-----|
| Delete old backups | No longer valid |
| Create new backups | Only way to recover |
| Verify new threshold | Confirm config correct |

---

## Use Cases

### Add Device for Redundancy
- 2-of-2 has no redundancy (lose one = lose all)
- Reshare to 2-of-3 adds safety margin

### Remove Compromised Device
- If device stolen/compromised
- Reshare excludes that device
- Old share becomes useless

### Replace Device
- Device broken/lost but have backup
- Add new device, exclude old

---

## TSS Advantage

Unlike multi-sig where configuration changes require:
- New addresses
- Fund migration
- On-chain transactions

TSS reshare:
- Same addresses persist
- No fund movement
- Configuration change only

---

_Updated: 2026-01-06_
