# agent-backend

**URL:** github.com/vultisig/agent-backend
**Language:** Go
**Purpose:** AI conversation agent powering the in-app chat across all Vultisig clients

---

## Overview

The "brain" of the agentic system. Takes natural language from users, calls Claude (via OpenRouter), executes tools, returns structured responses with actions for clients to execute locally (build TX, sign, install plugin, etc).

---

## Architecture

Two main layers: HTTP API (Echo) + AI processing loop.

```
cmd/
  server/main.go                → Main API server entry point
  scheduler/main.go             → Background task scheduler
internal/
  api/
    server.go                   → Routes, middleware
    middleware.go               → JWT auth (validated against Verifier, cached 3min)
    conversation.go             → Conversation CRUD
    message.go                  → Message processing + SSE streaming
    responses.go                → Response helpers
    starters.go                 → Conversation starters
    ratelimit.go                → Rate limiting
  service/agent/
    agent.go                    → Main loop: context → prompt → Claude → tool loop (max 8). 1,605 lines.
    executor.go                 → Tool execution dispatcher
    tools.go                    → Core tools
    prompt.go                   → System prompt assembly
    actions.go                  → Action definitions (build_send_tx, sign_tx, etc.)
    context.go                  → Conversation context management
    memory.go                   → Persistent user memory
    policy.go                   → Vault/policy authorization constraints
    interceptor.go              → Tool execution middleware
    headless.go                 → Headless (non-interactive) mode
    starters.go                 → Conversation starter logic
    types.go                    → Type definitions
  service/agent/scheduler/
    scheduler.go                → Background task execution
  mcp/client.go                 → MCP JSON-RPC 2.0 client (dynamic tool discovery)
  service/mcp/client.go         → MCP service client
  ai/client.go                  → OpenRouter API client
  service/verifier/client.go    → Verifier API client
```

---

## Key Concepts

- **Tool loop**: Claude calls tools → executor runs them → results fed back → repeat (max 8 rounds)
- **`respond_to_user` tool**: Special tool that returns structured actions for the client
- **Actions**: `build_send_tx`, `build_swap_tx`, `sign_tx`, `plugin_install`, `create_policy`, etc.
- **Context window**: Last 20 messages + auto-summarization after 30 messages
- **MCP integration**: Dynamically discovers tools from MCP server (cached 5min)
- **Memory**: Persistent user memories stored in PostgreSQL
- **Policy enforcement**: Vault-level policy constraints checked before tool execution
- **Headless mode**: Non-interactive agent mode for background/scheduled tasks
- **Scheduler**: Background task execution (separate binary)

---

## Dependencies

- PostgreSQL (conversations, messages, memory)
- Redis (auth cache, rate limits)
- OpenRouter (Claude LLM)
- Verifier service (auth validation)
- MCP server (crypto tools, optional)

---

## Related Repos

- `mcp` — MCP server providing 34+ crypto tools + 8 skills
- `verifier` — Auth validation + plugin discovery
- `vultisig-windows` — Reference client implementation (97 files, 35 tool handlers in `core/ui/agent/`)

---

_Updated: 2026-03-05_
