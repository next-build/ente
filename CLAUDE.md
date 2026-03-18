# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Ente is a fully open-source, end-to-end encrypted platform. This monorepo contains all client apps (mobile, web, desktop, CLI) and the server (Museum). Each major component has its own CLAUDE.md with component-specific guidance — defer to those when working within a specific area.

## Architecture

**Rust is foundational.** The `rust/core/` crate (ente-core) contains shared crypto and auth logic with no FFI. It is consumed by three binding layers:
- **Web**: `web/packages/wasm/` via `wasm-pack` (`#[wasm_bindgen]`)
- **Mobile**: `mobile/packages/rust/` and `mobile/apps/photos/rust/` via Flutter Rust Bridge (`#[frb]`)
- **CLI**: `rust/cli/` as a native binary

Changes to `rust/core/` affect web, mobile, and CLI builds.

**Server (Museum)** is a standalone Go service that all clients connect to. It is data-agnostic (all data is E2E encrypted) and handles auth, billing, replication, and storage proxying.

## Component Quick Reference

| Component | Path | Stack | Dev Command |
|-----------|------|-------|-------------|
| Web (Photos) | `web/` | Next.js, React 19, TypeScript, MUI | `yarn dev` (port 3000) |
| Web (Auth) | `web/` | Same | `yarn dev:auth` (port 3003) |
| Server | `server/` | Go 1.23, PostgreSQL 15, MinIO | `docker compose up --build` |
| Mobile | `mobile/` | Flutter 3.32.8, Dart, Rust (FRB) | `flutter run` |
| Desktop | `desktop/` | Electron 41, Next.js renderer | `yarn dev` |
| CLI | `cli/` | Go 1.23 | `go build -o bin/ente main.go` |
| Rust core | `rust/core/` | Pure Rust | `cargo test` |
| Docs | `docs/` | VitePress | `yarn dev` |

## Web Development

```bash
cd web
yarn install                    # Yarn Classic 1.22.22
yarn dev                        # Photos app (builds WASM first, then port 3000)
yarn workspace photos next dev -p 3000  # Skip WASM rebuild if already built
yarn dev:auth                   # Auth app (port 3003)
yarn dev:accounts               # Accounts (port 3001)
yarn dev:cast                   # Cast (port 3004)
yarn dev:embed                  # Embed (port 3006)
yarn lint                       # Prettier + ESLint + TypeScript check
yarn lint-fix                   # Auto-fix lint issues
```

Prerequisites: Node.js (^20.19.0 or >=22.12.0), Rust toolchain, `wasm-pack`.

The web directory uses Yarn workspaces. Apps are in `web/apps/`, shared code in `web/packages/`. WASM output (`web/packages/wasm/pkg/`) is gitignored and must be built at least once.

## Server Development

```bash
cd server
docker compose up --build       # Starts Museum + PostgreSQL + MinIO
curl http://localhost:8080/ping # Verify with "pong"

# Native Go (alternative)
go run cmd/museum/main.go

# Tests
ENV="test" go test -v ./pkg/...
```

Config: `server/configurations/local.yaml`. Override with `museum.yaml` (gitignored).

## Mobile Development

```bash
cd mobile
melos bootstrap                 # Link packages and run pub get
cd apps/photos                  # Or apps/auth, apps/locker
flutter run

# Pre-commit (mandatory — CI enforces zero issues)
dart format .
flutter analyze
```

Prerequisites: Flutter 3.32.8, Rust 1.90.0+, `flutter_rust_bridge_codegen`, git submodules (`git submodule update --init --recursive`).

## Desktop Development

```bash
cd desktop
yarn install
yarn dev                        # Concurrent Electron main + renderer
yarn build                      # Production build
yarn lint                       # Prettier + ESLint + TypeScript check
```

## Rust Core

```bash
cd rust/core
cargo fmt                       # Format
cargo clippy                    # Lint
cargo test                      # Test
cargo build                     # Build
```

## Commit Message Guidelines

**Commit conventions vary by component** — check the component's own CLAUDE.md. General rules:
- Subject line under 72 chars, single sentence
- No emojis, no promotional text
- **Web/Desktop**: No Co-Authored-By line, no body text
- **Mobile/Docs**: Use `Co-Authored-By: Claude <noreply@anthropic.com>`

## CI Checks

CI triggers are scoped by path. Key checks:
- **Web**: `yarn lint` (triggered by `web/**` or `rust/core/**` changes)
- **Mobile**: `dart format .`, `flutter analyze`, `cargo fmt`, `cargo clippy`, `cargo test` (triggered by `mobile/**` or `rust/core/**`)
- **Desktop**: lint via Dockerfile context

All linting must pass with zero issues before committing.

## Cross-Cutting Concerns

- **Git hooks**: Enable with `git config core.hooksPath hooks`
- **Git submodules**: Required for mobile — `git submodule update --init --recursive`
- **Docker builds**: Web and Desktop Dockerfiles build from repo root (need both `web/` and `rust/`)
- **Environment config**: Web apps connect to production by default. Set `NEXT_PUBLIC_ENTE_ENDPOINT` to point to a local server.
