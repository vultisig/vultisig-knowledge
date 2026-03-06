# Gotchas

_Known pitfalls, traps, and things that will waste your time if you don't know about them._

---

## Cross-Repo

### commondata changes break everything

Changing a `.proto` file in commondata affects iOS, Android, Windows, and all Go services. You must:
1. Edit proto in `proto/vultisig/`
2. Run `buf generate`
3. Commit generated code alongside proto changes
4. Update submodule refs in iOS and Android repos
5. Verify builds on all affected platforms

**Proto field removal is a breaking change.** Deprecate fields instead (reserve the number).

### vultisig-sdk core/ and lib/ are read-only

`packages/core/` and `packages/lib/` are mirrors of vultisig-windows code. If you edit them directly, your changes will be overwritten on the next `yarn sync-and-copy`. All SDK work goes in `packages/sdk/src/`.

### vultisig-windows is upstream for agent chat

The agent chat implementation in `core/ui/agent/` (97 files, 35 tool handlers) is the reference. iOS and Android will mirror this. Changes here need to be coordinated — you're defining the pattern everyone else follows.

---

## Go Services

### Context is always first param

```go
// Correct
func (s *Service) GetVault(ctx context.Context, id string) (*Vault, error)

// Wrong — will fail code review
func (s *Service) GetVault(id string, ctx context.Context) (*Vault, error)
```

### agent.go is 1,605 lines

The main processing loop in agent-backend (`internal/service/agent/agent.go`) is a monolith. Be very careful editing it — small changes can affect the entire tool execution flow. The max 8 iteration limit is intentional.

### MCP vault store is per-session

The vault store in the MCP server holds keys **in memory only, per session**. Keys are never persisted. If you're debugging "vault not found" errors, the session likely expired or reconnected.

### Redis is required for relay and vultiserver

The relay server and vultiserver both need Redis running. If tests fail with connection errors, check Redis first.

### go-wrappers has CGo dependencies

Building go-wrappers requires C compiler and the DKLS/Schnorr C libraries. CI uses Docker for reproducible builds. Local builds may fail if you don't have the right C toolchain.

---

## TypeScript

### No barrel files in applications

vultisig-windows and the SDK don't use barrel files (`index.ts` re-exports) in application code. Import directly from the source file.

### Yarn workspaces resolution

Both windows and SDK are Yarn workspace monorepos. If you add a dependency, add it to the right workspace:
```bash
yarn workspace @vultisig/desktop add some-package  # Not just `yarn add`
```

### WalletCore dependency needs GitHub auth (Android)

The Android app pulls Trust Wallet's WalletCore from GitHub Packages. Without `GITHUB_USER` + `GITHUB_TOKEN` in `~/.gradle/gradle.properties`, the build will fail with a cryptic 401 error.

---

## Crypto

### Never log key material

Across all repos: never log vault shares, key shares, private keys, or mnemonics. Not even in debug mode. This is a fireable offense.

### TSS keygen requires all participants online

TSS ceremony (keygen or keysign) requires all designated parties to be online and connected via the relay simultaneously. If one drops, the ceremony fails and must restart.

### ABI encoding errors = lost funds

In the MCP server, incorrect ABI encoding when building swap transactions can send funds to wrong addresses or lock them permanently. Always test with known-good inputs.

### DKLS23 vs GG20

The codebase is transitioning from GG20 to DKLS23. DKLS23 is faster (3 rounds vs 6) and supports key refresh. vultiserver still uses GG20 for some flows. Don't assume which protocol a given code path uses — check.

### Post-quantum (ML-DSA) is experimental

ml-dsa-tss is cutting-edge and not yet in production. It has WASM bindings for browser use but the API is not stable.

---

## iOS Specific

### No force unwraps in crypto paths

`vault.shares.first!` in a crypto code path = crash in production = potential loss of signing capability. Always use `guard let` or `if let`.

### Simulator vs Device for TSS

TSS operations work differently on simulator vs physical device. Always test signing flows on a real device before submitting.

### commondata is a submodule

Don't edit files under the `commondata/` directory in the iOS repo. Changes go in the commondata repo, then you update the submodule pointer.

---

## Android Specific

### Avoid `!!` operator

Kotlin's `!!` (force unwrap) is banned in crypto paths. Use `?.let`, `?:`, or `requireNotNull()` with a meaningful message.

### Fastlane manages releases

The `fastlane/` directory contains automated release configs. Changes affect the release pipeline — don't touch unless you know what you're doing.

---

## Infrastructure

### vultiserver has two components

API server and TSS worker are separate binaries. The API server handles HTTP requests, the worker handles TSS ceremonies. They communicate via Redis. If signing seems stuck, check both.

### Verifier has two binaries too

Same pattern: API server + worker. The worker does the actual TSS operations. Both need PostgreSQL, Redis, and S3 access.

### Recipes use magic constants

The recipes engine uses `MAGIC_CONSTANT` constraint type with values like `VULTISIG_TREASURY` and `THORCHAIN_VAULT`. These resolve to real addresses at runtime. If you add a new magic constant, register it in the resolver.

### app-recurring has four binaries

Server (API), scheduler (cron), worker (signing), tx_indexer (monitoring). All four must be running for the full DCA flow. Missing any one = silent failures.

---

## Build & CI

### Always run `yarn check:all` (TypeScript repos)

This runs lint + typecheck + test + knip (dead code detection). Don't skip knip — it catches unused exports that accumulate fast.

### Always run `go test -race` (Go repos)

The `-race` flag detects data races. Several Go services (relay, vultiserver, verifier) are concurrent — race conditions are real bugs, not theoretical.

### buf is required for commondata

Without the `buf` CLI installed, you can't validate or generate protobuf code. Install from https://buf.build.

---

_Updated: 2026-03-04_
