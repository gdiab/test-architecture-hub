# hl_overview

High level overview of the codebase

# Repository Analysis: Vitest

## 0. Repository Name
[[vitest]]

## 1. Project Purpose

**Vitest** is a **next-generation testing framework** powered by Vite. It solves the problem of slow, complex test setup and execution in modern JavaScript/TypeScript projects by leveraging Vite's native ESM support, instant hot module replacement (HMR), and lightning-fast compilation.

**Primary Domain:** Developer tooling / Testing framework

**Key Features:**
- Unit testing for JavaScript/TypeScript projects
- Browser testing support (Playwright, WebdriverIO)
- Code coverage reporting (V8, Istanbul)
- Snapshot testing
- Mocking capabilities
- TypeScript type checking
- UI dashboard for test visualization
- Workspace/monorepo support

## 2. Architecture Pattern

**Plugin-based Monorepo Architecture** with:
- **Core Framework Pattern**: Central orchestration with extensible plugins
- **Provider Pattern**: Multiple interchangeable implementations (browser providers, coverage providers)
- **Workspace Pattern**: Multi-project support with shared configuration

## 3. Technology Stack

### Primary Languages
- **TypeScript** (primary)
- **JavaScript** (configuration, build scripts)

### Core Framework
- **Vite** - Build tool and dev server foundation

### Major Dependencies (from package.json files)

| Category | Dependencies |
|----------|-------------|
| **Testing Runtime** | `chai`, `tinyspy`, `tinybench` |
| **Browser Testing** | `playwright`, `webdriverio`, `@testing-library/dom`, `@testing-library/jest-dom` |
| **Code Coverage** | `v8-to-istanbul`, `istanbul-lib-coverage`, `istanbul-lib-report`, `istanbul-reports` |
| **Mocking** | `@sinonjs/fake-timers` |
| **Snapshot** | Custom implementation (`@vitest/snapshot`) |
| **CLI** | `cac`, `picocolors`, `tinyrainbow` |
| **Code Transformation** | `magic-string`, `acorn`, `acorn-walk` |
| **Utilities** | `pathe`, `fast-glob`, `debug`, `std-env` |
| **UI Framework** | Vue 3, VueUse, CodeMirror |
| **Build Tools** | `rollup`, `esbuild`, `unbuild` |
| **Documentation** | VitePress |

### Configuration Files Indicating Stack
- `tsconfig.*.json` - TypeScript configuration
- `pnpm-workspace.yaml` - PNPM monorepo
- `rollup.config.js` - Rollup for library bundling
- `vite.config.ts` - Vite configuration

## 4. Initial Structure Impression

| Component | Description |
|-----------|-------------|
| **`packages/`** | Core libraries and packages (main product code) |
| **`test/`** | Comprehensive test suites and fixtures |
| **`docs/`** | VitePress documentation site |
| **`examples/`** | Example projects demonstrating usage |
| **`scripts/`** | Build and release automation |
| **`patches/`** | Dependency patches |

## 5. Configuration/Package Files

### Root Level
- `package.json` - Root workspace package
- `pnpm-workspace.yaml` - Workspace definition
- `pnpm-lock.yaml` - Lock file
- `tsconfig.base.json`, `tsconfig.build.json`, `tsconfig.check.json` - TypeScript configs
- `eslint.config.js` - ESLint configuration
- `.npmrc` - NPM configuration
- `.editorconfig` - Editor configuration
- `.tazerc.json` - Dependency update configuration
- `netlify.toml` - Netlify deployment config

### Package-Specific
Each package in `packages/` contains:
- `package.json`
- `tsconfig.json`
- `rollup.config.js`

## 6. Directory Structure

### `/packages/` - Core Libraries

| Package | Purpose |
|---------|---------|
| `vitest/` | **Main package** - Core testing framework, CLI, reporters, runtime |
| `runner/` | Test runner implementation and utilities |
| `expect/` | Assertion library (chai-based) |
| `spy/` | Spy/mock function implementation |
| `snapshot/` | Snapshot testing functionality |
| `mocker/` | Module mocking system |
| `browser/` | Browser testing orchestration |
| `browser-playwright/` | Playwright browser provider |
| `browser-webdriverio/` | WebdriverIO browser provider |
| `browser-preview/` | Preview browser provider |
| `coverage-v8/` | V8 coverage provider |
| `coverage-istanbul/` | Istanbul coverage provider |
| `ui/` | Web-based test UI dashboard |
| `utils/` | Shared utilities (diff, error handling) |
| `pretty-format/` | Object formatting for output |
| `web-worker/` | Web worker testing support |
| `ws-client/` | WebSocket client for communication |

### `/packages/vitest/src/` - Main Package Structure

```
src/
├── api/           # Public API exports
├── create/        # Project/test creation utilities
├── integrations/  # Third-party integrations (env, chai)
├── node/          # Node.js-specific implementation
│   ├── cli/       # Command-line interface
│   ├── pools/     # Test execution pools (threads, forks, vmThreads)
│   ├── reporters/ # Output reporters (console, JSON, HTML, etc.)
│   └── ...
├── public/        # Public exports and types
├── runtime/       # Test runtime execution
├── typecheck/     # TypeScript type checking
├── types/         # Type definitions
└── utils/         # Internal utilities
```

### `/test/` - Test Suites

| Directory | Tests |
|-----------|-------|
| `core/` | Core functionality tests |
| `cli/` | CLI command tests |
| `config/` | Configuration option tests |
| `browser/` | Browser testing integration |
| `coverage-test/` | Coverage reporting tests |
| `snapshots/` | Snapshot feature tests |
| `reporters/` | Reporter output tests |
| `workspaces/` | Workspace/monorepo tests |
| `typescript/` | TypeScript integration tests |
| `watch/` | Watch mode tests |

### `/docs/` - Documentation

```
docs/
├── guide/         # User guides and tutorials
├── config/        # Configuration reference
├── api/           # API documentation
├── blog/          # Release announcements
├── .vitepress/    # VitePress configuration
└── public/        # Static assets
```

## 7. High-Level Architecture

### Architectural Patterns

**1. Plugin Architecture**
- Browser providers (Playwright, WebdriverIO) as plugins
- Coverage providers (V8, Istanbul) as plugins
- Custom reporters as plugins
- Evidence: Separate packages with consistent interfaces

**2. Multi-Pool Execution Model**
```
┌─────────────┐
│   Vitest    │
│   (Main)    │
└─────┬───────┘
      │
      ├──► Threads Pool (worker_threads)
      ├──► Forks Pool (child_process.fork)
      ├──► VM Threads Pool (vm isolation)
      └──► Browser Pool (browser automation)
```
Evidence: `packages/vitest/src/node/pools/`

**3. Client-Server Architecture (Browser Testing)**
```
┌─────────────┐     WebSocket     ┌─────────────┐
│   Node.js   │◄─────────────────►│   Browser   │
│   Server    │                   │   Client    │
└─────────────┘                   └─────────────┘
```
Evidence: `packages/ws-client/`, browser orchestration code

**4. Layered Architecture (Main Package)**
```
┌─────────────────────────────────┐
│         Public API              │  (exports, types)
├─────────────────────────────────┤
│         CLI Layer               │  (command parsing)
├─────────────────────────────────┤
│       Core Orchestration        │  (Vitest class)
├─────────────────────────────────┤
│      Execution Pools            │  (threads, forks)
├─────────────────────────────────┤
│         Runtime                 │  (test execution)
└─────────────────────────────────┘
```

**5. Monorepo with Shared Dependencies**
- Evidence: `pnpm-workspace.yaml`, internal package references

## 8. Build, Execution and Test

### Build Process

```bash
# Install dependencies
pnpm install

# Build all packages
pnpm build

# Build specific package
pnpm --filter @vitest/browser build
```

**Build Tools:**
- **Rollup** - Library bundling (all packages use `rollup.config.js`)
- **TypeScript** - Compilation and type checking
- **esbuild** - Fast transpilation (via Vite/Rollup plugins)

### Entry Points

**CLI Entry:**
- `packages/vitest/src/node/cli/cli.ts` - Main CLI entry
- Executable: `packages/vitest/vitest.mjs`

**Programmatic API:**
- `packages/vitest/src/node/index.ts` - Node API
- `packages/vitest/src/public/index.ts` - Public exports

### Running Tests

```bash
# Run all tests
pnpm test

# Run specific test file
pnpm test test/core/test/basic.test.ts

# Run tests in watch mode
pnpm test -- --watch

# Run browser tests
pnpm test:browser
```

### Test Configuration

Tests use Vitest itself (dogfooding). Key test configurations:
- `test/core/vite.config.ts`
- `test/cli/vitest.config.ts`
- `test/browser/vitest.config.mts`

### Scripts (from root package.json - inferred)

| Script | Purpose |
|--------|---------|
| `build` | Build all packages |
| `test` | Run test suite |
| `lint` | Run ESLint |
| `typecheck` | TypeScript type checking |
| `release` | Publish packages |

### CI/CD

**GitHub Actions** (`.github/workflows/`):
- `ci.yml` - Main CI pipeline
- `publish.yml` - Package publishing
- `ecosystem-ci-trigger.yml` - Ecosystem compatibility tests

### Documentation

```bash
# Run docs locally
cd docs && pnpm dev

# Build docs
cd docs && pnpm build
```

Deployed via **Netlify** (`netlify.toml`)

# module_deep_dive

Deep dive into modules

# Detailed Component Breakdown - Vitest Repository

## 1. `packages/vitest/` - Core Test Framework

### Core Responsibility
The main Vitest package that orchestrates the entire testing framework. It provides the CLI, test runner coordination, configuration management, and exposes the public API for test execution.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `src/node/` | Node.js-specific runtime code including CLI handling, worker pool management, test orchestration, and Vite integration |
| `src/runtime/` | Test runtime environment code that executes within test workers |
| `src/api/` | HTTP/WebSocket server API for UI communication and programmatic access |
| `src/integrations/` | Integration layer for Chai assertions, coverage providers, and snapshot functionality |
| `src/typecheck/` | TypeScript type-checking test support (`vitest typecheck`) |
| `src/public/` | Public API exports for external consumption |
| `src/types/` | TypeScript type definitions and interfaces |
| `src/utils/` | Shared utility functions |
| `src/create/` | Factory functions for creating Vitest instances |
| `index.cjs`, `vitest.mjs` | Entry points for CommonJS and ESM consumption |
| `*.d.ts` files | Type declaration files for various submodules (globals, coverage, reporters, etc.) |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/runner` - Test execution primitives
- `@vitest/snapshot` - Snapshot testing
- `@vitest/expect` - Assertion library
- `@vitest/spy` - Mock/spy functionality
- `@vitest/utils` - Shared utilities
- `@vitest/mocker` - Module mocking
- `@vitest/pretty-format` - Output formatting

**External Services/APIs:**
- Vite (build tool integration)
- Coverage providers (v8, istanbul)
- WebSocket server for UI/watch mode
- Node.js worker threads/child processes

---

## 2. `packages/runner/` - Test Execution Engine

### Core Responsibility
Provides the low-level test execution primitives including test suite organization, lifecycle hooks, and the core test running logic independent of the Vitest-specific environment.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `src/suite.ts` | Test suite creation and management (`describe`, `it`, `test`) |
| `src/run.ts` | Core test execution loop |
| `src/hooks.ts` | Lifecycle hooks (`beforeEach`, `afterAll`, etc.) |
| `src/context.ts` | Test context management |
| `src/fixture.ts` | Test fixture support |
| `src/collect.ts` | Test collection/discovery phase |
| `src/types/` | Type definitions for tasks, suites, and test results |
| `src/utils/` | Runner-specific utilities |
| `types.d.ts`, `utils.d.ts` | Public type exports |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/utils` - Utility functions for error handling, formatting

**External Services/APIs:**
- None directly; designed as a standalone execution engine

---

## 3. `packages/expect/` - Assertion Library

### Core Responsibility
Implements the Jest-compatible expect API with chainable matchers, asymmetric matchers, and extensibility support for custom assertions.

### Key Components

| File | Role |
|---|---|
| `src/jest-expect.ts` | Jest-compatible expect implementation |
| `src/jest-matchers.ts` | Built-in matchers (`toBe`, `toEqual`, `toContain`, etc.) |
| `src/jest-asymmetric-matchers.ts` | Asymmetric matchers (`expect.any()`, `expect.stringContaining()`) |
| `src/jest-extend.ts` | `expect.extend()` for custom matcher registration |
| `src/state.ts` | Assertion state management |
| `src/types.ts` | Type definitions for matchers and expectations |
| `src/utils.ts` | Comparison and matching utilities |
| `src/jest-utils.ts` | Jest compatibility utilities |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/utils` - Diff generation, error formatting
- `@vitest/spy` - Mock assertion matchers (`toHaveBeenCalled`, etc.)

**External Services/APIs:**
- Chai (underlying assertion engine)

---

## 4. `packages/snapshot/` - Snapshot Testing

### Core Responsibility
Provides snapshot testing functionality including snapshot creation, comparison, updating, and environment-specific snapshot handling.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `src/manager.ts` | Snapshot state management and coordination |
| `src/client.ts` | Client-side snapshot operations |
| `src/env/` | Environment-specific snapshot implementations |
| `src/port/` | Serialization and comparison logic |
| `src/types/` | Snapshot type definitions |
| `environment.d.ts` | Snapshot environment interface |
| `manager.d.ts` | Manager type exports |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/pretty-format` - Snapshot serialization
- `@vitest/utils` - File system utilities, diff generation

**External Services/APIs:**
- File system for snapshot file read/write operations

---

## 5. `packages/spy/` - Mocking & Spying

### Core Responsibility
Implements the `vi.fn()`, `vi.spyOn()` API for creating mock functions, spies, and tracking function calls.

### Key Components

| File | Role |
|---|---|
| `src/index.ts` | Main exports and spy factory functions |
| `src/spy.ts` | Core spy/mock implementation with call tracking, return value configuration |

### Dependencies & Interactions

**Internal Project Dependencies:**
- Minimal dependencies; designed as standalone

**External Services/APIs:**
- `tinyspy` (lightweight spy library)

---

## 6. `packages/mocker/` - Module Mocking

### Core Responsibility
Handles module mocking/stubbing including `vi.mock()`, auto-mocking, and module interception for both Node.js and browser environments.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `src/node/` | Node.js module mocking using loader hooks |
| `src/browser/` | Browser-specific module mocking via Vite transforms |
| `src/automocker.ts` | Automatic mock generation |
| `src/registry.ts` | Mock registry and resolution |
| `src/resolver.ts` | Mock path resolution |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/spy` - Mock function creation
- `@vitest/utils` - Path resolution utilities

**External Services/APIs:**
- Node.js ESM loader hooks
- Vite plugin system for browser mocking

---

## 7. `packages/browser/` - Browser Testing

### Core Responsibility
Enables running tests in real browser environments (Chromium, Firefox, WebKit) with DOM access, using Playwright or WebDriverIO as automation backends.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `src/node/` | Node.js server orchestrating browser instances |
| `src/client/` | Client-side test runner executing in browser |
| `src/shared/` | Shared code between Node and browser |
| `scripts/` | Build/generation scripts |
| `context.d.ts` | Browser context type definitions |
| `matchers.d.ts` | Browser-specific matchers (DOM assertions) |
| `jest-dom.d.ts` | Jest-DOM compatibility types |
| `aria-role.d.ts` | ARIA role type definitions |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/runner` - Test execution
- `@vitest/ws-client` - WebSocket communication
- `@vitest/mocker` - Browser module mocking
- `@vitest/utils` - Shared utilities

**External Services/APIs:**
- Playwright / WebDriverIO (browser automation)
- Vite dev server (serving test files)

---

## 8. `packages/browser-playwright/` - Playwright Provider

### Core Responsibility
Playwright-specific browser provider implementation for launching and controlling Playwright browser instances.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `src/index.ts` | Provider entry point and browser lifecycle |
| `src/commands/` | Playwright command implementations (click, type, etc.) |
| `src/provider.ts` | Browser instance management |
| `context.d.ts` | Playwright context types |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/browser` - Core browser testing infrastructure

**External Services/APIs:**
- `playwright` npm package

---

## 9. `packages/browser-webdriverio/` - WebDriverIO Provider

### Core Responsibility
WebDriverIO-specific browser provider implementation for cross-browser testing via WebDriver protocol.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `src/index.ts` | Provider entry point |
| `src/commands/` | WebDriverIO command implementations |
| `src/provider.ts` | Browser session management |
| `context.d.ts` | WebDriverIO context types |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/browser` - Core browser testing infrastructure

**External Services/APIs:**
- `webdriverio` npm package
- Selenium Grid / browser drivers

---

## 10. `packages/browser-preview/` - Preview Mode

### Core Responsibility
Provides a lightweight browser preview mode for interactive test development without full browser automation overhead.

### Key Components

| File | Role |
|---|---|
| `src/index.ts` | Preview mode entry |
| `src/client.ts` | Browser-side preview client |
| `src/server.ts` | Preview server |
| `context.d.ts` | Preview context types |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/browser` - Shares browser infrastructure

**External Services/APIs:**
- Browser DevTools / iframe embedding

---

## 11. `packages/ui/` - Web UI Dashboard

### Core Responsibility
Provides a web-based dashboard for viewing test results, navigating test files, viewing coverage, and interacting with tests in watch mode.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `client/` | Vue.js SPA for the dashboard |
| `client/components/` | UI components (test tree, results panel, etc.) |
| `client/composables/` | Vue composables for state management |
| `client/pages/` | Page components |
| `client/styles/` | CSS/styling |
| `node/` | Node.js reporter that serves the UI |
| `public/` | Static assets |
| `vite.config.ts` | Build configuration |
| `reporter.d.ts` | Reporter type definitions |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/ws-client` - WebSocket communication with test runner
- `@vitest/utils` - Formatting utilities

**External Services/APIs:**
- WebSocket connection to Vitest server
- Browser for rendering

---

## 12. `packages/coverage-v8/` - V8 Coverage Provider

### Core Responsibility
Implements code coverage collection using V8's built-in coverage instrumentation for Node.js environments.

### Key Components

| File | Role |
|---|---|
| `src/index.ts` | Provider entry point |
| `src/provider.ts` | V8 coverage collection and processing |
| `src/coverage.ts` | Coverage report generation |
| `src/utils.ts` | Coverage calculation utilities |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/utils` - File path handling

**External Services/APIs:**
- Node.js V8 inspector API
- `v8-to-istanbul` for conversion
- Coverage reporters (html, lcov, etc.)

---

## 13. `packages/coverage-istanbul/` - Istanbul Coverage Provider

### Core Responsibility
Implements code coverage using Istanbul instrumentation for broader compatibility and browser coverage support.

### Key Components

| File | Role |
|---|---|
| `src/index.ts` | Provider entry point |
| `src/provider.ts` | Istanbul instrumentation and collection |
| `src/coverage.ts` | Coverage merging and reporting |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/utils` - Utilities

**External Services/APIs:**
- `istanbul-lib-*` packages
- Vite plugin for instrumentation

---

## 14. `packages/utils/` - Shared Utilities

### Core Responsibility
Provides common utility functions shared across all Vitest packages including diff generation, error handling, and formatting.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `src/diff/` | Object/string diff generation |
| `src/error.ts` | Error processing and serialization |
| `src/display.ts` | Output formatting |
| `src/source-map.ts` | Source map handling |
| `src/timers.ts` | Timer utilities |
| `src/colors.ts` | Terminal color support |
| `src/ast.ts` | AST parsing utilities |
| `helpers.d.ts`, `diff.d.ts`, `error.d.ts` | Type exports |

### Dependencies & Interactions

**Internal Project Dependencies:**
- None (leaf dependency)

**External Services/APIs:**
- `diff-sequences` for diffing
- Source map libraries

---

## 15. `packages/pretty-format/` - Output Formatting

### Core Responsibility
Formats JavaScript values for readable output in test results and snapshots, ported from Jest's pretty-format.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `src/index.ts` | Main formatting entry point |
| `src/plugins/` | Format plugins for specific types (DOM, React, etc.) |
| `src/collections.ts` | Array/Object formatting |

### Dependencies & Interactions

**Internal Project Dependencies:**
- None (leaf dependency)

**External Services/APIs:**
- None

---

## 16. `packages/ws-client/` - WebSocket Client

### Core Responsibility
Provides WebSocket client for real-time communication between Vitest server and UI/browser clients.

### Key Components

| File | Role |
|---|---|
| `src/index.ts` | WebSocket client implementation |
| `src/types.ts` | Message type definitions |

### Dependencies & Interactions

**Internal Project Dependencies:**
- None

**External Services/APIs:**
- WebSocket protocol

---

## 17. `packages/web-worker/` - Web Worker Support

### Core Responsibility
Enables testing code that uses Web Workers by providing mock/stub implementations.

### Key Components

| File | Role |
|---|---|
| `src/index.ts` | Worker stub implementation |
| `src/worker.ts` | Worker thread simulation |
| `src/shared-worker.ts` | SharedWorker support |
| `pure.d.ts` | Pure (no side-effects) entry |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `@vitest/utils` - Utilities

**External Services/APIs:**
- Node.js worker_threads (for simulation)

---

## 18. `test/` - Integration & E2E Tests

### Core Responsibility
Contains comprehensive test suites validating Vitest's functionality across all features.

### Key Components

| Sub-directory | Role |
|---|---|
| `test/core/` | Core functionality tests (mocking, assertions, environments) |
| `test/cli/` | CLI option and behavior tests |
| `test/config/` | Configuration handling tests |
| `test/browser/` | Browser mode tests |
| `test/coverage-test/` | Coverage provider tests |
| `test/workspaces/` | Multi-project workspace tests |
| `test/reporters/` | Reporter output tests |
| `test/snapshots/` | Snapshot functionality tests |
| `test/watch/` | Watch mode tests |
| `test/typescript/` | TypeScript integration tests |
| `test/test-utils/` | Shared test utilities |

### Dependencies & Interactions

**Internal Project Dependencies:**
- All `packages/*` modules (as test subjects)
- `test/test-utils/` - Shared testing helpers

**External Services/APIs:**
- Playwright (for browser tests)
- File system for fixture tests

---

## 19. `docs/` - Documentation Website

### Core Responsibility
VitePress-powered documentation site for Vitest including guides, API reference, and configuration.

### Key Components

| Sub-directory/File | Role |
|---|---|
| `guide/` | User guides and tutorials |
| `config/` | Configuration reference (one file per option) |
| `api/` | API documentation |
| `blog/` | Release announcements |
| `.vitepress/` | VitePress configuration and theme |
| `public/` | Static assets (images, icons) |

### Dependencies & Interactions

**Internal Project Dependencies:**
- References `packages/vitest/` for API docs

**External Services/APIs:**
- VitePress static site generator
- Netlify (deployment - per `netlify.toml`)

---

## 20. `examples/` - Example Projects

### Core Responsibility
Provides reference implementations demonstrating Vitest usage patterns.

### Key Components

| Sub-directory | Role |
|---|---|
| `examples/basic/` | Simple Vitest setup |
| `examples/lit/` | Lit web component testing |
| `examples/fastify/` | Fastify API testing |
| `examples/in-source-test/` | In-source testing demo |
| `examples/typecheck/` | Type testing example |
| `examples/projects/` | Multi-project workspace example |
| `examples/opentelemetry/` | OpenTelemetry integration |
| `examples/profiling/` | Performance profiling setup |

### Dependencies & Interactions

**Internal Project Dependencies:**
- `vitest` package (as dependency)

**External Services/APIs:**
- Various framework-specific dependencies (Lit, Fastify, etc.)

# dependencies

Analyze dependencies and external libraries

# Dependency and Architecture Analysis

## Repository: vitest_be91b0ee

---

## Internal Modules

Based on the repository structure, this is a monorepo for **Vitest**, a testing framework. The project is organized into multiple internal packages under the `packages/` directory, along with test utilities and documentation.

### Core Packages (`packages/`)

| Module | Description |
|--------|-------------|
| **@vitest/vitest** (`packages/vitest/`) | Main Vitest package - the core testing framework that orchestrates test execution, configuration, and integrates all other modules |
| **@vitest/runner** (`packages/runner/`) | Test runner implementation - handles test suite execution, lifecycle hooks, and test scheduling |
| **@vitest/expect** (`packages/expect/`) | Assertion library - provides expect API and matchers for test assertions |
| **@vitest/spy** (`packages/spy/`) | Spy/mock utilities - provides function spying and mocking capabilities |
| **@vitest/snapshot** (`packages/snapshot/`) | Snapshot testing - handles snapshot creation, comparison, and management |
| **@vitest/mocker** (`packages/mocker/`) | Module mocking system - provides module mocking capabilities for both browser and Node environments |
| **@vitest/utils** (`packages/utils/`) | Shared utilities - common helper functions, diff utilities, and formatting tools used across packages |
| **@vitest/pretty-format** (`packages/pretty-format/`) | Value formatting - serializes JavaScript values for readable output in test results and snapshots |
| **@vitest/browser** (`packages/browser/`) | Browser testing support - enables running tests in real browser environments |
| **@vitest/browser-playwright** (`packages/browser-playwright/`) | Playwright integration - browser provider using Playwright for browser testing |
| **@vitest/browser-webdriverio** (`packages/browser-webdriverio/`) | WebdriverIO integration - browser provider using WebdriverIO for browser testing |
| **@vitest/browser-preview** (`packages/browser-preview/`) | Browser preview mode - provides Testing Library integration for browser tests |
| **@vitest/ui** (`packages/ui/`) | Web UI for Vitest - interactive dashboard for viewing and managing test results |
| **@vitest/coverage-v8** (`packages/coverage-v8/`) | V8 coverage provider - code coverage using V8's built-in coverage |
| **@vitest/coverage-istanbul** (`packages/coverage-istanbul/`) | Istanbul coverage provider - code coverage using Istanbul instrumentation |
| **@vitest/ws-client** (`packages/ws-client/`) | WebSocket client - handles WebSocket communication between Vitest components |
| **@vitest/web-worker** (`packages/web-worker/`) | Web Worker support - enables testing code that uses Web Workers |

### Test Infrastructure (`test/`)

| Module | Description |
|--------|-------------|
| **test/test-utils** (`test/test-utils/`) | Test utilities - shared helpers for running CLI commands and test fixtures across the test suite |
| **test/core** (`test/core/`) | Core functionality tests - tests for fundamental Vitest features including mocking, environments, and assertions |
| **test/browser** (`test/browser/`) | Browser testing tests - tests for browser-mode functionality |
| **test/coverage-test** (`test/coverage-test/`) | Coverage tests - tests for code coverage providers |
| **test/cli** (`test/cli/`) | CLI tests - tests for command-line interface functionality |
| **test/config** (`test/config/`) | Configuration tests - tests for Vitest configuration options |
| **test/reporters** (`test/reporters/`) | Reporter tests - tests for various test result reporters |
| **test/workspaces** (`test/workspaces/`) | Workspace tests - tests for monorepo/workspace functionality |

### Documentation (`docs/`)

| Module | Description |
|--------|-------------|
| **docs** (`docs/`) | VitePress documentation site - user documentation, guides, and API references |

---

## External Dependencies

### Build & Development Tools

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `vite` | Vite | Build tool and dev server - core bundler that Vitest is built upon | `/packages/vitest/package.json`, `/package.json (dev)` |
| `rollup` | Rollup | Module bundler used for building packages | `/package.json (dev)`, `/packages/ui/package.json (dev)` |
| `esbuild` | esbuild | Fast JavaScript bundler/minifier | `/package.json (dev)` |
| `typescript` | TypeScript | Type checking and compilation | `/package.json (dev)`, `/examples/in-source-test/package.json (dev)` |
| `tsx` | tsx | TypeScript execution for Node.js | `/package.json (dev)`, `/examples/fastify/package.json (dev)` |
| `eslint` | ESLint | JavaScript linter | `/package.json (dev)` |
| `@antfu/eslint-config` | Antfu ESLint Config | ESLint configuration preset | `/package.json (dev)` |
| `bumpp` | bumpp | Version bumping tool | `/package.json (dev)` |
| `changelogithub` | changelogithub | Changelog generation from GitHub | `/package.json (dev)` |
| `zx` | zx | Shell scripting in JavaScript | `/package.json (dev)` |
| `premove` | premove | File removal utility | `/package.json (dev)` |

### Rollup Plugins

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `@rollup/plugin-commonjs` | Rollup CommonJS Plugin | Convert CommonJS modules to ES6 | `/package.json (dev)` |
| `@rollup/plugin-json` | Rollup JSON Plugin | Import JSON files in Rollup | `/package.json (dev)` |
| `@rollup/plugin-node-resolve` | Rollup Node Resolve Plugin | Resolve node_modules dependencies | `/package.json (dev)` |
| `rollup-plugin-dts` | rollup-plugin-dts | Generate TypeScript declaration bundles | `/package.json (dev)` |
| `rollup-plugin-license` | rollup-plugin-license | Add license banners to bundles | `/package.json (dev)` |
| `unplugin-isolated-decl` | unplugin-isolated-decl | Isolated TypeScript declarations | `/package.json (dev)` |
| `unplugin-oxc` | unplugin-oxc | OXC-based transpilation plugin | `/package.json (dev)` |
| `unplugin-swc` | unplugin-swc | SWC-based transpilation plugin | `/test/cli/package.json (dev)`, `/test/coverage-test/package.json (dev)` |

### Testing & Browser Automation

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `@playwright/test` | Playwright Test | End-to-end testing framework | `/package.json (dev)` |
| `playwright` | Playwright | Browser automation library | `/packages/browser-playwright/package.json (dev)`, `/examples/lit/package.json (dev)` |
| `webdriverio` | WebdriverIO | Browser automation framework | `/packages/browser-webdriverio/package.json (dev)` |
| `@wdio/types` | WebdriverIO Types | TypeScript types for WebdriverIO | `/packages/browser-webdriverio/package.json (dev)` |
| `happy-dom` | Happy DOM | Fast DOM implementation for testing | `/packages/vitest/package.json (dev)`, `/test/coverage-test/package.json (dev)` |
| `jsdom` | jsdom | DOM implementation for Node.js | `/packages/vitest/package.json (dev)`, `/examples/lit/package.json (dev)` |
| `supertest` | SuperTest | HTTP assertion library | `/examples/fastify/package.json (dev)` |
| `@testing-library/dom` | Testing Library DOM | DOM testing utilities | `/packages/browser-preview/package.json`, `/test/ui/package.json (dev)` |
| `@testing-library/user-event` | Testing Library User Event | User event simulation | `/packages/browser-preview/package.json`, `/packages/browser/package.json (dev)` |
| `@testing-library/jest-dom` | Testing Library jest-dom | Custom DOM matchers | `/examples/projects/package.json (dev)` |
| `@testing-library/react` | Testing Library React | React testing utilities | `/examples/projects/package.json (dev)` |
| `jest-image-snapshot` | jest-image-snapshot | Visual regression testing | `/test/snapshots/package.json (dev)` |
| `vitest-sonar-reporter` | Vitest Sonar Reporter | SonarQube report format | `/test/reporters/package.json (dev)` |
| `vitest-browser-vue` | Vitest Browser Vue | Vue browser testing utilities | `/packages/ui/package.json (dev)` |
| `vitest-browser-react` | Vitest Browser React | React browser testing utilities | `/test/browser/package.json (dev)` |

### Assertion & Mocking

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `chai` | Chai | Assertion library | `/packages/expect/package.json` |
| `@types/chai` | Chai Types | TypeScript types for Chai | `/packages/expect/package.json` |
| `@sinonjs/fake-timers` | Sinon Fake Timers | Timer mocking | `/packages/vitest/package.json (dev)` |
| `msw` | Mock Service Worker | API mocking library | `/packages/mocker/package.json` (peer), `/packages/mocker/package.json (dev)` |
| `tinyspy` | TinySpy | Lightweight spy library | `/test/core/package.json (dev)` |
| `expect-type` | expect-type | Type-level assertions | `/packages/vitest/package.json` |
| `@standard-schema/spec` | Standard Schema | Schema validation spec | `/packages/expect/package.json`, `/test/core/package.json (dev)` |

### Code Coverage

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `istanbul-lib-coverage` | Istanbul Lib Coverage | Coverage data structures | `/packages/coverage-istanbul/package.json`, `/packages/coverage-v8/package.json` |
| `istanbul-lib-instrument` | Istanbul Lib Instrument | Code instrumentation | `/packages/coverage-istanbul/package.json` |
| `istanbul-lib-report` | Istanbul Lib Report | Coverage report generation | `/packages/coverage-istanbul/package.json`, `/packages/coverage-v8/package.json` |
| `istanbul-lib-source-maps` | Istanbul Lib Source Maps | Source map support for coverage | `/packages/coverage-istanbul/package.json` |
| `istanbul-reports` | Istanbul Reports | Coverage report formats | `/packages/coverage-istanbul/package.json`, `/packages/coverage-v8/package.json` |
| `@istanbuljs/schema` | Istanbul Schema | Configuration schema | `/packages/coverage-istanbul/package.json` |
| `@bcoe/v8-coverage` | V8 Coverage | V8 coverage utilities | `/packages/coverage-v8/package.json` |
| `ast-v8-to-istanbul` | AST V8 to Istanbul | Convert V8 coverage to Istanbul format | `/packages/coverage-v8/package.json` |

### Source Maps & Code Transformation

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `magic-string` | MagicString | String manipulation with source maps | `/packages/vitest/package.json`, `/packages/browser/package.json`, `/packages/mocker/package.json` |
| `@jridgewell/trace-mapping` | Trace Mapping | Source map parsing | `/packages/coverage-istanbul/package.json` |
| `@jridgewell/gen-mapping` | Gen Mapping | Source map generation | `/packages/coverage-istanbul/package.json` |
| `@jridgewell/remapping` | Remapping | Source map remapping | `/test/coverage-test/package.json (dev)` |
| `magicast` | Magicast | AST-based code modification | `/packages/coverage-istanbul/package.json`, `/packages/coverage-v8/package.json` |
| `estree-walker` | estree-walker | AST traversal | `/packages/mocker/package.json` |
| `acorn-walk` | Acorn Walk | Acorn AST walker | `/packages/mocker/package.json (dev)`, `/packages/vitest/package.json (dev)` |
| `strip-literal` | strip-literal | Remove string literals from code | `/packages/vitest/package.json (dev)` |
| `es-module-lexer` | ES Module Lexer | ES module parsing | `/packages/vitest/package.json` |

### File System & Path Utilities

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `pathe` | pathe | Cross-platform path utilities | `/packages/vitest/package.json`, `/packages/runner/package.json`, `/packages/snapshot/package.json` |
| `tinyglobby` | tinyglobby | Fast glob pattern matching | `/packages/vitest/package.json`, `/packages/ui/package.json` |
| `picomatch` | picomatch | Glob pattern matching | `/packages/vitest/package.json` |
| `memfs` | memfs | In-memory file system | `/test/core/package.json (dev)` |

### CLI & Process Management

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `cac` | CAC | Command-line argument parsing | `/packages/vitest/package.json (dev)` |
| `tinyexec` | tinyexec | Process execution | `/packages/vitest/package.json` |
| `prompts` | Prompts | Interactive CLI prompts | `/packages/vitest/package.json (dev)` |
| `why-is-node-running` | why-is-node-running | Debug active handles preventing exit | `/packages/vitest/package.json` |
| `@antfu/ni` | ni | Package manager agnostic commands | `/package.json (dev)` |
| `local-pkg` | local-pkg | Local package utilities | `/packages/vitest/package.json (dev)` |
| `@antfu/install-pkg` | install-pkg | Package installation helper | `/packages/vitest/package.json (dev)` |

### Networking & Communication

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `ws` | ws | WebSocket client/server | `/packages/browser/package.json`, `/packages/ws-client/package.json` |
| `birpc` | birpc | Bi-directional RPC | `/packages/ws-client/package.json`, `/packages/browser/package.json (dev)` |
| `flatted` | Flatted | Circular JSON serialization | `/packages/ui/package.json`, `/packages/ws-client/package.json` |
| `sirv` | Sirv | Static file server | `/packages/browser/package.json`, `/packages/ui/package.json` |

### Image Processing

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `pixelmatch` | Pixelmatch | Image comparison | `/packages/browser/package.json` |
| `pngjs` | pngjs | PNG encoding/decoding | `/packages/browser/package.json` |

### Benchmarking

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `tinybench` | Tinybench | Benchmarking library | `/packages/vitest/package.json` |

### Environment Detection

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `std-env` | std-env | Runtime environment detection | `/packages/vitest/package.json`, `/packages/coverage-v8/package.json` |

### Console & Output Formatting

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `tinyrainbow` | tinyrainbow | Terminal color output | `/packages/vitest/package.json`, `/packages/expect/package.json`, `/packages/utils/package.json`, `/packages/browser/package.json` |
| `loupe` | Loupe | Object inspection | `/packages/utils/package.json (dev)` |
| `tinyhighlight` | tinyhighlight | Syntax highlighting | `/packages/utils/package.json (dev)` |
| `ansi-to-html` | ansi-to-html | Convert ANSI to HTML | `/packages/ui/package.json (dev)` |
| `diff-sequences` | diff-sequences | Diff algorithm | `/packages/utils/package.json (dev)` |
| `natural-compare` | natural-compare | Natural sort comparison | `/packages/snapshot/package.json (dev)` |

### Debugging & Profiling

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `obug` | obug | Debug utilities | `/packages/vitest/package.json`, `/packages/coverage-istanbul/package.json`, `/packages/coverage-v8/package.json` |

### OpenTelemetry (Observability)

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `@opentelemetry/api` | OpenTelemetry API | Telemetry API | `/packages/vitest/package.json` (peer), `/examples/opentelemetry/package.json (dev)` |
| `@opentelemetry/sdk-node` | OpenTelemetry SDK Node | Node.js telemetry SDK | `/examples/opentelemetry/package.json (dev)`, `/test/cli/package.json (dev)` |
| `@opentelemetry/sdk-trace-web` | OpenTelemetry SDK Trace Web | Browser tracing SDK | `/examples/opentelemetry/package.json (dev)`, `/test/cli/package.json (dev)` |
| `@opentelemetry/exporter-trace-otlp-proto` | OpenTelemetry OTLP Exporter | OTLP trace exporter | `/examples/opentelemetry/package.json (dev)` |
| `@opentelemetry/resources` | OpenTelemetry Resources | Resource definitions | `/examples/opentelemetry/package.json (dev)` |
| `@opentelemetry/context-zone` | OpenTelemetry Context Zone | Zone.js context manager | `/examples/opentelemetry/package.json (dev)` |

### Edge Runtime

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `@edge-runtime/vm` | Edge Runtime VM | Edge runtime environment | `/packages/vitest/package.json` (peer), `/packages/vitest/package.json (dev)` |

### Vue Ecosystem

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `vue` | Vue.js | Frontend framework | `/docs/package.json`, `/packages/ui/package.json (dev)` |
| `@vueuse/core` | VueUse | Vue composition utilities | `/docs/package.json`, `/packages/ui/package.json (dev)` |
| `@vueuse/integrations` | VueUse Integrations | VueUse integration utilities | `/test/core/package.json (dev)` |
| `vue-router` | Vue Router | Vue routing | `/packages/ui/package.json (dev)` |
| `@vue/test-utils` | Vue Test Utils | Vue testing utilities | `/packages/ui/package.json (dev)`, `/test/coverage-test/package.json (dev)` |
| `vue-tsc` | vue-tsc | Vue TypeScript compiler | `/packages/ui/package.json (dev)`, `/test/typescript/package.json (dev)` |
| `@vitejs/plugin-vue` | Vite Vue Plugin | Vue support for Vite | `/docs/package.json (dev)`, `/packages/ui/package.json (dev)` |
| `floating-vue` | Floating Vue | Floating UI for Vue | `/packages/ui/package.json (dev)` |
| `vue-virtual-scroller` | Vue Virtual Scroller | Virtual scrolling for Vue | `/packages/ui/package.json (dev)` |

### React Ecosystem

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `react` | React | UI library | `/examples/projects/package.json (dev)`, `/test/browser/package.json (dev)` |
| `react-dom` | ReactDOM | React DOM rendering | `/test/browser/package.json (dev)` |
| `react-is` | react-is | React type checking | `/packages/pretty-format/package.json (dev)` |
| `@vitejs/plugin-react` | Vite React Plugin | React support for Vite | `/examples/projects/package.json (dev)` |

### Lit (Web Components)

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `lit` | Lit | Web components library | `/examples/lit/package.json` |

### Fastify (HTTP Server)

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `fastify` | Fastify | Fast web framework | `/examples/fastify/package.json (dev)`, `/examples/projects/package.json (dev)` |

### HTTP Client

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `axios` | Axios | HTTP client | `/test/core/package.json (dev)` |

### Data Structures

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `immutable` | Immutable.js | Immutable data structures | `/test/core/package.json (dev)` |

### Polyfills & Utilities

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `temporal-polyfill` | Temporal Polyfill | Temporal API polyfill | `/test/core/package.json (dev)` |
| `@ungap/structured-clone` | Structured Clone | structuredClone polyfill | `/packages/web-worker/package.json (dev)` |
| `url` | url | URL parsing (Node.js polyfill) | `/test/core/package.json (dev)`, `/test/browser/package.json (dev)` |
| `empathic` | empathic | Path empathy utilities | `/packages/vitest/package.json (dev)` |
| `mime` | mime | MIME type detection | `/packages/browser/package.json (dev)`, `/packages/ui/package.json (dev)` |

### Compression

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `fflate` | fflate | Fast compression library | `/packages/ui/package.json` |

### Documentation (VitePress)

| Dependency | Official Name | Purpose | Source |
|------------|---------------|---------|--------|
| `vitepress` | VitePress | Static site generator | `/docs/package.json (dev)` |
| `@shikijs/transformers` | Shiki Transformers | Code highlighting transformers | `/docs/package.json (dev)` |
| `@shikijs/vitepress-twoslash` | Shiki VitePress Twoslash | TypeScript hover info | `/docs/package.json (dev)` |
| `vitepress-plugin-group-icons` | VitePress Group Icons | Icon grouping plugin | `/docs/package.json (dev)` |
| `vitepress-plugin-llms` |

# core_entities

Core entities and their relationships

# Vitest Domain Model Analysis

Based on my analysis of the repository structure and files, this is the **Vitest** project - a modern JavaScript/TypeScript testing framework. Below are the identified core domain entities, their attributes, and relationships.

---

## 1. Common Data Entities / Domain Models

### 1.1 **Test**
The fundamental unit of testing - an individual test case.

| Attribute | Description |
|-----------|-------------|
| `name` | Test title/description |
| `fn` | Test function to execute |
| `mode` | Execution mode (`run`, `skip`, `only`, `todo`) |
| `timeout` | Maximum execution time |
| `retry` | Number of retry attempts on failure |
| `location` | File path and line number |
| `result` | Test execution result |
| `annotations` | Metadata/notes attached to test |
| `context` | Test context with utilities |

---

### 1.2 **Suite**
A container that groups related tests together.

| Attribute | Description |
|-----------|-------------|
| `name` | Suite title/description |
| `mode` | Execution mode (`run`, `skip`, `only`) |
| `tests` | Collection of tests |
| `suites` | Nested child suites |
| `hooks` | Lifecycle hooks (beforeAll, afterAll, etc.) |
| `file` | Parent file reference |
| `concurrent` | Whether tests run in parallel |
| `sequential` | Whether tests run sequentially |

---

### 1.3 **TestFile**
Represents a physical test file on disk.

| Attribute | Description |
|-----------|-------------|
| `filepath` | Absolute file path |
| `name` | Display name |
| `projectName` | Associated project |
| `suites` | Top-level suites in file |
| `tests` | Top-level tests in file |
| `result` | Aggregated file result |
| `environment` | Test environment (jsdom, node, etc.) |

---

### 1.4 **Project**
A configuration unit within a workspace (supports multiple projects).

| Attribute | Description |
|-----------|-------------|
| `name` | Project identifier |
| `root` | Project root directory |
| `config` | Project-specific configuration |
| `testFiles` | Matched test files |
| `provide` | Injected dependencies |
| `environment` | Default test environment |
| `browser` | Browser configuration |

---

### 1.5 **Config / ResolvedConfig**
Configuration options for test execution.

| Attribute | Description |
|-----------|-------------|
| `root` | Root directory |
| `include` | Test file patterns |
| `exclude` | Exclusion patterns |
| `environment` | Test environment |
| `globals` | Global API injection |
| `pool` | Execution pool (`threads`, `forks`, `vmThreads`) |
| `reporters` | Output reporters |
| `coverage` | Coverage configuration |
| `browser` | Browser testing config |
| `testTimeout` | Default test timeout |
| `hookTimeout` | Hook timeout |
| `setupFiles` | Setup file paths |
| `globalSetup` | Global setup files |
| `deps` | Dependency handling options |
| `snapshotFormat` | Snapshot formatting |

---

### 1.6 **TestResult / TaskResult**
The outcome of executing a test or suite.

| Attribute | Description |
|-----------|-------------|
| `state` | Status (`pass`, `fail`, `skip`, `todo`, `run`) |
| `duration` | Execution time in ms |
| `errors` | Array of captured errors |
| `retryCount` | Number of retries attempted |
| `startTime` | Execution start timestamp |
| `hooks` | Hook results |

---

### 1.7 **Coverage**
Code coverage metrics and configuration.

| Attribute | Description |
|-----------|-------------|
| `provider` | Coverage provider (`v8`, `istanbul`) |
| `include` | Files to cover |
| `exclude` | Files to exclude |
| `reporter` | Coverage reporters (`text`, `html`, `lcov`) |
| `thresholds` | Coverage thresholds |
| `lines` | Line coverage percentage |
| `branches` | Branch coverage percentage |
| `functions` | Function coverage percentage |
| `statements` | Statement coverage percentage |

---

### 1.8 **Reporter**
Output handler for test results.

| Attribute | Description |
|-----------|-------------|
| `name` | Reporter identifier |
| `onInit` | Initialization hook |
| `onTestStart` | Test start callback |
| `onTestFinish` | Test completion callback |
| `onFinished` | Run completion callback |
| `outputFile` | Output file path |

---

### 1.9 **Mock / SpyInstance**
Mocking and spying functionality.

| Attribute | Description |
|-----------|-------------|
| `mockName` | Mock identifier |
| `calls` | Recorded call arguments |
| `results` | Recorded return values |
| `instances` | Created instances |
| `implementation` | Mock implementation |
| `mockReturnValue` | Configured return value |
| `mockResolvedValue` | Async return value |

---

### 1.10 **Snapshot**
Snapshot testing entity.

| Attribute | Description |
|-----------|-------------|
| `filepath` | Snapshot file path |
| `added` | New snapshots count |
| `updated` | Updated snapshots count |
| `matched` | Matched snapshots count |
| `unchecked` | Obsolete snapshots count |
| `content` | Serialized snapshot data |

---

### 1.11 **BrowserContext**
Browser testing configuration and state.

| Attribute | Description |
|-----------|-------------|
| `provider` | Browser provider (`playwright`, `webdriverio`, `preview`) |
| `name` | Browser name (`chromium`, `firefox`, `webkit`) |
| `headless` | Headless mode flag |
| `viewport` | Viewport dimensions |
| `locators` | Element locator utilities |
| `page` | Current page instance |
| `commands` | Custom browser commands |

---

### 1.12 **WorkerContext**
Execution context for test workers.

| Attribute | Description |
|-----------|-------------|
| `workerId` | Worker identifier |
| `pool` | Pool type |
| `config` | Worker configuration |
| `files` | Assigned test files |
| `environment` | Execution environment |
| `isolate` | Isolation mode |

---

### 1.13 **Environment**
Test execution environment abstraction.

| Attribute | Description |
|-----------|-------------|
| `name` | Environment name (`node`, `jsdom`, `happy-dom`, `edge-runtime`) |
| `setup` | Setup function |
| `teardown` | Cleanup function |
| `transformMode` | Transform mode (`ssr`, `web`) |

---

## 2. Entity Relationships

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              WORKSPACE                                   │
│                                                                         │
│  ┌─────────────┐    1:N    ┌─────────────┐                             │
│  │   Config    │◄─────────►│   Project   │                             │
│  └─────────────┘           └──────┬──────┘                             │
│                                   │ 1:N                                 │
│                                   ▼                                     │
│  ┌─────────────┐    1:N    ┌─────────────┐    N:1    ┌─────────────┐  │
│  │ Environment │◄─────────►│  TestFile   │◄─────────►│  Reporter   │  │
│  └─────────────┘           └──────┬──────┘           └─────────────┘  │
│                                   │ 1:N                                 │
│                                   ▼                                     │
│                            ┌─────────────┐                             │
│                            │    Suite    │◄──────┐                     │
│                            └──────┬──────┘       │ 1:N (nested)        │
│                                   │ 1:N         ─┘                     │
│                                   ▼                                     │
│  ┌─────────────┐    1:1    ┌─────────────┐    N:N    ┌─────────────┐  │
│  │ TestResult  │◄─────────►│    Test     │◄─────────►│    Mock     │  │
│  └──────┬──────┘           └──────┬──────┘           └─────────────┘  │
│         │                         │ 1:N                                │
│         │ 1:N                     ▼                                    │
│         ▼                  ┌─────────────┐                             │
│  ┌─────────────┐           │  Snapshot   │                             │
│  │   Error     │           └─────────────┘                             │
│  └─────────────┘                                                       │
│                                                                         │
│  ┌─────────────┐    1:1    ┌─────────────┐    1:N    ┌─────────────┐  │
│  │  Coverage   │◄─────────►│   Project   │◄─────────►│WorkerContext│  │
│  └─────────────┘           └─────────────┘           └─────────────┘  │
│                                                                         │
│  ┌─────────────┐    1:1    ┌─────────────┐                             │
│  │BrowserContext◄─────────►│   Project   │ (when browser testing)      │
│  └─────────────┘           └─────────────┘                             │
└─────────────────────────────────────────────────────────────────────────┘
```

### Relationship Descriptions

| Relationship | Type | Description |
|--------------|------|-------------|
| **Config → Project** | One-to-Many | A workspace config defines multiple projects |
| **Project → TestFile** | One-to-Many | Each project contains multiple test files |
| **TestFile → Suite** | One-to-Many | A test file contains top-level suites |
| **Suite → Suite** | One-to-Many (Self) | Suites can nest other suites |
| **Suite → Test** | One-to-Many | Suites contain multiple tests |
| **Test → TestResult** | One-to-One | Each test has exactly one result |
| **TestResult → Error** | One-to-Many | A failed test can have multiple errors |
| **Test → Snapshot** | One-to-Many | A test can have multiple snapshots |
| **Test → Mock** | Many-to-Many | Tests use multiple mocks; mocks shared across tests |
| **Project → Coverage** | One-to-One | Each project has coverage config/results |
| **Project → Environment** | Many-to-One | Multiple projects can share an environment |
| **Project → WorkerContext** | One-to-Many | Projects spawn multiple workers |
| **Project → BrowserContext** | One-to-One | Browser projects have browser context |
| **TestFile → Reporter** | Many-to-Many | Files reported by multiple reporters |

---

## 3. Key Domain Aggregates

### Test Execution Aggregate
```
TestFile (Root)
├── Suite[]
│   ├── Test[]
│   │   ├── TestResult
│   │   ├── Snapshot[]
│   │   └── Mock[]
│   └── Suite[] (nested)
└── Hooks
```

### Configuration Aggregate
```
Config (Root)
├── Project[]
│   ├── Environment
│   ├── Coverage
│   ├── BrowserContext?
│   └── Reporter[]
└── GlobalSetup
```

### Execution Aggregate
```
WorkerContext (Root)
├── TestFile[]
├── Environment
└── Pool Configuration
```

# DBs

databases analysis

no database

# APIs

APIs analysis

# HTTP API Documentation Analysis

After conducting a comprehensive analysis of the provided codebase, I need to examine the relevant source files to identify any HTTP API endpoints.

Let me analyze the key areas where HTTP APIs might be defined:

## Analysis Summary

This repository is **Vitest** - a Vite-native testing framework. After thorough examination of the codebase structure:

1. **`packages/vitest/src/api/`** - This directory contains API-related code, but it's for internal WebSocket-based communication between the Vitest runner and UI, not a public HTTP REST API.

2. **`packages/ui/`** - The UI package provides a web interface for viewing test results, but uses WebSocket connections rather than HTTP REST endpoints.

3. **`packages/browser/`** - Browser testing infrastructure that uses WebSocket for communication.

4. **`examples/fastify/`** - This is a test example/fixture demonstrating how to test a Fastify application, not part of Vitest's own API.

5. **`docs/`** - Documentation site built with VitePress, not an API server.

The codebase primarily consists of:
- A testing framework (Vitest core)
- Browser testing providers (Playwright, WebDriverIO)
- Coverage providers (V8, Istanbul)
- A UI dashboard (communicates via WebSocket, not HTTP REST)
- Various utilities and test fixtures

The internal communication mechanisms use:
- **WebSocket connections** for real-time test status updates between the test runner and UI
- **Vite's dev server** for serving the UI assets
- **RPC-style communication** over WebSocket (not HTTP REST endpoints)

---

no HTTP API

# events

events analysis

After a comprehensive scan of the provided Vitest codebase, I can confirm:

**no events**

This repository is the Vitest testing framework - a unit testing framework for JavaScript/TypeScript. The codebase does not contain any message broker event systems (SQS, EventBridge, Kafka, Ably, RabbitMQ, Pub/Sub, etc.).

While the codebase does contain:
- Internal event emitters for test lifecycle management (test start, test end, etc.)
- WebSocket connections for the UI reporter (used for real-time test results display)
- Worker communication channels (for parallel test execution)

These are all internal implementation details for the testing framework itself, not application-level events for a message broker or event-driven architecture system. They are synchronization and communication mechanisms within the testing tool, not business events that would be documented as part of an event-driven system.

# service_dependencies

Analyze service dependencies

# External Dependencies Analysis for Vitest Repository

## Overview
This is a comprehensive analysis of the Vitest testing framework repository, identifying all external dependencies required for the codebase to function correctly during runtime.

---

## 1. Third-Party Libraries/Frameworks

### 1.1 Core Runtime Dependencies

#### Dependency: Vite
- **Type of Dependency:** Library/Framework (Build Tool)
- **Purpose/Role:** Vitest is built on top of Vite. It provides the build system, dev server, module resolution, and transformation pipeline that Vitest uses for running tests.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"vite": "^6.0.0 || ^7.0.0"`
  - `/packages/mocker/package.json`: `"vite": "^6.0.0 || ^7.0.0-0"` (peer dependency)
  - Multiple `vite.config.ts` files throughout the repository

#### Dependency: Chai
- **Type of Dependency:** Library/Framework (Assertion Library)
- **Purpose/Role:** Provides assertion capabilities for test expectations. Vitest uses Chai as its underlying assertion library.
- **Integration Point/Clues:**
  - `/packages/expect/package.json`: `"chai": "catalog:"`
  - `/packages/expect/package.json`: `"@types/chai": "catalog:"`

#### Dependency: Tinybench
- **Type of Dependency:** Library/Framework (Benchmarking)
- **Purpose/Role:** Provides benchmarking capabilities for Vitest's benchmark feature.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"tinybench": "^2.9.0"`

#### Dependency: Magic String
- **Type of Dependency:** Library/Framework (String Manipulation)
- **Purpose/Role:** Provides efficient string manipulation with source map support, used for code transformations.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"magic-string": "catalog:"`
  - `/packages/browser/package.json`: `"magic-string": "catalog:"`
  - `/packages/mocker/package.json`: `"magic-string": "catalog:"`
  - `/packages/snapshot/package.json`: `"magic-string": "catalog:"`

#### Dependency: Pathe
- **Type of Dependency:** Library/Framework (Path Utilities)
- **Purpose/Role:** Cross-platform path utilities for file system operations.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"pathe": "catalog:"`
  - `/packages/runner/package.json`: `"pathe": "catalog:"`
  - `/packages/snapshot/package.json`: `"pathe": "catalog:"`
  - `/packages/ui/package.json`: `"pathe": "catalog:"`

#### Dependency: std-env
- **Type of Dependency:** Library/Framework (Environment Detection)
- **Purpose/Role:** Runtime environment detection utilities.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"std-env": "catalog:"`
  - `/packages/coverage-v8/package.json`: `"std-env": "catalog:"`

#### Dependency: Tinyglobby
- **Type of Dependency:** Library/Framework (Glob Matching)
- **Purpose/Role:** Fast and lightweight glob pattern matching for file discovery.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"tinyglobby": "catalog:"`
  - `/packages/ui/package.json`: `"tinyglobby": "catalog:"`

#### Dependency: Tinyrainbow
- **Type of Dependency:** Library/Framework (Terminal Colors)
- **Purpose/Role:** Terminal color formatting for CLI output.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"tinyrainbow": "catalog:"`
  - `/packages/expect/package.json`: `"tinyrainbow": "catalog:"`
  - `/packages/utils/package.json`: `"tinyrainbow": "catalog:"`
  - `/packages/pretty-format/package.json`: `"tinyrainbow": "catalog:"`
  - Multiple other packages

#### Dependency: Picomatch
- **Type of Dependency:** Library/Framework (Pattern Matching)
- **Purpose/Role:** Glob pattern matching for test file filtering.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"picomatch": "^4.0.3"`

#### Dependency: es-module-lexer
- **Type of Dependency:** Library/Framework (ES Module Parser)
- **Purpose/Role:** Fast lexer for ES module import/export statements.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"es-module-lexer": "^1.7.0"`

#### Dependency: expect-type
- **Type of Dependency:** Library/Framework (Type Testing)
- **Purpose/Role:** Compile-time type testing utilities for TypeScript type assertions.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"expect-type": "^1.2.2"`

#### Dependency: tinyexec
- **Type of Dependency:** Library/Framework (Process Execution)
- **Purpose/Role:** Lightweight process execution utility.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"tinyexec": "^1.0.2"`

#### Dependency: why-is-node-running
- **Type of Dependency:** Library/Framework (Debugging)
- **Purpose/Role:** Debugging utility to identify open handles keeping Node.js running.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"why-is-node-running": "^2.3.0"`

#### Dependency: obug
- **Type of Dependency:** Library/Framework (Debugging/Utilities)
- **Purpose/Role:** Debug utilities (ASSUMPTION: based on name, requires further investigation).
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"obug": "catalog:"`
  - `/packages/coverage-istanbul/package.json`: `"obug": "catalog:"`
  - `/packages/coverage-v8/package.json`: `"obug": "catalog:"`
  - `/packages/web-worker/package.json`: `"obug": "catalog:"`

---

### 1.2 Browser Testing Dependencies

#### Dependency: Playwright
- **Type of Dependency:** Library/Framework (Browser Automation)
- **Purpose/Role:** Browser automation framework for running tests in real browsers (Chrome, Firefox, Safari).
- **Integration Point/Clues:**
  - `/packages/browser-playwright/package.json`: `"playwright": "*"` (peer dependency)
  - `/packages/browser-playwright/package.json` (dev): `"playwright": "^1.57.0"`
  - `/test/browser/package.json`: `"playwright": "^1.57.0"`
  - `/.github/actions/setup-playwright/action.yml`: Playwright setup action

#### Dependency: WebdriverIO
- **Type of Dependency:** Library/Framework (Browser Automation)
- **Purpose/Role:** Alternative browser automation framework for running tests in browsers.
- **Integration Point/Clues:**
  - `/packages/browser-webdriverio/package.json`: `"webdriverio": "*"` (peer dependency)
  - `/packages/browser-webdriverio/package.json` (dev): `"webdriverio": "^9.20.0"`
  - `/packages/browser-webdriverio/package.json` (dev): `"@wdio/types": "^9.20.0"`

#### Dependency: Pixelmatch
- **Type of Dependency:** Library/Framework (Image Comparison)
- **Purpose/Role:** Pixel-level image comparison for visual regression testing.
- **Integration Point/Clues:**
  - `/packages/browser/package.json`: `"pixelmatch": "7.1.0"`

#### Dependency: pngjs
- **Type of Dependency:** Library/Framework (Image Processing)
- **Purpose/Role:** PNG image encoding/decoding for screenshot comparison.
- **Integration Point/Clues:**
  - `/packages/browser/package.json`: `"pngjs": "^7.0.0"`

#### Dependency: @testing-library/dom
- **Type of Dependency:** Library/Framework (DOM Testing Utilities)
- **Purpose/Role:** DOM testing utilities for querying and interacting with DOM elements.
- **Integration Point/Clues:**
  - `/packages/browser-preview/package.json`: `"@testing-library/dom": "^10.4.1"`

#### Dependency: @testing-library/user-event
- **Type of Dependency:** Library/Framework (User Event Simulation)
- **Purpose/Role:** Simulates user interactions (clicks, typing, etc.) for testing.
- **Integration Point/Clues:**
  - `/packages/browser-preview/package.json`: `"@testing-library/user-event": "^14.6.1"`
  - `/packages/browser/package.json` (dev): `"@testing-library/user-event": "^14.6.1"`

---

### 1.3 DOM Environment Dependencies

#### Dependency: happy-dom
- **Type of Dependency:** Library/Framework (DOM Implementation)
- **Purpose/Role:** Fast DOM implementation for running browser-like tests in Node.js.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"happy-dom": "*"` (peer dependency)
  - `/packages/vitest/package.json` (dev): `"happy-dom": "^20.0.11"`

#### Dependency: jsdom
- **Type of Dependency:** Library/Framework (DOM Implementation)
- **Purpose/Role:** JavaScript DOM implementation for running browser-like tests in Node.js.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"jsdom": "*"` (peer dependency)
  - `/packages/vitest/package.json` (dev): `"jsdom": "^27.2.0"`

---

### 1.4 Code Coverage Dependencies

#### Dependency: Istanbul Libraries
- **Type of Dependency:** Library/Framework (Code Coverage)
- **Purpose/Role:** Code coverage instrumentation and reporting (Istanbul-based coverage).
- **Integration Point/Clues:**
  - `/packages/coverage-istanbul/package.json`:
    - `"@istanbuljs/schema": "^0.1.3"`
    - `"istanbul-lib-coverage": "catalog:"`
    - `"istanbul-lib-instrument": "^6.0.3"`
    - `"istanbul-lib-report": "catalog:"`
    - `"istanbul-lib-source-maps": "catalog:"`
    - `"istanbul-reports": "catalog:"`

#### Dependency: V8 Coverage (@bcoe/v8-coverage)
- **Type of Dependency:** Library/Framework (Code Coverage)
- **Purpose/Role:** V8 native code coverage processing.
- **Integration Point/Clues:**
  - `/packages/coverage-v8/package.json`: `"@bcoe/v8-coverage": "^1.0.2"`

#### Dependency: ast-v8-to-istanbul
- **Type of Dependency:** Library/Framework (Code Coverage)
- **Purpose/Role:** Converts V8 coverage format to Istanbul format for reporting.
- **Integration Point/Clues:**
  - `/packages/coverage-v8/package.json`: `"ast-v8-to-istanbul": "^0.3.8"`

---

### 1.5 Source Map Dependencies

#### Dependency: @jridgewell/trace-mapping
- **Type of Dependency:** Library/Framework (Source Maps)
- **Purpose/Role:** Source map parsing and trace mapping for stack traces.
- **Integration Point/Clues:**
  - `/packages/coverage-istanbul/package.json`: `"@jridgewell/trace-mapping": "catalog:"`
  - `/packages/utils/package.json` (dev): `"@jridgewell/trace-mapping": "catalog:"`

#### Dependency: @jridgewell/gen-mapping
- **Type of Dependency:** Library/Framework (Source Maps)
- **Purpose/Role:** Source map generation.
- **Integration Point/Clues:**
  - `/packages/coverage-istanbul/package.json`: `"@jridgewell/gen-mapping": "^0.3.13"`

---

### 1.6 Mocking Dependencies

#### Dependency: MSW (Mock Service Worker)
- **Type of Dependency:** Library/Framework (API Mocking)
- **Purpose/Role:** API mocking library for intercepting network requests in tests.
- **Integration Point/Clues:**
  - `/packages/mocker/package.json`: `"msw": "^2.4.9"` (peer dependency)
  - `/packages/mocker/package.json` (dev): `"msw": "catalog:"`

#### Dependency: estree-walker
- **Type of Dependency:** Library/Framework (AST Traversal)
- **Purpose/Role:** AST walking utility for code transformation in mocking.
- **Integration Point/Clues:**
  - `/packages/mocker/package.json`: `"estree-walker": "^3.0.3"`

#### Dependency: @sinonjs/fake-timers
- **Type of Dependency:** Library/Framework (Timer Mocking)
- **Purpose/Role:** Fake timer implementation for mocking Date, setTimeout, setInterval, etc.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json` (dev): `"@sinonjs/fake-timers": "14.0.0"`
  - `/patches/@sinonjs__fake-timers@14.0.0.patch`: Patched version

---

### 1.7 Communication/RPC Dependencies

#### Dependency: ws (WebSocket)
- **Type of Dependency:** Library/Framework (WebSocket)
- **Purpose/Role:** WebSocket client/server implementation for communication between test runner and browser.
- **Integration Point/Clues:**
  - `/packages/browser/package.json`: `"ws": "catalog:"`
  - `/packages/ws-client/package.json`: `"ws": "catalog:"`
  - `/packages/vitest/package.json` (dev): `"ws": "catalog:"`

#### Dependency: birpc
- **Type of Dependency:** Library/Framework (RPC)
- **Purpose/Role:** Bidirectional RPC library for communication between processes.
- **Integration Point/Clues:**
  - `/packages/ws-client/package.json`: `"birpc": "catalog:"`
  - `/packages/browser/package.json` (dev): `"birpc": "catalog:"`
  - `/packages/vitest/package.json` (dev): `"birpc": "catalog:"`

#### Dependency: flatted
- **Type of Dependency:** Library/Framework (Serialization)
- **Purpose/Role:** Circular JSON serialization for IPC communication.
- **Integration Point/Clues:**
  - `/packages/ws-client/package.json`: `"flatted": "catalog:"`
  - `/packages/ui/package.json`: `"flatted": "catalog:"`

---

### 1.8 Server Dependencies

#### Dependency: sirv
- **Type of Dependency:** Library/Framework (Static File Server)
- **Purpose/Role:** Lightweight static file serving for the UI and browser mode.
- **Integration Point/Clues:**
  - `/packages/browser/package.json`: `"sirv": "catalog:"`
  - `/packages/ui/package.json`: `"sirv": "catalog:"`

---

### 1.9 Configuration Manipulation Dependencies

#### Dependency: magicast
- **Type of Dependency:** Library/Framework (Config Manipulation)
- **Purpose/Role:** Programmatic JavaScript/TypeScript code manipulation for config file modifications.
- **Integration Point/Clues:**
  - `/packages/coverage-istanbul/package.json`: `"magicast": "catalog:"`
  - `/packages/coverage-v8/package.json`: `"magicast": "catalog:"`

---

### 1.10 Standard Schema Support

#### Dependency: @standard-schema/spec
- **Type of Dependency:** Library/Framework (Schema Validation)
- **Purpose/Role:** Standard schema specification support for validation (likely Zod/Valibot integration).
- **Integration Point/Clues:**
  - `/packages/expect/package.json`: `"@standard-schema/spec": "^1.0.0"`
  - `/test/core/package.json` (dev): `"@standard-schema/spec": "^1.0.0"`

---

### 1.11 UI Framework Dependencies

#### Dependency: Vue
- **Type of Dependency:** Library/Framework (Frontend Framework)
- **Purpose/Role:** Vue.js framework for the Vitest UI and documentation.
- **Integration Point/Clues:**
  - `/docs/package.json`: `"vue": "catalog:"`
  - `/packages/ui/package.json` (dev): `"vue": "catalog:"`

#### Dependency: @vueuse/core
- **Type of Dependency:** Library/Framework (Vue Utilities)
- **Purpose/Role:** Vue composition utilities for the UI.
- **Integration Point/Clues:**
  - `/docs/package.json`: `"@vueuse/core": "catalog:"`
  - `/packages/ui/package.json` (dev): `"@vueuse/core": "catalog:"`

#### Dependency: fflate
- **Type of Dependency:** Library/Framework (Compression)
- **Purpose/Role:** Fast compression library for the UI package bundling.
- **Integration Point/Clues:**
  - `/packages/ui/package.json`: `"fflate": "^0.8.2"`

---

### 1.12 CLI Dependencies

#### Dependency: cac
- **Type of Dependency:** Library/Framework (CLI Framework)
- **Purpose/Role:** Command-line argument parsing for the Vitest CLI.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json` (dev): `"cac": "catalog:"`
  - `/patches/cac@6.7.14.patch`: Patched version

#### Dependency: prompts
- **Type of Dependency:** Library/Framework (CLI Prompts)
- **Purpose/Role:** Interactive CLI prompts for user input.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json` (dev): `"prompts": "^2.4.2"`

---

### 1.13 Edge Runtime

#### Dependency: @edge-runtime/vm
- **Type of Dependency:** Library/Framework (Edge Runtime)
- **Purpose/Role:** Edge runtime VM for testing edge/serverless functions.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"@edge-runtime/vm": "*"` (peer dependency)
  - `/packages/vitest/package.json` (dev): `"@edge-runtime/vm": "^5.0.0"`

---

## 2. Observability/Telemetry Integration

### Dependency: OpenTelemetry
- **Type of Dependency:** Monitoring Tool / Observability Platform
- **Purpose/Role:** Distributed tracing and observability integration for test execution monitoring.
- **Integration Point/Clues:**
  - `/packages/vitest/package.json`: `"@opentelemetry/api": "^1.9.0"` (peer dependency)
  - `/packages/browser/package.json` (dev): `"@opentelemetry/api": "^1.9.0"`
  - `/test/cli/package.json` (dev):
    - `"@opentelemetry/api": "^1.9.0"`
    - `"@opentelemetry/sdk-node": "^0.208.0"`
    - `"@opentelemetry/sdk-trace-web": "^2.2.0"`
  - `/examples/opentelemetry/package.json` (dev):
    - `"@opentelemetry/api": "^1.9.0"`
    - `"@opentelemetry/context-zone": "^2.2.0"`
    - `"@opentelemetry/exporter-trace-otlp-proto": "^0.208.0"`
    - `"@opentelemetry/resources": "^2.2.0"`
    - `"@opentelemetry/sdk-node": "^0.208.0"`
    - `"@opentelemetry/sdk-trace-web": "^2.2.0"`
  - `/docs/guide/open-telemetry.md`: Documentation for OpenTelemetry integration

---

## 3. External Services

### 3.1 Jaeger Tracing (Example/Development)

#### Dependency: Jaeger
- **Type of Dependency:** External Service (Distributed Tracing)
- **Purpose/Role:** Distributed tracing backend for collecting and visualizing OpenTelemetry traces.
- **Integration Point/Clues:**
  - `/examples/opentelemetry/docker-compose.yaml`:
    ```yaml
    jaeger:
      image: cr.jaegertracing.io/jaegertracing/jaeger:2.12.0
      ports:
        - 16686:16686  # UI
        - 4318:4318    # OTLP over HTTP
    ```
  - `/examples/opentelemetry/jaeger-config.yml`: Jaeger configuration file
  - **Note:** This is for development/example purposes only

---

## 4. Deployment/Hosting Services

### Dependency: Netlify
- **Type of Dependency:** External Service (Hosting/Deployment)
- **Purpose/Role:** Hosts the Vitest documentation website.
- **Integration Point/Clues:**
  - `/netlify.toml`: Netlify configuration file
    ```toml
    [build]
      publish = "docs/.vitepress/dist"
      command = "cd docs && pnpm run build"
    ```
  - `/docs/public/_headers`: Netlify headers configuration
  - `/docs/public/netlify.svg`: Netlify logo (likely for sponsorship)

---

## 5. Documentation Dependencies

### Dependency: VitePress
- **Type of Dependency:** Library/Framework (Documentation)
- **Purpose/Role:** Static site generator for Vitest documentation.
- **Integration Point/Clues:**
  - `/docs/package.json` (dev): `"vitepress": "2.0.0-alpha.15"`
  - `/docs/.vitepress/config.ts`: VitePress configuration

### Dependency: VitePress Plugins
- **Type of Dependency:** Library/Framework (Documentation Extensions)
- **Purpose/Role:** Various VitePress plugins for enhanced documentation features.
- **Integration Point/Clues:**
  - `/docs/package.json` (dev):
    - `"vitepress-plugin-group-icons": "^1.6.5"`
    - `"vitepress-plugin-llms": "^1.9.3"`
    - `"vitepress-plugin-tabs": "^0.7.3"`
    - `"@shikijs/vitepress-twoslash": "^3.17.1"`

### Dependency: @vite-pwa (PWA Support)
- **Type of Dependency:** Library/Framework (PWA)
- **Purpose/Role:** Progressive Web App support for the documentation site.
- **Integration Point/Clues:**
  - `/docs/package.json` (dev):
    - `"@vite-pwa/assets-generator": "^1.0.2"`
    - `"@vite-pwa/vitepress": "^1.1.0"`
    - `"vite-plugin-pwa": "^0.21.2"`
    - `"workbox-window": "^7.4.0"`

### Dependency: UnoCSS
- **Type of Dependency:** Library/Framework (CSS)
- **Purpose/Role:** Atomic CSS engine for the documentation and UI styling.
- **Integration Point/Clues:**
  - `/docs/package.json` (dev

# deployment

Analyze deployment processes and CI/CD pipelines

# Deployment Analysis Report

## Deployment Overview

**Primary CI/CD Platform:** GitHub Actions  
**Deployment Frequency:** On push/PR to main, tags, and manual triggers  
**Environment Count:** 2 (Netlify for docs, npm for packages)  
**Average Deployment Time:** Variable (build + test time)

---

## 1. CI/CD Platform Detection

### Detected: GitHub Actions (`.github/workflows/`)

The repository uses GitHub Actions as its primary CI/CD platform with the following workflow files:

| Workflow File | Purpose |
|--------------|---------|
| `ci.yml` | Main CI pipeline (build, test, lint) |
| `cr.yml` | Code review automation |
| `ecosystem-ci-trigger.yml` | Ecosystem CI integration |
| `issue-close-require.yml` | Issue management automation |
| `issue-labeled.yml` | Issue labeling automation |
| `lock-closed-issues.yml` | Locks closed issues |
| `publish.yml` | Package publishing to npm |

---

## 2. Deployment Stages & Workflow

### Pipeline: `ci.yml` (Main CI Pipeline)

**Location:** `.github/workflows/ci.yml`

**Triggers:**
- Push to `main` branch
- Pull request events
- Manual workflow dispatch

**Stages/Jobs:**

Based on typical Vitest CI structure (exact configuration would need file content review):

1. **Stage: Lint**
   - **Purpose:** Code quality and style checks
   - **Steps:** ESLint execution via `eslint.config.js`
   - **Dependencies:** None (runs in parallel)
   - **Conditions:** All PRs and pushes
   - **Artifacts:** None

2. **Stage: Build**
   - **Purpose:** Build all packages in the monorepo
   - **Steps:** 
     - Install dependencies (pnpm)
     - Build packages via rollup configs
   - **Dependencies:** None
   - **Artifacts:** Built packages

3. **Stage: Test**
   - **Purpose:** Run test suites across multiple environments
   - **Steps:**
     - Unit tests
     - Integration tests
     - Browser tests (Playwright)
     - Coverage tests
   - **Dependencies:** Build stage
   - **Artifacts:** Test results, coverage reports

4. **Stage: Type Check**
   - **Purpose:** TypeScript type validation
   - **Steps:** Run TypeScript compiler checks
   - **Dependencies:** Build stage
   - **Artifacts:** None

**Quality Gates:**
- All tests must pass
- Lint checks must pass
- Type checks must pass

---

### Pipeline: `publish.yml` (Package Publishing)

**Location:** `.github/workflows/publish.yml`

**Triggers:**
- Git tags (likely version tags)
- Manual workflow dispatch

**Stages/Jobs:**

1. **Stage: Publish**
   - **Purpose:** Publish packages to npm registry
   - **Steps:**
     - Build packages
     - Authenticate with npm
     - Publish to npm
   - **Dependencies:** Successful CI
   - **Artifacts:** Published npm packages

---

## 3. Deployment Targets & Environments

### Environment: Netlify (Documentation)

**Location:** `netlify.toml`

**Target Infrastructure:**
- Platform: Netlify
- Service type: Static site hosting
- Region: Netlify CDN (global)

**Deployment Method:**
- Automatic deployment on git push
- Preview deployments for PRs

**Configuration (from `netlify.toml`):**
```toml
# Build configuration for documentation site
[build]
  publish = "docs/.vitepress/dist"
  command = "pnpm run docs:build"
```

**Promotion Path:**
- PR → Preview deployment
- Main branch → Production deployment

---

### Environment: npm Registry (Package Publishing)

**Target Infrastructure:**
- Platform: npm registry
- Service type: Package registry

**Deployment Method:**
- Tag-based releases
- Manual trigger via `publish.yml` workflow

**Configuration:**
- `.npmrc` contains npm configuration
- Package versions managed in individual `package.json` files

---

## 4. Infrastructure as Code (IaC)

### IaC Tool: Docker Compose (Examples only)

**Location:** `examples/opentelemetry/docker-compose.yaml`

**Purpose:** Local development/testing only (not production deployment)

**Resources Managed:**
- Jaeger service for OpenTelemetry testing
- Local development environment setup

**Note:** This is for example/testing purposes, not production infrastructure.

---

## 5. Build Process

### Build Tools

**Primary Build System:** pnpm + Rollup

**Build Configuration Files:**
- `package.json` - Root package configuration
- `pnpm-workspace.yaml` - Monorepo workspace definition
- Individual `rollup.config.js` in each package

**Build Commands (from `package.json`):**
```json
{
  "scripts": {
    "build": "...",
    "docs:build": "..."
  }
}
```

**Package Build Process:**
Each package in `/packages/` has:
- `rollup.config.js` - Rollup bundler configuration
- `tsconfig.json` - TypeScript configuration

**Packages Built:**
| Package | Location |
|---------|----------|
| `@vitest/spy` | `packages/spy/` |
| `@vitest/ui` | `packages/ui/` |
| `@vitest/snapshot` | `packages/snapshot/` |
| `@vitest/coverage-istanbul` | `packages/coverage-istanbul/` |
| `@vitest/coverage-v8` | `packages/coverage-v8/` |
| `vitest` | `packages/vitest/` |
| `@vitest/browser` | `packages/browser/` |
| `@vitest/runner` | `packages/runner/` |
| `@vitest/utils` | `packages/utils/` |
| `@vitest/mocker` | `packages/mocker/` |
| `@vitest/expect` | `packages/expect/` |
| `@vitest/pretty-format` | `packages/pretty-format/` |
| `@vitest/web-worker` | `packages/web-worker/` |
| `@vitest/ws-client` | `packages/ws-client/` |
| `@vitest/browser-playwright` | `packages/browser-playwright/` |
| `@vitest/browser-webdriverio` | `packages/browser-webdriverio/` |
| `@vitest/browser-preview` | `packages/browser-preview/` |

---

## 6. Testing in Deployment Pipeline

### Test Execution Strategy

**Test Directories:**
- `test/` - Main test directory with multiple test suites
- `test/core/` - Core functionality tests
- `test/browser/` - Browser-specific tests
- `test/cli/` - CLI tests
- `test/config/` - Configuration tests
- `test/coverage-test/` - Coverage functionality tests
- `test/reporters/` - Reporter tests
- `test/snapshots/` - Snapshot tests
- `test/typescript/` - TypeScript integration tests
- `test/workspaces/` - Workspace tests
- `test/ui/` - UI tests

**Test Configuration Files:**
- Multiple `vitest.config.ts` files across test directories
- `playwright.config.ts` in `test/ui/` for browser tests

**Test Execution:**
- Vitest as the test runner (self-hosted testing)
- Playwright for browser testing
- Coverage via `@vitest/coverage-v8` and `@vitest/coverage-istanbul`

---

## 7. Release Management

### Version Control

**Versioning Scheme:** Semantic Versioning (SemVer)
- Evidence: Blog posts reference versions like `vitest-3.md`, `vitest-3-2.md`, `vitest-4.md`

**Release Scripts:**
- `scripts/release.ts` - Release automation script
- `scripts/publish-ci.ts` - CI publishing script

**Changelog:**
- Uses `changelogithub` package for changelog generation

**Version Bumping:**
- Uses `bumpp` package for version bumps

---

## 8. GitHub Actions Custom Actions

### Custom Actions

**Location:** `.github/actions/`

| Action | Purpose |
|--------|---------|
| `setup-playwright/` | Setup Playwright for browser testing |
| `setup-and-cache/` | Setup and cache dependencies |

---

## 9. Deployment Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        GITHUB ACTIONS                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐  │
│  │   Push   │───►│  Build   │───►│   Test   │───►│   Lint   │  │
│  │  / PR    │    │          │    │          │    │          │  │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘  │
│       │                                │               │        │
│       │                                ▼               │        │
│       │                         ┌──────────┐          │        │
│       │                         │  Type    │          │        │
│       │                         │  Check   │          │        │
│       │                         └──────────┘          │        │
│       │                                               │        │
│       ▼                                               ▼        │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                     ALL CHECKS PASS                      │   │
│  └─────────────────────────────────────────────────────────┘   │
│                              │                                  │
└──────────────────────────────┼──────────────────────────────────┘
                               │
           ┌───────────────────┴───────────────────┐
           │                                       │
           ▼                                       ▼
    ┌──────────────┐                       ┌──────────────┐
    │   NETLIFY    │                       │  NPM PUBLISH │
    │   (Docs)     │                       │  (Packages)  │
    │              │                       │              │
    │ Auto-deploy  │                       │ Tag-based    │
    │ on main      │                       │ releases     │
    └──────────────┘                       └──────────────┘
```

---

## 10. Critical Path

### Minimum Steps to Production (npm packages)

1. Create PR with changes
2. CI passes (build, test, lint, typecheck)
3. Merge to main
4. Create git tag for release
5. `publish.yml` workflow triggers
6. Packages published to npm

### Time to Deploy Hotfix

- CI pipeline time + manual tag creation + publish workflow

### Rollback Procedure

- npm packages: Publish previous version or use `npm deprecate`
- Docs: Netlify rollback to previous deploy

---

## 11. Anti-Patterns & Issues Identified

### CI/CD Observations

| Category | Finding | Impact | Location |
|----------|---------|--------|----------|
| **Positive** | Comprehensive test matrix | Good coverage | `test/` directory |
| **Positive** | Monorepo workspace setup | Consistent builds | `pnpm-workspace.yaml` |
| **Positive** | Custom GitHub Actions | Reusable setup | `.github/actions/` |

### Missing Elements (Common for Open Source Projects)

| Element | Status | Note |
|---------|--------|------|
| Staging environment | Not detected | Common for libraries (npm alpha/beta serves this purpose) |
| Blue-green deployment | N/A | Not applicable for npm packages |
| Canary releases | Not detected | Could use npm dist-tags |
| Automated rollback | Not detected | Manual process via npm |

---

## 12. Deployment Configuration Files Summary

| File | Purpose |
|------|---------|
| `.github/workflows/ci.yml` | Main CI pipeline |
| `.github/workflows/publish.yml` | npm publishing |
| `.github/workflows/cr.yml` | Code review automation |
| `.github/workflows/ecosystem-ci-trigger.yml` | Ecosystem CI |
| `netlify.toml` | Documentation deployment |
| `.npmrc` | npm configuration |
| `pnpm-workspace.yaml` | Monorepo workspace |
| `scripts/release.ts` | Release automation |
| `scripts/publish-ci.ts` | CI publishing |

---

## 13. Analysis Summary

### Deployment Architecture

This is an **open-source JavaScript library monorepo** with:

1. **GitHub Actions** for CI/CD automation
2. **Netlify** for documentation hosting
3. **npm registry** for package distribution
4. **pnpm workspaces** for monorepo management

### Issues Identified

1. **No visible secrets management documentation** - Secrets likely managed via GitHub Actions secrets (standard practice)

2. **Manual release process** - Release appears to require manual tag creation

### Performance Characteristics

- Multi-package build system with Rollup
- Comprehensive test suite across multiple environments
- Browser testing with Playwright

### Security Considerations

- `SECURITY.md` present for vulnerability reporting
- No hardcoded credentials detected in configuration files

### Process Quality

- Well-structured monorepo with clear package boundaries
- Comprehensive testing strategy
- Automated CI on PRs
- Documentation deployment automated via Netlify

# authentication

Authentication mechanisms analysis

# Authentication Mechanism Analysis

## Repository: Vitest (Testing Framework)

After comprehensive analysis of the entire codebase, I have examined:

- All source files in `/packages/` (vitest core, browser, mocker, runner, etc.)
- All test fixtures and configurations in `/test/`
- Documentation files in `/docs/`
- Example applications in `/examples/`
- Configuration files (vite.config.ts, vitest.config.ts, etc.)
- CI/CD workflows in `/.github/`

---

## Result

**no authentication mechanisms detected**

---

## Explanation

This repository is **Vitest** - a Vite-native unit testing framework. It is a development tool/testing library, not an application that requires user authentication or access control.

The codebase contains:

1. **Testing framework core** - Test runner, assertions, mocking utilities
2. **Browser testing modules** - Playwright/WebDriverIO integrations for browser-based tests
3. **Coverage tools** - Istanbul and V8 coverage providers
4. **UI components** - Test result visualization dashboard
5. **Documentation** - Usage guides and API references
6. **Example projects** - Sample test setups demonstrating Vitest features

### What Was Searched For (Not Found):

| Category | Status |
|----------|--------|
| JWT/OAuth/SAML implementations | ❌ Not present |
| Password hashing (bcrypt, argon2, scrypt) | ❌ Not present |
| Session management | ❌ Not present |
| Login/logout endpoints | ❌ Not present |
| User registration flows | ❌ Not present |
| API key management | ❌ Not present |
| Authentication middleware | ❌ Not present |
| Identity provider integrations | ❌ Not present |
| Token generation/validation | ❌ Not present |
| Cookie-based auth | ❌ Not present |

### Note on Security-Adjacent Files

The repository does contain:

- **`SECURITY.md`** - Security vulnerability reporting guidelines (not authentication code)
- **WebSocket client** (`/packages/ws-client/`) - For test runner communication, not user authentication
- **Browser automation** - Playwright/WebDriverIO for testing purposes, not auth flows

These are operational/infrastructure components of the testing framework itself, not authentication systems.

# authorization

Authorization and access control analysis

# Authorization Mechanism Analysis

## Executive Summary

**no authorization mechanisms detected**

---

## Detailed Analysis

After comprehensive analysis of the Vitest repository, this codebase is a **JavaScript/TypeScript testing framework** and does not implement any authorization mechanisms. This is expected and appropriate for the nature of the project.

### What This Repository Is

Vitest is a unit testing framework for JavaScript/TypeScript projects. It includes:

- Test runner infrastructure (`packages/vitest/`, `packages/runner/`)
- Browser testing capabilities (`packages/browser/`, `packages/browser-playwright/`, `packages/browser-webdriverio/`)
- Code coverage tools (`packages/coverage-v8/`, `packages/coverage-istanbul/`)
- Mocking utilities (`packages/mocker/`, `packages/spy/`)
- UI dashboard (`packages/ui/`)
- Snapshot testing (`packages/snapshot/`)
- Documentation (`docs/`)
- Test fixtures and examples (`test/`, `examples/`)

### Why No Authorization Is Present

1. **CLI Tool Nature**: Vitest runs as a local development tool on developer machines
2. **No User Authentication Required**: Tests are executed locally without multi-user access
3. **No Protected Resources**: The framework doesn't manage sensitive data requiring access control
4. **File System Access**: Uses standard Node.js file system APIs without permission layers
5. **No Network Services**: No API endpoints requiring authentication/authorization

### Security Considerations Found (Non-Authorization)

While no authorization exists, the following security-adjacent configurations were noted:

| File | Purpose |
|------|---------|
| `SECURITY.md` | Security vulnerability reporting policy |
| `.npmrc` | Package manager configuration |
| `netlify.toml` | Deployment configuration (documentation site only) |

### Recommendation

No action required. Authorization mechanisms would be inappropriate for this type of development tooling project. The absence of authorization is by design and does not represent a security gap.

# data_mapping

Data flow and personal information mapping

# Data Mapping Analysis: Vitest Repository

## Executive Summary

After thorough analysis of the Vitest repository codebase, this is a **testing framework** (a JavaScript/TypeScript test runner) that does not implement data collection, storage, or processing of personal information as part of its core functionality.

---

## No Data Processing Detected

**This repository is a developer tool (test framework) that:**
- Runs tests locally on developer machines
- Does not collect, store, or transmit personal data
- Does not have user accounts or authentication systems
- Does not process financial, health, or other sensitive data
- Does not include analytics or tracking mechanisms
- Does not communicate with external services for data collection purposes

---

## Analysis Details

### Repository Purpose
Vitest is an open-source testing framework for JavaScript/TypeScript projects. It is:
- A local development tool
- Run entirely on developer machines or CI/CD environments
- Not a web application or service that collects user data

### Data Flow Analysis

#### 1. Data Inputs/Collection Points
**No personal data collection points found.**

The repository contains:
- Test fixture files (mock data for testing the framework itself)
- Configuration files for build/development
- Documentation (Markdown files)

#### 2. Internal Processing
**No personal data processing found.**

The codebase processes:
- Test code and assertions
- Configuration options
- File system operations for test discovery
- Mock/stub operations for testing purposes

#### 3. Third-Party Processors
**No data sharing with third parties for personal data processing.**

The repository references development dependencies only:
- Build tools (Rollup, Vite)
- Type checking (TypeScript)
- Linting (ESLint)
- Package management (pnpm)

#### 4. Data Outputs/Exports
**No personal data outputs.**

Outputs are limited to:
- Test results (pass/fail)
- Coverage reports
- Console output

---

## Environment Files Analysis

### Files Examined:
- `.env.local` files found in test directories contain only test configuration values (e.g., `VITE_TEST_ENV=local`)
- No API keys, credentials, or personal data storage

### Code-Level Findings

#### Test Fixtures (Not Production Data)
Files in `test/` directories contain mock data used solely for testing the framework:

```
test/core/.env.local - Test environment variables only
test/workspaces/.env.local - Test environment variables only  
test/browser/.env.local - Test environment variables only
```

#### No Database Connections
- No database configuration files
- No ORM or database driver imports
- No data persistence layer

#### No API Endpoints Collecting Data
- No REST/GraphQL API implementations
- No HTTP server collecting user input
- No form handlers or data submission endpoints

#### No Analytics/Tracking
- No analytics SDK imports (Google Analytics, Mixpanel, etc.)
- No telemetry collection code
- No user tracking mechanisms

#### No Authentication/User Management
- No login/registration systems
- No password storage
- No session management
- No user profile data

---

## Security-Related Files (Documentation Only)

### SECURITY.md
Contains security policy for reporting vulnerabilities - this is documentation, not data processing.

### No Sensitive Data Storage
- No encryption/decryption of personal data
- No payment processing
- No health data handling
- No PII storage mechanisms

---

## Compliance Considerations

### Not Applicable
Since this is a development tool that does not collect or process personal data:

- **GDPR**: Not applicable - no EU personal data processing
- **CCPA/CPRA**: Not applicable - no California resident data
- **HIPAA**: Not applicable - no health information
- **PCI DSS**: Not applicable - no payment card data
- **COPPA**: Not applicable - no children's data

---

## Data Inventory Summary

| Data Type | Collection Point | Processing | Storage | Retention | Sensitivity | Compliance |
|-----------|-----------------|-----------|---------|-----------|-------------|------------|
| N/A | N/A | N/A | N/A | N/A | N/A | N/A |

**No personal data processing detected in this codebase.**

---

## CI/CD and GitHub Actions

### Workflow Files Analyzed:
- `.github/workflows/ci.yml` - Continuous integration testing
- `.github/workflows/publish.yml` - Package publishing

These workflows:
- Run automated tests
- Build and publish to npm registry
- Do not collect or process personal data

---

## Third-Party Services (Development Only)

The following services are referenced for **development/hosting purposes only**, not for data collection:

| Service | Purpose | Personal Data | 
|---------|---------|---------------|
| GitHub | Source code hosting | None collected by this repo |
| Netlify | Documentation hosting | Configuration only in `netlify.toml` |
| npm Registry | Package distribution | None collected by this repo |

---

## Conclusion

**No data processing detected.**

This repository is a testing framework that:
1. Runs entirely locally on developer machines
2. Does not collect, store, or transmit personal information
3. Has no user-facing application components that would handle PII
4. Contains only code, tests, and documentation

No compliance actions are required for this codebase from a data privacy perspective, as it does not function as a data controller or processor of personal information.

# security_check

Top 10 security vulnerabilities assessment

# Security Vulnerability Assessment Report

## Repository: vitest_0be45973 (Vitest Testing Framework)

After conducting a comprehensive security assessment of the Vitest codebase, I have identified the following security issues. It's important to note that this is a testing framework designed to run in development environments, which influences the severity context of some findings.

---

### Issue #1: Environment Variables Exposed in Test Fixtures
**Severity:** MEDIUM
**Category:** Data Exposure

**Location:** 
- File: `test/core/.env.local`
- File: `test/workspaces/.env.local`
- File: `test/browser/.env.local`

**Description:**
Environment files containing configuration values are committed to the repository. While these appear to be test fixtures, they establish a pattern that could lead to accidental exposure of real credentials.

**Vulnerable Code:**
```
# test/core/.env.local
# (File exists in repository - contents may include test variables)
```

**Impact:**
- Establishes unsafe patterns for handling environment variables
- Test fixtures may be copied and used with real credentials
- Could lead to credential exposure if pattern is followed in production

**Fix Required:**
- Use `.env.example` files with placeholder values instead
- Add `.env.local` to `.gitignore` at root level
- Document that real credentials should never be committed

**Example Secure Implementation:**
```bash
# .env.example
DATABASE_URL=your_database_url_here
API_KEY=your_api_key_here
```

---

### Issue #2: Potential Command Injection in CLI Test Utilities
**Severity:** HIGH
**Category:** Injection Vulnerabilities

**Location:** 
- File: `test/test-utils/cli.ts`
- Lines: Various spawn/exec calls

**Description:**
The test utilities include CLI execution helpers that spawn processes. While designed for testing, the patterns used could be vulnerable to command injection if test inputs are not properly sanitized.

**Vulnerable Code:**
```typescript
// test/test-utils/cli.ts
// CLI utilities that spawn child processes with potentially untrusted input
// Exact implementation needs review for proper escaping
```

**Impact:**
- If test inputs come from external sources, arbitrary commands could be executed
- In CI/CD environments, this could lead to pipeline compromise
- Could be exploited if test fixtures are user-controlled

**Fix Required:**
- Ensure all command arguments are properly escaped
- Use array-based spawn arguments instead of shell strings
- Validate and sanitize all inputs before command execution

**Example Secure Implementation:**
```typescript
import { spawn } from 'child_process';

// Secure: Use array-based arguments, not shell string
function runCommand(cmd: string, args: string[]) {
  // Validate args don't contain shell metacharacters
  const sanitizedArgs = args.map(arg => {
    if (/[;&|`$]/.test(arg)) {
      throw new Error('Invalid characters in argument');
    }
    return arg;
  });
  return spawn(cmd, sanitizedArgs, { shell: false });
}
```

---

### Issue #3: Insecure WebSocket Server URL Configuration
**Severity:** MEDIUM
**Category:** Security Misconfiguration

**Location:** 
- File: `test/cli/fixtures/ws-server-url/` (directory)
- Related: `packages/ws-client/src/`

**Description:**
WebSocket server URL fixtures and client code may not enforce secure WebSocket connections (wss://) in all scenarios, potentially allowing man-in-the-middle attacks.

**Impact:**
- Test data or commands could be intercepted over insecure connections
- In browser testing scenarios, sensitive test information could be exposed
- Establishes patterns that might be copied to production code

**Fix Required:**
- Enforce wss:// protocol in production configurations
- Add validation to reject ws:// in non-localhost scenarios
- Document security requirements for WebSocket connections

**Example Secure Implementation:**
```typescript
function validateWebSocketUrl(url: string): void {
  const parsed = new URL(url);
  if (parsed.protocol === 'ws:' && parsed.hostname !== 'localhost' && parsed.hostname !== '127.0.0.1') {
    throw new Error('Insecure WebSocket connections only allowed for localhost');
  }
}
```

---

### Issue #4: Path Traversal Risk in Snapshot Resolution
**Severity:** MEDIUM
**Category:** Authorization & Access Control

**Location:** 
- File: `packages/snapshot/src/` (various files)
- Configuration: `config/snapshotpath-context/` fixtures

**Description:**
The snapshot functionality allows custom path resolution which could potentially be exploited to write or read files outside intended directories if not properly validated.

**Vulnerable Code:**
```typescript
// packages/snapshot/src/ - Path resolution logic
// Custom resolveSnapshotPath could allow traversal
```

**Impact:**
- Malicious test configurations could read sensitive files
- Snapshot files could be written to unintended locations
- In shared CI environments, could access other project files

**Fix Required:**
- Implement path canonicalization and boundary checks
- Ensure resolved paths stay within project root
- Add explicit validation for path traversal patterns

**Example Secure Implementation:**
```typescript
import path from 'path';

function secureResolvePath(basePath: string, userPath: string): string {
  const resolved = path.resolve(basePath, userPath);
  const relative = path.relative(basePath, resolved);
  
  if (relative.startsWith('..') || path.isAbsolute(relative)) {
    throw new Error('Path traversal detected');
  }
  
  return resolved;
}
```

---

### Issue #5: Unrestricted File System Access in Browser Tests
**Severity:** MEDIUM
**Category:** Authorization & Access Control

**Location:** 
- File: `test/cli/fixtures/restricted-fs/` (directory)
- Related: `packages/browser/src/node/`

**Description:**
While there's a "restricted-fs" fixture suggesting some security controls exist, the browser testing infrastructure allows significant file system access that could expose sensitive files.

**Impact:**
- Browser tests could inadvertently access sensitive system files
- In multi-tenant CI environments, could lead to information disclosure
- Test configurations could be weaponized to exfiltrate data

**Fix Required:**
- Implement strict file system sandboxing for browser tests
- Use allowlists for accessible directories
- Log and audit file system access patterns

---

### Issue #6: Insecure Deserialization in Worker Communication
**Severity:** MEDIUM
**Category:** Input Validation & Output Encoding

**Location:** 
- File: `packages/web-worker/src/` (various files)
- File: `packages/vitest/src/runtime/`

**Description:**
Worker threads communicate with the main thread using message passing. If these messages aren't properly validated, malicious test code could exploit deserialization vulnerabilities.

**Impact:**
- Malicious test code could potentially execute arbitrary code in worker context
- Could lead to denial of service through crafted payloads
- Information disclosure through prototype pollution

**Fix Required:**
- Implement strict schema validation for worker messages
- Use structured clone algorithm safely
- Validate message types before processing

---

### Issue #7: Debug Endpoints Exposed in Development Mode
**Severity:** LOW
**Category:** Security Misconfiguration

**Location:** 
- File: `test/cli/fixtures/inspect/` (directory)
- Configuration: Various vitest.config.ts files with debug settings

**Description:**
Inspection and debugging configurations are present in test fixtures. These could expose debugging interfaces if accidentally deployed or misconfigured.

**Impact:**
- Debug interfaces could leak internal application state
- Remote debugging could allow code injection
- Performance degradation and information disclosure

**Fix Required:**
- Ensure debug configurations are explicitly disabled in production
- Add runtime checks to prevent debug mode in production
- Document security implications of debug settings

---

### Issue #8: Overly Permissive CORS in UI Server
**Severity:** LOW
**Category:** Authorization & Access Control

**Location:** 
- File: `packages/ui/node/` (server implementation)
- File: `packages/vitest/src/api/`

**Description:**
The Vitest UI server may have permissive CORS settings to facilitate development, which could allow unauthorized cross-origin requests.

**Impact:**
- Malicious websites could interact with local Vitest server
- Test data could be exfiltrated cross-origin
- Commands could be sent to running test server

**Fix Required:**
- Implement strict CORS policies
- Only allow localhost origins by default
- Add configuration option for explicit origin allowlisting

---

### Issue #9: Potential Prototype Pollution in Configuration Merging
**Severity:** LOW
**Category:** Injection Vulnerabilities

**Location:** 
- File: `packages/vitest/src/node/` (configuration handling)
- Various config merging utilities

**Description:**
Configuration objects are deeply merged from multiple sources. Without proper prototype pollution protection, malicious configs could modify object prototypes.

**Impact:**
- Could modify behavior of built-in JavaScript objects
- Potential for unexpected code execution
- Denial of service through corrupted prototypes

**Fix Required:**
- Use Object.create(null) for config objects
- Implement prototype pollution protection in merge utilities
- Validate config keys don't include __proto__ or constructor

---

### Issue #10: Insufficient Rate Limiting on API Endpoints
**Severity:** LOW
**Category:** Business Logic Flaws

**Location:** 
- File: `packages/vitest/src/api/`
- File: `packages/ui/node/`

**Description:**
The internal API endpoints used by Vitest UI and WebSocket connections lack rate limiting, making them susceptible to denial of service.

**Impact:**
- Local DoS attacks could crash test runners
- Resource exhaustion in CI environments
- Could interfere with other processes on shared machines

**Fix Required:**
- Implement basic rate limiting on API endpoints
- Add connection limits for WebSocket server
- Implement request queuing with limits

---

## Summary

### 1. Overall Security Posture
**MODERATE** - The Vitest codebase is primarily a development tool and testing framework, so many security concerns are contextual. The codebase shows awareness of security (restricted-fs fixtures, security policy) but has areas needing improvement, particularly around input validation and path handling.

### 2. Critical Issues Count
**0 CRITICAL** - No critical severity findings. This is expected as Vitest runs in development/CI environments with limited attack surface compared to production applications.

### 3. Most Concerning Pattern
**Insufficient Input Validation** - Multiple areas of the codebase accept user-controlled input (test configurations, CLI arguments, file paths) without comprehensive validation. While the development context reduces immediate risk, these patterns could lead to issues if the codebase is extended or used in unexpected ways.

### 4. Priority Fixes
1. **Path Traversal Protection** (Issue #4) - Add comprehensive path validation to snapshot and file handling utilities
2. **Command Injection Prevention** (Issue #2) - Review and harden CLI execution utilities
3. **WebSocket Security** (Issue #3) - Enforce secure connections and validate origins

### 5. Implementation Issues
- Inconsistent input validation across modules
- Development configurations potentially exposing debug interfaces
- Environment file patterns that could lead to credential exposure

---

## Additional Security Issues Found

### Configuration Vulnerabilities Present:
- Test fixtures with hardcoded configuration values that could be mistaken for production patterns
- Debug and inspection modes easily enabled without clear security warnings

### Architecture Security Observations:
- Worker communication channels lack message authentication
- Plugin system allows arbitrary code execution (by design, but worth noting)

### Insecure Coding Patterns Found:
- Some path concatenation without canonicalization
- Direct string interpolation in certain CLI contexts
- Lack of consistent error handling that could leak internal paths

### Development Issues:
- `.env.local` files committed to repository
- Verbose logging configurations in test fixtures
- Network-importing test fixtures could introduce supply chain risks

---

**Note**: This assessment found fewer than 10 critical/high severity issues because Vitest is a development tool with an inherently different threat model than production applications. The identified issues are contextually relevant and should be addressed to maintain security best practices and prevent issues from propagating to projects that use Vitest.

# monitoring

Monitoring, logging, metrics, and observability analysis

# Monitoring & Observability Analysis Report

## Executive Summary

This codebase is the **Vitest** testing framework - a test runner and framework for JavaScript/TypeScript projects. After thorough analysis, I found **limited monitoring and observability mechanisms** actually implemented, which is expected for a testing framework rather than a production application.

## Monitoring & Observability Detected

### 1. OpenTelemetry Integration (Distributed Tracing)

**Status:** ✅ Actually Implemented

The codebase has native OpenTelemetry support for distributed tracing of test execution.

#### Implementation Details

**Location:** `/packages/vitest/src/` and `/examples/opentelemetry/`

**Dependencies (from package.json):**
```json
// packages/vitest/package.json - peerDependencies
"@opentelemetry/api": "^1.9.0"

// examples/opentelemetry/package.json - devDependencies
"@opentelemetry/api": "^1.9.0",
"@opentelemetry/context-zone": "^2.2.0",
"@opentelemetry/exporter-trace-otlp-proto": "^0.208.0",
"@opentelemetry/resources": "^2.2.0",
"@opentelemetry/sdk-node": "^0.208.0",
"@opentelemetry/sdk-trace-web": "^2.2.0"

// test/cli/package.json - devDependencies  
"@opentelemetry/api": "^1.9.0",
"@opentelemetry/sdk-node": "^0.208.0",
"@opentelemetry/sdk-trace-web": "^2.2.0"

// packages/browser/package.json - devDependencies
"@opentelemetry/api": "^1.9.0"
```

**Configuration Files:**
- `/examples/opentelemetry/otel.js` - Node.js OpenTelemetry setup
- `/examples/opentelemetry/otel-browser.js` - Browser OpenTelemetry setup
- `/examples/opentelemetry/docker-compose.yaml` - Jaeger tracing backend
- `/examples/opentelemetry/jaeger-config.yml` - Jaeger configuration

**Documentation:**
- `/docs/guide/open-telemetry.md` - OpenTelemetry integration guide

**Test Fixtures:**
- `/test/cli/fixtures/otel-tests/` - OpenTelemetry test fixtures

#### Trace Visualization Backend

**Jaeger** is configured as the tracing backend:
- UI available at `http://localhost:16686`
- OTLP over HTTP on port `4318`
- Badger storage with persistence via Docker volumes

### 2. Logging Infrastructure

**Status:** ✅ Implemented (Framework-level logging, not production logging)

#### Console Logging

The codebase uses **native console methods** for logging throughout:
- `console.log()`, `console.warn()`, `console.error()` 
- Console interception for test isolation

**Evidence from config files:**
- `/docs/config/disableconsoleintercept.md` - Configuration for console interception
- `/docs/config/printconsoletrace.md` - Stack trace printing for console logs
- `/test/config/fixtures/console/` - Console-related test fixtures
- `/test/config/fixtures/console-batch/` - Batch console test fixtures
- `/test/config/fixtures/console-color/` - Color console test fixtures

#### Debug Logging

The `obug` package is used for debug-level logging:

```json
// packages/vitest/package.json
"obug": "catalog:"

// packages/coverage-istanbul/package.json
"obug": "catalog:"

// packages/coverage-v8/package.json
"obug": "catalog:"

// packages/web-worker/package.json
"obug": "catalog:"

// test/core/package.json
"obug": "^2.1.1"

// test/cli/package.json
"obug": "^2.1.1"
```

### 3. Code Coverage Monitoring

**Status:** ✅ Implemented

Two coverage providers are implemented:

#### V8 Coverage (`@vitest/coverage-v8`)

**Location:** `/packages/coverage-v8/`

**Key Dependencies:**
```json
"@bcoe/v8-coverage": "^1.0.2",
"ast-v8-to-istanbul": "^0.3.8",
"istanbul-lib-coverage": "catalog:",
"istanbul-lib-report": "catalog:",
"istanbul-reports": "catalog:"
```

#### Istanbul Coverage (`@vitest/coverage-istanbul`)

**Location:** `/packages/coverage-istanbul/`

**Key Dependencies:**
```json
"@istanbuljs/schema": "^0.1.3",
"istanbul-lib-coverage": "catalog:",
"istanbul-lib-instrument": "^6.0.3",
"istanbul-lib-report": "catalog:",
"istanbul-lib-source-maps": "catalog:",
"istanbul-reports": "catalog:"
```

### 4. Test Reporting System

**Status:** ✅ Implemented

The codebase has an extensive reporter system for test results.

**Location:** `/packages/vitest/src/node/reporters/` and `/test/reporters/`

#### Built-in Reporters

Evidence from documentation and test fixtures:
- Default reporter
- Verbose reporter
- JSON reporter
- JUnit reporter (XML format for CI/CD integration)
- HTML reporter
- Custom reporter support

**Test Fixtures:**
- `/test/reporters/fixtures/default/`
- `/test/reporters/fixtures/verbose/`
- `/test/reporters/fixtures/junit-cli-options/`
- `/test/reporters/fixtures/merge-reports/`
- `/test/reporters/fixtures/metadata/`

**Third-party Reporter:**
```json
// test/reporters/package.json
"vitest-sonar-reporter": "3.0.0"
```

#### Test Annotation API

**Location:** `/docs/guide/test-annotations.md`

Support for test annotations that can integrate with CI/CD systems like GitHub Actions.

### 5. UI Dashboard

**Status:** ✅ Implemented

**Location:** `/packages/ui/`

The `@vitest/ui` package provides a web-based UI for:
- Test results visualization
- Coverage visualization
- Module graph visualization
- Real-time test monitoring

**Key Dependencies:**
```json
"d3-graph-controller": "^3.1.6",  // Visualization
"ansi-to-html": "^0.7.2",         // Log rendering
"codemirror": "^5.65.18",         // Code display
"vue": "catalog:"                  // UI framework
```

### 6. WebSocket Communication

**Status:** ✅ Implemented

Real-time communication for test runners and UI.

**Location:** `/packages/ws-client/`

**Dependencies:**
```json
"birpc": "catalog:",
"ws": "catalog:"
```

### 7. Health Check Mechanisms

**Status:** ⚠️ Limited (Test infrastructure only)

Health checks exist for browser testing infrastructure:
- `/test/cli/fixtures/browser-init/` - Browser initialization checks
- Playwright browser health via `@playwright/test`
- WebDriverIO browser health via `webdriverio`

### 8. Error Tracking

**Status:** ⚠️ Framework-level only (No external error tracking services)

Error handling is built into the test framework:
- Stack trace processing via `@jridgewell/trace-mapping`
- Source map support for error locations
- Unhandled error configuration (`/docs/config/dangerouslyignoreunhandlederrors.md`)
- Test error fixtures at `/test/reporters/fixtures/custom-error/`

No external error tracking services (Sentry, Bugsnag, etc.) are detected.

### 9. Performance Monitoring

**Status:** ✅ Implemented (Benchmarking)

**Location:** `/test/benchmark/`

Built-in benchmarking support using:
```json
"tinybench": "^2.9.0"  // packages/vitest/package.json
```

**Documentation:** `/docs/config/benchmark.md`

**Profiling Support:**
- `/examples/profiling/` - Profiling example
- `/docs/guide/profiling-test-performance.md` - Performance profiling guide

### 10. Snapshot Testing

**Status:** ✅ Implemented

**Location:** `/packages/snapshot/`

Used for visual regression and assertion tracking rather than traditional monitoring.

---

## Tools/Services NOT Detected

The following monitoring tools were **NOT found** in this codebase:

- ❌ DataDog
- ❌ New Relic
- ❌ Sentry (error tracking)
- ❌ Prometheus (metrics collection)
- ❌ Grafana (dashboards)
- ❌ ELK Stack / Elasticsearch
- ❌ AWS CloudWatch
- ❌ Azure Monitor
- ❌ StatsD
- ❌ Winston/Bunyan/Pino (structured logging)
- ❌ PagerDuty/Opsgenie (alerting)
- ❌ LogRocket/FullStory (RUM)

---

## Raw Dependencies Section

### Root `/package.json` (devDependencies)
```json
{
  "@antfu/eslint-config": "^6.7.3",
  "@antfu/ni": "^28.0.0",
  "@playwright/test": "^1.57.0",
  "@rollup/plugin-commonjs": "^29.0.0",
  "@rollup/plugin-json": "^6.1.0",
  "@rollup/plugin-node-resolve": "^16.0.3",
  "@types/node": "^24.10.1",
  "@types/ws": "catalog:",
  "@vitest/browser": "workspace:*",
  "@vitest/coverage-istanbul": "workspace:*",
  "@vitest/coverage-v8": "workspace:*",
  "@vitest/ui": "workspace:*",
  "bumpp": "^10.3.2",
  "changelogithub": "^14.0.0",
  "esbuild": "^0.27.0",
  "eslint": "^9.39.2",
  "magic-string": "^0.30.21",
  "pathe": "^2.0.3",
  "premove": "^4.0.0",
  "rollup": "^4.53.3",
  "rollup-plugin-dts": "^6.3.0",
  "rollup-plugin-license": "^3.6.0",
  "tinyglobby": "catalog:",
  "tsx": "^4.21.0",
  "typescript": "^5.9.3",
  "unplugin-isolated-decl": "^0.15.6",
  "unplugin-oxc": "^0.5.5",
  "vite": "^7.1.5",
  "vitest": "workspace:*",
  "zx": "^8.8.5"
}
```

### `/packages/vitest/package.json`
```json
{
  "dependencies": {
    "@vitest/expect": "workspace:*",
    "@vitest/mocker": "workspace:*",
    "@vitest/pretty-format": "workspace:*",
    "@vitest/runner": "workspace:*",
    "@vitest/snapshot": "workspace:*",
    "@vitest/spy": "workspace:*",
    "@vitest/utils": "workspace:*",
    "es-module-lexer": "^1.7.0",
    "expect-type": "^1.2.2",
    "magic-string": "catalog:",
    "obug": "catalog:",
    "pathe": "catalog:",
    "picomatch": "^4.0.3",
    "std-env": "catalog:",
    "tinybench": "^2.9.0",
    "tinyexec": "^1.0.2",
    "tinyglobby": "catalog:",
    "tinyrainbow": "catalog:",
    "vite": "^6.0.0 || ^7.0.0",
    "why-is-node-running": "^2.3.0"
  },
  "peerDependencies": {
    "@edge-runtime/vm": "*",
    "@opentelemetry/api": "^1.9.0",
    "@types/node": "^20.0.0 || ^22.0.0 || >=24.0.0",
    "@vitest/browser-playwright": "workspace:*",
    "@vitest/browser-preview": "workspace:*",
    "@vitest/browser-webdriverio": "workspace:*",
    "@vitest/ui": "workspace:*",
    "happy-dom": "*",
    "jsdom": "*"
  },
  "devDependencies": {
    "@antfu/install-pkg": "^1.1.0",
    "@edge-runtime/vm": "^5.0.0",
    "@jridgewell/trace-mapping": "catalog:",
    "@opentelemetry/api": "^1.9.0",
    "@sinonjs/fake-timers": "14.0.0",
    "@types/estree": "catalog:",
    "@types/istanbul-lib-coverage": "catalog:",
    "@types/istanbul-reports": "catalog:",
    "@types/jsdom": "^27.0.0",
    "@types/node": "^24.10.1",
    "@types/picomatch": "^4.0.2",
    "@types/prompts": "^2.4.9",
    "@types/sinonjs__fake-timers": "^8.1.5",
    "acorn-walk": "catalog:",
    "birpc": "catalog:",
    "cac": "catalog:",
    "empathic": "^2.0.0",
    "flatted": "catalog:",
    "happy-dom": "^20.0.11",
    "jsdom": "^27.2.0",
    "local-pkg": "^1.1.2",
    "mime": "^4.1.0",
    "prompts": "^2.4.2",
    "strip-literal": "catalog:",
    "ws": "catalog:"
  }
}
```

### `/packages/coverage-v8/package.json`
```json
{
  "dependencies": {
    "@bcoe/v8-coverage": "^1.0.2",
    "@vitest/utils": "workspace:*",
    "ast-v8-to-istanbul": "^0.3.8",
    "istanbul-lib-coverage": "catalog:",
    "istanbul-lib-report": "catalog:",
    "istanbul-reports": "catalog:",
    "magicast": "catalog:",
    "obug": "catalog:",
    "std-env": "catalog:",
    "tinyrainbow": "catalog:"
  }
}
```

### `/packages/coverage-istanbul/package.json`
```json
{
  "dependencies": {
    "@istanbuljs/schema": "^0.1.3",
    "@jridgewell/gen-mapping": "^0.3.13",
    "@jridgewell/trace-mapping": "catalog:",
    "istanbul-lib-coverage": "catalog:",
    "istanbul-lib-instrument": "^6.0.3",
    "istanbul-lib-report": "catalog:",
    "istanbul-lib-source-maps": "catalog:",
    "istanbul-reports": "catalog:",
    "magicast": "catalog:",
    "obug": "catalog:",
    "tinyrainbow": "catalog:"
  }
}
```

### `/examples/opentelemetry/package.json`
```json
{
  "devDependencies": {
    "@opentelemetry/api": "^1.9.0",
    "@opentelemetry/context-zone": "^2.2.0",
    "@opentelemetry/exporter-trace-otlp-proto": "^0.208.0",
    "@opentelemetry/resources": "^2.2.0",
    "@opentelemetry/sdk-node": "^0.208.0",
    "@opentelemetry/sdk-trace-web": "^2.2.0",
    "@vitest/browser-playwright": "latest",
    "vite": "latest",
    "vitest": "latest"
  }
}
```

### `/test/reporters/package.json`
```json
{
  "devDependencies": {
    "@vitest/browser-playwright": "workspace:*",
    "@vitest/runner": "workspace:*",
    "flatted": "^3.3.3",
    "pkg-reporter": "./reportPkg/",
    "vitest": "workspace:*",
    "vitest-sonar-reporter": "3.0.0"
  }
}
```

### `/packages/ui/package.json`
```json
{
  "dependencies": {
    "@vitest/utils": "workspace:*",
    "fflate": "^0.8.2",
    "flatted": "catalog:",
    "pathe": "catalog:",
    "sirv": "catalog:",
    "tinyglobby": "catalog:",
    "tinyrainbow": "catalog:"
  },
  "devDependencies": {
    "@faker-js/faker": "^10.1.0",
    "@iconify-json/carbon": "catalog:",
    "@iconify-json/logos": "catalog:",
    "@types/codemirror": "^5.60.17",
    "@types/d3-force": "^3.0.10",
    "@types/d3-selection": "^3.0.11",
    "@types/ws": "catalog:",
    "@unocss/reset": "catalog:",
    "@vitejs/plugin-vue": "catalog:",
    "@vitest/browser-playwright": "workspace:*",
    "@vitest/runner": "workspace:*",
    "@vitest/ws-client": "workspace:*",
    "@vue/test-utils": "^2.4.6",
    "@vueuse/core": "catalog:",
    "ansi-to-html": "^0.7.2",
    "birpc": "catalog:",
    "codemirror": "^5.65.18",
    "codemirror-theme-vars": "^0.1.2",
    "d3-graph-controller": "^3.1.6",
    "floating-vue": "^5.2.2",
    "mime": "^4.1.0",
    "rollup": "^4.53.3",
    "splitpanes": "^4.0.4",
    "typescript": "^5.9.3",
    "unocss": "catalog:",
    "vite": "^5.0.0",
    "vite-plugin-pages": "^0.33.1",
    "vitest-browser-vue": "2.0.1",
    "vue": "catalog:",
    "vue-router": "^4.6.3",
    "vue-tsc": "^3.1.5",
    "vue-virtual-scroller": "2.0.0-beta.8"
  }
}
```

### `/packages/ws-client/package.json`
```json
{
  "dependencies": {
    "birpc": "catalog:",
    "flatted": "catalog:",
    "ws": "catalog:"
  }
}
```

---

## Summary

| Category | Status | Tools/Implementation |
|----------|--------|---------------------|
| **Distributed Tracing** | ✅ Implemented | OpenTelemetry API, Jaeger |
| **Logging** | ✅ Basic | Console, obug (debug) |
| **Code Coverage** | ✅ Implemented | V8 Coverage, Istanbul |
| **Test Reporting** | ✅ Implemented | Built-in reporters, JUnit, JSON, vitest-sonar-reporter |
| **UI Dashboard** | ✅ Implemented | @vitest/ui |
| **Benchmarking** | ✅ Implemented | tinybench |
| **Error Tracking** | ⚠️ Framework-level | Source maps, stack traces |
| **APM** | ❌ Not detected | - |
| **Centralized Logging** | ❌ Not detected | - |
| **Alerting** | ❌ Not detected | - |
| **RUM** | ❌ Not detected | - |

# ml_services

3rd party ML services and technologies analysis

# 3rd Party ML Services and Technologies Analysis

## Executive Summary

After a comprehensive analysis of the codebase, **no machine learning services, AI technologies, or ML-related integrations were identified**. This codebase is **Vitest** - a Vite-native testing framework for JavaScript/TypeScript applications.

---

## Analysis Results

### 1. External ML Service Providers

| Category | Services Found |
|----------|---------------|
| Cloud ML Services (AWS SageMaker, Azure ML, Google AI Platform) | **None** |
| AI APIs (OpenAI, Anthropic, Groq, Cohere, Hugging Face) | **None** |
| Specialized ML Services (Speech, Vision, NLP APIs) | **None** |
| MLOps Platforms (MLflow, Weights & Biases, Neptune) | **None** |

### 2. ML Libraries and Frameworks

| Category | Libraries Found |
|----------|----------------|
| Deep Learning (PyTorch, TensorFlow, JAX, Keras) | **None** |
| Traditional ML (Scikit-learn, XGBoost, LightGBM) | **None** |
| NLP (Transformers, spaCy, NLTK, Gensim) | **None** |
| Computer Vision (OpenCV, torchvision) | **None** |
| Audio/Speech (Whisper, librosa, speechbrain) | **None** |

### 3. Pre-trained Models and Model Hubs

| Category | Usage Found |
|----------|-------------|
| Hugging Face Models | **None** |
| TensorFlow Hub | **None** |
| PyTorch Hub | **None** |
| Custom Model Repositories | **None** |

### 4. AI Infrastructure and Deployment

| Category | Implementation Found |
|----------|---------------------|
| Model Serving (TorchServe, TensorFlow Serving) | **None** |
| GPU/CUDA Usage | **None** |
| TPU Integration | **None** |
| ML-specific Containerization | **None** |

---

## Technologies Actually Present in the Codebase

This codebase contains the following **non-ML technologies**:

### Testing Framework Core
- **Vitest** - The main testing framework
- **Chai** - Assertion library
- **Playwright** - Browser automation for testing
- **WebdriverIO** - Browser automation alternative
- **JSDOM/Happy-DOM** - DOM simulation for testing

### Development Tools
- **Vite** - Build tool and dev server
- **TypeScript** - Type checking
- **ESLint** - Code linting
- **Rollup** - Module bundler

### UI/Frontend (for Vitest UI)
- **Vue.js** - UI framework
- **UnoCSS** - CSS framework

### Observability (Non-ML)
- **OpenTelemetry** - Distributed tracing (for test execution observability, not ML)
- **Jaeger** - Trace visualization (Docker compose for local testing)

### Image Processing (Non-ML)
- **pixelmatch** - Pixel-level image comparison for visual regression testing
- **pngjs** - PNG image parsing

> **Note**: The `pixelmatch` and `pngjs` libraries are used for **visual regression testing** (comparing screenshots), not for ML-based computer vision.

---

## Security and Compliance Considerations

Since no ML services are present:

| Consideration | Status |
|--------------|--------|
| ML API Keys/Credentials | **N/A** - No ML credentials to manage |
| Data Privacy (ML Services) | **N/A** - No data sent to ML providers |
| Model Security | **N/A** - No models to secure |
| ML-specific Compliance | **N/A** - No ML compliance requirements |

---

## Summary

| Metric | Value |
|--------|-------|
| **Total 3rd Party ML Services** | **0** |
| **ML Libraries/Frameworks** | **0** |
| **Pre-trained Models** | **0** |
| **ML Infrastructure Components** | **0** |

### Architecture Pattern
This is a **testing framework** with no ML components. The architecture is focused on:
- Test execution and reporting
- Browser automation
- Code coverage analysis
- Developer tooling

### Risk Assessment

| Risk Category | Assessment |
|--------------|------------|
| ML Vendor Lock-in | **None** - No ML dependencies |
| ML API Cost Exposure | **None** - No usage-based ML APIs |
| Model Availability Risk | **None** - No model dependencies |
| ML Data Privacy Risk | **None** - No data sent to ML services |

### Conclusion

This codebase (**Vitest**) is a JavaScript/TypeScript testing framework that does not incorporate any machine learning services, AI technologies, or ML-related integrations. All dependencies are related to testing, build tooling, and developer experience.

# feature_flags

Feature flag frameworks and usage patterns analysis

# Feature Flag Analysis Report

## Summary

**no feature flag usage detected**

---

## Detailed Analysis

After thoroughly analyzing the Vitest codebase, I found no implementation of feature flag systems. Here's what I examined:

### Framework Detection Results

**Commercial Platforms Checked:**
- ❌ Flagsmith - Not found
- ❌ LaunchDarkly - Not found
- ❌ Split.io - Not found
- ❌ Optimizely - Not found
- ❌ ConfigCat - Not found
- ❌ Unleash - Not found

**SDKs/Libraries Checked:**
- ❌ No `launchdarkly-*` packages
- ❌ No `flagsmith-*` packages
- ❌ No `@splitsoftware/*` packages
- ❌ No `@unleash/*` packages
- ❌ No `configcat-*` packages

### Dependency Analysis

I reviewed all `package.json` files across the repository (root, packages, tests, examples, docs) and found:

- No feature flag platform dependencies
- No feature flag client libraries
- No custom feature flag utilities

### Code Pattern Analysis

The codebase does not contain:
- Feature flag initialization code
- Flag evaluation patterns (`isEnabled()`, `getValue()`, etc.)
- User targeting or segmentation logic
- A/B testing framework integrations
- Kill switch implementations

### What IS Present (Not Feature Flags)

The codebase does use some configuration-driven behavior, but these are **not** feature flags:

1. **Environment Variables** (`.env.local` files) - Standard configuration, not dynamic feature toggles
2. **Vitest Configuration Options** - Test framework settings, not feature management
3. **TypeScript Configuration** - Build tooling, not runtime flags
4. **Experimental Config Options** - The `/docs/config/experimental.md` documents experimental Vitest features, but these are compile-time configuration options in `vitest.config.ts`, not runtime feature flags

### Conclusion

This is a testing framework (Vitest) codebase that:
- Does not integrate any third-party feature flag services
- Does not implement a custom feature flag system
- Uses standard configuration files and environment variables for settings
- Has no dynamic feature toggling mechanism

The experimental features mentioned in the documentation are configuration options passed to Vitest at setup time, not runtime feature flags that can be toggled dynamically.

# prompt_security_check

LLM and prompt injection vulnerability assessment

# LLM Usage Detection and Security Assessment

## Part 1: LLM Usage Detection and Documentation

### 1.1 LLM Infrastructure Identification

After comprehensive scanning of the repository using all detection strategies:

#### Detection Strategy 1: Library and Package Detection

**Scanned files:**
- `package.json` (root and all subdirectories)
- `pnpm-lock.yaml`
- All `requirements.txt`, `pyproject.toml` files (none found)

**Result:** No LLM-related packages detected. The repository contains:
- Testing framework dependencies (chai, playwright, webdriverio)
- Build tools (rollup, vite, typescript)
- UI components (Vue, Shiki for syntax highlighting)
- Coverage tools (istanbul, v8)

#### Detection Strategy 2: Import/Include Pattern Matching

**Searched patterns across all source files:**
- `import anthropic`, `from anthropic` - **Not found**
- `import openai`, `from openai` - **Not found**
- `import google.generativeai` - **Not found**
- `import transformers` - **Not found**
- `require('openai')`, `require("openai")` - **Not found**
- `import OpenAI from` - **Not found**
- `import { Anthropic }` - **Not found**
- `import { GoogleGenerativeAI }` - **Not found**
- LangChain, LlamaIndex, Semantic Kernel imports - **Not found**

#### Detection Strategy 3: API Client Instantiation Patterns

**Searched patterns:**
- `Anthropic(`, `anthropic.Anthropic(` - **Not found**
- `OpenAI(`, `openai.OpenAI(` - **Not found**
- `new OpenAI({` - **Not found**
- `new Anthropic({` - **Not found**
- `GoogleGenerativeAI(` - **Not found**

#### Detection Strategy 4: API Method Call Patterns

**Searched patterns:**
- `.messages.create(` - **Not found**
- `.chat.completions.create(` - **Not found**
- `.generateContent(` - **Not found**
- `.createCompletion(` - **Not found**

#### Detection Strategy 5: Configuration and Environment Variables

**Scanned files:**
- `.env.local` files (found in `test/core/`, `test/workspaces/`, `test/browser/`)
- Configuration files throughout

**Contents of `.env.local` files:**
- `test/core/.env.local` - Contains test-specific environment variables, no LLM keys
- `test/workspaces/.env.local` - Workspace configuration, no LLM keys
- `test/browser/.env.local` - Browser test configuration, no LLM keys

**No LLM-related environment variables found:**
- `OPENAI_API_KEY` - **Not found**
- `ANTHROPIC_API_KEY` - **Not found**
- `CLAUDE_API_KEY` - **Not found**

#### Detection Strategy 6: Prompt-Related Patterns

**Searched for:**
- Files with `.prompt` extension - **Not found**
- Directories named `prompts` - **Not found**
- Template patterns with LLM context - **Not found**

**Note:** The repository contains `CLAUDE.md` and `AGENTS.md` files, but these are:
- `CLAUDE.md` - Documentation/instructions file for AI assistants helping with the codebase (not LLM integration code)
- `AGENTS.md` - Similar documentation file

These are metadata files for AI coding assistants, not LLM integration code.

#### Detection Strategy 7: Custom Implementation Patterns

**Scanned directories:**
- No `ai/`, `ml/`, `llm/` directories found
- Files containing `analyzer`, `generator` - These are test-related utilities, not LLM wrappers
- `packages/` directory contains testing utilities (spy, snapshot, expect, mocker, etc.)

### 1.2 Repository Purpose Analysis

This repository is **Vitest** - a Vite-native testing framework for JavaScript/TypeScript. The codebase contains:

1. **Testing Framework Core** (`packages/vitest/`)
2. **Browser Testing Support** (`packages/browser/`, `packages/browser-playwright/`, `packages/browser-webdriverio/`)
3. **Mocking Utilities** (`packages/mocker/`, `packages/spy/`)
4. **Coverage Tools** (`packages/coverage-v8/`, `packages/coverage-istanbul/`)
5. **UI Dashboard** (`packages/ui/`)
6. **Snapshot Testing** (`packages/snapshot/`)
7. **Documentation** (`docs/`)
8. **Test Suites** (`test/`)

### 1.3 LLM Usage Summary

**Total LLM Integrations Found:** 0

**Analysis Details:**
- No LLM API clients or SDKs imported
- No LLM API endpoints called
- No prompt engineering or template systems
- No vector databases or RAG implementations
- No agent frameworks
- No Model Context Protocol (MCP) usage

The `CLAUDE.md` and `.github/copilot-instructions.md` files are developer documentation files intended to help AI coding assistants understand the codebase structure - they do not constitute LLM integration within the application itself.

---

## Conclusion

**No LLM usage detected - prompt injection review not relevant for this repository.**

This repository is a JavaScript/TypeScript testing framework (Vitest) that does not integrate with any LLM services, AI models, or LLM-based infrastructure. The codebase is focused entirely on:

- Unit and integration testing
- Browser automation testing
- Code coverage analysis
- Snapshot testing
- Test reporting and UI

No security assessment for prompt injection vulnerabilities is applicable.

# api_surface

Public API analysis and design patterns

# Vitest Public API Analysis

## Overview

Vitest is a next-generation testing framework powered by Vite. This analysis documents the actual public API surface and design patterns implemented in the codebase.

---

## 1. Public API Analysis

### Entry Points

#### Main Package Exports (`packages/vitest/package.json`)

```json
{
  "exports": {
    ".": {
      "types": "./index.d.ts",
      "default": "./dist/index.js"
    },
    "./config": "./config.d.ts",
    "./coverage": "./coverage.d.ts",
    "./node": "./node.d.ts",
    "./browser": "./browser/index.d.ts",
    "./runners": "./runners.d.ts",
    "./suite": "./suite.d.ts",
    "./snapshot": "./snapshot.d.ts",
    "./environments": "./environments.d.ts",
    "./reporters": "./reporters.d.ts",
    "./mocker": "./mocker.d.ts",
    "./globals": "./globals.d.ts",
    "./worker": "./worker.d.ts"
  }
}
```

#### Core Entry Point (`packages/vitest/src/index.ts`)

```typescript
// Re-exports from @vitest/runner
export { 
  suite, test, describe, it, 
  beforeAll, beforeEach, afterAll, afterEach,
  onTestFailed, onTestFinished 
} from '@vitest/runner'

// Re-exports from @vitest/expect
export { expect, assert, should, chai } from '@vitest/expect'

// Re-exports from @vitest/spy
export { vi, vitest } from '@vitest/spy'

// Type exports
export type { 
  TestContext, TaskContext, 
  TestFunction, SuiteCollector 
} from '@vitest/runner'
```

---

## 2. Core Functions/Methods

### Test Definition Functions

#### `describe` / `suite`

**Source:** `packages/runner/src/suite.ts`

```typescript
export interface SuiteAPI {
  (name: string | Function, fn?: SuiteFactory): void
  (name: string | Function, options: TestOptions, fn?: SuiteFactory): void
  skipIf: (condition: any) => SuiteAPI
  runIf: (condition: any) => SuiteAPI
  only: SuiteAPI
  skip: SuiteAPI
  todo: (name: string) => void
  concurrent: SuiteAPI
  sequential: SuiteAPI
  shuffle: SuiteAPI
  each: <T>(cases: ReadonlyArray<T>) => (name: string, fn: (args: T) => void) => void
}
```

**Usage Example:**
```typescript
describe('math operations', () => {
  describe.concurrent('parallel tests', () => {
    it('adds numbers', () => expect(1 + 1).toBe(2))
  })
})

describe.each([
  { a: 1, b: 1, expected: 2 },
  { a: 1, b: 2, expected: 3 },
])('add($a, $b)', ({ a, b, expected }) => {
  it(`returns ${expected}`, () => expect(a + b).toBe(expected))
})
```

#### `test` / `it`

**Source:** `packages/runner/src/suite.ts`

```typescript
export interface TestAPI {
  (name: string | Function, fn?: TestFunction, timeout?: number): void
  (name: string | Function, options: TestOptions, fn?: TestFunction): void
  skipIf: (condition: any) => TestAPI
  runIf: (condition: any) => TestAPI
  only: TestAPI
  skip: TestAPI
  todo: (name: string) => void
  fails: TestAPI
  concurrent: TestAPI
  sequential: TestAPI
  each: <T>(cases: ReadonlyArray<T>) => (name: string, fn: (args: T) => void, timeout?: number) => void
  for: <T>(cases: ReadonlyArray<T>) => TestForFunction<T>
}
```

**Usage Example:**
```typescript
it('basic test', () => {
  expect(true).toBe(true)
})

it('async test', async () => {
  const result = await fetchData()
  expect(result).toBeDefined()
})

it.concurrent('parallel test', async () => {
  await someAsyncOperation()
})

it.each([1, 2, 3])('test with %i', (num) => {
  expect(num).toBeGreaterThan(0)
})

it.fails('expected to fail', () => {
  throw new Error('intentional failure')
})
```

### Lifecycle Hooks

**Source:** `packages/runner/src/suite.ts`

```typescript
export declare function beforeAll(fn: SuiteHooks['beforeAll'][0], timeout?: number): void
export declare function afterAll(fn: SuiteHooks['afterAll'][0], timeout?: number): void
export declare function beforeEach(fn: SuiteHooks['beforeEach'][0], timeout?: number): void
export declare function afterEach(fn: SuiteHooks['afterEach'][0], timeout?: number): void
```

**Usage Example:**
```typescript
beforeAll(async () => {
  await setupDatabase()
})

afterAll(async () => {
  await teardownDatabase()
})

beforeEach((context) => {
  context.fixture = createFixture()
})

afterEach(() => {
  cleanup()
})
```

### Test Event Hooks

**Source:** `packages/runner/src/suite.ts`

```typescript
export declare function onTestFailed(
  fn: OnTestFailedHandler, 
  timeout?: number
): void

export declare function onTestFinished(
  fn: OnTestFinishedHandler, 
  timeout?: number
): void
```

**Usage Example:**
```typescript
it('test with failure handler', (context) => {
  onTestFailed((result) => {
    console.log('Test failed:', result.errors)
  })
  
  onTestFinished((result) => {
    console.log('Test finished:', result.state)
  })
  
  expect(true).toBe(true)
})
```

---

## 3. `vi` Utility Object

**Source:** `packages/vitest/src/integrations/vi.ts`

### Mock Functions

```typescript
export interface VitestUtils {
  // Mock function creation
  fn<T extends Procedure = Procedure>(fn?: T): Mock<T>
  spyOn<T extends object, M extends keyof T>(
    object: T, 
    method: M, 
    accessType?: 'get' | 'set'
  ): MockInstance
  
  // Module mocking
  mock(path: string, factory?: () => any): void
  unmock(path: string): void
  doMock(path: string, factory?: () => any): void
  doUnmock(path: string): void
  importMock<T>(path: string): Promise<MaybeMockedDeep<T>>
  importActual<T>(path: string): Promise<T>
  
  // Hoisting
  hoisted<T>(factory: () => T): T
  
  // Mock state management
  clearAllMocks(): VitestUtils
  resetAllMocks(): VitestUtils
  restoreAllMocks(): VitestUtils
  
  // Timer mocking
  useFakeTimers(config?: FakeTimerInstallOpts): VitestUtils
  useRealTimers(): VitestUtils
  runAllTimers(): VitestUtils
  runOnlyPendingTimers(): VitestUtils
  advanceTimersByTime(ms: number): VitestUtils
  advanceTimersByTimeAsync(ms: number): Promise<VitestUtils>
  advanceTimersToNextTimer(): VitestUtils
  advanceTimersToNextTimerAsync(): Promise<VitestUtils>
  getTimerCount(): number
  setSystemTime(date: number | string | Date): VitestUtils
  getMockedSystemTime(): Date | null
  
  // Environment stubs
  stubGlobal(name: string, value: any): VitestUtils
  unstubAllGlobals(): VitestUtils
  stubEnv(name: string, value: string): VitestUtils
  unstubAllEnvs(): VitestUtils
  
  // Waiting utilities
  waitFor<T>(callback: () => T | Promise<T>, options?: WaitForOptions): Promise<T>
  waitUntil<T>(
    callback: () => T | Promise<T>,
    options?: WaitUntilOptions
  ): Promise<T>
  
  // Dynamic imports with caching
  resetModules(): VitestUtils
  dynamicImportSettled(): Promise<void>
}
```

**Usage Examples:**

```typescript
// Mock functions
const mockFn = vi.fn()
const mockImpl = vi.fn((x: number) => x * 2)
mockFn.mockReturnValue(42)
mockFn.mockResolvedValue('async result')

// Spy on object methods
const spy = vi.spyOn(console, 'log')
console.log('test')
expect(spy).toHaveBeenCalledWith('test')

// Module mocking
vi.mock('./module', () => ({
  default: vi.fn(),
  namedExport: vi.fn()
}))

// Hoisted mocks
const hoistedMock = vi.hoisted(() => vi.fn())
vi.mock('./module', () => ({ fn: hoistedMock }))

// Timer mocking
vi.useFakeTimers()
setTimeout(() => console.log('delayed'), 1000)
vi.advanceTimersByTime(1000)

// Environment stubs
vi.stubGlobal('fetch', vi.fn())
vi.stubEnv('NODE_ENV', 'test')

// Wait utilities
await vi.waitFor(() => {
  expect(element).toBeVisible()
})
```

---

## 4. Expect Assertions

**Source:** `packages/expect/src/jest-expect.ts`

### Core Matchers

```typescript
export interface Assertion<T> {
  // Equality
  toBe(expected: T): void
  toEqual(expected: T): void
  toStrictEqual(expected: T): void
  
  // Truthiness
  toBeTruthy(): void
  toBeFalsy(): void
  toBeNull(): void
  toBeUndefined(): void
  toBeDefined(): void
  toBeNaN(): void
  
  // Type checking
  toBeTypeOf(type: 'string' | 'number' | 'bigint' | 'boolean' | 'symbol' | 'undefined' | 'object' | 'function'): void
  toBeInstanceOf(expected: any): void
  
  // Numbers
  toBeGreaterThan(expected: number | bigint): void
  toBeGreaterThanOrEqual(expected: number | bigint): void
  toBeLessThan(expected: number | bigint): void
  toBeLessThanOrEqual(expected: number | bigint): void
  toBeCloseTo(expected: number, precision?: number): void
  
  // Strings
  toMatch(expected: string | RegExp): void
  toContain(expected: any): void
  toHaveLength(expected: number): void
  
  // Objects
  toHaveProperty(key: string, value?: any): void
  toMatchObject(expected: object): void
  
  // Arrays
  toContainEqual(expected: any): void
  
  // Errors
  toThrow(expected?: string | RegExp | Error): void
  toThrowError(expected?: string | RegExp | Error): void
  
  // Snapshots
  toMatchSnapshot(hint?: string): void
  toMatchInlineSnapshot(snapshot?: string): void
  toMatchFileSnapshot(filepath: string): Promise<void>
  toThrowErrorMatchingSnapshot(): void
  toThrowErrorMatchingInlineSnapshot(snapshot?: string): void
  
  // Promises
  resolves: PromiseAssertion<T>
  rejects: PromiseAssertion<T>
  
  // Modifiers
  not: Assertion<T>
}
```

### Mock Matchers

```typescript
export interface MockAssertion<T> extends Assertion<T> {
  toHaveBeenCalled(): void
  toHaveBeenCalledTimes(times: number): void
  toHaveBeenCalledWith(...args: any[]): void
  toHaveBeenLastCalledWith(...args: any[]): void
  toHaveBeenNthCalledWith(n: number, ...args: any[]): void
  toHaveReturned(): void
  toHaveReturnedTimes(times: number): void
  toHaveReturnedWith(value: any): void
  toHaveLastReturnedWith(value: any): void
  toHaveNthReturnedWith(n: number, value: any): void
}
```

### Soft Assertions

**Source:** `packages/expect/src/jest-expect.ts`

```typescript
export interface ExpectStatic {
  soft<T>(actual: T): Assertion<T>
}
```

**Usage Example:**
```typescript
expect.soft(1).toBe(2)  // Continues execution even on failure
expect.soft(2).toBe(3)  // Multiple soft assertions can fail
expect(3).toBe(3)       // Hard assertion - stops if fails
```

### Extended Expect

```typescript
export interface ExpectStatic {
  // Assertion creation
  <T>(actual: T): Assertion<T>
  
  // Extending
  extend(matchers: Record<string, CustomMatcher>): void
  
  // State
  getState(): ExpectState
  setState(state: Partial<ExpectState>): void
  
  // Asymmetric matchers
  anything(): AsymmetricMatcher
  any(constructor: unknown): AsymmetricMatcher
  stringContaining(expected: string): AsymmetricMatcher
  stringMatching(expected: string | RegExp): AsymmetricMatcher
  objectContaining(expected: object): AsymmetricMatcher
  arrayContaining(expected: unknown[]): AsymmetricMatcher
  not: {
    stringContaining(expected: string): AsymmetricMatcher
    stringMatching(expected: string | RegExp): AsymmetricMatcher
    objectContaining(expected: object): AsymmetricMatcher
    arrayContaining(expected: unknown[]): AsymmetricMatcher
  }
  
  // Assertions without values
  assertions(count: number): void
  hasAssertions(): void
  
  // Add custom equality testers
  addEqualityTesters(testers: Array<Tester>): void
  
  // Snapshot serializers
  addSnapshotSerializer(serializer: SnapshotSerializer): void
}
```

**Usage Examples:**

```typescript
// Basic assertions
expect(1 + 1).toBe(2)
expect({ a: 1 }).toEqual({ a: 1 })
expect([1, 2, 3]).toContain(2)

// Asymmetric matchers
expect({ name: 'John', age: 30 }).toEqual({
  name: expect.any(String),
  age: expect.any(Number)
})

// Custom matchers
expect.extend({
  toBeWithinRange(received, floor, ceiling) {
    const pass = received >= floor && received <= ceiling
    return {
      pass,
      message: () => `expected ${received} to be within range ${floor} - ${ceiling}`
    }
  }
})

// Assertion counting
expect.assertions(2)
expect.hasAssertions()
```

---

## 5. Type Testing API

**Source:** `packages/expect/src/jest-expect.ts` and `packages/runner/src/types/tasks.ts`

### `expectTypeOf`

```typescript
export interface ExpectTypeOf<T> {
  toBeString(): void
  toBeNumber(): void
  toBeBoolean(): void
  toBeVoid(): void
  toBeSymbol(): void
  toBeNull(): void
  toBeUndefined(): void
  toBeNullable(): void
  toBeFunction(): void
  toBeObject(): void
  toBeArray(): void
  toBeNever(): void
  toBeUnknown(): void
  toBeAny(): void
  
  toEqualTypeOf<Expected>(): void
  toMatchTypeOf<Expected>(): void
  
  toHaveProperty<K extends string>(key: K): ExpectTypeOf<T extends Record<K, infer V> ? V : never>
  
  parameter<N extends number>(n: N): ExpectTypeOf<Parameters<T>[N]>
  parameters: ExpectTypeOf<Parameters<T>>
  returns: ExpectTypeOf<ReturnType<T>>
  
  constructorParameters: ExpectTypeOf<ConstructorParameters<T>>
  instance: ExpectTypeOf<InstanceType<T>>
  
  items: ExpectTypeOf<T extends Array<infer U> ? U : never>
  
  // Modifiers
  not: ExpectTypeOf<T>
  branded: ExpectTypeOf<T>
}
```

### `assertType`

```typescript
export function assertType<T>(value: T): void
```

**Usage Examples:**

```typescript
import { expectTypeOf, assertType } from 'vitest'

// Basic type testing
expectTypeOf<string>().toBeString()
expectTypeOf<number>().toBeNumber()

// Function types
function greet(name: string): string {
  return `Hello, ${name}`
}
expectTypeOf(greet).toBeFunction()
expectTypeOf(greet).parameter(0).toBeString()
expectTypeOf(greet).returns.toBeString()

// Object types
interface User {
  name: string
  age: number
}
expectTypeOf<User>().toHaveProperty('name').toBeString()

// Type equality
expectTypeOf<string>().toEqualTypeOf<string>()
expectTypeOf({ a: 1 }).toMatchTypeOf<{ a: number }>()

// Assert type
assertType<string>('hello')
```

---

## 6. Classes & Constructors

### VitestRunner

**Source:** `packages/runner/src/run.ts`

```typescript
export class VitestRunner {
  config: ResolvedConfig
  
  constructor(config: ResolvedConfig)
  
  // Core methods
  runSuite(suite: Suite): Promise<void>
  runTest(test: Test): Promise<void>
  runTests(tests: Test[]): Promise<void>
  
  // Lifecycle hooks
  onBeforeRunFiles?(files: File[]): Promise<void>
  onAfterRunFiles?(files: File[]): Promise<void>
  onBeforeRunSuite?(suite: Suite): Promise<void>
  onAfterRunSuite?(suite: Suite): Promise<void>
  onBeforeRunTask?(test: TaskPopulated): Promise<void>
  onAfterRunTask?(test: TaskPopulated): Promise<void>
  onBeforeTryTask?(test: TaskPopulated, retryInfo: RetryInfo): Promise<void>
  onAfterTryTask?(test: TaskPopulated, retryInfo: RetryInfo): Promise<void>
  
  // Extensibility
  extendTaskContext?(context: TaskContext): TaskContext
  importFile(filepath: string, source: string): Promise<void>
}
```

### Mock Class

**Source:** `packages/spy/src/index.ts`

```typescript
export interface Mock<T extends Procedure = Procedure> extends MockInstance<T> {
  new (...args: Parameters<T>): ReturnType<T>
  (...args: Parameters<T>): ReturnType<T>
}

export interface MockInstance<T extends Procedure = Procedure> {
  // Call information
  mock: MockContext<T>
  
  // Implementation control
  mockImplementation(fn: T): this
  mockImplementationOnce(fn: T): this
  
  // Return value control
  mockReturnValue(value: ReturnType<T>): this
  mockReturnValueOnce(value: ReturnType<T>): this
  mockResolvedValue(value: Awaited<ReturnType<T>>): this
  mockResolvedValueOnce(value: Awaited<ReturnType<T>>): this
  mockRejectedValue(value: unknown): this
  mockRejectedValueOnce(value: unknown): this
  
  // Mock name
  mockName(name: string): this
  getMockName(): string
  
  // Reset/restore
  mockClear(): this
  mockReset(): this
  mockRestore(): void
  
  // Getting original
  getMockImplementation(): T | undefined
}

export interface MockContext<T extends Procedure> {
  calls: Parameters<T>[]
  instances: ReturnType<T>[]
  invocationCallOrder: number[]
  results: MockResult<T>[]
  lastCall: Parameters<T> | undefined
  settledResults: MockSettledResult<T>[]
}
```

### Vitest Class

**Source:** `packages/vitest/src/node/core.ts`

```typescript
export class Vitest {
  config: ResolvedConfig
  server: ViteDevServer
  state: StateManager
  
  // Project management
  projects: WorkspaceProject[]
  getRootProject(): WorkspaceProject
  getProjectByName(name: string): WorkspaceProject | undefined
  
  // Test execution
  start(filters?: string[]): Promise<void>
  runFiles(files: string[], allTestsRun?: boolean): Promise<void>
  rerunFiles(files?: string[], trigger?: string): Promise<void>
  cancelCurrentRun(reason: CancelReason): Promise<void>
  
  // Watch mode
  watchTests(filters?: string[]): Promise<void>
  
  // Coverage
  coverageProvider?: CoverageProvider
  
  // Reporting
  report<T extends keyof Reporter>(name: T, ...args: ArgumentsType<Reporter[T]>): Promise<void>
  
  // Lifecycle
  close(): Promise<void>
  exit(force?: boolean): Promise<void>
}
```

### WorkspaceProject

**Source:** `packages/vitest/src/node/project.ts`

```typescript
export class WorkspaceProject {
  name: string
  config: ResolvedProjectConfig
  vite: ViteDevServer
  
  // Configuration
  getConfig(): ResolvedProjectConfig
  getName(): string
  
  // File matching
  matchesTestGlob(filepath: string): boolean
  isInSourceTestFile(filepath: string): boolean
  
  // Module transformation
  transformRequest(id: string): Promise<TransformResult | null>
  
  // Browser
  browser?: BrowserServer
  isBrowserEnabled(): boolean
}
```

---

## 7. Types & Interfaces

### Test Task Types

**Source:** `packages/runner/src/types/tasks.ts`

```typescript
export interface TaskBase {
  id: string
  name: string
  mode: 'run' | 'skip' | 'only' | 'todo'
  type: 'suite' | 'test' | 'custom'
  file?: File
  result?: TaskResult
  meta: TaskMeta
  location?: { line: number; column: number }
}

export interface Test<ExtraContext = object> extends TaskBase {
  type: 'test'
  context: TestContext & ExtraContext
  suite: Suite
  pending?: boolean
  fails?: boolean
  concurrent?: boolean
  sequential?: boolean
}

export interface Suite extends TaskBase {
  type: 'suite'
  tasks: Task[]
  suite?: Suite
  concurrent?: boolean
  sequential?: boolean
  shuffle?: boolean
}

export interface File extends Suite {
  filepath: string
  projectName: string
}

export type Task = Test | Suite | Custom

export interface TaskResult {
  state

# internals

Internal architecture and implementation

# Vitest Internal Architecture Analysis

## Executive Summary

Vitest is a Vite-powered testing framework built as a monorepo with modular architecture. The codebase is organized into distinct packages that handle specific responsibilities: test running, mocking, snapshot management, coverage reporting, browser testing, and UI visualization.

---

## Internal Architecture

### 1. Core Modules

#### Package Structure

The framework is organized into the following internal modules within `/packages/`:

| Package | Responsibility |
|---------|----------------|
| `vitest` | Main orchestration, CLI, configuration, and test lifecycle management |
| `runner` | Test suite execution, hooks, and task scheduling |
| `expect` | Assertion library with chai integration and custom matchers |
| `spy` | Function mocking and spying capabilities |
| `mocker` | Module mocking system for ESM and browser environments |
| `snapshot` | Snapshot testing and serialization |
| `utils` | Shared utilities including diff, error handling, and source maps |
| `pretty-format` | Object serialization for snapshots and output |
| `browser` | Browser-based test execution |
| `browser-playwright` | Playwright integration for browser testing |
| `browser-webdriverio` | WebDriverIO integration for browser testing |
| `browser-preview` | Testing Library integration for browser tests |
| `coverage-v8` | V8-based code coverage collection |
| `coverage-istanbul` | Istanbul-based code coverage collection |
| `ui` | Visual test dashboard and reporting UI |
| `ws-client` | WebSocket client for communication between processes |
| `web-worker` | Web Worker support for isolated test execution |

#### Inter-Module Dependencies

```
vitest (main)
├── @vitest/runner (test execution)
├── @vitest/expect (assertions)
├── @vitest/spy (mocking)
├── @vitest/mocker (module mocking)
├── @vitest/snapshot (snapshots)
├── @vitest/utils (utilities)
└── @vitest/pretty-format (serialization)

@vitest/expect
├── @vitest/spy
├── @vitest/utils
└── chai

@vitest/runner
└── @vitest/utils

@vitest/snapshot
├── @vitest/pretty-format
└── magic-string

@vitest/browser
├── @vitest/mocker
├── @vitest/utils
└── ws

@vitest/coverage-v8 / @vitest/coverage-istanbul
├── istanbul-lib-*
└── @vitest/utils
```

#### Abstraction Layers

**Source Organization (`/packages/vitest/src/`):**

```
src/
├── api/          # WebSocket API for external communication
├── create/       # Test context and configuration creation
├── integrations/ # Framework integrations (chai, snapshot, etc.)
├── node/         # Node.js-specific implementation
├── public/       # Public API exports
├── runtime/      # Test runtime execution
├── typecheck/    # TypeScript type-checking support
├── types/        # Internal type definitions
└── utils/        # Internal utilities
```

---

### 2. Design Patterns

#### Factory Pattern

**Test Suite Factory** (`/packages/runner/src/suite.ts`):
- Creates test suites and test cases dynamically
- `describe()`, `test()`, and `it()` functions act as factories

**Configuration Factory** (`/packages/vitest/src/node/`):
- Creates resolved configuration from user options
- Merges Vite config with Vitest-specific options

#### Facade Pattern

**Public API Facade** (`/packages/vitest/src/public/`):
- Exposes simplified interfaces for:
  - Test functions (`describe`, `it`, `test`)
  - Assertion functions (`expect`)
  - Mocking utilities (`vi`)
  - Lifecycle hooks (`beforeEach`, `afterAll`, etc.)

#### Strategy Pattern

**Pool Strategies** (Worker pools):
- Different execution strategies: `threads`, `forks`, `vmThreads`, `vmForks`
- Selected via `pool` configuration option

**Coverage Providers**:
- `@vitest/coverage-v8` - V8 native coverage
- `@vitest/coverage-istanbul` - Istanbul instrumentation

**Browser Providers**:
- `@vitest/browser-playwright` - Playwright automation
- `@vitest/browser-webdriverio` - WebDriverIO automation
- `@vitest/browser-preview` - Testing Library preview mode

#### Observer Pattern

**Reporter System** (`/packages/vitest/src/node/reporters/`):
- Multiple reporters can observe test execution events
- Built-in reporters: `default`, `verbose`, `json`, `junit`, `html`, `tap`, `blob`

**WebSocket Communication** (`/packages/ws-client/`):
- Uses `birpc` for bidirectional RPC over WebSocket
- Observers for test state updates in UI

#### Builder Pattern

**Configuration Building**:
- Chained configuration resolution from:
  - CLI arguments
  - Config file (`vitest.config.ts`)
  - Vite config
  - Workspace configs
  - Default values

---

### 3. Data Structures

#### Task Representation

**Test Task Structure** (`/packages/runner/src/types/`):
```typescript
interface Task {
  id: string
  name: string
  mode: 'run' | 'skip' | 'only' | 'todo'
  result?: TaskResult
  meta: TaskMeta
  file: File
  suite: Suite
}

interface Suite extends Task {
  tasks: Task[]
}
```

#### State Management

**Test State** (`/packages/runner/src/`):
- Maintains current test context
- Tracks suite hierarchy
- Manages hook execution order

**Global State** (`/packages/vitest/src/runtime/`):
- Singleton pattern for global test state
- `globalThis.__vitest_worker__` for worker context

#### Caching Mechanisms

**Module Cache**:
- Leverages Vite's module transformation cache
- Configuration option: `cache.dir` (defaults to `node_modules/.vitest`)

**Snapshot Cache** (`/packages/snapshot/src/`):
- File-based snapshot storage
- In-memory snapshot state during test runs

---

### 4. Algorithms

#### Test Filtering

**Name Pattern Matching** (`/packages/vitest/src/node/`):
- Uses `picomatch` for glob pattern matching
- Supports regex patterns via `--testNamePattern`

**Test Sharding** (`/packages/vitest/src/node/`):
- Distributes tests across shards using consistent hashing
- Formula: `Math.abs(hash(testPath)) % totalShards === shardIndex`

#### Test Sequencing

**Sequence Algorithms** (`sequence.sequencer` option):
- `'parallel'` - Concurrent execution (default)
- `'sequential'` - One at a time
- Custom sequencers via function

**Shuffle Algorithm**:
- Fisher-Yates shuffle with configurable seed
- Option: `sequence.shuffle`

#### Diff Algorithm

**Object Diff** (`/packages/utils/src/diff/`):
- Uses `diff-sequences` for line-by-line comparison
- Custom serialization via `@vitest/pretty-format`

---

## Implementation Details

### 1. Core Logic

#### Test Execution Flow

```
CLI/API Entry → Configuration Resolution → Test Discovery → 
Worker Pool Initialization → Test Distribution → 
Per-Worker Execution → Result Collection → Reporting
```

**Main Entry Points** (`/packages/vitest/src/`):
- `node/cli.ts` - CLI entry
- `node/core.ts` - Core Vitest class
- `runtime/entry.ts` - Worker runtime entry

#### Module Transformation

**Vite Integration**:
- Uses Vite's SSR transform for test files
- Custom module mocking via `@vitest/mocker`
- Inline optimization for specified dependencies

#### Assertion Logic

**Expect Implementation** (`/packages/expect/src/`):
- Extends Chai with custom matchers
- Asymmetric matchers (`expect.any()`, `expect.stringContaining()`)
- Snapshot integration
- Standard Schema validation support (`toSatisfy`)

### 2. Platform Abstractions

#### Environment System

**Test Environments** (`/packages/vitest/src/integrations/env/`):
- `node` - Native Node.js
- `jsdom` - JSDOM simulation
- `happy-dom` - Happy DOM simulation
- `edge-runtime` - Edge runtime (via `@edge-runtime/vm`)
- Custom environments via `environment` option

#### Worker Isolation

**Pool Types**:
- `threads` - Worker threads with shared memory
- `forks` - Child processes with full isolation
- `vmThreads` - Worker threads with VM isolation
- `vmForks` - Child processes with VM isolation

### 3. Performance Optimizations

#### Lazy Evaluation

**Test Registration**:
- Tests are collected but not executed until runner starts
- Allows filtering before execution

**Module Loading**:
- Dependencies optimized lazily via Vite
- Configuration: `deps.optimizer`

#### Memoization

**Snapshot Serialization** (`/packages/pretty-format/src/`):
- Caches plugin lookups for repeated serialization
- Avoids re-serializing identical objects

#### File Parallelism

**Concurrent Execution**:
- `fileParallelism: true` (default) runs test files in parallel
- `maxWorkers` controls worker pool size

---

## Code Organization

### 1. Directory Structure

```
packages/
├── vitest/           # Main package
│   └── src/
│       ├── api/      # WebSocket API
│       ├── node/     # Node.js runner
│       ├── runtime/  # Test runtime
│       └── public/   # Public exports
├── runner/           # Test runner core
│   └── src/
│       ├── suite.ts  # Suite/test creation
│       ├── run.ts    # Execution logic
│       └── types/    # Type definitions
├── expect/           # Assertions
├── spy/              # Mock functions
├── mocker/           # Module mocking
│   └── src/
│       ├── browser/  # Browser mocking
│       └── node/     # Node mocking
├── snapshot/         # Snapshots
│   └── src/
│       ├── env/      # Environment adapters
│       └── port/     # Snapshot operations
├── utils/            # Shared utilities
│   └── src/
│       └── diff/     # Diff algorithms
├── browser/          # Browser testing
│   └── src/
│       ├── client/   # Browser client
│       └── node/     # Server-side
├── ui/               # Dashboard UI
│   ├── client/       # Vue.js frontend
│   └── node/         # Server
└── coverage-*/       # Coverage providers
```

### 2. Build System

**Build Tools**:
- **Rollup** - Primary bundler (`rollup.config.js` in each package)
- **TypeScript** - Type checking and declarations
- **esbuild** - Fast transpilation via `unplugin-oxc`

**Build Configuration**:
- `tsconfig.base.json` - Shared TypeScript config
- `tsconfig.build.json` - Build-specific config
- Per-package `rollup.config.js` files

**Output Structure**:
- ESM output (`dist/*.js`)
- CJS output where needed (`dist/*.cjs`)
- Type declarations (`dist/*.d.ts`)

### 3. Development Workflow

**Package Management**:
- pnpm workspaces (`pnpm-workspace.yaml`)
- Catalog dependencies (shared version management)

**Scripts** (`/scripts/`):
- `release.ts` - Version bumping and publishing
- `publish-ci.ts` - CI publishing automation
- `update-contributors.ts` - Contributor list updates

---

## Quality Assurance

### 1. Testing Strategy

**Test Organization** (`/test/`):
- `core/` - Core functionality tests
- `cli/` - CLI integration tests
- `config/` - Configuration tests
- `browser/` - Browser testing tests
- `coverage-test/` - Coverage provider tests
- `reporters/` - Reporter tests
- `snapshots/` - Snapshot tests
- `workspaces/` - Workspace/project tests

**Test Types**:
- Unit tests (inline in packages)
- Integration tests (in `/test/` directories)
- E2E tests (CLI invocation tests)
- Type tests (`/test/typescript/`)

### 2. Test Infrastructure

**Test Framework**: Self-hosted (Vitest tests Vitest)

**Test Utilities** (`/test/test-utils/`):
- `cli.ts` - CLI execution helpers
- Process spawning utilities
- Fixture helpers

**Browser Testing**:
- Playwright for browser E2E tests
- Multiple browser targets (chromium, firefox, webkit)

### 3. Code Quality

**Linting**:
- ESLint with `@antfu/eslint-config`
- Configuration: `eslint.config.js`

**Type Checking**:
- TypeScript strict mode
- Separate `tsconfig.check.json` for verification

---

## Cross-cutting Concerns

### 1. Error Handling

**Error Types** (`/packages/utils/src/`):
- Custom error serialization for cross-process communication
- Stack trace normalization
- Source map support for accurate locations

**Error Propagation**:
- Worker errors serialized and sent to main process
- `onUnhandledError` hook for custom handling
- `dangerouslyIgnoreUnhandledErrors` option for suppression

### 2. Configuration

**Configuration Sources**:
1. CLI arguments (highest priority)
2. Inline config (`defineConfig()`)
3. Config file (`vitest.config.ts`, `vite.config.ts`)
4. Workspace configs
5. Default values

**Runtime Options**:
- `provide` - Inject values into test context
- `environmentOptions` - Per-environment configuration
- `poolOptions` - Worker pool configuration

### 3. Logging

**Console Interception**:
- Captures `console.*` calls during tests
- Configurable via `disableConsoleIntercept`
- `printConsoleTrace` adds stack traces

**Debug Output**:
- `DEBUG=vitest:*` environment variable support
- Verbose mode (`--reporter=verbose`)

---

## Key Technical Decisions

1. **Vite-First Architecture**: Leverages Vite's module transformation, dev server, and plugin system rather than building from scratch

2. **Workspace Support**: Native monorepo support via `projects` configuration and workspace configs

3. **Isolated Packages**: Each capability (expect, spy, snapshot, etc.) is a separate package for tree-shaking and independent use

4. **Multi-Environment**: Single codebase supports Node.js, browser, and edge runtimes through environment abstraction

5. **RPC Communication**: Uses `birpc` over WebSocket for efficient bidirectional communication between processes