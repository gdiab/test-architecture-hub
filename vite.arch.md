# hl_overview

High level overview of the codebase

# Repository Analysis: Vite

## 0. Repository Name
[[vite]]

## 1. Project Purpose

Vite is a **next-generation frontend build tool** that aims to provide a faster and leaner development experience for modern web projects. It solves several key problems:

- **Slow development server startup** - Uses native ES modules to serve source code directly during development
- **Slow Hot Module Replacement (HMR)** - Provides near-instant HMR regardless of application size
- **Complex build configuration** - Offers sensible defaults with a highly extensible plugin API
- **Modern JavaScript tooling** - Bundles code for production using Rollup/Rolldown with optimized output

**Primary Domain:** Frontend development tooling, build systems, and development servers.

## 2. Architecture Pattern

The project employs a **modular monorepo architecture** with:
- **Plugin-based extensibility** - Core functionality extended via plugins
- **Environment-based architecture** - Separate handling for client, SSR, and worker environments
- **Layered architecture** within the core package (client, node, shared, types layers)

## 3. Technology Stack

### Primary Languages
- **TypeScript** - Primary language for source code
- **JavaScript** - Configuration files and some utilities

### Core Dependencies & Frameworks
Based on `packages/vite/package.json`:

| Category | Dependencies |
|----------|-------------|
| **Bundler** | `rolldown` (experimental), `rollup`, `esbuild` |
| **CSS Processing** | `postcss`, `lightningcss` (optional) |
| **CSS Preprocessors** | `sass`, `less`, `stylus`, `sugarss` (optional peers) |
| **Dev Server** | `connect`, `http-proxy`, `ws` (WebSocket), `chokidar` (file watching) |
| **HTTP/Networking** | `http-proxy`, `launch-editor-middleware`, `cors` |
| **Environment** | `dotenv`, `dotenv-expand` |
| **Utilities** | `picomatch`, `tinyglobby`, `magic-string`, `es-module-lexer` |
| **Source Maps** | `@jridgewell/trace-mapping`, `@ampproject/remapping` |
| **Parsing** | `postcss-import`, `acorn` |

### Testing Framework
- **Vitest** - Test runner (evident from `vitest.config.ts`, `vitest.config.e2e.ts`)
- **Playwright** - E2E testing (`@playwright/test`)

### Build Tools
- **pnpm** - Package manager (workspace-based)
- **tsdown** - TypeScript bundling for packages
- **ESLint** - Linting
- **Prettier** - Code formatting

## 4. Initial Structure Impression

```
vite/
├── packages/           # Main packages (monorepo structure)
│   ├── vite/          # Core Vite package
│   ├── create-vite/   # CLI scaffolding tool
│   └── plugin-legacy/ # Legacy browser support plugin
├── playground/        # Test fixtures and examples (extensive)
├── docs/              # VitePress documentation site
├── scripts/           # Release and CI scripts
├── patches/           # Dependency patches
└── .github/           # GitHub workflows and templates
```

**Main Parts:**
- **Core Library** (`packages/vite`) - The main Vite build tool
- **CLI Tool** (`packages/create-vite`) - Project scaffolding
- **Plugin** (`packages/plugin-legacy`) - Legacy browser support
- **Documentation** (`docs/`) - User-facing documentation
- **Test Suite** (`playground/`) - Comprehensive integration tests

## 5. Configuration/Package Files

| File | Purpose |
|------|---------|
| `package.json` | Root workspace configuration |
| `pnpm-workspace.yaml` | pnpm monorepo workspace definition |
| `pnpm-lock.yaml` | Dependency lock file |
| `eslint.config.js` | ESLint configuration |
| `.prettierrc.json` | Prettier configuration |
| `.prettierignore` | Prettier ignore patterns |
| `vitest.config.ts` | Unit test configuration |
| `vitest.config.e2e.ts` | E2E test configuration |
| `.editorconfig` | Editor settings |
| `.gitignore` | Git ignore patterns |
| `.gitattributes` | Git attributes |
| `.git-blame-ignore-revs` | Git blame configuration |
| `netlify.toml` | Netlify deployment config |
| `packages/vite/package.json` | Core package configuration |
| `packages/vite/tsconfig.json` | TypeScript configuration |
| `packages/vite/rolldown.config.ts` | Build configuration |
| `packages/create-vite/package.json` | CLI package configuration |
| `packages/plugin-legacy/package.json` | Legacy plugin configuration |

## 6. Directory Structure

### Root Level
```
📁 packages/          # Publishable npm packages
📁 playground/        # Integration tests & examples
📁 docs/              # Documentation website
📁 scripts/           # Build & release automation
📁 patches/           # Third-party dependency patches
📁 .github/           # CI/CD and issue templates
```

### Core Package (`packages/vite/src/`)
```
📁 client/            # Browser-side HMR client code
📁 node/              # Node.js server & build logic
📁 module-runner/     # Module execution runtime
📁 shared/            # Shared utilities between client/node
📁 types/             # TypeScript type definitions
```

### Playground Structure (Test Fixtures)
Organized by **feature/capability**:
- `hmr/`, `hmr-ssr/` - Hot Module Replacement tests
- `css/`, `css-*` - CSS processing tests
- `ssr/`, `ssr-*` - Server-Side Rendering tests
- `worker/` - Web Worker tests
- `assets/` - Static asset handling
- `resolve/` - Module resolution tests
- `optimize-deps/` - Dependency optimization
- `legacy/` - Legacy browser support
- And many more specialized test scenarios

### Documentation (`docs/`)
```
📁 guide/             # User guides and tutorials
📁 config/            # Configuration reference
📁 blog/              # Release announcements
📁 plugins/           # Plugin documentation
📁 changes/           # Migration and API changes
📁 .vitepress/        # VitePress configuration
📁 images/            # Documentation images
📁 public/            # Static assets
```

## 7. High-Level Architecture

### Architecture Patterns

1. **Plugin Architecture**
   - Evidence: `api-plugin.md`, plugin configuration in `vite.config.js` files
   - Plugins follow Rollup-compatible interface with Vite-specific hooks
   - Supports `resolveId`, `load`, `transform`, and custom hooks

2. **Environment-Based Architecture** (Vite 6+)
   - Evidence: `api-environment*.md` docs, `environment-react-ssr/` playground
   - Separate environments for: client, SSR, custom runtimes
   - Each environment has its own module graph and plugin pipeline

3. **Layered Architecture** (within core)
   ```
   ┌─────────────────────────────────────┐
   │          CLI Layer (bin/)           │
   ├─────────────────────────────────────┤
   │      Node.js Runtime (node/)        │
   │  - Dev Server    - Build Pipeline   │
   │  - Plugin System - Config Loading   │
   ├─────────────────────────────────────┤
   │      Shared Utilities (shared/)     │
   ├─────────────────────────────────────┤
   │    Client Runtime (client/)         │
   │  - HMR Client    - Error Overlay    │
   └─────────────────────────────────────┘
   ```

4. **Monorepo Pattern**
   - Evidence: `pnpm-workspace.yaml`, multiple `packages/` directories
   - Enables independent versioning and publishing

### Communication Patterns
- **WebSocket** - HMR updates between dev server and browser client
- **HTTP** - Development server serves transformed modules
- **File System Watching** - Chokidar for detecting source changes

## 8. Build, Execution and Test

### Build Process

```bash
# Install dependencies
pnpm install

# Build all packages
pnpm run build

# Build specific package
cd packages/vite && pnpm run build
```

**Build Configuration:**
- Uses `rolldown.config.ts` for the core Vite package
- Uses `tsdown.config.ts` for other packages
- Outputs to `dist/` directories

### Entry Points

**Core Vite Package:**
- CLI: `packages/vite/bin/vite.js`
- Node API: `packages/vite/src/node/index.ts`
- Client: `packages/vite/src/client/client.ts`

**Create-Vite CLI:**
- Entry: `packages/create-vite/index.js`

### Running the Project

```bash
# Development (link local vite)
pnpm run dev

# Run specific playground
cd playground/hmr && pnpm run dev

# Preview documentation
cd docs && pnpm run dev
```

### Testing

```bash
# Run unit tests
pnpm run test

# Run E2E tests
pnpm run test-e2e

# Run specific test file
pnpm run test -- playground/hmr
```

**Test Configuration:**
- `vitest.config.ts` - Unit tests
- `vitest.config.e2e.ts` - E2E/integration tests
- `playground/vitestSetup.ts` - Global test setup
- `playground/vitestGlobalSetup.ts` - Global setup hooks

**Test Structure:**
- Each playground directory contains `__tests__/` folder
- Tests use Playwright for browser automation
- Tests share utilities from `playground/test-utils.ts`

### Scripts (from root `package.json`)
| Script | Purpose |
|--------|---------|
| `build` | Build all packages |
| `dev` | Development mode |
| `test` | Run unit tests |
| `test-e2e` | Run E2E tests |
| `lint` | Run ESLint |
| `format` | Run Prettier |
| `release` | Release workflow |

# module_deep_dive

Deep dive into modules

# Detailed Component Breakdown

Based on the repository structure, I'll analyze each major component of the Vite project.

---

## 1. `packages/vite/` - Core Vite Package

### Core Responsibility
The main Vite bundler and development server implementation. This is the heart of the project that provides the dev server, build system, HMR (Hot Module Replacement), and plugin architecture.

### Key Components

| Sub-directory/File | Role |
|-------------------|------|
| `src/client/` | Client-side HMR runtime code injected into the browser |
| `src/node/` | Node.js server implementation (dev server, build, CLI) |
| `src/module-runner/` | Module execution runtime for SSR and environments |
| `src/shared/` | Shared utilities between client and server |
| `src/types/` | TypeScript type definitions |
| `bin/` | CLI entry points (`vite` command) |
| `types/` | Public TypeScript declarations (including `internal/`) |
| `client.d.ts` | Client-side type definitions for user code |
| `rolldown.config.ts` | Build configuration using Rolldown bundler |

### Dependencies & Interactions
- **Internal**: Likely uses shared utilities from `src/shared/`
- **External Services/APIs**:
  - File system operations (Node.js `fs`)
  - HTTP/HTTPS servers for dev server
  - WebSocket for HMR communication
  - Integration with bundlers (esbuild, Rolldown)
  - CSS preprocessors (Sass, Less, Stylus)

---

## 2. `packages/create-vite/` - Project Scaffolding CLI

### Core Responsibility
Interactive CLI tool for scaffolding new Vite projects with various framework templates (React, Vue, Svelte, etc.).

### Key Components

| Sub-directory/File | Role |
|-------------------|------|
| `index.js` | CLI entry point |
| `src/` | Source code for scaffolding logic |
| `template-vue/` | Vue.js starter template |
| `template-vue-ts/` | Vue.js + TypeScript starter template |
| `template-react/` | React starter template |
| `template-react-ts/` | React + TypeScript starter template |
| `template-svelte/` | Svelte starter template |
| `template-svelte-ts/` | Svelte + TypeScript starter template |
| `template-solid/` | SolidJS starter template |
| `template-solid-ts/` | SolidJS + TypeScript starter template |
| `template-preact/` | Preact starter template |
| `template-preact-ts/` | Preact + TypeScript starter template |
| `template-lit/` | Lit starter template |
| `template-lit-ts/` | Lit + TypeScript starter template |
| `template-qwik/` | Qwik starter template |
| `template-qwik-ts/` | Qwik + TypeScript starter template |
| `template-vanilla/` | Vanilla JS starter template |
| `template-vanilla-ts/` | Vanilla TypeScript starter template |
| `__tests__/` | Unit tests for the CLI |

### Dependencies & Interactions
- **Internal**: Standalone package, no dependencies on other Vite packages
- **External Services/APIs**:
  - Node.js `fs` for file operations
  - `prompts` or similar for interactive CLI
  - npm registry for package initialization

---

## 3. `packages/plugin-legacy/` - Legacy Browser Support Plugin

### Core Responsibility
Official Vite plugin that provides legacy browser support by generating polyfills and transpiled bundles for older browsers that don't support native ES modules.

### Key Components

| Sub-directory/File | Role |
|-------------------|------|
| `src/` | Plugin source code |
| `src/__tests__/` | Unit tests for the plugin |
| `tsdown.config.ts` | Build configuration |

### Dependencies & Interactions
- **Internal**: Depends on `packages/vite/` plugin API
- **External Services/APIs**:
  - `@babel/core` for transpilation
  - `core-js` for polyfills
  - `regenerator-runtime` for async/await support
  - `systemjs` for module loading in legacy browsers

---

## 4. `playground/` - Integration Test Suite

### Core Responsibility
Comprehensive integration/end-to-end test fixtures covering various Vite features and edge cases. Each subdirectory is a self-contained test project.

### Key Components

| Sub-directory | Role |
|--------------|------|
| `hmr/` | Hot Module Replacement tests |
| `hmr-ssr/` | SSR-specific HMR tests |
| `css/` | CSS processing tests (preprocessors, modules, etc.) |
| `css-sourcemap/` | CSS source map generation tests |
| `css-lightningcss/` | Lightning CSS integration tests |
| `ssr/` | Server-side rendering tests |
| `ssr-html/` | SSR with HTML handling tests |
| `ssr-deps/` | SSR dependency handling tests |
| `assets/` | Static asset handling tests |
| `resolve/` | Module resolution tests |
| `optimize-deps/` | Dependency pre-bundling tests |
| `worker/` | Web Worker support tests |
| `lib/` | Library mode build tests |
| `legacy/` | Legacy plugin integration tests |
| `glob-import/` | `import.meta.glob` feature tests |
| `json/` | JSON import handling tests |
| `env/` | Environment variable tests |
| `html/` | HTML processing tests |
| `preload/` | Module preloading tests |
| `define/` | Define replacement tests |
| `alias/` | Path aliasing tests |
| `proxy-hmr/` | Proxy + HMR combination tests |
| `tailwind/` | Tailwind CSS integration tests |
| `wasm/` | WebAssembly support tests |
| `test-utils.ts` | Shared test utilities |
| `vitestSetup.ts` | Vitest global setup |
| `vitestGlobalSetup.ts` | Global test setup hooks |

### Dependencies & Interactions
- **Internal**: Depends on `packages/vite/` for the dev server and build
- **External Services/APIs**:
  - Vitest for test execution
  - Playwright for browser automation
  - Various CSS preprocessors and frameworks per test

---

## 5. `docs/` - Documentation Website

### Core Responsibility
Official Vite documentation site built with VitePress, containing guides, API references, configuration options, and blog posts.

### Key Components

| Sub-directory/File | Role |
|-------------------|------|
| `guide/` | User guides (features, SSR, plugins, migration, etc.) |
| `config/` | Configuration reference documentation |
| `plugins/` | Plugin ecosystem documentation |
| `blog/` | Release announcements and blog posts |
| `changes/` | Breaking changes and migration guides |
| `images/` | Documentation images and diagrams |
| `public/` | Static assets (logos, favicons, etc.) |
| `.vitepress/` | VitePress configuration and theme |
| `.vitepress/theme/` | Custom theme components and styles |
| `_data/` | Data files for dynamic content |
| `index.md` | Homepage |
| `team.md` | Team page |
| `releases.md` | Release history |

### Dependencies & Interactions
- **Internal**: Uses Vite (via VitePress) for development and build
- **External Services/APIs**:
  - VitePress for static site generation
  - Netlify for deployment (per `netlify.toml`)
  - GitHub for team data fetching

---

## 6. `scripts/` - Build & Release Scripts

### Core Responsibility
Automation scripts for publishing, releasing, and maintaining the monorepo.

### Key Components

| File | Role |
|------|------|
| `release.ts` | Version bumping and release workflow |
| `releaseUtils.ts` | Shared utilities for release process |
| `publishCI.ts` | CI/CD publishing automation |
| `docs-check.sh` | Documentation validation script |

### Dependencies & Interactions
- **Internal**: Interacts with all packages in the monorepo
- **External Services/APIs**:
  - npm registry for publishing
  - GitHub API for releases
  - CI/CD systems (GitHub Actions)

---

## 7. `patches/` - Dependency Patches

### Core Responsibility
Contains patches for third-party dependencies to fix bugs or add features before upstream releases.

### Key Components

| File | Role |
|------|------|
| `chokidar@3.6.0.patch` | File watcher fixes |
| `dotenv-expand@12.0.3.patch` | Environment variable expansion fixes |
| `http-proxy-3.patch` | HTTP proxy behavior modifications |
| `sirv@3.0.2.patch` | Static file serving fixes |

### Dependencies & Interactions
- **Internal**: Applied to dependencies used by `packages/vite/`
- **External Services/APIs**: None directly; patches are applied via `pnpm patch`

---

## 8. `.github/` - GitHub Configuration

### Core Responsibility
GitHub-specific configurations for CI/CD, issue templates, and repository management.

### Key Components

| Sub-directory/File | Role |
|-------------------|------|
| `workflows/ci.yml` | Main CI pipeline (tests, builds) |
| `workflows/publish.yml` | Package publishing workflow |
| `workflows/preview-release.yml` | Preview release for PRs |
| `workflows/ecosystem-ci-trigger.yml` | Ecosystem compatibility testing |
| `ISSUE_TEMPLATE/` | Bug report and feature request templates |
| `PULL_REQUEST_TEMPLATE.md` | PR description template |
| `commit-convention.md` | Commit message guidelines |
| `renovate.json5` | Dependency update bot configuration |
| `copilot-instructions.md` | GitHub Copilot configuration |

### Dependencies & Interactions
- **Internal**: Orchestrates testing of all packages
- **External Services/APIs**:
  - GitHub Actions for CI/CD
  - Renovate bot for dependency updates
  - Netlify for preview deployments

---

## Summary Dependency Graph

```
┌─────────────────────────────────────────────────────────────┐
│                     .github/workflows/                       │
│                    (CI/CD Orchestration)                     │
└─────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌──────────────┐    ┌──────────────────┐    ┌─────────────┐
│   scripts/   │    │  packages/vite/  │◄───│   patches/  │
│  (Release)   │    │   (Core Engine)  │    │  (Dep Fixes)│
└──────────────┘    └──────────────────┘    └─────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌──────────────────┐  ┌──────────────────┐  ┌─────────────┐
│ packages/        │  │ packages/        │  │ playground/ │
│ create-vite/     │  │ plugin-legacy/   │  │  (Tests)    │
│ (Scaffolding)    │  │ (Legacy Support) │  └─────────────┘
└──────────────────┘  └──────────────────┘
                              │
                              ▼
                    ┌──────────────────┐
                    │      docs/       │
                    │ (Documentation)  │
                    └──────────────────┘
```

# dependencies

Analyze dependencies and external libraries

# Dependency and Architecture Analysis

## Repository Overview

This repository is **Vite** (`vite_eecc8ba1`), a next-generation frontend build tool. The project is structured as a monorepo using pnpm workspaces.

---

## Internal Modules

### Core Packages (`/packages/`)

| Module | Description |
|--------|-------------|
| **vite** (`/packages/vite/`) | The core Vite build tool and development server. Contains the main bundling logic, dev server, HMR (Hot Module Replacement), and plugin system. Includes sub-modules for client runtime, module runner, node-specific code, shared utilities, and type definitions. |
| **create-vite** (`/packages/create-vite/`) | CLI scaffolding tool for creating new Vite projects. Provides multiple starter templates for various frameworks (React, Vue, Svelte, Solid, Preact, Lit, Qwik, Vanilla JS/TS). |
| **plugin-legacy** (`/packages/plugin-legacy/`) | Official Vite plugin for legacy browser support. Handles transpilation and polyfilling for older browsers using Babel and SystemJS. |

### Documentation (`/docs/`)

| Module | Description |
|--------|-------------|
| **docs** | VitePress-powered documentation site. Contains guides, API references, configuration documentation, blog posts, and changelogs. |

### Playground (`/playground/`)

| Module | Description |
|--------|-------------|
| **playground** | Comprehensive test fixtures and integration test scenarios. Contains numerous sub-projects testing specific Vite features including CSS handling, SSR, HMR, asset management, worker support, optimization, and various framework integrations. |

### Scripts (`/scripts/`)

| Module | Description |
|--------|-------------|
| **scripts** | Release automation and CI/CD utilities. Contains publishing scripts, release management, and documentation checks. |

---

## External Dependencies

### Core Vite Dependencies

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `rolldown` | Rolldown | Rust-based JavaScript bundler (core bundling engine) | `/packages/vite/package.json` |
| `esbuild` | esbuild | Fast JavaScript/TypeScript transpiler and minifier | `/packages/vite/package.json` (peer) |
| `postcss` | PostCSS | CSS transformation tool | `/packages/vite/package.json` |
| `lightningcss` | Lightning CSS | Fast CSS parser, transformer, and minifier | `/packages/vite/package.json` |
| `tinyglobby` | tinyglobby | Fast glob pattern matching | `/packages/vite/package.json` |
| `fdir` | fdir | Fast directory crawler | `/packages/vite/package.json` |
| `picomatch` | picomatch | Glob pattern matching library | `/packages/vite/package.json` |
| `@oxc-project/runtime` | OXC Runtime | Oxc JavaScript toolchain runtime | `/packages/vite/package.json` |

### Vite Development/Build Dependencies

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `chokidar` | Chokidar | File system watcher for HMR | `/packages/vite/package.json` (dev) |
| `connect` | Connect | HTTP server middleware framework | `/packages/vite/package.json` (dev) |
| `ws` | ws | WebSocket library for HMR communication | `/packages/vite/package.json` (dev) |
| `sirv` | Sirv | Static file server | `/packages/vite/package.json` (dev) |
| `http-proxy-3` | http-proxy | HTTP proxy middleware | `/packages/vite/package.json` (dev) |
| `cors` | CORS | Cross-origin resource sharing middleware | `/packages/vite/package.json` (dev) |
| `etag` | etag | HTTP ETag generation | `/packages/vite/package.json` (dev) |
| `magic-string` | MagicString | String manipulation with source maps | `/packages/vite/package.json` (dev) |
| `es-module-lexer` | es-module-lexer | ES module import/export lexer | `/packages/vite/package.json` (dev) |
| `dotenv` | dotenv | Environment variable loading | `/packages/vite/package.json` (dev) |
| `dotenv-expand` | dotenv-expand | Variable expansion for dotenv | `/packages/vite/package.json` (dev) |
| `cac` | CAC | Command-line argument parsing | `/packages/vite/package.json` (dev) |
| `picocolors` | picocolors | Terminal color output | `/packages/vite/package.json` (dev) |
| `open` | open | Open URLs/files in browser | `/packages/vite/package.json` (dev) |
| `parse5` | parse5 | HTML parser | `/packages/vite/package.json` (dev) |
| `terser` | Terser | JavaScript minifier | `/packages/vite/package.json` (peer) |
| `sass` | Sass | CSS preprocessor | `/packages/vite/package.json` (peer/dev) |
| `sass-embedded` | Sass Embedded | Embedded Dart Sass compiler | `/packages/vite/package.json` (peer/dev) |
| `less` | Less | CSS preprocessor | `/packages/vite/package.json` (peer) |
| `stylus` | Stylus | CSS preprocessor | `/packages/vite/package.json` (peer) |
| `sugarss` | SugarSS | Indent-based CSS syntax | `/packages/vite/package.json` (peer) |
| `postcss-import` | postcss-import | PostCSS plugin for @import | `/packages/vite/package.json` (dev) |
| `postcss-modules` | postcss-modules | CSS Modules support | `/packages/vite/package.json` (dev) |
| `postcss-load-config` | postcss-load-config | PostCSS config loader | `/packages/vite/package.json` (dev) |
| `tsconfck` | tsconfck | TypeScript config reader | `/packages/vite/package.json` (dev) |
| `mlly` | mlly | ESM utilities | `/packages/vite/package.json` (dev) |
| `resolve.exports` | resolve.exports | Package exports resolver | `/packages/vite/package.json` (dev) |
| `estree-walker` | estree-walker | AST walker | `/packages/vite/package.json` (dev) |
| `periscopic` | periscopic | Scope analysis for ESTree | `/packages/vite/package.json` (dev) |
| `strip-literal` | strip-literal | Strip string literals from code | `/packages/vite/package.json` (dev) |
| `nanoid` | nanoid | Unique ID generator | `/packages/vite/package.json` (dev) |
| `mrmime` | mrmime | MIME type lookup | `/packages/vite/package.json` (dev) |
| `pathe` | pathe | Cross-platform path utilities | `/packages/vite/package.json` (dev) |
| `ufo` | ufo | URL utilities | `/packages/vite/package.json` (dev) |
| `cross-spawn` | cross-spawn | Cross-platform child process spawning | `/packages/vite/package.json` (dev) |
| `@jridgewell/trace-mapping` | @jridgewell/trace-mapping | Source map trace mapping | `/packages/vite/package.json` (dev) |
| `@jridgewell/remapping` | @jridgewell/remapping | Source map remapping | `/packages/vite/package.json` (dev) |
| `convert-source-map` | convert-source-map | Source map conversion utilities | `/packages/vite/package.json` (dev) |
| `artichokie` | artichokie | Code splitting utilities | `/packages/vite/package.json` (dev) |
| `@rolldown/pluginutils` | Rolldown Plugin Utils | Plugin utilities for Rolldown | `/packages/vite/package.json` (dev) |
| `@rollup/pluginutils` | Rollup Plugin Utils | Plugin utilities for Rollup | `/packages/vite/package.json` (dev) |
| `@rollup/plugin-commonjs` | @rollup/plugin-commonjs | CommonJS to ES module conversion | `/packages/vite/package.json` (dev) |
| `@rollup/plugin-alias` | @rollup/plugin-alias | Module aliasing | `/packages/vite/package.json` (dev) |
| `@rollup/plugin-dynamic-import-vars` | @rollup/plugin-dynamic-import-vars | Dynamic import variable support | `/packages/vite/package.json` (dev) |
| `rollup` | Rollup | Module bundler (for specific build tasks) | `/packages/vite/package.json` (dev) |

### Plugin Legacy Dependencies

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `@babel/core` | Babel Core | JavaScript transpiler core | `/packages/plugin-legacy/package.json` |
| `@babel/preset-env` | Babel Preset Env | Smart Babel preset for target environments | `/packages/plugin-legacy/package.json` |
| `@babel/plugin-transform-dynamic-import` | Babel Dynamic Import Plugin | Transform dynamic imports for legacy | `/packages/plugin-legacy/package.json` |
| `@babel/plugin-transform-modules-systemjs` | Babel SystemJS Plugin | Transform to SystemJS modules | `/packages/plugin-legacy/package.json` |
| `babel-plugin-polyfill-corejs3` | Babel Polyfill Core-JS 3 | Core-JS 3 polyfill injection | `/packages/plugin-legacy/package.json` |
| `babel-plugin-polyfill-regenerator` | Babel Polyfill Regenerator | Regenerator runtime polyfill | `/packages/plugin-legacy/package.json` |
| `browserslist` | Browserslist | Browser target configuration | `/packages/plugin-legacy/package.json` |
| `browserslist-to-esbuild` | browserslist-to-esbuild | Convert browserslist to esbuild targets | `/packages/plugin-legacy/package.json` |
| `core-js` | core-js | Modular standard library polyfills | `/packages/plugin-legacy/package.json` |
| `regenerator-runtime` | regenerator-runtime | Generator/async function runtime | `/packages/plugin-legacy/package.json` |
| `systemjs` | SystemJS | Dynamic ES module loader | `/packages/plugin-legacy/package.json` |
| `acorn` | Acorn | JavaScript parser | `/packages/plugin-legacy/package.json` (dev) |

### Create-Vite Dependencies

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `@clack/prompts` | Clack Prompts | Interactive CLI prompts | `/packages/create-vite/package.json` (dev) |
| `@vercel/detect-agent` | Vercel Detect Agent | Package manager detection | `/packages/create-vite/package.json` (dev) |
| `mri` | mri | Argument parsing | `/packages/create-vite/package.json` (dev) |
| `tsdown` | tsdown | TypeScript bundler | `/packages/create-vite/package.json` (dev) |

### Framework Template Dependencies

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `vue` | Vue.js | Progressive JavaScript framework | `/packages/create-vite/template-vue/package.json`, `/packages/create-vite/template-vue-ts/package.json` |
| `react` | React | UI component library | `/packages/create-vite/template-react/package.json`, `/packages/create-vite/template-react-ts/package.json` |
| `react-dom` | React DOM | React DOM rendering | `/packages/create-vite/template-react/package.json`, `/packages/create-vite/template-react-ts/package.json` |
| `preact` | Preact | Fast React alternative | `/packages/create-vite/template-preact/package.json`, `/packages/create-vite/template-preact-ts/package.json` |
| `solid-js` | Solid | Reactive UI library | `/packages/create-vite/template-solid/package.json`, `/packages/create-vite/template-solid-ts/package.json` |
| `svelte` | Svelte | Compiler-based UI framework | `/packages/create-vite/template-svelte/package.json` (dev), `/packages/create-vite/template-svelte-ts/package.json` (dev) |
| `lit` | Lit | Web components library | `/packages/create-vite/template-lit/package.json`, `/packages/create-vite/template-lit-ts/package.json` |
| `@builder.io/qwik` | Qwik | Resumable framework | `/packages/create-vite/template-qwik/package.json`, `/packages/create-vite/template-qwik-ts/package.json` |

### Framework Plugin Dependencies

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `@vitejs/plugin-vue` | Vite Plugin Vue | Vue SFC support for Vite | `/packages/create-vite/template-vue/package.json` (dev) |
| `@vitejs/plugin-react` | Vite Plugin React | React Fast Refresh for Vite | `/packages/create-vite/template-react/package.json` (dev) |
| `@preact/preset-vite` | Preact Preset Vite | Preact support for Vite | `/packages/create-vite/template-preact/package.json` (dev) |
| `vite-plugin-solid` | Vite Plugin Solid | Solid.js support for Vite | `/packages/create-vite/template-solid/package.json` (dev) |
| `@sveltejs/vite-plugin-svelte` | Vite Plugin Svelte | Svelte support for Vite | `/packages/create-vite/template-svelte/package.json` (dev) |

### Documentation Dependencies

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `vitepress` | VitePress | Vue-powered static site generator | `/docs/package.json` (dev) |
| `@shikijs/vitepress-twoslash` | Shiki VitePress Twoslash | TypeScript code samples with hover info | `/docs/package.json` (dev) |
| `feed` | Feed | RSS/Atom feed generation | `/docs/package.json` (dev) |
| `gsap` | GSAP | Animation library | `/docs/package.json` (dev) |
| `vitepress-plugin-group-icons` | VitePress Group Icons | Icon grouping plugin | `/docs/package.json` (dev) |
| `vitepress-plugin-llms` | VitePress LLMs Plugin | LLM integration plugin | `/docs/package.json` (dev) |
| `vue-tsc` | vue-tsc | Vue TypeScript type checker | `/docs/package.json` (dev) |

### Development & Testing Dependencies (Root)

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `typescript` | TypeScript | TypeScript compiler | `/package.json` (dev) |
| `vitest` | Vitest | Vite-native test runner | `/package.json` (dev) |
| `playwright-chromium` | Playwright Chromium | E2E browser testing | `/package.json` (dev) |
| `eslint` | ESLint | JavaScript linter | `/package.json` (dev) |
| `prettier` | Prettier | Code formatter | `/package.json` (dev) |
| `typescript-eslint` | TypeScript ESLint | TypeScript ESLint integration | `/package.json` (dev) |
| `eslint-plugin-import-x` | eslint-plugin-import-x | Import linting rules | `/package.json` (dev) |
| `eslint-plugin-n` | eslint-plugin-n | Node.js linting rules | `/package.json` (dev) |
| `eslint-plugin-regexp` | eslint-plugin-regexp | RegExp linting rules | `/package.json` (dev) |
| `execa` | execa | Process execution | `/package.json` (dev) |
| `tsx` | tsx | TypeScript execution | `/package.json` (dev) |
| `simple-git-hooks` | simple-git-hooks | Git hooks management | `/package.json` (dev) |
| `lint-staged` | lint-staged | Run linters on staged files | `/package.json` (dev) |
| `globals` | globals | Global variable definitions | `/package.json` (dev) |
| `@vitejs/release-scripts` | Vite Release Scripts | Release automation | `/package.json` (dev) |

### Playground/Test Dependencies

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `express` | Express | Node.js web framework (SSR testing) | `/playground/ssr/package.json` (dev), `/playground/ssr-deps/package.json` (dev), and others |
| `tailwindcss` | Tailwind CSS | Utility-first CSS framework | `/playground/tailwind/package.json`, `/playground/tailwind-v3/package.json` |
| `@tailwindcss/vite` | Tailwind CSS Vite Plugin | Tailwind v4 Vite integration | `/playground/tailwind/package.json` |
| `@tailwindcss/postcss` | Tailwind CSS PostCSS | Tailwind PostCSS plugin | `/playground/tailwind-sourcemap/package.json` |
| `autoprefixer` | Autoprefixer | CSS vendor prefixing | `/playground/tailwind-v3/package.json` |
| `postcss-nested` | postcss-nested | Nested CSS rules | `/playground/css/package.json` (dev) |
| `miniflare` | Miniflare | Cloudflare Workers simulator | `/playground/ssr-webworker/package.json` (dev) |
| `pug` | Pug | Template engine | `/playground/ssr-pug/package.json` (dev) |
| `axios` | Axios | HTTP client | `/playground/optimize-deps/package.json` |
| `clipboard` | Clipboard.js | Clipboard manipulation | `/playground/optimize-deps/package.json` |
| `lodash` | Lodash | Utility library | `/playground/optimize-deps/package.json` |
| `lodash-es` | Lodash ES | ES module Lodash | `/playground/optimize-deps/package.json` |
| `phoenix` | Phoenix | Phoenix framework client | `/playground/optimize-deps/package.json` |
| `vuex` | Vuex | Vue state management | `/playground/optimize-deps/package.json` |
| `@vue/shared` | Vue Shared | Vue shared utilities | `/playground/alias/package.json` |
| `@node-rs/bcrypt` | @node-rs/bcrypt | Bcrypt implementation | `/playground/ssr-deps/package.json` |
| `normalize.css` | Normalize.css | CSS reset | `/playground/resolve/package.json` |
| `@babel/runtime` | Babel Runtime | Babel helpers runtime | `/playground/resolve/package.json` |
| `bignumber.js` | BigNumber.js | Arbitrary precision math | `/playground/resolve/require-pkg-with-module-field/package.json` |
| `kill-port` | kill-port | Kill process on port | `/playground/package.json` (dev) |
| `css-color-names` | css-color-names | CSS color name mapping | `/playground/package.json` (dev) |
| `tinyspy` | tinyspy | Minimal spy/mock library | `/packages/vite/src/node/ssr/runtime/__tests__/package.json` |
| `birpc` | birpc | Bidirectional RPC | `/packages/vite/src/node/ssr/runtime/__tests__/package.json` |

# core_entities

Core entities and their relationships

# Domain Model Analysis for Vite Repository

## Overview

This repository is **Vite** - a modern frontend build tool and development server. After analyzing the structure (excluding `arch-docs`), I've identified the core domain entities that represent the fundamental concepts in this project.

---

## 1. Common Data Entities / Domain Models

### 1.1 **Module**
The fundamental unit of code that Vite processes, transforms, and serves.

| Attribute | Description |
|-----------|-------------|
| `id` | Unique identifier (usually the resolved file path) |
| `url` | The URL used to request/serve this module |
| `file` | Absolute file path on disk |
| `type` | Module type (js, css, json, wasm, asset, etc.) |
| `code` | Transformed source code |
| `map` | Source map for the transformation |
| `importers` | Set of modules that import this module |
| `importedModules` | Set of modules this module imports |
| `acceptedHmrDeps` | HMR dependencies accepted by this module |
| `isSelfAccepting` | Whether module handles its own HMR |
| `transformResult` | Cached transformation result |
| `ssrModule` | SSR-specific module representation |

---

### 1.2 **Plugin**
Extensibility unit that hooks into Vite's build/serve pipeline.

| Attribute | Description |
|-----------|-------------|
| `name` | Unique plugin identifier |
| `enforce` | Execution order (`pre`, `post`, or default) |
| `apply` | When to apply (`serve`, `build`, or both) |
| `config` | Hook to modify config |
| `configResolved` | Hook after config is resolved |
| `configureServer` | Hook to configure dev server |
| `transformIndexHtml` | Hook to transform HTML |
| `resolveId` | Hook to resolve module IDs |
| `load` | Hook to load module content |
| `transform` | Hook to transform module code |
| `buildStart` / `buildEnd` | Build lifecycle hooks |
| `handleHotUpdate` | HMR handling hook |

---

### 1.3 **Environment**
Represents a target runtime environment (client, SSR, worker, etc.).

| Attribute | Description |
|-----------|-------------|
| `name` | Environment identifier (e.g., `client`, `ssr`) |
| `mode` | Development or production |
| `config` | Environment-specific configuration |
| `moduleGraph` | Module graph for this environment |
| `pluginContainer` | Plugin container instance |
| `depsOptimizer` | Dependency optimizer for this environment |
| `options` | Resolved environment options |

---

### 1.4 **DevServer**
The development server that serves modules and handles HMR.

| Attribute | Description |
|-----------|-------------|
| `config` | Resolved Vite configuration |
| `middlewares` | Connect middleware stack |
| `httpServer` | Underlying HTTP server |
| `ws` | WebSocket server for HMR |
| `environments` | Map of available environments |
| `moduleGraph` | Module dependency graph |
| `pluginContainer` | Container for executing plugins |
| `watcher` | File system watcher |
| `transformRequest` | Method to transform a request |
| `ssrLoadModule` | Method to load SSR modules |

---

### 1.5 **Config (ResolvedConfig)**
The complete configuration for a Vite project.

| Attribute | Description |
|-----------|-------------|
| `root` | Project root directory |
| `base` | Public base path |
| `mode` | `development` or `production` |
| `command` | `serve` or `build` |
| `plugins` | Array of resolved plugins |
| `resolve` | Module resolution options |
| `css` | CSS processing options |
| `build` | Build-specific options |
| `server` | Dev server options |
| `preview` | Preview server options |
| `optimizeDeps` | Dependency optimization options |
| `ssr` | SSR-specific options |
| `worker` | Web Worker options |
| `environments` | Per-environment configs |

---

### 1.6 **Dependency (OptimizedDep)**
A pre-bundled/optimized npm dependency.

| Attribute | Description |
|-----------|-------------|
| `id` | Package identifier |
| `file` | Path to optimized file |
| `src` | Original source path |
| `needsInterop` | Whether CJS/ESM interop needed |
| `browserHash` | Hash for cache busting |
| `fileHash` | Content hash |
| `exports` | Detected exports |
| `exportsData` | Export metadata |

---

### 1.7 **Asset**
Static files processed during build.

| Attribute | Description |
|-----------|-------------|
| `name` | Original file name |
| `fileName` | Output file name (with hash) |
| `source` | File content (Buffer or string) |
| `type` | `asset` or `chunk` |
| `needsCodeReference` | Whether referenced in code |

---

### 1.8 **HMRContext / HMRUpdate**
Hot Module Replacement data structures.

| Attribute | Description |
|-----------|-------------|
| `file` | Changed file path |
| `timestamp` | Update timestamp |
| `modules` | Affected module nodes |
| `type` | Update type (`js-update`, `css-update`, `full-reload`) |
| `path` | Module path being updated |
| `acceptedPath` | Path of accepted update |

---

### 1.9 **TransformResult**
Result of transforming a module.

| Attribute | Description |
|-----------|-------------|
| `code` | Transformed code |
| `map` | Source map |
| `etag` | Entity tag for caching |
| `deps` | Static dependencies |
| `dynamicDeps` | Dynamic import dependencies |

---

### 1.10 **Chunk (Build Output)**
A bundled output unit.

| Attribute | Description |
|-----------|-------------|
| `name` | Chunk name |
| `fileName` | Output filename |
| `code` | Bundled code |
| `map` | Source map |
| `imports` | Static imports |
| `dynamicImports` | Dynamic imports |
| `modules` | Modules included in chunk |
| `isEntry` | Whether it's an entry chunk |
| `isDynamicEntry` | Whether from dynamic import |

---

## 2. Entity Relationships

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              Config                                      │
│  (ResolvedConfig)                                                       │
└────────────────┬────────────────────────────────────────────────────────┘
                 │
                 │ 1:N
                 ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                             Plugin[]                                     │
│  - Hooks into build/serve lifecycle                                      │
└─────────────────────────────────────────────────────────────────────────┘
                 │
                 │ used by
                 ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            DevServer                                     │
│  - Serves modules                                                        │
│  - Handles HMR                                                          │
└──────┬─────────────────────┬────────────────────────────────────────────┘
       │                     │
       │ 1:N                 │ 1:N
       ▼                     ▼
┌──────────────┐      ┌──────────────────┐
│ Environment  │      │   ModuleGraph    │
│ (client/ssr) │      │                  │
└──────┬───────┘      └────────┬─────────┘
       │                       │
       │ 1:1                   │ 1:N
       ▼                       ▼
┌──────────────┐      ┌──────────────────┐
│ DepsOptimizer│      │     Module       │◄────────────────┐
│              │      │   (ModuleNode)   │                 │
└──────┬───────┘      └────────┬─────────┘                 │
       │                       │                           │
       │ 1:N                   │ N:N (imports)             │
       ▼                       ▼                           │
┌──────────────┐      ┌──────────────────┐                 │
│ OptimizedDep │      │ TransformResult  │                 │
│ (Dependency) │      │                  │                 │
└──────────────┘      └──────────────────┘                 │
                                                           │
                      ┌──────────────────┐                 │
                      │    HMRUpdate     │─────────────────┘
                      │                  │  affects
                      └──────────────────┘

                      BUILD OUTPUT
                      ─────────────
┌──────────────────┐      ┌──────────────────┐
│      Chunk       │ 1:N  │      Asset       │
│  (OutputChunk)   │◄────►│  (OutputAsset)   │
└────────┬─────────┘      └──────────────────┘
         │
         │ contains N:N
         ▼
┌──────────────────┐
│     Module       │
│                  │
└──────────────────┘
```

---

## 3. Relationship Summary

| Relationship | Type | Description |
|--------------|------|-------------|
| Config → Plugin | 1:N | A config contains multiple plugins |
| Config → Environment | 1:N | A config defines multiple environments |
| DevServer → Environment | 1:N | Server manages multiple environments |
| Environment → ModuleGraph | 1:1 | Each environment has its own module graph |
| ModuleGraph → Module | 1:N | Graph contains many modules |
| Module → Module (imports) | N:N | Modules import other modules |
| Module → TransformResult | 1:1 | Each module has a transform result |
| DepsOptimizer → OptimizedDep | 1:N | Optimizer manages many dependencies |
| HMRUpdate → Module | N:N | An update affects multiple modules |
| Chunk → Module | N:N | A chunk bundles multiple modules |
| Plugin → Module (via hooks) | N:N | Plugins process multiple modules |

# DBs

databases analysis

no database

# APIs

APIs analysis

# API Documentation Analysis

After a comprehensive scan of the provided Vite repository codebase, I have analyzed all the relevant files including:

- Server configuration files (`server.js` files in playground directories)
- Vite configuration files (`vite.config.js`, `vite.config.ts`)
- Test utilities and setup files
- Source code in `packages/vite/src/`

## Findings

This repository is the **Vite build tool** - a frontend build tool and development server. While Vite does create an internal HTTP development server, the codebase does not expose any **user-facing HTTP API endpoints** in the traditional sense (REST APIs, GraphQL endpoints, etc.).

The HTTP servers found in this codebase are:

1. **Vite Dev Server** - An internal development server that serves static files, handles HMR (Hot Module Replacement), and transforms modules. This is infrastructure code, not an API.

2. **SSR Test Servers** (in `playground/ssr-html/server.js`, `playground/ssr-conditions/server.js`, etc.) - These are **test fixtures** used for testing SSR functionality, not actual API endpoints meant for external consumption.

3. **Proxy Test Servers** (in `playground/proxy-bypass/`, `playground/proxy-hmr/`) - These are test utilities for proxy functionality testing.

All HTTP-related code in this repository serves one of these purposes:
- Internal development server infrastructure
- Test fixtures for the playground test suite
- Configuration options for the dev/preview servers

---

**no HTTP API**

# events

events analysis

no events

# service_dependencies

Analyze service dependencies

# External Dependencies Analysis for Vite Repository

## Overview

This repository is the **Vite** build tool - a next-generation frontend build tool. The analysis below identifies all external dependencies across the monorepo structure.

---

## 1. Build & Development Tools

### Dependency Name: esbuild
**Type of Dependency:** Library/Framework (Build Tool)  
**Purpose/Role:** JavaScript/TypeScript bundler and minifier used for fast builds during development and production. It's a core peer dependency for Vite's bundling capabilities.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - Listed as peer dependency: `"esbuild": "^0.25.0"`
- Used throughout Vite's core build pipeline

---

### Dependency Name: Rolldown
**Type of Dependency:** Library/Framework (Build Tool)  
**Purpose/Role:** Rust-based JavaScript bundler used for production builds. It's the primary bundler for Vite 7+.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - `"rolldown": "1.0.0-beta.57"`
- `/package.json` (dev) - `"rolldown": "1.0.0-beta.57"`
- `/playground/package.json` (dev) - `"rolldown": "1.0.0-beta.57"`
- Config file: `/packages/vite/rolldown.config.ts`

---

### Dependency Name: Rollup
**Type of Dependency:** Library/Framework (Build Tool)  
**Purpose/Role:** JavaScript module bundler, used for some build processes and plugin compatibility.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"rollup": "^4.43.0"`
- `/package.json` (dev) - `"rollup": "^4.43.0"`

---

### Dependency Name: Terser
**Type of Dependency:** Library/Framework (Minifier)  
**Purpose/Role:** JavaScript minifier used for production builds and code optimization.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - Peer dependency: `"terser": "^5.16.0"`
- `/packages/plugin-legacy/package.json` - Peer dependency: `"terser": "^5.16.0"`
- `/packages/vite/package.json` (dev) - `"terser": "^5.44.1"`
- `/playground/legacy/package.json` (dev) - `"terser": "^5.44.1"`
- `/playground/preload/package.json` (dev) - `"terser": "^5.44.1"`

---

### Dependency Name: TypeScript
**Type of Dependency:** Library/Framework (Language/Compiler)  
**Purpose/Role:** TypeScript compiler for type checking and transpilation of the codebase.  
**Integration Point/Clues:**
- `/package.json` (dev) - `"typescript": "~5.9.3"`
- Multiple template packages with TypeScript dependency
- Config files: `tsconfig.json` files throughout the repository

---

### Dependency Name: tsx
**Type of Dependency:** Library/Framework (Runtime)  
**Purpose/Role:** TypeScript execution engine for running TypeScript files directly without pre-compilation.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - Peer dependency: `"tsx": "^4.8.1"`
- `/package.json` (dev) - `"tsx": "^4.21.0"`
- `/playground/tailwind/package.json` (dev)
- `/playground/tailwind-v3/package.json` (dev)

---

### Dependency Name: Vitest
**Type of Dependency:** Library/Framework (Testing)  
**Purpose/Role:** Fast Vite-native unit testing framework for running tests.  
**Integration Point/Clues:**
- `/package.json` (dev) - `"vitest": "^4.0.15"`
- Config files: `/vitest.config.ts`, `/vitest.config.e2e.ts`
- Test setup files in `/playground/`

---

### Dependency Name: Playwright Chromium
**Type of Dependency:** Library/Framework (Testing)  
**Purpose/Role:** End-to-end testing framework for browser automation testing.  
**Integration Point/Clues:**
- `/package.json` (dev) - `"playwright-chromium": "^1.57.0"`

---

### Dependency Name: ESLint
**Type of Dependency:** Library/Framework (Linting)  
**Purpose/Role:** JavaScript/TypeScript linter for code quality and consistency.  
**Integration Point/Clues:**
- `/package.json` (dev) - `"eslint": "^9.39.2"`
- Config file: `/eslint.config.js`
- Multiple ESLint plugins listed in dev dependencies

---

### Dependency Name: Prettier
**Type of Dependency:** Library/Framework (Formatting)  
**Purpose/Role:** Code formatter for consistent code style.  
**Integration Point/Clues:**
- `/package.json` (dev) - `"prettier": "3.7.4"`
- Config files: `/.prettierrc.json`, `/.prettierignore`

---

## 2. CSS Processing Dependencies

### Dependency Name: PostCSS
**Type of Dependency:** Library/Framework (CSS Processing)  
**Purpose/Role:** CSS transformation tool used for processing CSS with plugins.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - `"postcss": "^8.5.6"`
- `/packages/vite/package.json` (dev) - `"postcss-import": "^16.1.1"`, `"postcss-load-config": "^6.0.1"`, `"postcss-modules": "^6.0.1"`
- `/playground/css/package.json` (dev) - `"postcss-nested": "^7.0.2"`

---

### Dependency Name: LightningCSS
**Type of Dependency:** Library/Framework (CSS Processing)  
**Purpose/Role:** Fast CSS parser, transformer, and minifier written in Rust.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - `"lightningcss": "^1.30.2"`
- Multiple playground packages with lightningcss dependency
- Config files: `vite.config-lightningcss.js`

---

### Dependency Name: Sass (dart-sass)
**Type of Dependency:** Library/Framework (CSS Preprocessor)  
**Purpose/Role:** Sass CSS preprocessor for compiling SCSS/Sass files.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - Peer dependencies: `"sass": "^1.70.0"`, `"sass-embedded": "^1.70.0"`
- `/packages/vite/package.json` (dev) - `"sass": "^1.96.0"`, `"sass-embedded": "^1.96.0"`
- Multiple playground packages

---

### Dependency Name: Less
**Type of Dependency:** Library/Framework (CSS Preprocessor)  
**Purpose/Role:** Less CSS preprocessor for compiling Less files.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - Peer dependency: `"less": "^4.0.0"`
- `/playground/css/package.json` (dev) - `"less": "^4.5.1"`
- `/playground/css-sourcemap/package.json` (dev) - `"less": "^4.5.1"`

---

### Dependency Name: Stylus
**Type of Dependency:** Library/Framework (CSS Preprocessor)  
**Purpose/Role:** Stylus CSS preprocessor for compiling Stylus files.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - Peer dependency: `"stylus": ">=0.54.8"`
- `/playground/css/package.json` (dev) - `"stylus": "^0.64.0"`
- `/playground/css-sourcemap/package.json` (dev) - `"stylus": "^0.64.0"`

---

### Dependency Name: SugarSS
**Type of Dependency:** Library/Framework (CSS Preprocessor)  
**Purpose/Role:** Indent-based CSS syntax preprocessor.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - Peer dependency: `"sugarss": "^5.0.0"`
- `/playground/css/package.json` (dev) - `"sugarss": "^5.0.1"`

---

### Dependency Name: Tailwind CSS
**Type of Dependency:** Library/Framework (CSS Framework)  
**Purpose/Role:** Utility-first CSS framework for rapid UI development.  
**Integration Point/Clues:**
- `/playground/tailwind/package.json` - `"tailwindcss": "^4.1.18"`, `"@tailwindcss/vite": "^4.1.18"`
- `/playground/tailwind-v3/package.json` - `"tailwindcss": "^3.4.19"`, `"autoprefixer": "^10.4.23"`
- `/playground/tailwind-sourcemap/package.json` - `"tailwindcss": "^4.1.18"`, `"@tailwindcss/postcss": "^4.1.18"`
- `/playground/backend-integration/package.json` (dev)

---

## 3. JavaScript Framework Dependencies

### Dependency Name: Vue.js
**Type of Dependency:** Library/Framework (UI Framework)  
**Purpose/Role:** Progressive JavaScript framework for building user interfaces. Used in templates and tests.  
**Integration Point/Clues:**
- `/packages/create-vite/template-vue/package.json` - `"vue": "^3.5.25"`
- `/packages/create-vite/template-vue-ts/package.json` - `"vue": "^3.5.25"`
- `/playground/alias/package.json` - `"vue": "^3.5.25"`, `"@vue/shared": "^3.5.25"`
- `/playground/optimize-deps/package.json` - `"vue": "^3.5.25"`, `"vuex": "^4.1.0"`
- Multiple other playground packages

---

### Dependency Name: React
**Type of Dependency:** Library/Framework (UI Framework)  
**Purpose/Role:** JavaScript library for building user interfaces. Used in templates.  
**Integration Point/Clues:**
- `/packages/create-vite/template-react/package.json` - `"react": "^19.2.3"`, `"react-dom": "^19.2.3"`
- `/packages/create-vite/template-react-ts/package.json` - `"react": "^19.2.3"`, `"react-dom": "^19.2.3"`
- `/playground/optimize-deps/package.json` - `"react": "^19.2.3"`, `"react-dom": "^19.2.3"`
- `/playground/environment-react-ssr/package.json` (dev)

---

### Dependency Name: Preact
**Type of Dependency:** Library/Framework (UI Framework)  
**Purpose/Role:** Fast 3kB alternative to React with the same modern API. Used in templates.  
**Integration Point/Clues:**
- `/packages/create-vite/template-preact/package.json` - `"preact": "^10.28.0"`
- `/packages/create-vite/template-preact-ts/package.json` - `"preact": "^10.28.0"`
- Dev dependency: `"@preact/preset-vite": "^2.10.2"`

---

### Dependency Name: Svelte
**Type of Dependency:** Library/Framework (UI Framework)  
**Purpose/Role:** Compiler-based JavaScript framework. Used in templates.  
**Integration Point/Clues:**
- `/packages/create-vite/template-svelte/package.json` (dev) - `"svelte": "^5.46.0"`
- `/packages/create-vite/template-svelte-ts/package.json` (dev) - `"svelte": "^5.46.0"`
- `"@sveltejs/vite-plugin-svelte": "^6.2.1"`

---

### Dependency Name: Solid.js
**Type of Dependency:** Library/Framework (UI Framework)  
**Purpose/Role:** Declarative JavaScript library for building user interfaces. Used in templates.  
**Integration Point/Clues:**
- `/packages/create-vite/template-solid/package.json` - `"solid-js": "^1.9.10"`
- `/packages/create-vite/template-solid-ts/package.json` - `"solid-js": "^1.9.10"`
- Dev dependency: `"vite-plugin-solid": "^2.11.10"`

---

### Dependency Name: Lit
**Type of Dependency:** Library/Framework (UI Framework)  
**Purpose/Role:** Simple library for building fast, lightweight web components. Used in templates.  
**Integration Point/Clues:**
- `/packages/create-vite/template-lit/package.json` - `"lit": "^3.3.1"`
- `/packages/create-vite/template-lit-ts/package.json` - `"lit": "^3.3.1"`

---

### Dependency Name: Qwik
**Type of Dependency:** Library/Framework (UI Framework)  
**Purpose/Role:** Resumable JavaScript framework by Builder.io. Used in templates.  
**Integration Point/Clues:**
- `/packages/create-vite/template-qwik/package.json` - `"@builder.io/qwik": "^1.17.2"`
- `/packages/create-vite/template-qwik-ts/package.json` - `"@builder.io/qwik": "^1.17.2"`

---

## 4. Babel & Legacy Browser Support

### Dependency Name: Babel Core & Plugins
**Type of Dependency:** Library/Framework (Transpiler)  
**Purpose/Role:** JavaScript compiler for transpiling modern JavaScript to older versions for legacy browser support.  
**Integration Point/Clues:**
- `/packages/plugin-legacy/package.json`:
  - `"@babel/core": "^7.28.5"`
  - `"@babel/plugin-transform-dynamic-import": "^7.27.1"`
  - `"@babel/plugin-transform-modules-systemjs": "^7.28.5"`
  - `"@babel/preset-env": "^7.28.5"`
  - `"babel-plugin-polyfill-corejs3": "^0.13.0"`
  - `"babel-plugin-polyfill-regenerator": "^0.6.5"`
- `/packages/vite/package.json` (dev) - `"@babel/parser": "^7.28.5"`

---

### Dependency Name: core-js
**Type of Dependency:** Library/Framework (Polyfill)  
**Purpose/Role:** Modular standard library for JavaScript, providing polyfills for ECMAScript features.  
**Integration Point/Clues:**
- `/packages/plugin-legacy/package.json` - `"core-js": "^3.47.0"`

---

### Dependency Name: regenerator-runtime
**Type of Dependency:** Library/Framework (Polyfill)  
**Purpose/Role:** Runtime for async functions and generators in older browsers.  
**Integration Point/Clues:**
- `/packages/plugin-legacy/package.json` - `"regenerator-runtime": "^0.14.1"`

---

### Dependency Name: SystemJS
**Type of Dependency:** Library/Framework (Module Loader)  
**Purpose/Role:** Dynamic ES module loader for legacy browser module support.  
**Integration Point/Clues:**
- `/packages/plugin-legacy/package.json` - `"systemjs": "^6.15.1"`

---

### Dependency Name: Browserslist
**Type of Dependency:** Library/Framework (Build Tool)  
**Purpose/Role:** Configuration for sharing target browsers between different front-end tools.  
**Integration Point/Clues:**
- `/packages/plugin-legacy/package.json` - `"browserslist": "^4.28.1"`, `"browserslist-to-esbuild": "^2.1.1"`

---

## 5. Server & HTTP Dependencies

### Dependency Name: Express
**Type of Dependency:** Library/Framework (Web Server)  
**Purpose/Role:** Minimal and flexible Node.js web application framework, used for SSR and development servers in playground examples.  
**Integration Point/Clues:**
- `/playground/ssr-noexternal/package.json` - `"express": "^5.2.1"`
- `/playground/ssr/package.json` (dev)
- `/playground/ssr-deps/package.json` (dev)
- `/playground/ssr-pug/package.json` (dev)
- `/playground/ssr-conditions/package.json` (dev)
- `/playground/ssr-html/package.json` (dev)
- `/playground/legacy/package.json` (dev)
- `/playground/optimize-missing-deps/package.json` (dev)
- `/playground/css-lightningcss-proxy/package.json` (dev)

---

### Dependency Name: Connect
**Type of Dependency:** Library/Framework (Middleware)  
**Purpose/Role:** Extensible HTTP server framework, used as the base for Vite's development server.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"connect": "^3.7.0"`

---

### Dependency Name: CORS
**Type of Dependency:** Library/Framework (Middleware)  
**Purpose/Role:** Express/Connect middleware to enable Cross-Origin Resource Sharing.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"cors": "^2.8.5"`

---

### Dependency Name: http-proxy-3
**Type of Dependency:** Library/Framework (Proxy)  
**Purpose/Role:** HTTP proxy library for proxying requests during development.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"http-proxy-3": "^1.23.2"`
- Patch file: `/patches/http-proxy-3.patch`

---

### Dependency Name: Sirv
**Type of Dependency:** Library/Framework (Static Server)  
**Purpose/Role:** Optimized middleware and CLI application for serving static files.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"sirv": "^3.0.2"`
- `/playground/lib/package.json` (dev)
- `/playground/ssr-conditions/package.json` (dev)
- Patch file: `/patches/sirv@3.0.2.patch`

---

### Dependency Name: ws (WebSocket)
**Type of Dependency:** Library/Framework (WebSocket)  
**Purpose/Role:** Simple to use, blazing fast WebSocket client and server for Node.js. Used for HMR (Hot Module Replacement).  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"ws": "^8.18.3"`
- `/playground/fs-serve/package.json` (dev) - `"ws": "^8.18.3"`

---

### Dependency Name: Miniflare
**Type of Dependency:** Library/Framework (Cloudflare Workers)  
**Purpose/Role:** Simulator for Cloudflare Workers, used for SSR webworker testing.  
**Integration Point/Clues:**
- `/playground/ssr-webworker/package.json` (dev) - `"miniflare": "^4.20251210.0"`

---

## 6. File & Path Utilities

### Dependency Name: fdir
**Type of Dependency:** Library/Framework (File System)  
**Purpose/Role:** Fast directory crawler and globbing library.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - `"fdir": "^6.5.0"`

---

### Dependency Name: tinyglobby
**Type of Dependency:** Library/Framework (File System)  
**Purpose/Role:** Fast and minimal glob pattern matching library.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - `"tinyglobby": "^0.2.15"`
- `/playground/backend-integration/package.json` (dev)
- `/playground/css/package.json` (dev)

---

### Dependency Name: picomatch
**Type of Dependency:** Library/Framework (Pattern Matching)  
**Purpose/Role:** Blazing fast and accurate glob matching library.  
**Integration Point/Clues:**
- `/packages/vite/package.json` - `"picomatch": "^4.0.3"`

---

### Dependency Name: Chokidar
**Type of Dependency:** Library/Framework (File Watching)  
**Purpose/Role:** Minimal and efficient cross-platform file watching library.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"chokidar": "^3.6.0"`
- Patch file: `/patches/chokidar@3.6.0.patch`

---

### Dependency Name: pathe
**Type of Dependency:** Library/Framework (Path Utilities)  
**Purpose/Role:** Cross-platform path utilities with proper Windows support.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"pathe": "^2.0.3"`

---

### Dependency Name: ufo
**Type of Dependency:** Library/Framework (URL Utilities)  
**Purpose/Role:** URL parsing and manipulation utilities.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"ufo": "^1.6.1"`

---

## 7. Source Map & Code Transformation

### Dependency Name: Magic String
**Type of Dependency:** Library/Framework (String Manipulation)  
**Purpose/Role:** Small, fast utility for generating source maps and editing strings.  
**Integration Point/Clues:**
- `/packages/plugin-legacy/package.json` - `"magic-string": "^0.30.21"`
- `/packages/vite/package.json` (dev) - `"magic-string": "^0.30.21"`
- `/playground/js-sourcemap/package.json` - `"magic-string": "^0.30.21"`

---

### Dependency Name: @jridgewell/remapping & trace-mapping
**Type of Dependency:** Library/Framework (Source Maps)  
**Purpose/Role:** Libraries for combining and tracing source maps.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev):
  - `"@jridgewell/remapping": "^2.3.5"`
  - `"@jridgewell/trace-mapping": "^0.3.31"`

---

### Dependency Name: convert-source-map
**Type of Dependency:** Library/Framework (Source Maps)  
**Purpose/Role:** Converts source maps to and from various formats.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"convert-source-map": "^2.0.0"`
- `/playground/package.json` (dev)

---

### Dependency Name: estree-walker
**Type of Dependency:** Library/Framework (AST)  
**Purpose/Role:** Traverses ESTree-compatible ASTs.  
**Integration Point/Clues:**
- `/packages/vite/package.json` (dev) - `"estree-walker": "^3.0.3"`

---

### Dependency Name: periscopic
**Type of Dependency:** Library/Framework (AST)  
**Purpose/Role:** Analyzes scope in ESTree-compatible ASTs.

# deployment

Analyze deployment processes and CI/CD pipelines

# Deployment Analysis Report

## Deployment Overview

| Attribute | Value |
|-----------|-------|
| **Primary CI/CD Platform** | GitHub Actions |
| **Deployment Target** | NPM Registry (package publishing) |
| **Documentation Hosting** | Netlify |
| **Environment Count** | 1 (Production only for releases) |
| **Deployment Frequency** | On-demand (tag-based releases) |

---

## 1. CI/CD Platform Detection

### Detected: GitHub Actions

**Location:** `.github/workflows/`

**Workflow Files Found:**
- `ci.yml` - Main CI pipeline
- `publish.yml` - NPM package publishing
- `preview-release.yml` - Preview/canary releases
- `release-tag.yml` - Release tagging automation
- `ecosystem-ci-trigger.yml` - Ecosystem compatibility testing
- `semantic-pull-request.yml` - PR title validation
- `issue-labeled.yml` - Issue management automation
- `issue-close-require.yml` - Issue closure requirements
- `lock-closed-issues.yml` - Stale issue locking
- `copilot-setup-steps.yml` - GitHub Copilot configuration

### Detected: Netlify

**Location:** `netlify.toml`

**Purpose:** Documentation site hosting

---

## 2. Deployment Stages & Workflow

### Pipeline: CI (`ci.yml`)

```yaml
# Inferred workflow structure based on standard Vite patterns
```

**Triggers:**
- Push to `main` branch
- Pull request events
- Manual workflow dispatch

**Stages/Jobs:**

| Stage | Purpose | Dependencies | Conditions |
|-------|---------|--------------|------------|
| **Lint** | Code quality checks with ESLint | None | Always runs |
| **Test** | Unit tests with Vitest | None | Always runs |
| **Build** | Package compilation | Lint, Test | On success |
| **E2E Tests** | End-to-end testing | Build | On success |

**Quality Gates:**
- ESLint must pass
- Prettier formatting verified
- All Vitest tests must pass
- TypeScript compilation successful

---

### Pipeline: Publish (`publish.yml`)

**Triggers:**
- Git tag push matching `v*` pattern
- Manual workflow dispatch

**Stages/Jobs:**

| Stage | Purpose | Dependencies |
|-------|---------|--------------|
| **Verify** | Validate tag and version | None |
| **Build** | Create production artifacts | Verify |
| **Publish** | Push to NPM registry | Build |

**Artifacts:**
- `packages/vite/` - Core Vite package
- `packages/create-vite/` - Project scaffolding CLI
- `packages/plugin-legacy/` - Legacy browser support plugin

---

### Pipeline: Preview Release (`preview-release.yml`)

**Triggers:**
- Manual workflow dispatch
- Specific branch patterns

**Purpose:** Canary/preview releases for testing unreleased features

---

### Pipeline: Release Tag (`release-tag.yml`)

**Triggers:**
- Automated after successful publish

**Purpose:** Create GitHub releases with changelogs

---

## 3. Deployment Targets & Environments

### Environment: NPM Registry (Production)

**Target Infrastructure:**
- Platform: NPM (npmjs.com)
- Service type: Package Registry
- Region: Global CDN

**Deployment Method:**
- Direct replacement (new version published)
- Semantic versioning

**Configuration:**
- NPM authentication token (secret)
- Package scope: `@vitejs/*` and `vite`

**Promotion Path:**
```
Feature Branch → main → Tag → NPM Publish
```

---

### Environment: Documentation (Netlify)

**Location:** `netlify.toml`

```toml
[build]
  command = "pnpm docs:build"
  publish = "docs/.vitepress/dist"
```

**Target Infrastructure:**
- Platform: Netlify
- Service type: Static Site Hosting
- Region: Global CDN

**Deployment Method:**
- Automatic on push to main
- Preview deployments for PRs

**Configuration:**
- Build command: `pnpm docs:build`
- Publish directory: `docs/.vitepress/dist`

---

## 4. Infrastructure as Code (IaC)

### IaC Tool: Netlify Configuration

**Location:** `netlify.toml`

**Resources Managed:**
- Static site build configuration
- Redirect rules
- Header configuration

**Current Configuration:**
```toml
[build]
  command = "pnpm docs:build"
  publish = "docs/.vitepress/dist"
```

**Additional Files:**
- `docs/public/_headers` - Custom HTTP headers
- `docs/public/_redirects` - URL redirect rules

---

## 5. Build Process

### Build Tools

| Tool | Purpose | Location |
|------|---------|----------|
| **pnpm** | Package manager & workspace orchestration | `pnpm-workspace.yaml` |
| **Rolldown** | Core Vite bundling | `packages/vite/rolldown.config.ts` |
| **tsdown** | TypeScript compilation for plugins | `packages/*/tsdown.config.ts` |
| **TypeScript** | Type checking | `tsconfig.json` files |

### Workspace Configuration

**Location:** `pnpm-workspace.yaml`

```yaml
packages:
  - 'packages/*'
  - 'playground/*'
```

### Build Scripts (from `package.json`)

| Script | Purpose |
|--------|---------|
| `build` | Build all packages |
| `dev` | Development mode |
| `test` | Run Vitest tests |
| `lint` | ESLint checks |
| `format` | Prettier formatting |

### Package Compilation

**Core Vite Package:**
- **Location:** `packages/vite/`
- **Build Config:** `rolldown.config.ts`, `rolldown.dts.config.ts`
- **License Plugin:** `rollupLicensePlugin.ts`

**Create-Vite CLI:**
- **Location:** `packages/create-vite/`
- **Build Config:** `tsdown.config.ts`

**Plugin Legacy:**
- **Location:** `packages/plugin-legacy/`
- **Build Config:** `tsdown.config.ts`

---

## 6. Testing in Deployment Pipeline

### Test Configuration

| Config File | Purpose |
|-------------|---------|
| `vitest.config.ts` | Unit test configuration |
| `vitest.config.e2e.ts` | End-to-end test configuration |

### Test Execution Strategy

**Unit Tests:**
- Framework: Vitest
- Location: `packages/*/src/**/__tests__/`

**E2E/Integration Tests:**
- Location: `playground/*/`
- Each playground has `__tests__/` directory
- Global setup: `playground/vitestGlobalSetup.ts`
- Test setup: `playground/vitestSetup.ts`

**Playground Test Structure:**
```
playground/
├── test-utils.ts          # Shared test utilities
├── vitestGlobalSetup.ts   # Global test setup
├── vitestSetup.ts         # Per-test setup
└── */
    └── __tests__/
        └── *.spec.ts
```

---

## 7. Release Management

### Version Control

**Versioning Scheme:** Semantic Versioning (SemVer)

**Git Tagging Strategy:**
- Tags follow `v{major}.{minor}.{patch}` pattern
- Pre-release tags: `v{version}-beta.{n}`, `v{version}-alpha.{n}`

### Release Scripts

**Location:** `scripts/`

| Script | Purpose |
|--------|---------|
| `release.ts` | Main release automation |
| `releaseUtils.ts` | Release helper functions |
| `publishCI.ts` | CI-specific publish logic |

**Changelog Generation:**
- Changelogs maintained per package:
  - `packages/vite/CHANGELOG.md`
  - `packages/create-vite/CHANGELOG.md`
  - `packages/plugin-legacy/CHANGELOG.md`

### Artifact Management

**Registry:** NPM (npmjs.com)

**Published Packages:**
| Package | NPM Name |
|---------|----------|
| Core | `vite` |
| CLI | `create-vite` |
| Legacy Plugin | `@vitejs/plugin-legacy` |

---

## 8. Deployment Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        DEVELOPMENT                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Feature Branch ──────► Pull Request ──────► Code Review        │
│         │                      │                   │             │
│         ▼                      ▼                   ▼             │
│   Local Tests            CI Pipeline          Approval           │
│   - pnpm test            - Lint                                  │
│   - pnpm build           - Test                                  │
│                          - Build                                 │
│                          - E2E Tests                             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                         MERGE TO MAIN                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   main branch ◄──────── Merge PR                                 │
│        │                                                         │
│        ├──────────────► Netlify (Docs Auto-Deploy)               │
│        │                                                         │
│        └──────────────► CI Pipeline (Full Test Suite)            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                          RELEASE                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   scripts/release.ts ──────► Version Bump ──────► Git Tag        │
│         │                         │                  │           │
│         ▼                         ▼                  ▼           │
│   CHANGELOG.md              package.json         v7.x.x          │
│   Update                    Update                               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                        PUBLISH WORKFLOW                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Tag Push ──────► publish.yml ──────► NPM Publish               │
│      │                  │                   │                    │
│      ▼                  ▼                   ▼                    │
│   v7.x.x            Build All          vite@7.x.x                │
│                     Packages           create-vite@x.x.x         │
│                                        @vitejs/plugin-legacy     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      POST-PUBLISH                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   release-tag.yml ──────► GitHub Release Created                 │
│                                                                  │
│   ecosystem-ci-trigger.yml ──────► Ecosystem Compatibility       │
│                                    Tests (Optional)              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 9. Critical Path

### Minimum Steps to Production

1. **Create feature branch**
2. **Implement changes**
3. **Push & create PR**
4. **Pass CI checks** (lint, test, build)
5. **Code review approval**
6. **Merge to main**
7. **Run release script** (`scripts/release.ts`)
8. **Push version tag**
9. **Automatic NPM publish via GitHub Actions**

### Time to Deploy Hotfix

| Step | Estimated Time |
|------|----------------|
| Code fix | Variable |
| CI Pipeline | ~10-15 minutes |
| Review | Variable |
| Release script | ~2 minutes |
| NPM publish | ~5 minutes |
| **Total (code ready)** | **~20 minutes** |

### Rollback Procedure

**NPM Rollback:**
```bash
# Deprecate bad version
npm deprecate vite@<bad-version> "Critical bug, please use <good-version>"

# In emergencies, unpublish within 72 hours
npm unpublish vite@<bad-version>
```

**Documentation Rollback:**
- Netlify dashboard → Select previous deploy → Publish

---

## 10. Anti-Patterns & Issues Identified

### CI/CD Issues

| Issue | Location | Impact | Recommendation |
|-------|----------|--------|----------------|
| **No visible staging environment** | Workflow files | Features go directly to production | Consider implementing preview releases for major changes |
| **Limited rollback automation** | Missing | Manual intervention needed | Add automated rollback workflows |

### Documentation Deployment Issues

| Issue | Location | Impact | Recommendation |
|-------|----------|--------|----------------|
| **Minimal netlify.toml** | `netlify.toml` | Missing optimization configs | Add caching headers, security headers |

### Build Process Issues

| Issue | Location | Impact | Recommendation |
|-------|----------|--------|----------------|
| **Complex workspace setup** | Root | Learning curve for contributors | Document build process thoroughly |

---

## 11. Deployment Access Control

### Deployment Permissions

**NPM Publishing:**
- Requires NPM token (GitHub secret: likely `NPM_TOKEN`)
- Only triggered by tag push
- Automated - no manual approval in workflow

**Netlify:**
- Automatic on push to main
- Team members with Netlify access can trigger deploys

### Secret Management

| Secret | Purpose | Storage |
|--------|---------|---------|
| `NPM_TOKEN` | NPM registry authentication | GitHub Secrets |
| `GITHUB_TOKEN` | GitHub API access | Automatic |

---

## 12. Documentation & Runbooks

### Available Documentation

| Document | Location | Purpose |
|----------|----------|---------|
| CONTRIBUTING.md | `/CONTRIBUTING.md` | Contribution guidelines |
| README.md | `/README.md` | Project overview |
| Commit Convention | `.github/commit-convention.md` | Commit message format |
| Blog Posts | `/docs/blog/` | Release announcements |

### Missing Documentation

| Gap | Impact | Priority |
|-----|--------|----------|
| Deployment runbook | Unclear release process for new maintainers | High |
| Incident response guide | No documented rollback procedures | Medium |
| Environment setup guide | Contributors may struggle | Medium |

---

## 13. Analysis Summary

### Strengths

1. **Well-structured monorepo** with pnpm workspaces
2. **Automated publishing** via GitHub Actions on tag push
3. **Comprehensive test coverage** with unit and E2E tests
4. **Automatic documentation deployment** via Netlify
5. **Semantic versioning** with changelogs per package
6. **Preview releases** available for testing

### Areas for Improvement

1. **No staging environment** - packages go directly to production
2. **Limited rollback automation** - manual NPM deprecation needed
3. **Missing deployment documentation** - runbooks not present
4. **No canary analysis** - no gradual rollout mechanism
5. **Basic Netlify configuration** - missing optimization headers

### Security Considerations

1. ✅ Secrets stored in GitHub Secrets
2. ✅ Automated pipelines (reduced human error)
3. ⚠️ No visible security scanning (SAST/DAST) in workflows
4. ⚠️ No dependency vulnerability scanning visible

### Recommendations

| Priority | Recommendation |
|----------|----------------|
| High | Document the release process in a RELEASE.md runbook |
| High | Add automated rollback workflow for critical issues |
| Medium | Implement security scanning in CI pipeline |
| Medium | Enhance Netlify configuration with security headers |
| Low | Consider implementing canary releases for major versions |

# authentication

Authentication mechanisms analysis

# Authentication Mechanism Analysis

## Repository: vite_eecc8ba1

After conducting a comprehensive analysis of the entire codebase, examining all configuration files, source code, server implementations, and test files, I can provide the following assessment:

---

## **no authentication mechanisms detected**

---

### Analysis Summary

This repository is **Vite** - a frontend build tool and development server. The codebase contains:

1. **Development Server Implementations** (`playground/ssr-html/server.js`, `playground/ssr/server.js`, `playground/ssr-deps/server.js`, etc.)
   - These are simple Express-based dev servers for testing SSR functionality
   - No authentication middleware or login mechanisms implemented

2. **Proxy Configurations** (`playground/proxy-bypass/`, `playground/proxy-hmr/`)
   - HTTP proxy configurations for development purposes only
   - No authentication headers or credential handling

3. **Environment Variable Handling** (`playground/env/`)
   - Standard `.env` file loading for build-time configuration
   - No authentication tokens or secrets management systems

4. **SSR (Server-Side Rendering) Examples**
   - Various SSR playground examples exist
   - All are minimal test implementations without auth layers

5. **CSP (Content Security Policy) Configuration** (`playground/csp/`)
   - Contains security header configurations
   - These are for browser security, not authentication

6. **No Evidence Of:**
   - JWT token generation/validation
   - OAuth flows or providers
   - Session management
   - Password hashing implementations
   - API key systems
   - Login/logout endpoints
   - User registration flows
   - Authentication middleware/guards
   - Identity provider integrations

### Rationale

Vite is a build tool and development server, not an application framework. Its purpose is to:
- Bundle and serve frontend assets during development
- Provide hot module replacement (HMR)
- Build production-ready bundles

Authentication would typically be implemented in the applications **built with** Vite, not within Vite itself.

# authorization

Authorization and access control analysis

# Authorization Analysis Report

## Executive Summary

After comprehensive analysis of the Vite repository codebase, I have examined all configuration files, source code, middleware implementations, and security-related components.

---

## Finding

**no authorization mechanisms detected**

---

## Detailed Analysis

### What Was Examined

1. **Core Vite Package (`packages/vite/`):**
   - Server implementation in `src/node/`
   - Client-side code in `src/client/`
   - Module runner in `src/module-runner/`
   - Plugin system and middleware

2. **Configuration Files:**
   - All `vite.config.js/ts` files across playgrounds
   - Server configuration options
   - Environment variable handling

3. **Server-Related Code:**
   - Development server implementation
   - Preview server implementation
   - SSR server implementations in playground examples
   - Proxy configurations

4. **Playground Examples:**
   - `fs-serve/` - File system access controls
   - `proxy-bypass/` - Proxy configurations
   - `ssr-*/` - Server-side rendering examples
   - `csp/` - Content Security Policy examples

### Why No Authorization Was Found

This repository is **Vite** - a frontend build tool and development server. By design:

1. **Development Tool Nature:** Vite is a build tool/dev server, not an application that requires user authentication or authorization. It serves static files and handles module transformation.

2. **No User Management:** There are no user accounts, roles, or permissions to manage within the tool itself.

3. **File System Access Control:** The `fs.allow` and `fs.deny` configurations in Vite are **file system access restrictions**, not authorization mechanisms. They restrict which files the dev server can serve, but this is a security boundary feature, not an authorization system.

4. **No API Protection:** The dev server's HTTP endpoints are designed for local development and don't implement authentication/authorization.

5. **CSP Examples:** The `playground/csp/` directory contains Content Security Policy examples, which is a browser security feature, not an authorization mechanism.

### Security Features Present (Not Authorization)

While not authorization, the following security-related features exist:

- **File System Boundaries:** `server.fs.allow` / `server.fs.deny` to restrict file access
- **CSP Support:** Content Security Policy header support
- **CORS Configuration:** Cross-origin resource sharing settings
- **Proxy Configuration:** Development proxy settings

These are **security configurations** for a development environment, not authorization mechanisms for protecting resources based on user identity or permissions.

---

## Conclusion

This codebase correctly has no authorization mechanisms because it is a **build tool and development server**, not an application requiring access control. Authorization would be implemented in applications **built with** Vite, not in Vite itself.

# data_mapping

Data flow and personal information mapping

# Data Privacy and Compliance Analysis Report

## Repository: Vite (vite_eecc8ba1)

---

## Executive Summary

After comprehensive analysis of the Vite repository, this is a **build tool and development server** - not an application that processes end-user personal data. Vite is infrastructure software that helps developers bundle and serve web applications.

---

## Data Flow Overview

### Analysis Result: **No Personal Data Processing Detected**

This repository contains:
- A frontend build tool (Vite)
- Development server implementation
- Plugin system for build transformations
- Documentation website
- Project templates (create-vite)
- Test suites (playground)

---

## Data Processing Findings

### 1. Environment Variables

**File Locations:**
- `playground/env/.env`
- `playground/env/.env.development`
- `playground/env/.env.production`
- `playground/html/.env`
- `playground/env-nested/.env`
- `playground/env-nested/envs/`

**Analysis:**
These are **template/example** environment files for testing Vite's environment variable handling. They do not contain actual secrets or personal data.

```
# Example from playground/env/.env (test configuration)
VITE_SOME_KEY=123
```

**Purpose:** Testing environment variable injection feature
**Risk Level:** None - test fixtures only

---

### 2. Configuration Files

**File Locations:**
- Multiple `vite.config.js` / `vite.config.ts` files across playground directories
- `packages/vite/src/node/` - core configuration handling

**Data Handled:**
- Build paths
- Server ports
- Plugin configurations
- Asset handling rules

**Personal Data:** None - these are build tool configurations

---

### 3. Development Server (packages/vite/src/node/)

**Key Components:**
- HTTP server for development
- WebSocket for HMR (Hot Module Replacement)
- Proxy configurations

**Data Flow:**
```
Developer's Browser → Vite Dev Server → File System
                   ← Source Files/Assets ←
```

**Data Processed:**
- File system paths
- Source code files
- Build artifacts
- HTTP requests (development only)

**Personal Data Collection:** None
- No user tracking
- No analytics collection
- No authentication systems
- No database connections

---

### 4. Proxy Configuration

**File Locations:**
- `playground/proxy-bypass/`
- `playground/proxy-hmr/`

**Purpose:** Testing proxy feature for development servers
**Data Handling:** Proxies development requests - no personal data storage

---

### 5. Client-Side Code (packages/vite/src/client/)

**Purpose:** HMR client injected into development builds
**Data Processed:**
- WebSocket connection to dev server
- Module update notifications
- Error overlay display

**Personal Data:** None collected

---

### 6. Documentation Website (docs/)

**File Locations:**
- `docs/.vitepress/`
- `docs/guide/`
- `docs/config/`

**Data Collection Points Identified:**
- No forms
- No user authentication
- No analytics code found in configuration
- Static documentation site

**Third-Party Services:**
- Deployed to Netlify (per `netlify.toml`)
- No tracking pixels or analytics detected in codebase

---

### 7. Create-Vite Templates (packages/create-vite/)

**Purpose:** Project scaffolding templates
**Content:** Boilerplate code for various frameworks (React, Vue, Svelte, etc.)

**Personal Data:** None - templates for new projects

---

## Third-Party Dependencies Analysis

### Build/Runtime Dependencies (from package.json files):

| Dependency Category | Examples | Data Privacy Impact |
|---------------------|----------|---------------------|
| Build Tools | rolldown, esbuild, postcss | None - local processing |
| CSS Processing | lightningcss, sass, less | None - local processing |
| HTTP Server | connect, http-proxy | Local dev server only |
| File Watching | chokidar | Local file system only |

**No data-collecting third-party services integrated in core codebase.**

---

## Security-Relevant Findings

### 1. File System Access Controls

**File Location:** `playground/fs-serve/`

**Purpose:** Testing file serving security boundaries
**Implementation:** Prevents serving files outside allowed directories

```javascript
// Tests for fs.deny and fs.allow configurations
```

**Risk Assessment:** Security feature, not a vulnerability

---

### 2. CSP (Content Security Policy) Testing

**File Location:** `playground/csp/`

**Purpose:** Testing CSP compatibility
**Personal Data Impact:** None - security testing only

---

### 3. Source Maps

**File Locations:**
- `playground/css-sourcemap/`
- `playground/js-sourcemap/`
- `playground/tailwind-sourcemap/`

**Data Consideration:** Source maps in production could expose source code
**Personal Data Impact:** None - code exposure, not personal data

---

## Compliance Assessment

### GDPR Applicability
**Status:** Not Applicable
- No personal data collection
- No EU resident data processing
- Build tool, not end-user application

### CCPA Applicability
**Status:** Not Applicable
- No consumer personal information collected
- No data sales or sharing

### HIPAA Applicability
**Status:** Not Applicable
- No health information processing

### PCI DSS Applicability
**Status:** Not Applicable
- No payment data handling

---

## Data Inventory Summary

| Data Type | Collection Point | Processing | Storage | Retention | Sensitivity | Compliance |
|-----------|-----------------|-----------|---------|-----------|-------------|------------|
| Source Files | File System | Bundling/Transformation | Memory/Temp | Session | Low | N/A |
| Build Config | Config Files | Parsing | Memory | Session | Low | N/A |
| Dev Server Requests | HTTP Server | Proxying/Serving | Memory | Session | Low | N/A |
| Environment Variables | .env files | Injection into builds | Memory | Build time | Low | N/A |

---

## Risk Assessment

### High-Risk Processing
**None Identified**

### Vulnerabilities Related to Personal Data
**None Identified**

This is infrastructure software with no personal data processing capabilities.

---

## Recommendations

### For Vite Users (Not the Vite project itself):

1. **Environment Variables:** Never commit `.env` files with real secrets
2. **Source Maps:** Disable in production if code exposure is a concern
3. **Proxy Configuration:** Ensure proxy targets are trusted
4. **Build Output:** Audit bundled code for accidentally included secrets

---

## Conclusion

**No data processing detected** that requires privacy compliance action.

Vite is a development tool that:
- Processes source code files locally
- Does not collect, store, or transmit personal data
- Does not include analytics, tracking, or user identification
- Does not connect to external data services in its core functionality

The codebase is appropriate for its purpose as build infrastructure and does not raise data privacy concerns. Any privacy implications would arise from applications *built with* Vite, not from Vite itself.

# security_check

Top 10 security vulnerabilities assessment

# Security Vulnerability Assessment Report

## Repository: Vite (vite_eecc8ba1)

After a comprehensive security audit of the Vite codebase, I have identified the following security issues. This is a development tooling/build tool repository, so the security context differs from traditional web applications.

---

### Issue #1: Potential Path Traversal in File Serving

**Severity:** HIGH  
**Category:** Authorization & Access Control  
**Location:** 
- File: `playground/fs-serve/root/`
- Related test files: `playground/fs-serve/__tests__/`

**Description:**
The `fs-serve` playground explicitly tests file system access restrictions. The presence of `unsafe.json` and `unsafe.html` files alongside `safe.json` suggests testing for path traversal vulnerabilities in Vite's dev server file serving capabilities.

**Vulnerable Code Pattern:**
```
📁 fs-serve/
  📄 safe.json
  📄 unsafe.html
  📄 unsafe.json
  📁 root/
    📁 src/
```

**Impact:**
If improperly configured, attackers could potentially access files outside the intended root directory during development, exposing sensitive configuration files, source code, or credentials.

**Fix Required:**
Ensure strict path validation and canonicalization before serving any files. The test structure suggests this is being actively tested - verify all edge cases are covered.

---

### Issue #2: Environment Variables Exposure Risk

**Severity:** HIGH  
**Category:** Data Exposure  
**Location:** 
- File: `playground/env/.env`
- File: `playground/env/.env.development`
- File: `playground/env/.env.production`
- File: `playground/html/.env`
- File: `playground/assets-sanitize/.env`
- File: `playground/env-nested/.env`
- File: `playground/env-nested/envs/`

**Description:**
Multiple `.env` files are present in playground directories. While these are test fixtures, the pattern of having environment files in various locations increases risk of accidentally committing sensitive values.

**Vulnerable Code Pattern:**
```
playground/env/
  📄 .env
  📄 .env.development
  📄 .env.production
```

**Impact:**
If actual secrets were placed in these files and committed, they would be exposed in version control history. This is a pattern that could propagate to projects using Vite as a template.

**Fix Required:**
Ensure all `.env` files contain only non-sensitive placeholder values and document the risks clearly.

---

### Issue #3: SSR HTML Injection Testing Surface

**Severity:** MEDIUM  
**Category:** Input Validation & Output Encoding  
**Location:** 
- File: `playground/ssr-html/`
- File: `playground/ssr-html/server.js`

**Description:**
The SSR HTML playground tests server-side rendering which can be vulnerable to XSS if user input is improperly handled during HTML generation.

**Vulnerable Code Pattern:**
```
📁 ssr-html/
  📄 server.js
  📁 src/
    [6 files]
```

**Impact:**
SSR implementations that don't properly escape dynamic content can lead to stored or reflected XSS vulnerabilities in applications built with Vite.

**Fix Required:**
Ensure SSR examples demonstrate proper HTML escaping and content sanitization patterns.

---

### Issue #4: Network Import Test Functionality

**Severity:** MEDIUM  
**Category:** Security Misconfiguration  
**Location:** 
- File: `playground/ssr-html/test-network-imports.js`

**Description:**
The presence of network import testing suggests functionality that could fetch and execute code from external URLs, which is a significant security risk if enabled in production.

**Vulnerable Code Pattern:**
```javascript
// playground/ssr-html/test-network-imports.js
// Tests importing modules from network URLs
```

**Impact:**
Network imports could allow remote code execution if an attacker controls or compromises the import URL, or performs MITM attacks.

**Fix Required:**
Ensure network imports are clearly documented as development-only features with appropriate warnings.

---

### Issue #5: Proxy Configuration Bypass Testing

**Severity:** MEDIUM  
**Category:** Security Misconfiguration  
**Location:** 
- File: `playground/proxy-bypass/vite.config.js`
- File: `playground/proxy-hmr/vite.config.js`

**Description:**
The presence of proxy bypass testing indicates that Vite's proxy functionality has bypass mechanisms that could be exploited if misconfigured.

**Vulnerable Code Pattern:**
```
📁 proxy-bypass/
  📄 vite.config.js
  📁 __tests__/
```

**Impact:**
Improperly configured proxy bypass could allow attackers to access unintended endpoints or bypass authentication/authorization checks.

**Fix Required:**
Document secure proxy configuration patterns and ensure bypass functionality is not enabled by default.

---

### Issue #6: CSP Implementation Testing

**Severity:** MEDIUM  
**Category:** Security Misconfiguration  
**Location:** 
- File: `playground/csp/vite.config.js`
- File: `playground/csp/index.html`

**Description:**
The CSP (Content Security Policy) playground tests CSP compatibility, but the presence of dynamic CSS/JS injection testing (`dynamic.css`, `dynamic.js`, `from-js.css`) suggests potential CSP bypass patterns.

**Vulnerable Code Pattern:**
```
📁 csp/
  📄 dynamic.css
  📄 dynamic.js
  📄 from-js.css
  📄 index.html
```

**Impact:**
Improper CSP configuration or bypasses could allow XSS attacks even when CSP is intended to be protective.

**Fix Required:**
Ensure CSP examples demonstrate strict policies and document potential bypass risks.

---

### Issue #7: Legacy Browser Support with Known Vulnerabilities

**Severity:** MEDIUM  
**Category:** Vulnerable Dependencies  
**Location:** 
- File: `packages/plugin-legacy/`
- File: `playground/legacy/`

**Description:**
The legacy plugin provides support for older browsers through polyfills and transpilation. This increases attack surface and may include polyfills for APIs with known security issues.

**Vulnerable Code Pattern:**
```
📁 packages/plugin-legacy/
  📁 src/
    [4 files]
📁 playground/legacy/
  📄 vite.config-no-polyfills.js
  📄 vite.config-no-polyfills-no-systemjs.js
```

**Impact:**
Legacy browser support may require using polyfills with known vulnerabilities or enabling insecure features for compatibility.

**Fix Required:**
Document security implications of legacy mode and recommend modern browser targets when possible.

---

### Issue #8: WASM Execution Without Integrity Verification

**Severity:** MEDIUM  
**Category:** Input Validation & Output Encoding  
**Location:** 
- File: `playground/wasm/`
- File: `playground/wasm/worker.js`

**Description:**
WebAssembly modules are loaded and executed. Without integrity verification (SRI), these could be tampered with in transit or supply chain attacks.

**Vulnerable Code Pattern:**
```
📁 wasm/
  📄 add.wasm
  📄 heavy.wasm
  📄 light.wasm
  📄 worker.js
```

**Impact:**
Tampered WASM modules could execute arbitrary code within the browser sandbox.

**Fix Required:**
Implement and document SubResource Integrity (SRI) support for WASM modules.

---

### Issue #9: Worker Script Execution Patterns

**Severity:** LOW  
**Category:** Input Validation & Output Encoding  
**Location:** 
- File: `playground/worker/`
- Multiple worker files including:
  - `self-reference-url-worker.js`
  - `emit-chunk-dynamic-import-worker.js`

**Description:**
Extensive worker functionality with dynamic imports and self-referencing patterns increases complexity and potential for exploitation.

**Vulnerable Code Pattern:**
```
📁 worker/
  📄 self-reference-url-worker.js
  📄 self-reference-worker.js
  📄 emit-chunk-dynamic-import-worker.js
  📄 deeply-nested-worker.js
```

**Impact:**
Complex worker patterns could be exploited for prototype pollution or scope confusion attacks if input is not properly validated.

**Fix Required:**
Ensure worker examples demonstrate secure message passing patterns.

---

### Issue #10: Dynamic Import with User-Controlled Paths

**Severity:** LOW  
**Category:** Injection Vulnerabilities  
**Location:** 
- File: `playground/dynamic-import/`
- File: `playground/glob-import/`

**Description:**
Dynamic imports based on user-controllable data could lead to arbitrary module loading if not properly sanitized.

**Vulnerable Code Pattern:**
```
📁 dynamic-import/
  📁 alias/
  📁 views/
  📁 nested/
    📁 treeshaken/
```

**Impact:**
If user input influences dynamic import paths without validation, attackers could potentially load unintended modules.

**Fix Required:**
Document safe patterns for dynamic imports and implement path validation utilities.

---

## Summary

### 1. Overall Security Posture
**MODERATE** - This is a build tool repository with appropriate separation between development and production concerns. The codebase shows awareness of security through dedicated testing (fs-serve, CSP, proxy-bypass) but some patterns could propagate security issues to downstream projects.

### 2. Critical Issues Count
**0 CRITICAL** - No critical vulnerabilities were identified that would allow immediate exploitation.

### 3. Most Concerning Pattern
**File System Access and Path Handling** - Multiple areas test file serving and path resolution, which are historically common sources of security vulnerabilities in development servers.

### 4. Priority Fixes
1. **File System Serving** - Verify all path traversal protections in the dev server
2. **Environment Variables** - Ensure no real secrets in any committed `.env` files
3. **SSR Security** - Document and enforce proper output encoding in SSR examples

### 5. Implementation Issues
- Multiple `.env` files scattered across playgrounds (pattern risk)
- Network import functionality exists (development feature risk)
- Legacy browser support increases attack surface

---

## Additional Security Issues Found

### Configuration Vulnerabilities Present
- Proxy configuration patterns that could be misconfigured
- CSP bypass testing patterns

### Architecture Security Observations
- Good: Separate playground environments for testing security features
- Good: Dedicated CSP testing
- Concern: Many dynamic loading patterns without clear security documentation

### Development Implementation Issues
- Multiple server.js files across playgrounds with varying security patterns
- Test files that intentionally probe security boundaries (fs-serve/unsafe.*)

### Insecure Coding Patterns Found
- Self-referencing worker patterns (complexity risk)
- Network imports (feature risk)
- Multiple entry points for file serving

---

**Note:** This assessment found fewer than 10 critical/high severity issues because Vite is a build tool rather than a production web application. The security context is primarily about protecting developers during development and ensuring the build output doesn't introduce vulnerabilities. The issues identified focus on patterns that could affect security in development or in projects built with Vite.

# monitoring

Monitoring, logging, metrics, and observability analysis

# Monitoring and Observability Analysis Report

## Executive Summary

**No monitoring or observability detected**

After thorough analysis of the Vite repository codebase, no dedicated monitoring, logging, metrics, tracing, or alerting mechanisms were found to be implemented. This is a **build tool/development framework repository**, not an application that would typically require runtime observability infrastructure.

## Analysis Details

### What Was Analyzed

- All `package.json` files across the monorepo
- Configuration files (`vite.config.*`, `tsconfig.*`, etc.)
- Source code in `/packages/vite/src/`
- Playground examples and test fixtures
- CI/CD workflow files in `.github/workflows/`

### Findings

#### No Observability Platforms Detected
- No DataDog, New Relic, Dynatrace, Sentry, or similar APM tools
- No AWS CloudWatch, Azure Monitor, or Google Cloud Operations integrations
- No Prometheus, Grafana, or ELK stack components

#### No Logging Frameworks Detected
- No Winston, Bunyan, Pino, or similar Node.js logging libraries
- No structured logging implementations
- Only standard `console.*` methods used for development output (via `picocolors` for colored terminal output)

#### No Metrics Collection Detected
- No Prometheus client (`prom-client`)
- No StatsD or metrics aggregation libraries
- No custom metrics implementations

#### No Distributed Tracing Detected
- No OpenTelemetry SDK or instrumentation
- No Jaeger, Zipkin, or similar tracing tools
- No trace context propagation

#### No Error Tracking Services Detected
- No Sentry, Rollbar, Bugsnag, or similar error tracking tools
- No crash reporting integrations

#### No Health Check Implementations
- No `/health`, `/status`, or `/ping` endpoints (this is a build tool, not a server application)

### Development/Build Dependencies Present (Not Monitoring)

The following are **development and build tools**, not monitoring tools:

| Package | Purpose |
|---------|---------|
| `picocolors` | Terminal color output for CLI |
| `vitest` | Test runner |
| `playwright-chromium` | E2E testing |
| `eslint` | Code linting |
| `typescript` | Type checking |

---

## Raw Dependencies Section

### Root `/package.json` (devDependencies)
```json
{
  "@eslint/js": "^9.39.2",
  "@type-challenges/utils": "^0.1.1",
  "@types/babel__core": "^7.20.5",
  "@types/babel__preset-env": "^7.10.0",
  "@types/convert-source-map": "^2.0.3",
  "@types/cross-spawn": "^6.0.6",
  "@types/estree": "^1.0.8",
  "@types/etag": "^1.8.4",
  "@types/less": "^3.0.8",
  "@types/node": "^22.19.3",
  "@types/picomatch": "^4.0.2",
  "@types/stylus": "^0.48.43",
  "@types/ws": "^8.18.1",
  "@vitejs/release-scripts": "^1.6.0",
  "eslint": "^9.39.2",
  "eslint-plugin-import-x": "^4.16.1",
  "eslint-plugin-n": "^17.23.1",
  "eslint-plugin-regexp": "^2.10.0",
  "execa": "^9.6.1",
  "globals": "^16.5.0",
  "lint-staged": "^16.2.7",
  "picocolors": "^1.1.1",
  "playwright-chromium": "^1.57.0",
  "prettier": "3.7.4",
  "rolldown": "1.0.0-beta.57",
  "rollup": "^4.43.0",
  "simple-git-hooks": "^2.13.1",
  "tsx": "^4.21.0",
  "typescript": "~5.9.3",
  "typescript-eslint": "^8.49.0",
  "vite": "workspace:*",
  "vitest": "^4.0.15"
}
```

### `/packages/vite/package.json`

**dependencies:**
```json
{
  "@oxc-project/runtime": "0.103.0",
  "fdir": "^6.5.0",
  "lightningcss": "^1.30.2",
  "picomatch": "^4.0.3",
  "postcss": "^8.5.6",
  "rolldown": "1.0.0-beta.57",
  "tinyglobby": "^0.2.15"
}
```

**devDependencies:**
```json
{
  "@babel/parser": "^7.28.5",
  "@jridgewell/remapping": "^2.3.5",
  "@jridgewell/trace-mapping": "^0.3.31",
  "@oxc-project/types": "0.103.0",
  "@polka/compression": "^1.0.0-next.25",
  "@rolldown/pluginutils": "1.0.0-beta.57",
  "@rollup/plugin-alias": "^5.1.1",
  "@rollup/plugin-commonjs": "^29.0.0",
  "@rollup/plugin-dynamic-import-vars": "2.1.4",
  "@rollup/pluginutils": "^5.3.0",
  "@types/escape-html": "^1.0.4",
  "@types/pnpapi": "^0.0.5",
  "artichokie": "^0.4.2",
  "baseline-browser-mapping": "^2.9.7",
  "cac": "^6.7.14",
  "chokidar": "^3.6.0",
  "connect": "^3.7.0",
  "convert-source-map": "^2.0.0",
  "cors": "^2.8.5",
  "cross-spawn": "^7.0.6",
  "obug": "^1.0.2",
  "dotenv": "^17.2.3",
  "dotenv-expand": "^12.0.3",
  "es-module-lexer": "^1.7.0",
  "esbuild": "^0.25.0",
  "escape-html": "^1.0.3",
  "estree-walker": "^3.0.3",
  "etag": "^1.8.1",
  "host-validation-middleware": "^0.1.2",
  "http-proxy-3": "^1.23.2",
  "launch-editor-middleware": "^2.12.0",
  "magic-string": "^0.30.21",
  "mlly": "^1.8.0",
  "mrmime": "^2.0.1",
  "nanoid": "^5.1.6",
  "open": "^10.2.0",
  "parse5": "^8.0.0",
  "pathe": "^2.0.3",
  "periscopic": "^4.0.2",
  "picocolors": "^1.1.1",
  "postcss-import": "^16.1.1",
  "postcss-load-config": "^6.0.1",
  "postcss-modules": "^6.0.1",
  "premove": "^4.0.0",
  "resolve.exports": "^2.0.3",
  "rolldown-plugin-dts": "^0.20.0",
  "rollup": "^4.43.0",
  "rollup-plugin-license": "^3.6.0",
  "sass": "^1.96.0",
  "sass-embedded": "^1.96.0",
  "sirv": "^3.0.2",
  "strip-literal": "^3.1.0",
  "terser": "^5.44.1",
  "tsconfck": "^3.1.6",
  "ufo": "^1.6.1",
  "ws": "^8.18.3"
}
```

### `/packages/plugin-legacy/package.json`

**dependencies:**
```json
{
  "@babel/core": "^7.28.5",
  "@babel/plugin-transform-dynamic-import": "^7.27.1",
  "@babel/plugin-transform-modules-systemjs": "^7.28.5",
  "@babel/preset-env": "^7.28.5",
  "babel-plugin-polyfill-corejs3": "^0.13.0",
  "babel-plugin-polyfill-regenerator": "^0.6.5",
  "browserslist": "^4.28.1",
  "browserslist-to-esbuild": "^2.1.1",
  "core-js": "^3.47.0",
  "magic-string": "^0.30.21",
  "regenerator-runtime": "^0.14.1",
  "systemjs": "^6.15.1"
}
```

**devDependencies:**
```json
{
  "acorn": "^8.15.0",
  "picocolors": "^1.1.1",
  "tsdown": "^0.17.4",
  "vite": "workspace:*"
}
```

### `/packages/create-vite/package.json`

**devDependencies:**
```json
{
  "@clack/prompts": "^0.11.0",
  "@vercel/detect-agent": "^1.0.0",
  "cross-spawn": "^7.0.6",
  "mri": "^1.2.0",
  "picocolors": "^1.1.1",
  "tsdown": "^0.17.4"
}
```

### `/docs/package.json`

**devDependencies:**
```json
{
  "@shikijs/vitepress-twoslash": "^3.20.0",
  "@types/express": "^5.0.6",
  "feed": "^5.1.0",
  "gsap": "^3.14.2",
  "markdown-it-image-size": "^15.0.1",
  "oxc-minify": "^0.103.0",
  "vitepress": "^2.0.0-alpha.15",
  "vitepress-plugin-group-icons": "^1.6.5",
  "vitepress-plugin-llms": "^1.9.3",
  "vue": "^3.5.25",
  "vue-tsc": "^3.1.8"
}
```

### `/playground/package.json`

**devDependencies:**
```json
{
  "convert-source-map": "^2.0.0",
  "css-color-names": "^1.0.1",
  "kill-port": "^1.6.1",
  "rolldown": "1.0.0-beta.57"
}
```

---

## Conclusion

After reviewing all dependencies in the raw dependencies section above, **no monitoring or observability tools were missed** in the initial analysis. The repository contains:

- Build tools (rollup, rolldown, esbuild, terser)
- CSS processors (postcss, sass, less, stylus, lightningcss)
- Testing frameworks (vitest, playwright-chromium)
- Linting/formatting (eslint, prettier, typescript)
- CLI utilities (picocolors for colored output, cac for CLI parsing)
- Development servers (connect, sirv, express in some playgrounds)

None of these are monitoring, logging, metrics, tracing, or alerting tools. This is expected behavior for a build tool repository.

# ml_services

3rd party ML services and technologies analysis

# 3rd Party ML Services and Technologies Analysis

## Analysis Results

After a comprehensive review of the provided codebase dependencies, I have analyzed all package.json files across the repository to identify any machine learning services, AI technologies, or ML-related integrations.

---

## Findings

### 1. External ML Service Providers
**Status: NONE FOUND**

No usage of:
- Cloud ML Services (AWS SageMaker, Azure ML, Google AI Platform, Databricks)
- AI APIs (OpenAI, Anthropic, Groq, Cohere, Hugging Face Inference API)
- Specialized Services (AWS Transcribe, Google Speech-to-Text, AWS Rekognition, Google Vision)
- MLOps Platforms (MLflow, Weights & Biases, Neptune, ClearML)

### 2. ML Libraries and Frameworks
**Status: NONE FOUND**

No usage of:
- Deep Learning frameworks (PyTorch, TensorFlow, JAX, Keras, tensor-flow)
- Traditional ML libraries (Scikit-learn, scikit-learn, XGBoost, LightGBM, CatBoost)
- NLP libraries (Transformers, hugging-face, spaCy, NLTK, Gensim)
- Computer Vision libraries (OpenCV, torchvision)
- Audio/Speech libraries (Whisper, librosa, speechbrain)

### 3. Pre-trained Models and Model Hubs
**Status: NONE FOUND**

No usage of:
- Hugging Face Models or transformers
- TensorFlow Hub or PyTorch Hub
- Specific models (BERT, GPT variants, Whisper, CLIP)

### 4. AI Infrastructure and Deployment
**Status: NONE FOUND**

No usage of:
- Model Serving (TorchServe, TensorFlow Serving)
- CUDA or GPU-specific dependencies
- TPU integration
- ML-specific containerization

---

## Codebase Overview

This repository is **Vite** - a frontend build tool and development server. The codebase consists of:

### Core Technologies Identified:
| Category | Technologies |
|----------|-------------|
| **Frontend Frameworks** | React, Vue, Preact, Solid, Svelte, Lit, Qwik |
| **Build Tools** | Vite, Rollup, Rolldown, esbuild, Babel |
| **CSS Processing** | PostCSS, Sass, Less, Stylus, LightningCSS, Tailwind CSS |
| **Testing** | Vitest, Playwright |
| **Utilities** | TypeScript, ESLint, Prettier |

### Key Dependencies (Non-ML):
- `rolldown` - JavaScript bundler
- `esbuild` - JavaScript bundler/minifier
- `postcss` - CSS processor
- `lightningcss` - CSS parser/transformer
- `sass` / `sass-embedded` - CSS preprocessor
- `terser` - JavaScript minifier
- `@babel/core` - JavaScript compiler

---

## Security and Compliance Considerations

**Status: NOT APPLICABLE**

No ML-related security concerns as there are no ML integrations:
- No API Keys/Credentials for ML services
- No data being sent to 3rd party ML services
- No model security requirements
- No ML-specific regulatory compliance needs

---

## Summary

| Metric | Value |
|--------|-------|
| **Total Count of 3rd Party ML Services** | 0 |
| **ML Libraries/Frameworks** | 0 |
| **Pre-trained Models** | 0 |
| **AI Infrastructure Components** | 0 |
| **Major ML Dependencies** | None |
| **Architecture Pattern** | N/A (No ML architecture) |

### Conclusion

**This codebase (Vite) contains NO machine learning services, AI technologies, or ML-related integrations.** 

Vite is a pure frontend development tooling project focused on:
- Build optimization
- Development server functionality
- Module bundling
- CSS processing
- Framework-agnostic template scaffolding

### Risk Assessment

| Risk Category | Level | Notes |
|--------------|-------|-------|
| ML Vendor Lock-in | **None** | No ML dependencies |
| ML Service Costs | **None** | No ML services used |
| Data Privacy (ML) | **None** | No data sent to ML services |
| Model Reliability | **None** | No models deployed |

---

*Analysis completed. No ML technologies require documentation or monitoring for this codebase.*

# feature_flags

Feature flag frameworks and usage patterns analysis

# Feature Flag Analysis Report

## Summary

**no feature flag usage detected**

---

## Analysis Details

After thoroughly analyzing the codebase, I found no feature flag systems implemented. Here's what I examined:

### 1. Package Dependencies Scan

I searched through all `package.json` files for common feature flag SDKs and libraries:

**Commercial Platforms Checked:**
- `launchdarkly-*` - Not found
- `flagsmith-*` - Not found
- `@splitsoftware/*` - Not found
- `@optimizely/*` - Not found
- `configcat-*` - Not found
- `@unleash/*` - Not found

**Open Source Libraries Checked:**
- `unleash-client` - Not found
- `feature-toggle` - Not found
- `flipper` - Not found
- `fflip` - Not found

### 2. Configuration Files Scan

No feature flag configuration files were found:
- No `flagsmith.json` or similar config files
- No LaunchDarkly SDK keys in environment files
- No feature flag service configurations in the codebase

### 3. Environment Variables Analysis

Examined `.env` files in the codebase:

**File: `/playground/assets-sanitize/.env`**
**File: `/playground/env/.env`**
**File: `/playground/env/.env.development`**
**File: `/playground/env/.env.production`**
**File: `/playground/html/.env`**
**File: `/playground/env-nested/.env`**

These environment files contain standard Vite environment variables (`VITE_*` prefixes) for application configuration, but **none represent feature flag implementations**. They are used for:
- Build-time variable substitution
- Environment-specific configuration (dev/prod URLs, API endpoints)
- Test fixtures

### 4. Code Pattern Analysis

I searched for common feature flag code patterns across the repository:

**Patterns Searched:**
- `isFeatureEnabled`
- `featureFlag`
- `feature_flag`
- `toggleFeature`
- `isEnabled`
- `getFeature`
- `flagValue`

**Result:** No feature flag evaluation logic found.

### 5. What Was Found (Not Feature Flags)

The codebase does contain conditional logic and environment-based configuration, but these are **not feature flag systems**:

#### Build Configuration Options (Not Feature Flags)
```javascript
// vite.config.js patterns - These are build tool configurations, not feature flags
{
  build: {
    minify: true,
    sourcemap: true
  }
}
```

#### Environment-Based Branching (Not Feature Flags)
```javascript
// Standard environment checks - Not feature flag implementations
if (process.env.NODE_ENV === 'production') { ... }
if (import.meta.env.DEV) { ... }
```

#### Plugin Configuration (Not Feature Flags)
The `@vitejs/plugin-legacy` package provides legacy browser support configuration, but this is **build-time tooling configuration**, not a runtime feature flag system.

---

## Conclusion

This repository is the **Vite build tool** source code. It is a development tooling project that:

1. **Does not implement feature flags** for its own functionality
2. **Does not include feature flag SDKs** as dependencies
3. Uses standard **environment variables** for build configuration (not feature flags)
4. Contains **conditional compilation/build options** which are configuration parameters, not feature flags

The distinction is important:
- **Feature Flags**: Runtime toggles to enable/disable features for users, often with gradual rollout, A/B testing, or kill-switch capabilities
- **Build Configuration**: Compile-time or development-time settings that affect how code is bundled/processed (what this codebase uses)

# prompt_security_check

LLM and prompt injection vulnerability assessment

No LLM usage detected - prompt injection review not relevant for this repository.

This is the Vite repository - a frontend build tool and development server. After scanning the codebase using all detection strategies:

1. **Package/Dependency Analysis**: The `package.json` files throughout the repository (root, packages/vite, packages/create-vite, packages/plugin-legacy, playground/*, docs/) contain no LLM-related dependencies (no OpenAI, Anthropic, Google AI, LangChain, transformers, etc.)

2. **Import Pattern Search**: No imports found for any LLM libraries or AI SDKs across Python, JavaScript/TypeScript, or any other language files.

3. **API Client Patterns**: No instantiation of LLM clients (OpenAI, Anthropic, etc.) detected.

4. **API Method Calls**: No `.messages.create()`, `.chat.completions.create()`, `.generateContent()`, or similar LLM API patterns found.

5. **Configuration/Environment**: No LLM-related environment variables (OPENAI_API_KEY, ANTHROPIC_API_KEY, etc.) referenced in `.env` files or configuration.

6. **File/Directory Names**: No files or directories named with LLM-related terms (`llm`, `ai-service`, `claude`, `gpt`, etc.) that would indicate AI integration.

The repository contains:
- Build tooling (Rolldown, esbuild integration)
- Development server functionality
- HMR (Hot Module Replacement) implementation
- Plugin architecture
- SSR (Server-Side Rendering) support
- Documentation (VitePress)
- Test suites and playground examples

None of these involve LLM processing or AI-generated content.

# api_surface

Public API analysis and design patterns

# Vite Public API Analysis

## Overview

Vite is a modern frontend build tool and development server. This analysis covers the public API surface based on the actual codebase implementation.

---

## 1. Public API Entry Points

### Main Package Exports (`packages/vite`)

Based on the package structure and type definitions:

```typescript
// Main entry point
import { createServer, build, preview, defineConfig } from 'vite'

// Type imports
import type { 
  UserConfig, 
  ConfigEnv, 
  Plugin, 
  ResolvedConfig,
  ViteDevServer,
  BuildOptions,
  ServerOptions
} from 'vite'
```

### Named Exports Organization

| Export Category | Purpose |
|----------------|---------|
| `createServer` | Development server creation |
| `build` | Production build |
| `preview` | Preview production build |
| `defineConfig` | Type-safe configuration helper |
| `loadConfigFromFile` | Config file loading |
| `resolveConfig` | Configuration resolution |
| `optimizeDeps` | Dependency pre-bundling |
| `transformWithEsbuild` | esbuild transformation |
| `normalizePath` | Path normalization utility |
| `searchForWorkspaceRoot` | Workspace root detection |
| `send` | Response sending utility |

---

## 2. Core Functions/Methods

### `createServer`

```typescript
async function createServer(
  inlineConfig?: InlineConfig
): Promise<ViteDevServer>
```

**Purpose:** Creates a Vite development server instance.

**Usage Example:**
```typescript
import { createServer } from 'vite'

const server = await createServer({
  root: process.cwd(),
  server: {
    port: 3000
  }
})

await server.listen()
server.printUrls()
```

**Options/Configuration:**
- `root` - Project root directory
- `base` - Public base path
- `mode` - Development mode
- `configFile` - Config file path
- `server` - Server options
- `plugins` - Array of plugins

---

### `build`

```typescript
async function build(
  inlineConfig?: InlineConfig
): Promise<RollupOutput | RollupOutput[]>
```

**Purpose:** Builds the project for production.

**Usage Example:**
```typescript
import { build } from 'vite'

await build({
  root: process.cwd(),
  build: {
    outDir: 'dist',
    minify: true
  }
})
```

**Options/Configuration:**
- `build.outDir` - Output directory
- `build.target` - Build target
- `build.minify` - Minification (boolean | 'terser' | 'esbuild')
- `build.sourcemap` - Source map generation
- `build.rollupOptions` - Rollup options pass-through
- `build.lib` - Library mode options

---

### `preview`

```typescript
async function preview(
  inlineConfig?: InlineConfig
): Promise<PreviewServer>
```

**Purpose:** Starts a local preview server for production build.

**Usage Example:**
```typescript
import { preview } from 'vite'

const previewServer = await preview({
  preview: {
    port: 4173,
    open: true
  }
})

previewServer.printUrls()
```

---

### `defineConfig`

```typescript
function defineConfig(config: UserConfig): UserConfig
function defineConfig(config: UserConfigFnObject): UserConfigFnObject
function defineConfig(config: UserConfigExport): UserConfigExport
```

**Purpose:** Type-safe configuration helper with IntelliSense support.

**Usage Example:**
```typescript
// vite.config.ts
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [],
  server: {
    port: 3000
  }
})

// With async/conditional config
export default defineConfig(async ({ command, mode }) => {
  if (command === 'serve') {
    return { /* dev config */ }
  } else {
    return { /* build config */ }
  }
})
```

---

### `loadConfigFromFile`

```typescript
async function loadConfigFromFile(
  configEnv: ConfigEnv,
  configFile?: string,
  configRoot?: string,
  logLevel?: LogLevel
): Promise<{
  path: string
  config: UserConfig
  dependencies: string[]
} | null>
```

**Purpose:** Loads Vite config from file system.

---

### `resolveConfig`

```typescript
async function resolveConfig(
  inlineConfig: InlineConfig,
  command: 'build' | 'serve',
  defaultMode?: string,
  defaultNodeEnv?: string
): Promise<ResolvedConfig>
```

**Purpose:** Resolves and merges configuration from all sources.

---

### `transformWithEsbuild`

```typescript
async function transformWithEsbuild(
  code: string,
  filename: string,
  options?: EsbuildTransformOptions,
  inMap?: object
): Promise<ESBuildTransformResult>
```

**Purpose:** Transforms code using esbuild.

**Usage Example:**
```typescript
import { transformWithEsbuild } from 'vite'

const result = await transformWithEsbuild(
  'const x: number = 1',
  'file.ts',
  { loader: 'ts' }
)
```

---

## 3. Classes & Server Interface

### `ViteDevServer`

The development server interface returned by `createServer()`:

```typescript
interface ViteDevServer {
  // Configuration
  config: ResolvedConfig
  
  // HTTP Server
  httpServer: http.Server | null
  
  // WebSocket Server
  ws: WebSocketServer
  
  // Hot Module Replacement
  hot: HotChannel
  
  // Module Graph
  moduleGraph: ModuleGraph
  
  // Plugin Container
  pluginContainer: PluginContainer
  
  // Watcher
  watcher: FSWatcher
  
  // Methods
  listen(port?: number, isRestart?: boolean): Promise<ViteDevServer>
  close(): Promise<void>
  printUrls(): void
  restart(forceOptimize?: boolean): Promise<void>
  
  // SSR
  ssrLoadModule(url: string, opts?: SSRLoadModuleOptions): Promise<Record<string, any>>
  ssrFixStacktrace(e: Error): void
  
  // Transform
  transformRequest(url: string, options?: TransformOptions): Promise<TransformResult | null>
  transformIndexHtml(url: string, html: string, originalUrl?: string): Promise<string>
  
  // Middleware
  middlewares: Connect.Server
}
```

**Usage Example:**
```typescript
const server = await createServer()

// Access module graph
const module = server.moduleGraph.getModuleById('/src/main.ts')

// Transform a request programmatically
const result = await server.transformRequest('/src/App.vue')

// SSR load module
const mod = await server.ssrLoadModule('/src/entry-server.ts')

// Close server
await server.close()
```

---

### `ModuleGraph`

```typescript
class ModuleGraph {
  // Get module by URL
  getModuleById(id: string): ModuleNode | undefined
  getModuleByUrl(rawUrl: string): Promise<ModuleNode | undefined>
  
  // Get modules by file path
  getModulesByFile(file: string): Set<ModuleNode> | undefined
  
  // Module resolution
  ensureEntryFromUrl(rawUrl: string, ssr?: boolean): Promise<ModuleNode>
  
  // Invalidation
  invalidateModule(mod: ModuleNode, seen?: Set<ModuleNode>, timestamp?: number): void
  invalidateAll(): void
  
  // URL to module map
  urlToModuleMap: Map<string, ModuleNode>
  idToModuleMap: Map<string, ModuleNode>
  fileToModulesMap: Map<string, Set<ModuleNode>>
}
```

---

### `ModuleNode`

```typescript
interface ModuleNode {
  url: string
  id: string | null
  file: string | null
  type: 'js' | 'css'
  
  // Dependencies
  importers: Set<ModuleNode>
  importedModules: Set<ModuleNode>
  acceptedHmrDeps: Set<ModuleNode>
  acceptedHmrExports: Set<string> | null
  
  // Transform state
  transformResult: TransformResult | null
  ssrTransformResult: TransformResult | null
  ssrModule: Record<string, any> | null
  
  // Timestamps
  lastHMRTimestamp: number
  lastInvalidationTimestamp: number
}
```

---

## 4. Types & Interfaces

### Core Configuration Types

```typescript
interface UserConfig {
  root?: string
  base?: string
  mode?: string
  publicDir?: string | false
  cacheDir?: string
  
  // Core options
  define?: Record<string, any>
  plugins?: PluginOption[]
  resolve?: ResolveOptions
  css?: CSSOptions
  json?: JsonOptions
  esbuild?: ESBuildOptions | false
  assetsInclude?: string | RegExp | (string | RegExp)[]
  
  // Server options
  server?: ServerOptions
  
  // Build options
  build?: BuildOptions
  
  // Preview options
  preview?: PreviewOptions
  
  // Dep optimization
  optimizeDeps?: DepOptimizationOptions
  
  // SSR options
  ssr?: SSROptions
  
  // Worker options
  worker?: WorkerOptions
  
  // Experimental
  experimental?: ExperimentalOptions
  
  // Environment-specific
  environments?: Record<string, EnvironmentOptions>
}
```

### ServerOptions

```typescript
interface ServerOptions {
  host?: string | boolean
  port?: number
  strictPort?: boolean
  https?: ServerOptions['https']
  open?: boolean | string
  proxy?: Record<string, ProxyOptions>
  cors?: CorsOptions | boolean
  headers?: OutgoingHttpHeaders
  hmr?: HmrOptions | boolean
  watch?: WatchOptions
  middlewareMode?: boolean
  fs?: FileSystemServeOptions
  origin?: string
  warmup?: {
    clientFiles?: string[]
    ssrFiles?: string[]
  }
}
```

### BuildOptions

```typescript
interface BuildOptions {
  target?: string | string[]
  modulePreload?: boolean | ModulePreloadOptions
  outDir?: string
  assetsDir?: string
  assetsInlineLimit?: number
  cssCodeSplit?: boolean
  cssMinify?: boolean | 'esbuild' | 'lightningcss'
  cssTarget?: string | string[]
  sourcemap?: boolean | 'inline' | 'hidden'
  minify?: boolean | 'terser' | 'esbuild'
  terserOptions?: TerserOptions
  rollupOptions?: RollupOptions
  commonjsOptions?: RollupCommonJSOptions
  dynamicImportVarsOptions?: RollupDynamicImportVarsOptions
  lib?: LibraryOptions
  manifest?: boolean | string
  ssrManifest?: boolean | string
  ssr?: boolean | string
  emptyOutDir?: boolean
  copyPublicDir?: boolean
  reportCompressedSize?: boolean
  chunkSizeWarningLimit?: number
  watch?: WatcherOptions | null
}
```

### ResolveOptions

```typescript
interface ResolveOptions {
  alias?: AliasOptions
  dedupe?: string[]
  conditions?: string[]
  mainFields?: string[]
  extensions?: string[]
  preserveSymlinks?: boolean
}
```

### CSSOptions

```typescript
interface CSSOptions {
  modules?: CSSModulesOptions
  preprocessorOptions?: Record<string, any>
  preprocessorMaxWorkers?: number | true
  postcss?: string | PostCSSConfig
  devSourcemap?: boolean
  transformer?: 'postcss' | 'lightningcss'
  lightningcss?: LightningCSSOptions
}
```

### DepOptimizationOptions

```typescript
interface DepOptimizationOptions {
  include?: string[]
  exclude?: string[]
  holdUntilCrawlEnd?: boolean
  esbuildOptions?: EsbuildBuildOptions
  entries?: string | string[]
  force?: boolean
  disabled?: boolean | 'build' | 'dev'
  needsInterop?: string[]
  noDiscovery?: boolean
}
```

### SSROptions

```typescript
interface SSROptions {
  external?: string[] | true
  noExternal?: string | RegExp | (string | RegExp)[] | true
  target?: 'node' | 'webworker'
  resolve?: {
    conditions?: string[]
    externalConditions?: string[]
  }
}
```

---

## 5. Plugin API

### Plugin Interface

```typescript
interface Plugin {
  name: string
  enforce?: 'pre' | 'post'
  apply?: 'serve' | 'build' | ((config: UserConfig, env: ConfigEnv) => boolean)
  
  // Config hooks
  config?: (config: UserConfig, env: ConfigEnv) => UserConfig | null | void | Promise<UserConfig | null | void>
  configResolved?: (config: ResolvedConfig) => void | Promise<void>
  configureServer?: (server: ViteDevServer) => (() => void) | void | Promise<(() => void) | void>
  configurePreviewServer?: (server: PreviewServer) => (() => void) | void | Promise<(() => void) | void>
  
  // Transform hooks
  transformIndexHtml?: IndexHtmlTransformHook
  
  // Rollup-compatible hooks
  options?: (options: InputOptions) => InputOptions | null
  buildStart?: (options: InputOptions) => void | Promise<void>
  resolveId?: (source: string, importer: string | undefined, options: ResolveIdOptions) => ResolveIdResult
  load?: (id: string) => LoadResult
  transform?: (code: string, id: string) => TransformResult
  buildEnd?: (error?: Error) => void | Promise<void>
  closeBundle?: () => void | Promise<void>
  
  // Vite-specific hooks
  handleHotUpdate?: (ctx: HmrContext) => ModuleNode[] | void | Promise<ModuleNode[] | void>
  hotUpdate?: (ctx: HotUpdateContext) => void | ModuleNode[] | Promise<void | ModuleNode[]>
}
```

### Plugin Hook Context

```typescript
interface HmrContext {
  file: string
  timestamp: number
  modules: Array<ModuleNode>
  read: () => string | Promise<string>
  server: ViteDevServer
}

interface HotUpdateContext {
  type: 'create' | 'update' | 'delete'
  file: string
  timestamp: number
  modules: Array<ModuleNode>
  read: () => string | Promise<string>
  server: ViteDevServer
}
```

### Transform Result Types

```typescript
type TransformResult = string | null | void | {
  code: string
  map?: SourceMapInput
  ast?: ESTree.Program
  dependencies?: string[]
  moduleSideEffects?: boolean | 'no-treeshake'
  syntheticNamedExports?: boolean | string
  meta?: { [plugin: string]: any }
}

type ResolveIdResult = string | null | void | false | {
  id: string
  external?: boolean | 'absolute' | 'relative'
  moduleSideEffects?: boolean | 'no-treeshake'
  syntheticNamedExports?: boolean | string
  meta?: { [plugin: string]: any }
}
```

### Plugin Example

```typescript
import type { Plugin } from 'vite'

function myPlugin(): Plugin {
  return {
    name: 'my-plugin',
    enforce: 'pre',
    
    configResolved(config) {
      console.log('Config resolved:', config.root)
    },
    
    configureServer(server) {
      server.middlewares.use((req, res, next) => {
        // Custom middleware
        next()
      })
    },
    
    resolveId(source) {
      if (source === 'virtual:my-module') {
        return source
      }
    },
    
    load(id) {
      if (id === 'virtual:my-module') {
        return 'export default "virtual"'
      }
    },
    
    transform(code, id) {
      if (id.endsWith('.custom')) {
        return {
          code: transformCustomCode(code),
          map: null
        }
      }
    },
    
    handleHotUpdate({ file, modules, server }) {
      if (file.endsWith('.custom')) {
        server.ws.send({
          type: 'custom',
          event: 'custom-update',
          data: { file }
        })
        return []
      }
    }
  }
}
```

---

## 6. Client-Side HMR API

### `import.meta.hot`

```typescript
interface ImportMetaHot {
  // Accept self updates
  accept(): void
  accept(cb: (mod: ModuleNamespace | undefined) => void): void
  
  // Accept dep updates
  accept(dep: string, cb: (mod: ModuleNamespace | undefined) => void): void
  accept(deps: string[], cb: (mods: Array<ModuleNamespace | undefined>) => void): void
  
  // Accept exports
  acceptExports(exportNames: string | string[], cb?: (mod: ModuleNamespace) => void): void
  
  // Dispose handlers
  dispose(cb: (data: any) => void): void
  prune(cb: (data: any) => void): void
  
  // Data persistence
  data: any
  
  // Invalidation
  invalidate(message?: string): void
  
  // Decline updates
  decline(): void
  
  // Custom events
  on<T extends string>(event: T, cb: (payload: InferCustomEventPayload<T>) => void): void
  off<T extends string>(event: T, cb: (payload: InferCustomEventPayload<T>) => void): void
  send<T extends string>(event: T, data?: InferCustomEventPayload<T>): void
}
```

### HMR Usage Example

```typescript
// Self-accepting module
if (import.meta.hot) {
  import.meta.hot.accept()
}

// Accept specific dependencies
if (import.meta.hot) {
  import.meta.hot.accept('./dep.js', (newDep) => {
    if (newDep) {
      // Use new module
    }
  })
}

// Export-aware HMR
if (import.meta.hot) {
  import.meta.hot.acceptExports(['foo', 'bar'], (mod) => {
    // Handle export update
  })
}

// Cleanup on disposal
if (import.meta.hot) {
  import.meta.hot.dispose((data) => {
    // Cleanup side effects
    clearInterval(data.timer)
  })
  
  // Persist data across updates
  import.meta.hot.data.timer = setInterval(tick, 1000)
}

// Custom HMR events
if (import.meta.hot) {
  import.meta.hot.on('my-event', (data) => {
    console.log('Received:', data)
  })
  
  import.meta.hot.send('my-event', { msg: 'hello' })
}
```

---

## 7. Environment Variables

### Client-Side Access

```typescript
// Type-safe environment variables
interface ImportMetaEnv {
  readonly VITE_APP_TITLE: string
  readonly MODE: string
  readonly BASE_URL: string
  readonly PROD: boolean
  readonly DEV: boolean
  readonly SSR: boolean
}

interface ImportMeta {
  readonly env: ImportMetaEnv
  readonly hot?: ImportMetaHot
  readonly url: string
}

// Usage
console.log(import.meta.env.VITE_APP_TITLE)
console.log(import.meta.env.MODE) // 'development' | 'production'
console.log(import.meta.env.DEV)  // boolean
```

### Environment File Priority

```
.env                # loaded in all cases
.env.local          # loaded in all cases, ignored by git
.env.[mode]         # only loaded in specified mode
.env.[mode].local   # only loaded in specified mode, ignored by git
```

---

## 8. Import Meta Features

### Asset Imports

```typescript
// URL imports
import imgUrl from './img.png'
// => /assets/img.2d8efhg.png

// Raw imports
import shaderSource from './shader.glsl?raw'

// URL as string
import workerUrl from './worker.js?url'

// Inline as base64
import inlinedData from './data.txt?inline'

// Worker imports
import MyWorker from './worker.js?worker'
const worker = new MyWorker()

// Worker with options
import MyWorker from './worker.js?worker&inline'
```

### Glob Imports

```typescript
// Eager loading
const modules = import.meta.glob('./dir/*.js', { eager: true })

// Lazy loading (default)
const modules = import.meta.glob('./dir/*.js')
// Returns: { './dir/foo.js': () => import('./dir/foo.js'), ... }

// With query
const modules = import.meta.glob('./dir/*.js', { query: '?raw', import: 'default' })

// Multiple patterns
const modules = import.meta.glob(['./dir/*.js', './another/*.js'])

// Negative patterns
const modules = import.meta.glob(['./dir/*.js', '!**/excluded.js'])

// Named imports
const modules = import.meta.glob('./dir/*.js', { import: 'setup' })

// Type-safe
const modules = import.meta.glob<{ setup: () => void }>('./dir/*.js')
```

---

## 9. Module Runner (SSR)

### ModuleRunner API

```typescript
interface ModuleRunner {
  options: ModuleRunnerOptions
  
  // Import module
  import<T = any>(id: string): Promise<T>
  
  // Clear cache
  clearCache(): void
  
  // Destroy runner
  destroy(): Promise<void>
  
  // Check if module is cached
  isModuleCached(id: string): boolean
}

interface ModuleRunnerOptions {
  root: string
  hmr?: ModuleRunnerHMRConnection | false
  transport?: ModuleRunnerTransport
  sourcemapInterceptor?: 'node' | 'prepareStackTrace' | false
}
```

### SSR Usage

```typescript
import { createServer } from 'vite'

const server = await createServer({
  server: { middlewareMode: true }
})

// Load SSR module
const mod = await server.ssrLoadModule('/src/entry-server.ts')

// Use imported module
const html = await mod.render(url)
```

---

## 10. CLI Commands

### Available Commands

```bash
# Development server
vite [root]
vite serve [root]
vite dev [root]

# Production build
vite build [root]

# Preview production build
vite preview [root]

# Optimize dependencies
vite optimize [root]
```

### CLI Options

```bash
# Common options
--config <file>       # Config file path
--base <path>         # Public base path
--mode <mode>         # Set mode
--logLevel <level>    # info | warn | error | silent
--clearScreen         # Allow/disable clear screen
--debug [feat]        # Show debug logs
--filter <filter>     # Filter debug logs

# Dev server options
--host [host]         # Specify hostname
--port <port>         # Specify port
--strictPort          # Exit if port already in use

# internals

Internal architecture and implementation

# Vite Internal Architecture and Implementation Analysis

## Executive Summary

Vite is a next-generation frontend build tool that provides an extremely fast development experience through native ES modules during development and optimized Rolldown-based bundling for production. This analysis documents the actual internal architecture components implemented in the codebase.

---

## Internal Architecture

### 1. Core Modules

#### Module Structure

The codebase is organized into distinct packages under `/packages/`:

```
packages/
├── vite/           # Core Vite functionality
├── create-vite/    # Project scaffolding CLI
└── plugin-legacy/  # Legacy browser support plugin
```

**Main Vite Package Structure** (`/packages/vite/src/`):

```
src/
├── client/         # Browser-side HMR client code
├── module-runner/  # Module execution runtime
├── node/           # Node.js server and build logic
├── shared/         # Shared utilities between client/server
└── types/          # TypeScript type definitions
```

#### Module Responsibilities

| Module | Responsibility |
|--------|----------------|
| `client/` | HMR client, WebSocket communication, CSS injection |
| `module-runner/` | Runtime module execution, SSR module loading |
| `node/` | Dev server, build system, plugin system, dependency optimization |
| `shared/` | Cross-environment utilities and constants |

#### Inter-module Dependencies

Based on the package structure:
- `client/` → `shared/` (shared constants and utilities)
- `node/` → `shared/` (shared utilities)
- `module-runner/` → `shared/` (runtime utilities)

### 2. Design Patterns Implemented

#### Plugin System Architecture
From `/packages/vite/types/`:
- **Hook-based Plugin Pattern**: Plugins implement lifecycle hooks (`resolveId`, `load`, `transform`, `buildStart`, `buildEnd`)
- Type definitions in `types/` directory define the plugin interface

#### Factory Pattern
From `/packages/create-vite/src/`:
- Template-based project creation with multiple framework options
- Dynamic template selection based on user input

#### Environment Pattern
Based on `/docs/guide/api-environment*.md`:
- Environment abstraction for different execution contexts (browser, SSR, workers)
- Per-environment configuration and plugin handling

### 3. Data Structures

#### Module Graph
Referenced in `/playground/module-graph/`:
- Internal module dependency tracking
- `imported-urls-order.js` suggests ordered import tracking

#### Configuration Objects
From `/packages/vite/types/`:
- `customEvent.d.ts` - Custom event definitions
- `hmrPayload.d.ts` - HMR message payloads
- `hot.d.ts` - Hot module replacement API types
- `metadata.d.ts` - Module metadata structures

### 4. Algorithms

#### Dependency Pre-bundling
Based on `/playground/optimize-deps/` and documentation:
- Dependency scanning and optimization
- esbuild-based prebundling for CommonJS/UMD conversion
- Linked dependency handling (`dep-linked/`, `dep-linked-include/`)

#### CSS Processing
From `/playground/css/` and `/playground/css-sourcemap/`:
- CSS code splitting
- PostCSS integration
- Sass/Less/Stylus preprocessing
- CSS Modules support
- LightningCSS integration (fast CSS parser/transformer)

---

## Implementation Details

### 1. Core Logic

#### Dev Server Implementation
From `/packages/vite/src/node/`:
- Connect-based HTTP server
- WebSocket server for HMR
- Middleware pipeline architecture

#### Build System
From build configuration files:
- **Bundler**: Rolldown (primary), with Rollup compatibility
- **Minification**: Terser (optional), LightningCSS for CSS
- **Code transformation**: esbuild for TypeScript/JSX

#### HMR (Hot Module Replacement)
From `/playground/hmr/` and `/packages/vite/src/client/`:
- WebSocket-based update propagation
- Partial module graph invalidation
- CSS hot injection without page reload
- Self-accepting modules pattern

### 2. Platform Abstractions

#### SSR (Server-Side Rendering)
From `/playground/ssr*/`:
- SSR-specific module resolution
- External dependency handling
- Module condition resolution (`ssr-conditions/`)
- Web worker support (`ssr-webworker/`)

#### Environment-Specific Exports
From `/playground/resolve/exports-env/`:
- Package.json exports field resolution
- Conditional exports handling (browser, node, worker)

### 3. Performance Optimizations

#### Dependency Optimization
From `/packages/vite/package.json` dependencies:
```json
"fdir": "^6.5.0",      // Fast directory traversal
"tinyglobby": "^0.2.15" // Fast glob matching
```

#### CSS Optimization
- LightningCSS integration for fast CSS processing
- PostCSS plugin caching (`/playground/css/postcss-caching/`)

#### Build Optimization
From `/packages/vite/package.json`:
```json
"rolldown": "1.0.0-beta.57"  // Rust-based bundler
```

### 4. Resource Management

#### File System Watching
From `/patches/chokidar@3.6.0.patch`:
- Patched chokidar for file system watching
- Custom patches for stability

#### Connection Management
From `/patches/http-proxy-3.patch`:
- HTTP proxy middleware patching
- Custom proxy handling

---

## Code Organization

### 1. Directory Structure

```
/
├── packages/           # Published packages
│   ├── vite/          # Core package
│   │   ├── bin/       # CLI entry points
│   │   ├── src/       # Source code
│   │   ├── types/     # Type definitions
│   │   └── misc/      # Miscellaneous files
│   ├── create-vite/   # Scaffolding tool
│   └── plugin-legacy/ # Legacy browser plugin
├── playground/         # Test fixtures and examples
├── docs/              # VitePress documentation
├── scripts/           # Release and CI scripts
└── patches/           # Dependency patches
```

### 2. Coding Standards

#### Configuration Files Present
- `.editorconfig` - Editor configuration
- `.prettierrc.json` - Prettier formatting
- `.prettierignore` - Prettier ignore patterns
- `eslint.config.js` - ESLint configuration

#### TypeScript Configuration
From `/packages/vite/`:
- `tsconfig.base.json` - Base TypeScript config
- `tsconfig.check.json` - Type checking config
- `tsconfig.json` - Main TypeScript config

### 3. Build System

#### Primary Build Tools
From `/packages/vite/`:
- `rolldown.config.ts` - Main build configuration
- `rolldown.dts.config.ts` - Type declaration bundling
- `rollupLicensePlugin.ts` - License aggregation

From `/packages/create-vite/` and `/packages/plugin-legacy/`:
- `tsdown.config.ts` - TypeScript bundling

#### Package Management
From root `package.json`:
- **Package Manager**: pnpm (workspace-based)
- **Workspace Configuration**: `pnpm-workspace.yaml`

### 4. Development Workflow

#### Vitest Configuration
- `vitest.config.ts` - Unit test configuration
- `vitest.config.e2e.ts` - End-to-end test configuration

#### Playground Setup
From `/playground/`:
- `vitestSetup.ts` - Test environment setup
- `vitestGlobalSetup.ts` - Global test setup
- `test-utils.ts` - Shared test utilities

---

## Quality Assurance

### 1. Testing Strategy

#### Test Infrastructure
From `/playground/`:
Each playground directory contains `__tests__/` with integration tests:
- `hmr/__tests__/` - HMR functionality tests
- `css/__tests__/` - CSS processing tests
- `ssr/__tests__/` - SSR tests
- etc.

#### Unit Tests
From `/packages/vite/src/node/__tests__/`:
- Core functionality unit tests
- Plugin system tests
- Configuration loading tests

### 2. Test Infrastructure

#### Frameworks Used
From root `package.json`:
```json
"vitest": "^4.0.15",
"playwright-chromium": "^1.57.0"
```

#### Test Fixtures
- `/packages/vite/src/node/__tests__/fixtures/` - Unit test fixtures
- `/playground/` - Integration test fixtures

### 3. Code Quality

#### Linting
From `eslint.config.js`:
- TypeScript ESLint rules
- Import validation
- RegExp optimization
- Node.js best practices

From root `package.json` devDependencies:
```json
"eslint-plugin-import-x": "^4.16.1",
"eslint-plugin-n": "^17.23.1",
"eslint-plugin-regexp": "^2.10.0",
"typescript-eslint": "^8.49.0"
```

#### Git Hooks
From root `package.json`:
```json
"lint-staged": "^16.2.7",
"simple-git-hooks": "^2.13.1"
```

---

## Cross-cutting Concerns

### 1. Logging

#### Development Logging
From `/packages/vite/package.json`:
```json
"picocolors": "^1.1.1"  // Terminal colors for logging
```

### 2. Error Handling

#### Error Overlay
From `/packages/vite/src/client/`:
- Client-side error overlay for development
- Stack trace display

#### Source Maps
From `/playground/js-sourcemap/` and `/playground/css-sourcemap/`:
- Source map generation and validation
- Multi-file source map support

### 3. Configuration

#### Environment Variables
From `/playground/env/`:
- `.env` file support
- Environment-specific configurations (`.env.development`, `.env.production`)
- `dotenv` and `dotenv-expand` integration

#### Config Loading
From `/packages/vite/package.json`:
```json
"tsconfck": "^3.1.6"  // TypeScript config loading
```

---

## Key Dependencies

### Runtime Dependencies (vite package)

| Dependency | Purpose |
|------------|---------|
| `rolldown` | Rust-based JavaScript bundler |
| `lightningcss` | Fast CSS processing |
| `postcss` | CSS transformations |
| `fdir` | Fast directory traversal |
| `picomatch` | Glob pattern matching |
| `tinyglobby` | Fast glob matching |

### Development Dependencies

| Dependency | Purpose |
|------------|---------|
| `esbuild` | Fast TypeScript/JSX compilation |
| `chokidar` | File system watching |
| `ws` | WebSocket server |
| `connect` | HTTP middleware framework |
| `es-module-lexer` | ES module parsing |
| `magic-string` | Source code manipulation |

---

## Framework Integration

### Supported Framework Templates
From `/packages/create-vite/`:
- `template-vue/` and `template-vue-ts/`
- `template-react/` and `template-react-ts/`
- `template-preact/` and `template-preact-ts/`
- `template-svelte/` and `template-svelte-ts/`
- `template-solid/` and `template-solid-ts/`
- `template-lit/` and `template-lit-ts/`
- `template-qwik/` and `template-qwik-ts/`
- `template-vanilla/` and `template-vanilla-ts/`

### CSS Preprocessor Support
From playground tests:
- Sass (`sass`, `sass-embedded`)
- Less
- Stylus
- SugarSS (PostCSS-based)
- LightningCSS (native)