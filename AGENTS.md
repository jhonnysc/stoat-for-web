# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Stoat for Web: the official web client for Stoat (formerly Revolt), built with Solid.js, Vite, Panda CSS, and Lingui. pnpm workspace with three packages:

- `packages/client` — the app. All real work happens here.
- `packages/stoat.js` — git submodule, the JS client SDK (workspace dep `stoat.js`).
- `packages/solid-livekit-components` — git submodule, voice/video components.

Both submodules must be populated and built before the client compiles. In a fresh clone or worktree run `git submodule update --init` first (the `packages/client/assets` submodule is private brand assets with `update = none`; `scripts/copyAssets.mjs` falls back to `scripts/assets_fallback` when it is empty).

Tooling is driven by `mise` (tasks live in `.mise/tasks/`, tool versions in `.mise/config.toml`). Every mise task is a thin wrapper around `pnpm --filter client exec ...`, so the equivalent pnpm command is listed below for environments without mise.

## Commands

Setup:

```bash
mise install:frozen        # pnpm install --frozen-lockfile
mise build:deps            # builds stoat.js + solid-livekit-components, then lingui compile
cp packages/client/.env.example packages/client/.env
```

Develop:

```bash
mise dev                   # pnpm --filter client exec vite --host  (http://localhost:5173)
mise build                 # vite build -> packages/client/dist
mise build:prod            # same with BASE_PATH=/app/
mise start                 # vite preview on :4173 (used by Playwright)
```

Checks (CI runs exactly these; `mise check` runs all of them):

```bash
mise build:check           # pnpm --filter client exec tsc --noEmit
mise lint                  # pnpm exec eslint --ext .ts,.tsx packages/client
mise lint:fix
mise format                # pnpm exec prettier --check 'packages/client/**/*.{ts,tsx,json}'
mise format:fix
mise lingui                # extract + compile i18n catalogs (CI warns if catalogs are stale)
```

Tests (Playwright e2e only, no unit test runner):

```bash
mise test:e2e:install-deps # playwright install
mise test:e2e              # pnpm --filter client exec playwright test  (starts `mise start` itself)
pnpm --filter client exec playwright test e2e/it-works.spec.ts          # single spec
pnpm --filter client exec playwright test --project=chromium -g "name"  # filter by project / title
mise test:e2e:show-report
```

Rebuild a single dep after changing a submodule: `pnpm --filter stoat.js run build`.

Panda CSS codegen (`styled-system/`, gitignored) runs via the client `prepare` script on install. If `styled-system/jsx` imports fail, run `pnpm --filter client exec panda codegen`.

## Architecture

### Path aliases = internal packages

`packages/client/components/*` are treated as internal packages, exposed as `@revolt/<dir>` (aliases generated in `vite.config.ts` from the directory listing, mirrored in `tsconfig.json`). Import across them via the alias, not relative paths. Main ones:

| Alias | Purpose |
|---|---|
| `@revolt/state` | Persisted global state (`State` class, one `AbstractStore` subclass per key) |
| `@revolt/client` | `ClientController`: session lifecycle state machine, notifications, sounds |
| `@revolt/modal` | Modal registry: `types.ts` (discriminated union), `modals.tsx` (mount), `modals/*.tsx` |
| `@revolt/ui` | Design system (`components/design`), layout, floating UI, themes, Solid directives |
| `@revolt/app` | App-level interface pieces (settings, channels, desktop titlebar) and context menus |
| `@revolt/i18n` | Lingui provider, dayjs locales, error translation |
| `@revolt/markdown` | remark/rehype pipeline + ProseMirror composer |
| `@revolt/rtc` | LiveKit voice state |
| `@revolt/instance` | Multi-instance support (`/i/:host` routes) |
| `@revolt/common` | `env.ts` (VITE_* resolution, official-backend fallback), device/breakpoints |
| `@revolt/keybinds`, `@revolt/routing` | Keybind handling, router re-exports |

`packages/client/src/` holds the entry (`index.tsx`: route table + provider nesting in `MountContext`), `Interface.tsx` (logged-in layout), and page components under `src/interface/`. The route list in `index.tsx` is the source of truth for the routes listed in README.

### State (`@revolt/state`)

`State` in `components/state/index.tsx` owns one instance of every store, persists to IndexedDB via localforage (debounced 1200ms, except `auth`), and hydrates on load. Adding a store requires four edits: create `stores/<Name>.ts` with `Type<Name>`, add it to the `Store` type in `stores/index.ts`, extend `AbstractStore<"key", Type>` implementing `hydrate()`, `default()`, `clean()` (must tolerate arbitrary/legacy input), and add the field to `State`. Lists are alphabetical. Some stores (`notifications`, `ordering`, `release-notes`) sync with the backend through `stores/Sync.ts` and `SyncWorker.tsx`. Full walkthrough: `doc/src/components/state/creating.md`.

### Session lifecycle (`@revolt/client`)

`Controller.ts` implements the state machine documented in `doc/src/components/client/session-lifecycle.md` (READY → LOGGING_IN → CONNECTED, with DISCONNECTED/RECONNECTING/OFFLINE, exponential retry). `Interface.tsx` reads `lifecycle.state()` to decide banners/loading. State is not cleared on logout implicitly; the controller does it explicitly.

### Modals (`@revolt/modal`)

Open with `useModals().openModal({ type: "...", ...props })`. Adding a modal: add a variant to the `Modals` union in `types.ts`, create `modals/<Name>.tsx` using `Dialog`, register it in `modals.tsx`. Use Form2 (`@revolt/ui`, wraps solid-forms) for input modals, TanStack `useMutation` with `mutateAsync` + `onError: showError` for action modals, never both. Action handlers returning a Promise close the dialog on resolve; returning `false` keeps it open. See `doc/src/components/modal/guidelines.md`.

### Styling

Panda CSS: `styled` from `styled-system/jsx`, `css()` from `styled-system/css`. Theme tokens are Material 3 roles as CSS vars (`var(--md-sys-color-*)`), plus `--borderRadius-*`, `--gap-*`, `--fonts-*`, `--transitions-*`, `--brand-presence-*`. Breakpoints come from `components/common/Breakpoint.ts` and are wired into Panda conditions in `panda.config.ts`. Icons: use `<Symbol>` (Material Symbols) for new UI; only use Material Design Icons where the surrounding component already does.

### Solid directives

`components/ui/directives/*.ts` (`floating`, `scrollable`, `invisibleScrollable`, `autoComplete`) are auto-imported by `codegen.plugin.ts` whenever a `.tsx` file uses `use:<name>`. The `// @codegen directives` comment expands to forward all `use:*` props on a wrapper component.

### i18n

Lingui with Solid macros: `import { Trans, useLingui, Plural } from "@lingui/solid/macro"` (the `@lingui-solid/...` path in older docs is stale). Catalogs live in `components/i18n/catalogs/<locale>/messages.po`; `en` is the source locale. Do not commit catalog updates in feature PRs; maintainers run `mise lingui` after merge. `mise lingui:pr` resets catalogs to `main` before extracting if you need to see string diffs locally.

### Environment / backends

`components/common/lib/env.ts` resolves `VITE_HOST` / `VITE_API_URL` (and `VITE_DEV_*` overrides in dev only). Unset values fall back to the official Stoat backend. Setting `VITE_API_URL` without `VITE_HOST` logs an error. The Dockerfile builds with `__VITE_*__` placeholders that `docker/inject.js` replaces at container start.

## Conventions

From `GUIDELINES.md` and the ESLint/Prettier config:

- Never destructure reactive Solid props; use `splitProps` / `mergeProps`.
- 2-space indent, Prettier with `prettier-plugin-organize-imports` (type imports first). Run `mise format:fix` before committing; CI fails on formatting.
- JSDoc comment above every class, constant, Solid component, and non-overriding method/function.
- Import external libraries in one place and re-export (e.g. `@revolt/routing` wraps `@solidjs/router`); import only types from `stoat.js` inside `@revolt/ui`.
- Accessibility: semantic HTML, aria labels, keyboard navigation.
- Unused vars prefixed `_` are allowed by lint.
- PR titles must be Conventional Commits (`feat:`, `fix:`, `fix(ui):` ...); release-please generates `CHANGELOG.md` from them. PR template asks for test steps and a declaration of any LLM usage.
- Branch workflow uses git-town (`git-town.toml`), main branch is `main`.

## Docs

`doc/` is an mdbook (`mise mdbook` to serve) published at https://stoatchat.github.io/for-web/. It has the authoritative write-ups for state stores, modals, Form2, themes, session lifecycle, and Lingui usage; check there before inventing a pattern.
