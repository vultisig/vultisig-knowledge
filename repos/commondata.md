# commondata

**URL:** github.com/vultisig/commondata
**Language:** Protobuf (source), Swift + Go (generated)
**Security Tier:** HIGH
**Purpose:** Shared data contract for all Vultisig apps

---

## Overview

Source of truth for cross-platform communication schemas. Protobuf definitions generate Swift and Go code used by iOS, Android, Windows, and all Go services. Breaking changes here affect the entire ecosystem.

---

## Folder Structure

```
commondata/
├── proto/vultisig/           → .proto definitions (SOURCE OF TRUTH)
├── Sources/swift/vultisig/   → Generated Swift code (do not edit)
├── go/vultisig/              → Generated Go code (do not edit)
├── tokens/                   → Token metadata
├── buf.yaml                  → Buf linting configuration
├── buf.gen.yaml              → Code generation config
├── Package.swift             → Swift package manifest
└── go.mod                    → Go module definition
```

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `proto/vultisig/` | Source of truth — all changes start here |
| `Sources/swift/` | Generated Swift code — never edit manually |
| `go/vultisig/` | Generated Go code — never edit manually |
| `tokens/` | Token metadata (affects balance display) |

---

## Key Commands

```bash
buf format --write                              # Format protos
buf lint && buf build                           # Validate
buf generate                                    # Generate Swift/Go code
buf format --write && buf lint && buf build && buf generate  # Full workflow
```

---

## Update Workflow

1. Edit `.proto` files in `proto/vultisig/`
2. Run `buf format --write && buf lint && buf build`
3. Run `buf generate`
4. Commit generated code alongside proto changes
5. Update submodule refs in iOS, Android repos

---

## Proto Conventions

- Proto3 syntax
- Package: `vultisig.v1`
- Field naming: snake_case
- Enums: UPPER_SNAKE_CASE with 0 = UNSPECIFIED
- **Field removal is a breaking change** — deprecate instead (reserve the number)

---

## Dependencies

- **buf** — Required for all operations (https://buf.build)
- Used as git submodule in: vultisig-ios, vultisig-android

---

_Updated: 2026-03-04_
