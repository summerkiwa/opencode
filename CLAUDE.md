# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build & Development Commands

```bash
# Development (runs OpenCode CLI in packages/opencode by default)
bun dev                           # Run against packages/opencode
bun dev <directory>               # Run against a specific directory
bun dev .                         # Run against repo root

# Type checking (across all packages)
bun typecheck

# Build standalone executable
./packages/opencode/script/build.ts --single    # Current platform only
./packages/opencode/script/build.ts             # All platforms

# Run the built executable
./packages/opencode/dist/opencode-<platform>/bin/opencode
```

### Package-specific commands

```bash
# Web app (packages/app)
bun run --cwd packages/app dev              # Dev server at http://localhost:5173
bun run --cwd packages/app test             # Playwright E2E tests
bun run --cwd packages/app test:e2e:ui      # E2E tests with interactive UI

# Desktop app (packages/desktop) - requires Rust toolchain
bun run --cwd packages/desktop tauri dev    # Full native dev (http://localhost:1420)
bun run --cwd packages/desktop dev          # Web dev server only

# Unit tests
bun test --cwd packages/opencode            # Core package tests

# Regenerate SDK after API changes
./packages/sdk/js/script/build.ts
```

## Architecture

OpenCode is an open-source AI coding agent with a client-server architecture. The server runs locally and can be driven by multiple clients (TUI, web, desktop).

### Key Packages

| Package | Purpose |
|---------|---------|
| `packages/opencode` | Core CLI, server, business logic. Entry point for the application |
| `packages/app` | Shared web UI components (SolidJS) |
| `packages/desktop` | Tauri native desktop wrapper |
| `packages/ui` | Component library, themes, icons, styles |
| `packages/sdk/js` | TypeScript SDK for OpenCode server |
| `packages/plugin` | `@opencode-ai/plugin` SDK for building plugins |

### Core Structure (packages/opencode/src/)

- `index.ts` - CLI entry point (yargs-based)
- `cli/cmd/tui/` - Terminal UI (SolidJS + @opentui)
- `server/` - Hono-based HTTP server
- `agent/` - Agent logic and implementation
- `provider/` - LLM provider integrations (Anthropic, OpenAI, Google, etc.)
- `mcp/` - Model Context Protocol support
- `lsp/` - Language Server Protocol support
- `session/` - Session management
- `permission/` - Permission/access control

### Built-in Agents

- **build** - Default full-access agent for development
- **plan** - Read-only agent for analysis (denies edits, asks permission for bash)
- **general** - Subagent for complex searches (invoke with `@general`)

## Code Style

- Use `const` over `let`
- Avoid `else` statements (use early returns)
- Avoid unnecessary destructuring (use `obj.a` instead of `const { a } = obj`)
- Prefer single-word variable names
- Use `.catch()` over `try`/`catch` when possible
- Avoid `any` types
- Use Bun APIs (e.g., `Bun.file()`)

## Important Notes

- Default branch is `dev`
- Do not run `bun test` from root (disabled intentionally)
- After API/SDK changes, run `./script/generate.ts` to regenerate
- Desktop app requires Rust toolchain (see Tauri prerequisites)
- Use parallel tool calls when applicable
