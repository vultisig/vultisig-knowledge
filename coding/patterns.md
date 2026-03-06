# Coding Patterns

_Cross-repo conventions and patterns used across the Vultisig codebase._

---

## Naming Conventions

### Go Repos

| Element | Convention | Example |
|---------|-----------|---------|
| Package | lowercase, single word | `vault`, `relay`, `tools` |
| Exported types | PascalCase | `VaultStore`, `KeyShare` |
| Unexported | camelCase | `sessionKey`, `handleSign` |
| Files | snake_case | `vault_store.go`, `build_swap_tx.go` |
| Test files | `_test.go` suffix | `vault_store_test.go` |
| Errors | `Err` prefix variable | `ErrVaultNotFound` |
| Interfaces | `-er` suffix or descriptive | `Signer`, `VaultStore` |

### TypeScript Repos (windows, sdk, website-prod)

| Element | Convention | Example |
|---------|-----------|---------|
| Files (components) | PascalCase | `VaultCard.tsx` |
| Files (utils) | camelCase | `formatAmount.ts` |
| Types | PascalCase, `type` keyword | `type VaultInfo = {...}` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| Enums | `as const` objects | `const Chain = { ... } as const` |
| Hooks | `use` prefix | `useVaultBalance` |

### Swift (iOS)

| Element | Convention | Example |
|---------|-----------|---------|
| Types/Protocols | PascalCase | `VaultViewModel`, `Signable` |
| Properties/Methods | camelCase | `vaultShares`, `startKeygen()` |
| Files | Match primary type | `VaultViewModel.swift` |

### Kotlin (Android)

| Element | Convention | Example |
|---------|-----------|---------|
| Classes | PascalCase | `VaultRepository` |
| Functions/Properties | camelCase | `getVaultBalance()` |
| Constants | UPPER_SNAKE_CASE in companion | `companion object { const val MAX_SHARES = 3 }` |

---

## Error Handling

### Go — Wrap with context

```go
// Always wrap errors with what you were doing
result, err := s.vault.GetShares(ctx, vaultID)
if err != nil {
    return fmt.Errorf("getting shares for vault %s: %w", vaultID, err)
}
```

Sentinel errors for expected cases:
```go
var ErrVaultNotFound = errors.New("vault not found")

// Callers check with errors.Is()
if errors.Is(err, ErrVaultNotFound) { ... }
```

### TypeScript — Typed errors at boundaries

```typescript
// Validate external data at boundaries with Zod
const VaultSchema = z.object({
  id: z.string().uuid(),
  shares: z.array(z.string()).min(1),
});

// Internal code can trust validated data
```

### Swift — guard let, no force unwrap

```swift
guard let share = vault.shares.first else {
    throw VaultError.noShares
}
// Never: vault.shares.first!
```

### Kotlin — Sealed classes for state

```kotlin
sealed class VaultState {
    object Loading : VaultState()
    data class Ready(val vault: Vault) : VaultState()
    data class Error(val message: String) : VaultState()
}
```

---

## Architecture Patterns

### MVVM (iOS, Android, Windows)

All client apps use MVVM:
- **Views** — UI only, observe ViewModel state
- **ViewModels** — Business logic, expose state
- **Models** — Data structures
- **Services** — API calls, crypto operations

State flows one direction: Service → ViewModel → View.

### Domain Layers (vultisig-windows)

```
lib/       → Pure utilities (no Vultisig deps, reusable anywhere)
core/      → Vultisig business logic (chain, mpc, config)
clients/   → Platform-specific (desktop, extension)
```

Import rule: `clients/` → `core/` → `lib/`. Never the reverse.

### Repository Pattern (Android, agent-backend)

```
UI → ViewModel → Repository → DataSource (API / DB / Cache)
```

Repository abstracts where data comes from.

### Tool Registration (MCP)

One file per tool in `internal/tools/`. Register via:
```go
server.AddTool(mcp.NewTool("get_balance", ...), handler)
```

### Echo HTTP Pattern (Go services)

```go
// Standard route setup
e := echo.New()
e.Use(middleware.Logger())
e.Use(middleware.Recover())
api := e.Group("/api/v1")
api.Use(authMiddleware)
api.GET("/vaults/:id", h.GetVault)
```

---

## State Management

### React (windows, sdk, marketplace)

- **Server state**: TanStack React Query (caching, refetching)
- **Local state**: React useState/useReducer
- **No global state store** — React Query handles it

### SwiftUI (iOS)

- `@Observable` macro on ViewModels (iOS 17+)
- `@MainActor` for all ViewModels
- `@Environment` for dependency injection

### Compose (Android)

- `StateFlow` in ViewModels
- `collectAsState()` in Composables
- `viewModelScope` for coroutines

---

## Testing Patterns

### Go — Table-driven tests

```go
func TestGetAddress(t *testing.T) {
    tests := []struct {
        name    string
        chain   Chain
        pubKey  string
        want    string
        wantErr bool
    }{
        {"ethereum", ChainETH, "0x...", "0x...", false},
        {"bitcoin", ChainBTC, "0x...", "bc1...", false},
        {"invalid", ChainUnknown, "", "", true},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := GetAddress(tt.chain, tt.pubKey)
            if tt.wantErr { require.Error(t, err); return }
            require.Equal(t, tt.want, got)
        })
    }
}
```

Always run with `-race` flag.

### TypeScript — Vitest

```typescript
describe("Vultisig", () => {
  it("derives address for ETH", () => {
    const address = deriveAddress(Chain.ETH, pubKey);
    expect(address).toMatch(/^0x[a-fA-F0-9]{40}$/);
  });
});
```

### Swift — XCTest / Swift Testing

```swift
func testVaultCreation() async throws {
    let vault = try await VaultService.create(name: "Test")
    XCTAssertNotNil(vault.id)
    XCTAssertEqual(vault.shares.count, 2)
}
```

---

## Commit Conventions

All repos use conventional commits:

```
fix: correct balance display for UTXO chains (#123)
feat: add Aave V3 supply tool (#456)
refactor: extract vault store to separate module
docs: update MCP tool documentation
test: add table-driven tests for address derivation
chore: bump dependencies
```

Format: `type: description (#issue)` — lowercase, imperative mood, reference issue number.

---

## Dependency Management

| Ecosystem | Tool | Lock File |
|-----------|------|-----------|
| Go | go mod | go.sum |
| TypeScript | Yarn | yarn.lock |
| Swift | SPM | Package.resolved |
| Kotlin | Gradle | gradle.lockfile |
| Protobuf | buf | buf.lock |

### Submodule Pattern

`commondata` is embedded as a git submodule in iOS, Android repos:
```bash
git submodule update --init --recursive  # After clone
git submodule update --remote            # Pull latest
```

### Upstream Sync (SDK)

`packages/core/` and `packages/lib/` in vultisig-sdk are synced from vultisig-windows:
```bash
yarn sync-and-copy  # Pull latest from windows repo
```
Never edit these directories directly in the SDK repo.

---

_Updated: 2026-03-04_
