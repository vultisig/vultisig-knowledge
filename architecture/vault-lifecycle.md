# Vault Lifecycle

_Source: docs.vultisig.com_
_See also: [vault-creation.md](./vault-creation.md) for step-by-step, [signing-flow.md](./signing-flow.md) for keysign, [reshare.md](./reshare.md) for reshare protocol_

## 1. Create (Keygen)

**Requirements:** 100% of devices online

**Process:**
1. Initiating device creates session
2. QR code generated with session details
3. Other devices scan to join
4. Distributed key generation completes
5. Each device receives unique vault share
6. Shared public key generates on-chain addresses

**Output:**
- Vault shares (one per device)
- Public addresses (the "vault")
- No private key exists

**Critical:** Individual shares contain no funds - the addresses do.

---

## 2. Use (Keysign)

**Requirements:** 67%+ threshold (e.g., 2-of-3)

**Process:**
1. **Session Initiation:** Host device starts session, generates QR
2. **Device Pairing:** Other devices scan QR to join
3. **Signing Ceremony:** Threshold devices jointly sign using TSS
4. **Broadcast:** Signed transaction sent to blockchain

**Fast Vault:** Single device + Vultiserver auto-cosigns
**Secure Vault:** Multiple physical devices participate

---

## 3. Reshare

**Purpose:** Modify vault configuration without changing addresses

**Use Cases:**
- Add device (2-of-2 → 2-of-3)
- Remove compromised device
- Replace non-responsive device

**Requirements:** Current signing threshold

**Critical:** After reshare:
- All vault shares change
- Old backups become incompatible
- Replace all backups immediately

---

## 4. Recover

**Normal Recovery:**
- Restore from backup shares
- Re-pair devices via reshare

**Emergency Recovery (Last Resort):**
- Reconstruct actual private key from threshold shares
- One-way operation - vault becomes single-sig
- Use only if Vultisig software unavailable

**Supported chains:** BTC, BCH, LTC, DOGE, ETH, RUNE, MAYA only

---

## Backup Strategy (Secure Vault)

For maximum redundancy:
- 3 different platforms (Mac, iOS, Android)
- Unique passwords per share
- 3 separate cloud providers (Google, iCloud, Dropbox)
- Different email addresses with 2FA

**Result:** Recovery possible worldwide with just email + passwords

---

_Updated: 2026-01-06_
