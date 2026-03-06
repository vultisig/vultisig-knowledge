# mcp

**URL:** github.com/vultisig/mcp
**Language:** Go
**Purpose:** MCP server exposing crypto operations to AI agents

---

## Overview

Model Context Protocol (MCP) server providing 34+ tools and 8 skills for AI agents to interact with 29+ blockchains. Supports address derivation, balance queries, transaction building, swap routing, ABI encoding, DeFi protocols (Aave V3), and prediction markets (Polymarket).

---

## Architecture

```
cmd/mcp-server/main.go          → Entry point, server setup
internal/
  vault/store.go                 → Per-session vault key storage (thread-safe, in-memory only)
  tools/                         → Tool implementations (1 file per tool)
    polymarket/                  → Polymarket prediction market tools (13 files)
  protocols/
    aavev3/tools.go              → Aave V3 DeFi protocol tools
    registry.go                  → Protocol registry
  skills/
    skills.go                    → Skill registration
    files/                       → Skill markdown docs (8 skills)
  evm/
    chains.go                    → EVM chain definitions (10 chains)
  ethereum/                      → EVM client wrapper
  blockchair/                    → UTXO chain API client
  coingecko/                     → Token discovery API
  polymarket/                    → Polymarket API client
```

Transport: stdio (default, for Claude Desktop) or HTTP (`-http :9091`)

---

## Tools (34+)

**Vault:** set_vault_info, get_address (29+ chains)
**Balances:** evm_get_balance, evm_get_token_balance, get_sol_balance, get_spl_token_balance, get_xrp_balance
**UTXO:** get_utxo_transactions, list_utxos (via utxo_chains.go)
**TX building:** build_evm_tx, build_btc_send, build_bch_send, build_ltc_send, build_doge_send, build_dash_send, build_zec_send, build_xrp_send, build_solana_tx, build_spl_transfer_tx, build_swap_tx, build_solana_swap
**EVM:** evm_tx_info, evm_call, evm_check_allowance, abi_encode, abi_decode
**Utility:** search_token, convert_amount, get_price, get_tx_status
**Fee rates:** btc_fee_rate, bch_fee_rate, ltc_fee_rate, doge_fee_rate, dash_fee_rate, maya_fee_rate
**Aave V3:** aave_v3_deposit, aave_v3_withdraw, aave_v3_borrow, aave_v3_repay, aave_v3_get_balances, aave_v3_get_rates
**Polymarket:** register, search, market, orderbook, price, positions, trades, open_orders, approvals, build_order, place_bet, submit_order, cancel_order

## Skills (8)

- evm-token-transfer.md — ERC-20 transfer walkthrough
- evm-contract-call.md — Contract interaction guide
- utxo-transfer.md — UTXO transaction building guide
- send-transfer.md — General send transfer guide
- swap-trading.md — Swap/trading workflow
- custom-transactions.md — Custom transaction building
- spark-savings.md — ERC-4626 vault guide
- polymarket-trading.md — Prediction market trading guide

---

## Supported EVM Chains (10)

Ethereum, BSC, Polygon, Avalanche, Arbitrum, Optimism, Base, Blast, Mantle, Zksync

## Other Supported Chains

Bitcoin, Bitcoin-Cash, Litecoin, Dogecoin, Dash, Zcash, Solana, XRP, MayaChain, THORChain, Cosmos, CronosChain, Dydx, Kujira, Noble, Osmosis, Sui, Terra, TerraClassic, Tron

---

## Related Repos

- `agent-backend` — Primary consumer (JSON-RPC 2.0 client)
- `vultisig-go` — Go chain implementations used by tools
- `recipes` — Shared tx parsing used by swap builder

---

_Updated: 2026-03-05_
