# Common Issues

_Known issues and support patterns_

---

## Critical: Fast Vault Share Import Error

**Frequency:** #1 support ticket cause

### The Problem

User imports the wrong fast vault share (server-side share instead of device share) and can no longer sign transactions.

### Why It Happens

Fast Vault = 2-of-2 setup:
1. **Device share** - stored on user's device
2. **Server share** - stored on Vultiserver, emailed as backup

When user gets new device or restores:
- They import the **server share** (from email backup) instead of their **device share**
- Now they have: server share on device + server share on Vultiserver
- Can't sign because both shares are the same

### User Impact

- Cannot sign any transactions
- Funds appear locked
- Often leads to panic/support escalation

### Prevention

1. **Clear labeling** - Device share vs Server share must be obvious
2. **Import validation** - Warn if importing server share to device
3. **Backup education** - Emphasize backing up device share separately

### Recovery

If user backed up device share: restore from that backup

If user did NOT back up device share: **funds are inaccessible** (this is the nightmare scenario)

---

## Cross-Platform Keysign Failures

**Frequency:** Common after feature releases

### The Problem

Keysigning fails when devices are on different app versions with incompatible protocol changes.

### Why It Happens

- iOS ships update before Android (or vice versa)
- New feature changes signing protocol slightly
- Old version can't communicate with new version

### Prevention

- Coordinated release schedules
- Protocol versioning
- Feature flags to enable only when all platforms ready

### User Workaround

Update all devices to same version before attempting keysign.

---

## Balance/Fee Inconsistencies

**Frequency:** Occasional

### The Problem

Different devices show different balances or fee estimates for same wallet.

### Why It Happens

- Different RPC endpoints
- Caching differences
- Chain data sync timing

### User Workaround

Refresh wallet, ensure all devices synced, use initiating device as source of truth.

---

_Updated: 2026-01-06_
