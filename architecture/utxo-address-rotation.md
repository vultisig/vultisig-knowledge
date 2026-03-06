# UTXO Address Rotation Spec

**Status:** Draft
**Author:** Paaao
**Date:** 2026-01-07
**Scope:** Bitcoin first, then LTC/DOGE/BCH

---

## Goal

Automatic address rotation for UTXO chains without breaking reshare or requiring new keygen ceremonies.

- Change addresses: rotate on every spend
- Receive addresses: rotate on every receive (toggle for static in settings)
- UX: invisible to user
- Privacy: break transaction graph, standard hygiene

---

## Derivation Scheme

### Child Key Derivation

```
Master:     Q = x·G                          (from TSS keygen)
Chain code: c = SHA256("vultisig-chaincode" || Q)
Child i:    Q_i = Q + SHA256(c || type || i)·G

type = 0x00 for receive, 0x01 for change
i    = uint32 index
```

All devices derive identical `Q_i` from shared `Q`. No communication required for derivation.

### Signing for Child Address

During DKLS23 signing, each device adds the public tweak to their protocol:

```
tweak = SHA256(c || type || i)

// Integrated into DKLS23 - each device's share contribution is adjusted
// The tweak is public, so no additional rounds needed
```

**No new keygen.** Same signing ceremony, adjusted shares.

---

## Index Synchronization

### The Problem

2-of-3 vault: A and B sign, C is offline.
C doesn't know which indices were used.

### Solution: Blockchain as Source of Truth

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  Device A   │     │  Device B   │     │  Device C   │
│             │     │             │     │  (offline)  │
│ signs tx    │     │ signs tx    │     │             │
│ change → i=5│     │ change → i=5│     │             │
└──────┬──────┘     └──────┬──────┘     └─────────────┘
       │                   │
       └───────┬───────────┘
               ▼
        ┌─────────────┐
        │ Blockchain  │  ← source of truth
        └─────────────┘
               │
       ┌───────┴───────────┐
       ▼                   ▼
┌─────────────┐     ┌─────────────┐
│  Device A   │     │  Device C   │
│  i=5 cached │     │  syncs...   │
│             │     │  scans chain│
│             │     │  discovers 5│
└─────────────┘     └─────────────┘
```

### Index Discovery Algorithm

```typescript
async function discoverIndices(masterPubkey: Q, type: 'receive' | 'change'): number {
  const GAP_LIMIT = 20;
  let index = 0;
  let consecutiveEmpty = 0;

  while (consecutiveEmpty < GAP_LIMIT) {
    const childPubkey = deriveChild(Q, type, index);
    const address = pubkeyToAddress(childPubkey);
    const hasHistory = await checkAddressHistory(address);

    if (hasHistory) {
      consecutiveEmpty = 0;
    } else {
      consecutiveEmpty++;
    }
    index++;
  }

  return index - GAP_LIMIT; // next unused index
}
```

### When to Sync

| Event | Action |
|-------|--------|
| App open | Background scan (cached indices as fallback) |
| Before receive screen | Quick scan or use cache if fresh (<5 min) |
| After signing | Increment local index immediately |
| Device rejoins vault | Full scan to catch up |

### Local Cache Structure

```typescript
interface VaultIndices {
  receive: {
    nextIndex: number;
    lastScan: timestamp;
  };
  change: {
    nextIndex: number;
    lastScan: timestamp;
  };
}
```

Stored in vault metadata, per-chain.

---

## Transaction Building

### Spend Flow

```
1. User initiates send
2. Select UTXOs (from all known addresses: Q, Q_0, Q_1, ...)
3. Change address = Q_{change.nextIndex}
4. Build transaction
5. Sign via DKLS23 (with tweak for each input's derivation path)
6. Broadcast
7. Increment change.nextIndex locally
8. Other devices discover on next sync
```

### Receive Flow

```
1. User opens receive screen
2. Show address Q_{receive.nextIndex}
3. Monitor for incoming tx
4. On deposit detected: increment receive.nextIndex
5. Next receive screen shows new address
```

---

## Multi-Input Signing

A transaction may spend UTXOs from multiple derived addresses.

```
Inputs:
  - UTXO at Q_3 (receive index 3)
  - UTXO at Q_7 (change index 7, type=change)

Each input requires different tweak during signing.
```

**DKLS23 handles this:** Sign each input separately with its specific tweak. Standard for any wallet spending multiple addresses.

---

## Settings

```
Settings > Privacy > Address Rotation

[Bitcoin]
  Receive addresses:  ○ Static  ● Rotating (recommended)
  Change addresses:   Always rotating (cannot disable)

[Litecoin]
  ...
```

Change rotation is mandatory (no privacy downside, pure upside).
Receive rotation is optional (some users want stable deposit address).

---

## Edge Cases

| Case | Handling |
|------|----------|
| Gap limit exceeded | Warn user, allow manual index override |
| Device offline for months | Full rescan on rejoin (slow but correct) |
| Index conflict (race) | Blockchain is truth, higher index wins |
| Reshare | Indices preserved (Q unchanged, all Q_i unchanged) |
| Vault restore from backup | Full scan required |

---

## Migration

Existing vaults:
- `receive.nextIndex = 0` (current address becomes Q_0 = Q + 0 = Q... wait, no)

**Actually:** To preserve backwards compatibility:

```
Index -1 (or "master") = Q itself (no tweak)
Index 0+ = Q + tweak

Existing funds at Q remain spendable (sign with zero tweak)
New addresses start at index 0
```

This way existing users keep their current address as a valid receive/spend address.

---

## Security Considerations

1. **Tweak is public:** Derived from public Q, no secret leakage
2. **Same security model:** Threshold signing unchanged
3. **No single point of failure:** Index discovery from chain, not central server
4. **Reshare safe:** Master key unchanged, all derivations persist

---

## Open Questions

1. **Relay server role:** Should indices be synced via relay for faster UX? (privacy tradeoff)
2. **Index storage:** Vault file only, or also encrypted cloud backup?
3. **Gap limit:** 20 standard, or configurable?

---

# Implementation: Required Changes

## Component Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        CHANGES REQUIRED                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐        │
│  │ go-wrappers  │   │ vultisig-go  │   │  commondata  │        │
│  │ DKLS23+tweak │   │ derivation   │   │  protobuf    │        │
│  └──────┬───────┘   └──────┬───────┘   └──────┬───────┘        │
│         │                  │                  │                 │
│         └────────┬─────────┴─────────┬────────┘                 │
│                  ▼                   ▼                          │
│         ┌──────────────┐   ┌──────────────┐                    │
│         │ vultisig-sdk │   │   backends   │                    │
│         │  TypeScript  │   │ (optional)   │                    │
│         └──────┬───────┘   └──────────────┘                    │
│                │                                                │
│    ┌───────────┼───────────┬───────────────┐                   │
│    ▼           ▼           ▼               ▼                   │
│ ┌──────┐  ┌────────┐  ┌─────────┐  ┌──────────┐               │
│ │ iOS  │  │Android │  │ Windows │  │ Extension│               │
│ └──────┘  └────────┘  └─────────┘  └──────────┘               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 1. Cryptographic Layer: `go-wrappers`

**Repo:** `vultisig/go-wrappers`
**Files:** DKLS23 bindings

### Changes

```go
// Current signature
func Sign(share []byte, message []byte) ([]byte, error)

// New signature - add optional tweak
func SignWithTweak(share []byte, message []byte, tweak []byte) ([]byte, error)
```

### Implementation

```go
// tweak.go (new file)
package dkls23

import (
    "crypto/sha256"
)

// DeriveTweak computes the additive tweak for child key derivation
// tweak = SHA256(chaincode || type || index)
func DeriveTweak(chaincode []byte, addressType byte, index uint32) []byte {
    h := sha256.New()
    h.Write(chaincode)
    h.Write([]byte{addressType})

    indexBytes := make([]byte, 4)
    binary.BigEndian.PutUint32(indexBytes, index)
    h.Write(indexBytes)

    return h.Sum(nil)
}

// SignWithTweak signs a message using tweaked share
// The tweak is added to the share's contribution during signing
func SignWithTweak(share []byte, message []byte, tweak []byte) ([]byte, error) {
    if len(tweak) == 0 {
        return Sign(share, message) // no tweak = original behavior
    }

    // Add tweak to share before signing protocol
    // Implementation depends on DKLS23 internals
    tweakedShare := addTweakToShare(share, tweak)
    return Sign(tweakedShare, message)
}
```

### Notes

- Tweak is added as scalar to share contribution
- Both parties add same tweak (public value)
- Result: valid signature for `Q + tweak·G`

---

## 2. Core Library: `vultisig-go`

**Repo:** `vultisig/vultisig-go`
**Package:** `address/`

### New Files

```
address/
  ├── derivation.go      (new)
  ├── derivation_test.go (new)
  └── scanner.go         (new)
```

### derivation.go

```go
package address

import (
    "crypto/sha256"
    "encoding/binary"

    "github.com/btcsuite/btcd/btcec/v2"
)

const (
    AddressTypeReceive byte = 0x00
    AddressTypeChange  byte = 0x01

    ChainCodePrefix = "vultisig-chaincode"
)

// DeriveChainCode generates deterministic chain code from master pubkey
func DeriveChainCode(masterPubkey []byte) []byte {
    h := sha256.New()
    h.Write([]byte(ChainCodePrefix))
    h.Write(masterPubkey)
    return h.Sum(nil)
}

// DeriveChildPubkey derives child public key without private key
// Q_i = Q + SHA256(chaincode || type || index)·G
func DeriveChildPubkey(masterPubkey []byte, chaincode []byte, addrType byte, index uint32) ([]byte, error) {
    // Parse master pubkey
    Q, err := btcec.ParsePubKey(masterPubkey)
    if err != nil {
        return nil, err
    }

    // Compute tweak scalar
    tweak := computeTweak(chaincode, addrType, index)

    // tweak·G
    var tweakPoint btcec.JacobianPoint
    btcec.ScalarBaseMultNonConst(tweak, &tweakPoint)
    tweakPoint.ToAffine()

    // Q + tweak·G
    var result btcec.JacobianPoint
    btcec.AddNonConst(&Q.ToJacobian(), &tweakPoint, &result)
    result.ToAffine()

    childPubkey := btcec.NewPublicKey(&result.X, &result.Y)
    return childPubkey.SerializeCompressed(), nil
}

// DeriveChildAddress derives Bitcoin address for child index
func DeriveChildAddress(masterPubkey []byte, chaincode []byte, addrType byte, index uint32, network Network) (string, error) {
    childPubkey, err := DeriveChildPubkey(masterPubkey, chaincode, addrType, index)
    if err != nil {
        return "", err
    }
    return PubkeyToAddress(childPubkey, network)
}

// GetTweakForSigning returns the tweak bytes needed for signing
func GetTweakForSigning(chaincode []byte, addrType byte, index uint32) []byte {
    return computeTweak(chaincode, addrType, index)
}

func computeTweak(chaincode []byte, addrType byte, index uint32) *btcec.ModNScalar {
    h := sha256.New()
    h.Write(chaincode)
    h.Write([]byte{addrType})

    indexBytes := make([]byte, 4)
    binary.BigEndian.PutUint32(indexBytes, index)
    h.Write(indexBytes)

    var scalar btcec.ModNScalar
    scalar.SetBytes((*[32]byte)(h.Sum(nil)))
    return &scalar
}
```

### scanner.go

```go
package address

import (
    "context"
)

const DefaultGapLimit = 20

// BlockchainClient interface for address history lookup
type BlockchainClient interface {
    HasAddressHistory(ctx context.Context, address string) (bool, error)
}

// Scanner discovers used address indices from blockchain
type Scanner struct {
    client   BlockchainClient
    gapLimit int
}

func NewScanner(client BlockchainClient) *Scanner {
    return &Scanner{
        client:   client,
        gapLimit: DefaultGapLimit,
    }
}

// ScanResult contains discovered indices
type ScanResult struct {
    NextReceiveIndex uint32
    NextChangeIndex  uint32
    UsedAddresses    []UsedAddress
}

type UsedAddress struct {
    Type    byte
    Index   uint32
    Address string
}

// Scan discovers all used addresses and returns next unused indices
func (s *Scanner) Scan(ctx context.Context, masterPubkey []byte, network Network) (*ScanResult, error) {
    chaincode := DeriveChainCode(masterPubkey)

    result := &ScanResult{
        UsedAddresses: make([]UsedAddress, 0),
    }

    // Scan receive addresses
    result.NextReceiveIndex = s.scanType(ctx, masterPubkey, chaincode, AddressTypeReceive, network, &result.UsedAddresses)

    // Scan change addresses
    result.NextChangeIndex = s.scanType(ctx, masterPubkey, chaincode, AddressTypeChange, network, &result.UsedAddresses)

    // Also check master address (index -1 / no tweak)
    masterAddr, _ := PubkeyToAddress(masterPubkey, network)
    if hasHistory, _ := s.client.HasAddressHistory(ctx, masterAddr); hasHistory {
        result.UsedAddresses = append(result.UsedAddresses, UsedAddress{
            Type:    0xFF, // special marker for master
            Index:   0,
            Address: masterAddr,
        })
    }

    return result, nil
}

func (s *Scanner) scanType(ctx context.Context, masterPubkey, chaincode []byte, addrType byte, network Network, used *[]UsedAddress) uint32 {
    var index uint32 = 0
    consecutiveEmpty := 0

    for consecutiveEmpty < s.gapLimit {
        addr, err := DeriveChildAddress(masterPubkey, chaincode, addrType, index, network)
        if err != nil {
            break
        }

        hasHistory, err := s.client.HasAddressHistory(ctx, addr)
        if err != nil {
            break
        }

        if hasHistory {
            *used = append(*used, UsedAddress{
                Type:    addrType,
                Index:   index,
                Address: addr,
            })
            consecutiveEmpty = 0
        } else {
            consecutiveEmpty++
        }
        index++
    }

    return index - uint32(s.gapLimit)
}
```

---

## 3. Protocol Schemas: `commondata`

**Repo:** `vultisig/commondata`
**Files:** Protobuf definitions

### vault.proto changes

```protobuf
// Add to existing Vault message
message Vault {
    // ... existing fields ...

    // Address rotation indices per UTXO chain
    map<string, AddressIndices> utxo_indices = 20;

    // Settings
    AddressRotationSettings rotation_settings = 21;
}

message AddressIndices {
    uint32 next_receive_index = 1;
    uint32 next_change_index = 2;
    int64 last_scan_timestamp = 3;
}

message AddressRotationSettings {
    bool receive_rotation_enabled = 1;  // default true
    // change rotation always enabled, no toggle
}
```

### keysign.proto changes

```protobuf
// Add tweak info to signing request
message KeysignPayload {
    // ... existing fields ...

    // For UTXO: tweak data per input
    repeated InputTweak input_tweaks = 15;
}

message InputTweak {
    uint32 input_index = 1;      // which input in the tx
    bytes tweak = 2;             // 32-byte tweak for this input's address
}
```

---

## 4. TypeScript SDK: `vultisig-sdk`

**Repo:** `vultisig/vultisig-sdk`

### New Files

```
src/
  ├── derivation/
  │   ├── child-key.ts    (new)
  │   ├── scanner.ts      (new)
  │   └── index.ts        (new)
```

### child-key.ts

```typescript
import { sha256 } from '@noble/hashes/sha256';
import { secp256k1 } from '@noble/curves/secp256k1';
import { concatBytes, numberToBytesBE } from '@noble/curves/abstract/utils';

const CHAINCODE_PREFIX = new TextEncoder().encode('vultisig-chaincode');

export const AddressType = {
  RECEIVE: 0x00,
  CHANGE: 0x01,
} as const;

export type AddressType = typeof AddressType[keyof typeof AddressType];

export function deriveChainCode(masterPubkey: Uint8Array): Uint8Array {
  return sha256(concatBytes(CHAINCODE_PREFIX, masterPubkey));
}

export function computeTweak(
  chaincode: Uint8Array,
  addressType: AddressType,
  index: number
): Uint8Array {
  const indexBytes = numberToBytesBE(index, 4);
  return sha256(concatBytes(chaincode, new Uint8Array([addressType]), indexBytes));
}

export function deriveChildPubkey(
  masterPubkey: Uint8Array,
  chaincode: Uint8Array,
  addressType: AddressType,
  index: number
): Uint8Array {
  const tweak = computeTweak(chaincode, addressType, index);

  // Q_i = Q + tweak·G
  const Q = secp256k1.ProjectivePoint.fromHex(masterPubkey);
  const tweakPoint = secp256k1.ProjectivePoint.BASE.multiply(
    secp256k1.utils.normPrivateKeyToScalar(tweak)
  );
  const childPoint = Q.add(tweakPoint);

  return childPoint.toRawBytes(true); // compressed
}

export function getTweakForSigning(
  chaincode: Uint8Array,
  addressType: AddressType,
  index: number
): Uint8Array {
  return computeTweak(chaincode, addressType, index);
}
```

### scanner.ts

```typescript
import { deriveChainCode, deriveChildPubkey, AddressType } from './child-key';
import { pubkeyToAddress } from '../address';

const DEFAULT_GAP_LIMIT = 20;

export interface BlockchainClient {
  hasAddressHistory(address: string): Promise<boolean>;
}

export interface ScanResult {
  nextReceiveIndex: number;
  nextChangeIndex: number;
  usedAddresses: Array<{
    type: AddressType;
    index: number;
    address: string;
  }>;
}

export async function scanIndices(
  masterPubkey: Uint8Array,
  client: BlockchainClient,
  network: 'mainnet' | 'testnet',
  gapLimit = DEFAULT_GAP_LIMIT
): Promise<ScanResult> {
  const chaincode = deriveChainCode(masterPubkey);
  const result: ScanResult = {
    nextReceiveIndex: 0,
    nextChangeIndex: 0,
    usedAddresses: [],
  };

  // Scan receive
  result.nextReceiveIndex = await scanType(
    masterPubkey,
    chaincode,
    AddressType.RECEIVE,
    client,
    network,
    gapLimit,
    result.usedAddresses
  );

  // Scan change
  result.nextChangeIndex = await scanType(
    masterPubkey,
    chaincode,
    AddressType.CHANGE,
    client,
    network,
    gapLimit,
    result.usedAddresses
  );

  return result;
}

async function scanType(
  masterPubkey: Uint8Array,
  chaincode: Uint8Array,
  addressType: AddressType,
  client: BlockchainClient,
  network: 'mainnet' | 'testnet',
  gapLimit: number,
  usedAddresses: ScanResult['usedAddresses']
): Promise<number> {
  let index = 0;
  let consecutiveEmpty = 0;

  while (consecutiveEmpty < gapLimit) {
    const childPubkey = deriveChildPubkey(masterPubkey, chaincode, addressType, index);
    const address = pubkeyToAddress(childPubkey, network);
    const hasHistory = await client.hasAddressHistory(address);

    if (hasHistory) {
      usedAddresses.push({ type: addressType, index, address });
      consecutiveEmpty = 0;
    } else {
      consecutiveEmpty++;
    }
    index++;
  }

  return index - gapLimit;
}
```

---

## 5. Mobile Apps: iOS/Android/Windows

### Changes per platform

| Component | Change |
|-----------|--------|
| **Vault model** | Add `utxoIndices` map, `rotationSettings` |
| **Receive screen** | Show derived address based on `nextReceiveIndex` |
| **Send flow** | Use derived change address, include tweaks in keysign |
| **Settings** | Add "Address Rotation" toggle under Privacy |
| **Sync** | Background scanner on app open, after keysign |

### iOS Example (Swift)

```swift
// VaultIndices.swift (new)
struct AddressIndices: Codable {
    var nextReceiveIndex: UInt32 = 0
    var nextChangeIndex: UInt32 = 0
    var lastScanTimestamp: Date?
}

// Vault+Rotation.swift (extension)
extension Vault {
    func getReceiveAddress(for chain: UTXOChain) -> String {
        guard rotationSettings.receiveRotationEnabled else {
            return masterAddress(for: chain) // static
        }

        let indices = utxoIndices[chain.id] ?? AddressIndices()
        return deriveChildAddress(
            type: .receive,
            index: indices.nextReceiveIndex,
            chain: chain
        )
    }

    func getChangeAddress(for chain: UTXOChain) -> String {
        let indices = utxoIndices[chain.id] ?? AddressIndices()
        return deriveChildAddress(
            type: .change,
            index: indices.nextChangeIndex,
            chain: chain
        )
    }

    private func deriveChildAddress(type: AddressType, index: UInt32, chain: UTXOChain) -> String {
        let chaincode = Derivation.chainCode(from: publicKey)
        let childPubkey = Derivation.childPubkey(
            master: publicKey,
            chaincode: chaincode,
            type: type,
            index: index
        )
        return WalletCore.address(from: childPubkey, coin: chain.coinType)
    }
}

// KeysignViewModel+Rotation.swift
extension KeysignViewModel {
    func buildUTXOTransaction() -> UTXOTransaction {
        var tx = UTXOTransaction()

        // Select UTXOs from all known addresses
        let allAddresses = getAllDerivedAddresses()
        tx.inputs = selectUTXOs(from: allAddresses, amount: sendAmount)

        // Change goes to next derived address
        tx.changeAddress = vault.getChangeAddress(for: chain)

        // Build tweak data for each input
        tx.inputTweaks = tx.inputs.map { input in
            getTweakForAddress(input.address)
        }

        return tx
    }

    func afterSuccessfulSign() {
        // Increment change index
        var indices = vault.utxoIndices[chain.id] ?? AddressIndices()
        indices.nextChangeIndex += 1
        vault.utxoIndices[chain.id] = indices
        vault.save()
    }
}
```

### Settings UI

```swift
// SettingsView.swift
Section("Privacy") {
    Toggle("Rotate receive addresses", isOn: $vault.rotationSettings.receiveRotationEnabled)
        .help("Generate new address for each deposit (recommended)")

    Text("Change addresses always rotate")
        .font(.caption)
        .foregroundColor(.secondary)
}
```

---

## 6. Transaction Builder Changes

### UTXO Selection

Must scan ALL derived addresses for UTXOs:

```typescript
async function getAllUTXOs(vault: Vault, chain: UTXOChain): Promise<UTXO[]> {
  const addresses: string[] = [];

  // Master address (for backwards compat)
  addresses.push(vault.masterAddress(chain));

  // All derived receive addresses up to nextIndex + gap
  const indices = vault.utxoIndices[chain.id];
  for (let i = 0; i < indices.nextReceiveIndex + GAP_LIMIT; i++) {
    addresses.push(vault.deriveAddress('receive', i, chain));
  }

  // All derived change addresses
  for (let i = 0; i < indices.nextChangeIndex + GAP_LIMIT; i++) {
    addresses.push(vault.deriveAddress('change', i, chain));
  }

  // Fetch UTXOs for all addresses
  return chain.client.getUTXOs(addresses);
}
```

### Keysign Payload

```typescript
interface UTXOKeysignPayload {
  // ... existing fields ...

  inputTweaks: Array<{
    inputIndex: number;
    tweak: Uint8Array;  // 32 bytes, or empty for master address
  }>;
}
```

### Multi-Input Signing

Each input may have different tweak:

```typescript
async function signUTXOTransaction(tx: UTXOTransaction, vault: Vault) {
  const signatures = [];

  for (let i = 0; i < tx.inputs.length; i++) {
    const input = tx.inputs[i];
    const tweak = tx.inputTweaks[i]?.tweak || new Uint8Array(); // empty = master

    const sighash = computeSighash(tx, i);
    const sig = await dkls23.signWithTweak(vault.share, sighash, tweak);
    signatures.push(sig);
  }

  return assembleTransaction(tx, signatures);
}
```

---

## 7. Blockchain Client Changes

### Required API Additions

```typescript
interface UTXOBlockchainClient {
  // Existing
  getUTXOs(address: string): Promise<UTXO[]>;
  broadcastTransaction(tx: string): Promise<string>;

  // New: batch operations for efficiency
  getUTXOsMulti(addresses: string[]): Promise<Map<string, UTXO[]>>;
  hasAddressHistory(address: string): Promise<boolean>;
  hasAddressHistoryMulti(addresses: string[]): Promise<Map<string, boolean>>;
}
```

### Blockchair/Mempool API Usage

```typescript
// Example: Mempool.space API
async function hasAddressHistory(address: string): Promise<boolean> {
  const response = await fetch(`https://mempool.space/api/address/${address}`);
  const data = await response.json();
  return data.chain_stats.tx_count > 0 || data.mempool_stats.tx_count > 0;
}

// Batch version for efficiency
async function hasAddressHistoryMulti(addresses: string[]): Promise<Map<string, boolean>> {
  // Mempool doesn't have batch API, so parallelize
  const results = await Promise.all(
    addresses.map(async (addr) => [addr, await hasAddressHistory(addr)])
  );
  return new Map(results);
}
```

---

## Implementation Phases

### Phase 1: Bitcoin Change Addresses (2-3 weeks)

| Task | Component | Effort |
|------|-----------|--------|
| DKLS23 tweak support | go-wrappers | 3d |
| Child derivation lib | vultisig-go | 2d |
| Scanner implementation | vultisig-go | 2d |
| Protobuf schema updates | commondata | 1d |
| TX builder changes | all apps | 3d |
| Keysign flow changes | all apps | 2d |
| Testing | all | 2d |

### Phase 2: Bitcoin Receive Rotation (1-2 weeks)

| Task | Component | Effort |
|------|-----------|--------|
| Receive screen changes | all apps | 2d |
| Settings toggle | all apps | 1d |
| Deposit monitoring | all apps | 2d |
| Testing | all | 2d |

### Phase 3: Other UTXO Chains (1 week per chain)

| Task | Component | Effort |
|------|-----------|--------|
| LTC address derivation | vultisig-go, sdk | 2d |
| LTC integration | all apps | 2d |
| DOGE integration | all apps | 2d |
| BCH integration | all apps | 2d |

---

## Testing Requirements

### Unit Tests

```go
// derivation_test.go
func TestDeriveChildPubkey(t *testing.T) {
    master := hexToBytes("02...")
    chaincode := DeriveChainCode(master)

    child0 := DeriveChildPubkey(master, chaincode, AddressTypeReceive, 0)
    child1 := DeriveChildPubkey(master, chaincode, AddressTypeReceive, 1)

    assert.NotEqual(t, child0, child1)
    assert.NotEqual(t, child0, master) // child != master
}

func TestTweakSigning(t *testing.T) {
    // Create test vault shares
    // Sign with tweak
    // Verify signature against derived pubkey
}
```

### Integration Tests

1. **Derive → Sign → Verify**: Full round trip with tweaked signing
2. **Multi-input**: Transaction spending from multiple derived addresses
3. **Scanner accuracy**: Compare discovered indices with known test data
4. **Cross-device sync**: Device A signs, Device B discovers via scan

### Testnet Deployment

1. Deploy to Bitcoin testnet first
2. Create test vault, generate multiple receive addresses
3. Fund addresses, spend with change rotation
4. Verify all addresses and signatures on-chain

---

## Rollout Plan

1. **Internal alpha**: Team testing on testnet
2. **Beta flag**: Feature flag for opt-in users (mainnet)
3. **Default for new vaults**: New vaults get rotation enabled by default
4. **Migration prompt**: Existing users prompted to enable

---

## References

- BIP32: HD Wallets
- BIP44: Multi-account hierarchy
- DKLS23: Threshold ECDSA
- Vultisig TSS architecture docs
