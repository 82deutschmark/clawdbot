# Project knowledge

This file gives Codebuff context about your project: goals, commands, conventions, and gotchas.

---

## 🆕 Beginner's Guide (If You're New to Development)

This section explains the basics for non-developers working with this project.

### What is a "Shell" or "Terminal"?

A **shell** (or **terminal**) is a text-based way to interact with your computer. Instead of clicking icons, you type commands. Think of it like texting your computer instructions.

- **On Windows:** You're likely using **WSL2** (Windows Subsystem for Linux) — this runs a Linux environment inside Windows
- **On Mac:** Use the built-in **Terminal** app
- **On Linux:** Use any terminal emulator

### What is WSL2 (Windows Subsystem for Linux)?

WSL2 lets you run Linux inside Windows. This project recommends it because:
- Many development tools work better on Linux
- The commands in this project are written for Linux/Mac
- It's like having a Linux computer inside your Windows computer

**To open WSL2:** Search for "Ubuntu" or "WSL" in your Windows start menu.

### Common Terms Explained

| Term | What it means |
|------|---------------|
| **pnpm** | A package manager that downloads and manages code libraries (like an app store for code) |
| **Node.js** | The program that runs JavaScript/TypeScript code on your computer |
| **TypeScript** | A programming language (like JavaScript but with extra safety features) |
| **CLI** | "Command Line Interface" — a program you control by typing commands |
| **Git** | A tool that tracks changes to code (like "Track Changes" in Word, but for code) |
| **Repository/Repo** | A folder containing a project and its history of changes |

### Basic Commands You'll Use

```bash
# Navigate to a folder
cd /path/to/folder

# Go up one folder
cd ..

# List files in current folder
ls

# Install project dependencies (run this first!)
pnpm install

# Run the main program
pnpm clawdbot

# Run tests to make sure things work
pnpm test
```

### File Paths on WSL2

In WSL2, your Windows files are accessible at `/mnt/c/` (for C: drive):
- Windows: `C:\Projects\clawdbot`
- WSL2: `/mnt/c/Projects/clawdbot`

### Getting Help

- **If a command fails:** Read the error message — it often tells you what's wrong
- **If you're stuck:** Ask in the [Discord](https://discord.gg/clawd)
- **Clawdbot help:** Run `pnpm clawdbot --help` to see available commands

---

## Overview

**Clawdbot** is a personal AI assistant that runs on your own devices. It answers you on messaging channels (WhatsApp, Telegram, Slack, Discord, Google Chat, Signal, iMessage, Microsoft Teams, Matrix, WebChat) and includes voice/speech features on macOS/iOS/Android.

## Quickstart

- **Setup:** `pnpm install` (or `bun install`)
- **Dev:** `pnpm clawdbot ...` or `pnpm dev` (runs via tsx)
- **Build:** `pnpm build` (tsc + asset copy)
- **Test:** `pnpm test` (vitest parallel), `pnpm test:coverage`
- **Lint:** `pnpm lint` (oxlint), `pnpm format` (oxfmt)
- **Gateway dev:** `pnpm gateway:watch` (auto-reload on changes)
- **TUI:** `pnpm tui`

## Architecture

- **Key directories:**
  - `src/` — main source (CLI in `src/cli`, commands in `src/commands`, infra in `src/infra`, media in `src/media`)
  - `src/channels/` — channel routing logic
  - `src/telegram/`, `src/discord/`, `src/slack/`, `src/signal/`, `src/imessage/`, `src/web/` — channel implementations
  - `extensions/` — plugin workspace packages (msteams, matrix, zalo, etc.)
  - `docs/` — Mintlify documentation
  - `test/` — test helpers and mocks
  - `ui/` — control UI (Vite + Lit)
  - `apps/` — native apps (macos, ios, android)
  - `Swabble/` — Swift package
  - `scripts/` — build/dev scripts

- **Data flow:** Gateway (WebSocket control plane) → Pi agent (RPC) → Channels (WhatsApp/Telegram/etc.)

## Conventions

- **Runtime:** Node ≥22 required
- **Package manager:** pnpm (keep `pnpm-lock.yaml` in sync); Bun also supported for TS execution
- **Language:** TypeScript (ESM), strict typing, avoid `any`
- **Formatting/linting:** Oxlint + Oxfmt; run `pnpm lint` before commits
- **File size:** Aim for ~700 LOC max; split/refactor when helpful
- **Naming:** "Clawdbot" for product/docs headings; "clawdbot" for CLI/package/paths
- **Tests:** Vitest with 70% coverage thresholds; colocated `*.test.ts` files
- **Comments:** Add brief comments for non-obvious logic

## Patterns to follow

- Use existing patterns for CLI options and dependency injection via `createDefaultDeps`
- Reuse helpers from `src/cli/progress.ts` for CLI progress (osc-progress + @clack/prompts)
- Use `src/terminal/table.ts` for status output with ANSI-safe wrapping
- Use `src/terminal/palette.ts` for colors (no hardcoded colors)
- Tool schemas: avoid `Type.Union`, use `stringEnum`/`optionalStringEnum` for string lists

## Things to avoid

- Don't use `any` type
- Don't commit real phone numbers, videos, or live config values
- Don't edit `node_modules`
- Don't update the Carbon dependency
- Don't patch dependencies without explicit approval
- Don't use `format` as a property name in tool schemas (reserved keyword)
- Never send streaming/partial replies to external messaging surfaces

## Testing

- `pnpm test` — parallel unit tests
- `pnpm test:coverage` — with coverage report
- `pnpm test:e2e` — end-to-end tests
- `pnpm test:live` — live tests with real API keys (requires `CLAWDBOT_LIVE_TEST=1`)
- `pnpm test:docker:all` — full Docker test suite

## Config & Credentials

- Config: `~/.clawdbot/clawdbot.json`
- Credentials: `~/.clawdbot/credentials/`
- Sessions: `~/.clawdbot/sessions/`
- Agent workspace: `~/clawd` (default)

## Useful scripts

- `pnpm clawdbot onboard` — interactive setup wizard
- `pnpm clawdbot doctor` — diagnose config/service issues
- `pnpm clawdbot channels status --probe` — check channel connections
- `scripts/committer "<msg>" <files...>` — create scoped commits
