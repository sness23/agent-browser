# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

agent-browser is a headless browser automation CLI for AI agents. It uses a client-daemon architecture with a fast Rust CLI that communicates with a Node.js daemon running Playwright.

## Build and Development Commands

```bash
# Install dependencies
pnpm install

# Build TypeScript (src/ → dist/)
pnpm build

# Build native Rust CLI (requires Rust: https://rustup.rs)
pnpm build:native

# Run daemon in development mode with hot reload
pnpm dev

# Type checking
pnpm typecheck

# Format code
pnpm format

# Run tests
pnpm test

# Run tests in watch mode
pnpm test:watch

# Cross-platform builds (requires Docker for Linux/Windows)
pnpm build:linux
pnpm build:macos
pnpm build:windows
pnpm build:all-platforms
```

## Architecture

```
User CLI Command (Rust CLI in cli/)
        ↓
    commands.rs - Parses arguments
        ↓
    connection.rs - Connects to daemon via Unix socket or TCP
        ↓
    daemon.ts (src/) - Socket server, session management
        ↓
    protocol.ts - Deserializes command using Zod schemas
        ↓
    actions.ts - Routes to specific handler
        ↓
    browser.ts - Playwright API wrapper (BrowserManager class)
        ↓
    snapshot.ts - Accessibility tree generation with refs (@e1, @e2)
```

### Key Source Files

- **src/daemon.ts**: Socket-based daemon server, manages sessions
- **src/browser.ts**: BrowserManager class wrapping Playwright (launch, click, fill, navigate)
- **src/actions.ts**: Command execution handlers (~1,800 lines, 100+ action handlers)
- **src/protocol.ts**: Zod schemas for command/response serialization
- **src/snapshot.ts**: Accessibility tree generation with element refs
- **src/types.ts**: TypeScript interfaces for all command types
- **cli/src/main.rs**: Rust CLI entry point, daemon spawning
- **cli/src/commands.rs**: Rust command parsing
- **cli/src/output.rs**: Output formatting

### Session Management

- Sessions use Unix sockets at `/tmp/agent-browser-{session}.sock` (Linux/macOS)
- Windows uses TCP with port hash for isolation
- Each session has isolated browser instance, cookies, and storage
- Rust CLI auto-spawns Node.js daemon if not running

### Ref System

The snapshot command generates refs (@e1, @e2) for deterministic element selection. These refs are cached from the last snapshot and used by subsequent commands like `click @e1`.

## Code Style

- Do not use emojis in code, output, or documentation. Unicode symbols (✓, ✗, →, ⚠) are acceptable.
- TypeScript strict mode is enabled
- Prettier for formatting

## Testing

Tests are in `src/*.test.ts` files using Vitest:
- `src/protocol.test.ts`: Command parsing tests
- `src/browser.test.ts`: Browser interaction tests
- `test/serverless.test.ts`: Serverless Chromium compatibility

Run a specific test file:
```bash
pnpm test src/protocol.test.ts
```

## Source Code Reference

Source code for dependencies is available in `opensrc/` for deeper understanding of implementation details. See `opensrc/sources.json` for the list of available packages.

To fetch source code for a package:
```bash
npx opensrc <package>           # npm package (e.g., npx opensrc zod)
npx opensrc crates:<package>    # Rust crate (e.g., npx opensrc crates:serde)
npx opensrc <owner>/<repo>      # GitHub repo
```
