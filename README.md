# vultisig-knowledge

Shared knowledge base for developers and AI agents working on Vultisig repos.

## Quick Start

See **[INDEX.md](./INDEX.md)** for full navigation.

### For Agents

If you're an AI agent working on a Vultisig repo:

1. Read `repos/<your-repo>.md` for repo overview
2. Read `coding/gotchas.md` to avoid known pitfalls
3. If touching crypto/TSS code, read `architecture/mpc-tss-explained.md`
4. If working cross-platform, read `repos/index.md` for dependency graph

### Structure

```
architecture/    — System architecture (MPC/TSS, signing, vaults, relay, plugins)
repos/           — Per-repo docs (all 24 repos, organized by security tier)
coding/          — Cross-repo patterns, gotchas, dependency matrix
support/         — Common issues, emergency recovery
```

## Contributing

When making changes to a Vultisig repo that affect its structure, dependencies, or architecture:
1. Update the relevant doc in this repo
2. Open a PR with the change
