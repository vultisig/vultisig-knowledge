# Vault Creation

_Source: docs.vultisig.com_
_See also: [vault-lifecycle.md](./vault-lifecycle.md) for full lifecycle, [app-communication.md](./app-communication.md) for network modes_

## Fast Vault

**What:** Hot wallet - single device + Vultiserver auto-cosigning

**Best For:** Daily transactions, smaller amounts

### Creation Steps

1. Download and open Vultisig
2. Select **Fast Vault**
3. Choose vault name, enter email
4. Set password for server share (encrypts backup)
5. Optional: Set password hint
6. Complete keygen
7. Enter 4-digit verification code from email
8. Backup device share to separate storage

### Backup Structure

| Share | Storage |
|-------|---------|
| Device share | Your device (backup separately) |
| Server share | Sent to email during creation |

**Critical:** Password is the only way to access your backup.

---

## Secure Vault

**What:** Cold wallet - user-controlled devices only

**Best For:** Serious asset protection, shared wallets, DAOs

### Requirements

| Setup | Devices | Signing | Redundancy |
|-------|---------|---------|------------|
| 2-of-3 | 3 | 2 | Yes (1) | **Recommended** |
| 3-of-4 | 4 | 3 | Yes (1) | Maximum security |
| 2-of-2 | 2 | 2 | None | Not recommended |

### Creation Steps

**On Initiating Device:**
1. Select **Secure Vault**
2. Choose vault name
3. Generate QR code for pairing

**On Pairing Devices:**
1. Scan initiating device's QR code

**Completion:**
1. Tap Next when all devices connect
2. Wait for keygen across all devices

### Network Options

- **Internet:** Relay server (different networks)
- **Local:** WiFi/mDNS (same network, max privacy)

### Best Practices

For maximum redundancy:
- 3 different platforms (Mac, iOS, Android)
- Unique passwords per vault share
- 3 separate cloud providers (Google, iCloud, Dropbox)
- Different email addresses with 2FA each

**After Creation:** Immediately backup every device.

---

_Updated: 2026-01-06_
