# Yoshi Player - AI Agent Instructions

**Project:** Desktop music player application built with Tauri (Rust) + SvelteKit (TypeScript/Svelte)  
**Version:** 0.1.0  
**Type:** Monorepo with frontend (Node.js/Vite) and backend (Rust/Tauri)

## Quick Start for Agents

### Run Commands

```bash
# Start development (both frontend Vite dev server + Tauri backend)
pnpm tauri dev        # Runs from repo root

# Frontend only (useful for UI work)
pnpm dev              # Starts Vite on port 1420

# TypeScript/Svelte checks
pnpm check            # Full type checking
pnpm check:watch      # Watch mode for development

# Build production app
pnpm tauri build      # Creates dist app in src-tauri/target/release/
```

## Project Structure

| Path | Purpose |
|------|---------|
| `src/` | SvelteKit frontend (SPA mode, no SSR) |
| `src/routes/+page.svelte` | Main app UI |
| `src/routes/+layout.ts` | Layout config (sets `ssr = false`) |
| `src-tauri/` | Rust/Tauri backend |
| `src-tauri/src/lib.rs` | Tauri app builder, command handlers |
| `src-tauri/src/main.rs` | Entry point (calls `lib::run()`) |
| `src-tauri/tauri.conf.json` | Tauri config (ports, window size, CSP) |
| `database/music.db` | SQLite database file |

## Frontend Stack

- **Framework:** SvelteKit 2.9.0 + Svelte 5.0.0
- **Build tool:** Vite 6.0.3
- **Language:** TypeScript 5.6.2
- **Styling:** CSS (no CSS framework configured)
- **Dev server port:** 1420 (HMR: 1421)
- **Mode:** SPA (Static Adapter with index.html fallback)
- **Tauri API:** `@tauri-apps/api` v2 for IPC calls

### Frontend Patterns

1. **Tauri Commands (IPC):** Invoke Rust functions from Svelte components
   ```typescript
   import { invoke } from "@tauri-apps/api/core";
   const result = await invoke("command_name", { param: value });
   ```

2. **Component Organization:** Place components in `src/lib/` or co-locate with routes

3. **TypeScript:** Strict mode enabled in `tsconfig.json`

## Backend Stack

- **Language:** Rust (stable)
- **Framework:** Tauri 2.x
- **Database:** SQLite with better-sqlite3 v12.11.1
- **ORM:** Drizzle ORM v0.45.2 + drizzle-kit v0.31.10

### Backend Patterns

1. **Tauri Commands:** Use `#[tauri::command]` macro
   ```rust
   #[tauri::command]
   fn command_name(param: String) -> String {
       // Return value is automatically serialized to JSON
   }
   ```

2. **Command Registration:** Add functions to `generate_handler![]` macro in `lib.rs`:
   ```rust
   tauri::Builder::default()
       .invoke_handler(tauri::generate_handler![command1, command2])
       .run(tauri::generate_context!())
   ```

3. **Plugins:** Currently using `tauri_plugin_opener` for opening URLs/files

## Database

- **Location:** `database/music.db` (SQLite)
- **ORM:** Drizzle ORM (see package.json for version)
- **Status:** No schema migrations found yet — define schema as needed
- **Driver:** better-sqlite3 (synchronous, single-threaded)

### Database Setup

When working with database:
- Use Drizzle ORM for type-safe queries
- Run `drizzle-kit` for migrations: `pnpm exec drizzle-kit generate:sqlite`
- Keep schema definition in a dedicated file (e.g., `src-tauri/src/schema.rs`)

## Development Workflow

### Adding a New Tauri Command

1. **Define in Rust** (`src-tauri/src/lib.rs` or separate module)
   ```rust
   #[tauri::command]
   fn my_command(input: String) -> Result<String, String> {
       Ok(format!("Processed: {}", input))
   }
   ```

2. **Register in builder** (update `generate_handler![]`)

3. **Call from Svelte** (`src/routes/+page.svelte`)
   ```typescript
   const result = await invoke("my_command", { input: "test" });
   ```

### Running Tests

- **Frontend:** Use Vitest (not yet configured; add if needed)
- **Backend:** Rust tests with `cargo test` (run from `src-tauri/`)

## Environment

- **Node version:** 22 (see `docker-compose.yml`)
- **Package manager:** pnpm (configured in `pnpm-workspace.yaml`)
- **Tauri dev requires:** Rust toolchain installed

## Build Configuration

- **Vite config:** `vite.config.js` (fixed port 1420)
- **Tauri config:** `src-tauri/tauri.conf.json`
  - Runs `pnpm dev` before dev server
  - Runs `pnpm build` before production build
  - Serves from `../build` (frontend dist)
  - Window: 800×600 default

## Common Pitfalls

1. **Port conflicts:** Vite runs on port 1420, HMR on 1421 — ensure not in use
2. **Database locking:** better-sqlite3 is single-threaded; handle concurrent access carefully
3. **CSP is disabled** (`"csp": null` in tauri.conf.json) — be aware for security-sensitive features
4. **SPA mode:** No server-side rendering; all routing happens client-side
5. **Rust rebuilds:** Changes to `src-tauri/src/` may require stopping/restarting `pnpm tauri dev`

## Next Steps for Agents

When starting work:
1. Identify whether task is frontend (SvelteKit) or backend (Rust/Tauri)
2. Check if new Tauri commands are needed for backend functionality
3. For UI: follow SvelteKit conventions, place in `src/routes/` or `src/lib/`
4. For Rust: add command with proper error handling, update `generate_handler![]`, register in IPC
5. Run `pnpm tauri dev` to test both frontend and backend together

## References

- [Tauri Docs](https://tauri.app)
- [SvelteKit Docs](https://kit.svelte.dev)
- [Drizzle ORM Docs](https://orm.drizzle.team)
