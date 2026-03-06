# vultisig-android

**URL:** github.com/vultisig/vultisig-android
**Platform:** Android
**Language:** Kotlin (99.8%)

---

## Overview

Android wallet app implementing TSS keygen/keysign via DKLS23. Jetpack Compose UI with MVVM + Repository pattern.

---

## Folder Structure

```
vultisig-android/
├── app/src/main/java/com/vultisig/wallet/
│   ├── app/              → Application class, Activities, Startup
│   └── ui/
│       ├── screens/      → Compose screens (organized by feature: keygen, keysign, send, etc.)
│       ├── components/   → Reusable UI components
│       ├── models/       → ViewModels (co-located with screens, not separate folder)
│       ├── navigation/   → Navigation logic
│       └── theme/        → UI theming
├── data/src/main/kotlin/com/vultisig/wallet/data/
│   ├── repositories/    → Data access layer (organized with subfolders)
│   ├── di/              → Hilt dependency injection modules
│   ├── models/          → Data models
│   └── ...              → API clients, mappers, etc.
├── commondata/          → Shared protobuf schemas (submodule, do not edit)
├── fastlane/            → Automated deployment
├── config/lint/         → Code quality rules
└── .github/             → CI/CD workflows
```

**Note:** ViewModels are in `app/.../ui/models/`, NOT a dedicated `viewmodel/` folder. Repositories are in `data/` module (Kotlin), not `app/`.

---

## Key Entry Points

| File/Folder | Purpose |
|-------------|---------|
| `app/` | Main application module |
| `app/.../ui/models/` | ViewModels with StateFlow (co-located with screens) |
| `data/.../repositories/` | Data access layer (Kotlin module) |
| `app/.../` + JNI AARs | TSS bindings via JNI (dkls-release.aar, mobile-tss-lib.aar) — do not modify without review |
| `data/` | Separate Kotlin module for data layer, repositories, DI |
| `commondata/` | Shared protobuf schemas (submodule, do not edit) |

---

## Key Commands

```bash
./gradlew assembleDebug       # Build
./gradlew testDebugUnitTest   # Test
./gradlew lint                # Android lint
./gradlew ktlintCheck         # Kotlin style
./gradlew ktlintFormat        # Auto-format
```

---

## Key Flows

- **Vault creation**: UI → ViewModel → Repository → Crypto (JNI) → Relay
- **Transaction signing**: ViewModel builds TX → keysign via TSS → broadcast
- **Agent chat**: Not yet implemented (reference impl in vultisig-windows)

---

## Dependencies

- **Trust Wallet WalletCore** — Chain support (requires GitHub credentials)
- **commondata** — Protobuf schemas (git submodule)
- **Jetpack Compose** — UI framework
- **Hilt** — Dependency injection (@HiltViewModel on ViewModels)
- **Ktor** — HTTP client
- **Room** — Local database

---

## Build Requirements

GitHub credentials needed for WalletCore:
```
GITHUB_USER=<username>
GITHUB_TOKEN=<token>
```
Set in `~/.gradle/gradle.properties` or environment.

---

## Notes

- Kotlin 99.8%, modern Android best practices
- Fastlane for automated builds/releases
- Sealed classes for UI state (Loading, Success, Error)
- Coroutines + `viewModelScope` for async, avoid `!!` operator

---

_Updated: 2026-03-05_
