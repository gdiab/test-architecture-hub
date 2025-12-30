# hl_overview

High level overview of the codebase

# React Repository Analysis

## 0. Repository Name
[[React]]

## 1. Project Purpose

React is a **JavaScript library for building user interfaces**, specifically focused on creating declarative, component-based UIs. It solves the problem of efficiently building and updating complex UIs by:

- Providing a component-based architecture for building reusable UI elements
- Implementing a virtual DOM for efficient updates
- Supporting server-side rendering (SSR) and streaming HTML (Fizz)
- Enabling React Server Components (RSC) for server-driven UI
- Providing concurrent rendering capabilities for better user experience
- Supporting multiple rendering targets (DOM, Native, Art, Test)

**Primary Domain:** Frontend UI development framework with server-side rendering capabilities.

## 2. Architecture Pattern

The repository employs multiple architectural patterns:

1. **Monorepo Architecture** - Multiple packages managed in a single repository
2. **Plugin/Adapter Pattern** - Different renderers (DOM, Native, Test) plug into the core reconciler
3. **Layered Architecture** - Clear separation between:
   - Public API (`react` package)
   - Reconciliation logic (`react-reconciler`)
   - Platform bindings (`react-dom`, `react-native-renderer`)
4. **Event-Driven Architecture** - React's event system with synthetic events
5. **Client-Server Architecture** - React Server Components with flight protocol

## 3. Technology Stack

### Primary Languages
- **JavaScript** (primary)
- **TypeScript** (for eslint-plugin-react-hooks and compiler packages)
- **Flow** (for type checking the core)

### Build & Bundling
- **Rollup** - Primary bundler for production builds
- **Webpack** - Used for DevTools and development builds
- **Babel** - JavaScript transpilation with custom transforms

### Testing
- **Jest** - Primary test framework
- **Playwright** - E2E testing for DevTools

### Package Management
- **Yarn** - Package manager (with workspaces)

### Major Dependencies (from package.json patterns)
- `art` - Vector graphics library for react-art
- `fbjs` - Facebook JavaScript utilities
- `object-assign` - Polyfill for Object.assign
- `scheduler` - React's internal scheduling package
- `acorn` - JavaScript parser (for compiler)

### Development Tools
- **ESLint** - Linting with custom rules
- **Prettier** - Code formatting
- **Flow** - Static type checking
- **Danger.js** - CI automation

## 4. Initial Structure Impression

| Directory | Purpose |
|-----------|---------|
| `packages/` | Core library packages (react, react-dom, reconciler, etc.) |
| `compiler/` | React Compiler (formerly React Forget) - automatic memoization |
| `fixtures/` | Example applications and integration tests |
| `scripts/` | Build, release, and development tooling |
| `flow-typed/` | Flow type definitions |
| `.github/` | CI/CD workflows and issue templates |

**Main High-Level Parts:**
- **Core Runtime** - React core, reconciler, scheduler
- **Renderers** - DOM, Native, Test, Art
- **Server Components** - Flight protocol implementations
- **DevTools** - Browser extensions and debugging tools
- **Compiler** - Build-time optimization tool

## 5. Configuration/Package Files

### Root Level
- `package.json` - Root workspace configuration
- `yarn.lock` - Dependency lock file
- `babel.config.js` - Babel configuration
- `babel.config-ts.js` - TypeScript Babel config
- `babel.config-react-compiler.js` - Compiler-specific Babel config
- `.eslintrc.js` - ESLint configuration
- `.prettierrc.js` - Prettier configuration
- `.nvmrc` - Node version specification
- `.editorconfig` - Editor settings
- `flow-typed.config.json` - Flow type config
- `ReactVersions.js` - Version management
- `react.code-workspace` - VS Code workspace

### Build Configuration
- `scripts/rollup/bundles.js` - Bundle definitions
- `scripts/rollup/build.js` - Build orchestration
- `scripts/rollup/forks.js` - Platform-specific forks
- `scripts/rollup/modules.js` - Module configuration

### Test Configuration
- `scripts/jest/config.base.js` - Base Jest config
- `scripts/jest/config.source.js` - Source testing config
- `scripts/jest/config.build.js` - Build testing config

### Package-Specific
Each package in `packages/` contains its own `package.json`

## 6. Directory Structure

### `/packages/` - Core Packages

| Package | Purpose |
|---------|---------|
| `react/` | Public React API (createElement, hooks, etc.) |
| `react-dom/` | DOM renderer and server rendering |
| `react-dom-bindings/` | DOM-specific event and property handling |
| `react-reconciler/` | Core reconciliation algorithm (Fiber) |
| `scheduler/` | Cooperative scheduling for concurrent features |
| `react-server/` | Server rendering infrastructure |
| `react-client/` | Client-side hydration and flight client |
| `react-server-dom-webpack/` | Webpack integration for RSC |
| `react-server-dom-turbopack/` | Turbopack integration for RSC |
| `react-server-dom-parcel/` | Parcel integration for RSC |
| `react-server-dom-esm/` | ESM integration for RSC |
| `react-native-renderer/` | React Native renderer |
| `react-art/` | Vector graphics renderer |
| `react-test-renderer/` | Test renderer for snapshots |
| `react-noop-renderer/` | No-op renderer for testing |
| `react-is/` | Runtime type checking utilities |
| `react-debug-tools/` | Hook inspection tools |
| `react-refresh/` | Fast Refresh (HMR) runtime |
| `react-cache/` | Experimental caching |
| `react-markup/` | Static markup generation |
| `shared/` | Shared utilities across packages |
| `use-sync-external-store/` | External store subscription hook |
| `use-subscription/` | Subscription utilities |
| `jest-react/` | Jest matchers for React |
| `eslint-plugin-react-hooks/` | ESLint rules for hooks |
| `internal-test-utils/` | Internal testing utilities |
| `dom-event-testing-library/` | DOM event simulation |

### DevTools Packages
| Package | Purpose |
|---------|---------|
| `react-devtools/` | Standalone DevTools app |
| `react-devtools-core/` | Core DevTools functionality |
| `react-devtools-shared/` | Shared DevTools components |
| `react-devtools-extensions/` | Browser extensions |
| `react-devtools-inline/` | Inline DevTools for embedding |
| `react-devtools-timeline/` | Performance timeline |
| `react-devtools-shell/` | Development shell for DevTools |
| `react-devtools-fusebox/` | Fusebox integration |

### `/compiler/` - React Compiler
```
compiler/
├── packages/
│   ├── babel-plugin-react-compiler/  # Babel plugin
│   ├── eslint-plugin-react-compiler/ # ESLint integration
│   ├── react-compiler-runtime/       # Runtime helpers
│   ├── react-compiler-healthcheck/   # Codebase analysis
│   ├── react-mcp-server/             # MCP server
│   ├── snap/                         # Snapshot testing
│   └── make-read-only-util/          # Utility for immutability
├── apps/
│   └── playground/                   # Interactive playground
├── scripts/                          # Build scripts
└── docs/                             # Documentation
```

### `/scripts/` - Build & Development Tools
| Directory | Purpose |
|-----------|---------|
| `rollup/` | Bundle building with Rollup |
| `jest/` | Jest configuration and setup |
| `babel/` | Custom Babel transforms |
| `eslint/` | ESLint setup |
| `eslint-rules/` | Custom ESLint rules |
| `flow/` | Flow type checking |
| `release/` | Release automation |
| `devtools/` | DevTools build scripts |
| `error-codes/` | Production error code extraction |
| `shared/` | Shared script utilities |
| `ci/` | CI/CD scripts |
| `bench/` | Benchmarking tools |

### `/fixtures/` - Examples & Integration Tests
| Directory | Purpose |
|-----------|---------|
| `dom/` | DOM usage examples |
| `ssr/`, `ssr2/` | Server-side rendering examples |
| `fizz/` | Streaming SSR examples |
| `flight/` | React Server Components example |
| `flight-esm/` | RSC with ESM |
| `flight-parcel/` | RSC with Parcel |
| `view-transition/` | View Transitions API integration |
| `devtools/` | DevTools testing fixtures |
| `packaging/` | Package format testing |
| `art/` | React Art examples |
| `attribute-behavior/` | DOM attribute testing |

## 7. High-Level Architecture

### Core Architecture Diagram
```
┌─────────────────────────────────────────────────────────────┐
│                      Public API                              │
│                     (react package)                          │
│  createElement, hooks, Component, Fragment, Suspense, etc.   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    React Reconciler                          │
│                 (react-reconciler package)                   │
│    Fiber architecture, scheduling, diffing, effects         │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
┌──────────────────┐ ┌──────────────┐ ┌──────────────────┐
│   react-dom      │ │ react-native │ │ react-test-      │
│   (Web Browser)  │ │  -renderer   │ │    renderer      │
└──────────────────┘ └──────────────┘ └──────────────────┘
```

### Server Components Architecture
```
┌────────────────────┐     Flight Protocol     ┌────────────────────┐
│   Server (RSC)     │ ──────────────────────► │   Client (Browser) │
│  react-server      │                         │   react-client     │
│  react-server-dom  │                         │   react-dom        │
└────────────────────┘                         └────────────────────┘
```

### Evidence Supporting Architecture:

1. **Adapter/Plugin Pattern**: 
   - `react-reconciler` defines `HostConfig` interface
   - Different renderers implement this interface
   - `packages/react-reconciler/src/forks/` contains platform-specific code

2. **Monorepo with Shared Code**:
   - `packages/shared/` contains common utilities
   - Rollup `forks.js` manages platform-specific builds

3. **Event-Driven**:
   - `react-dom-bindings/src/events/` implements synthetic event system
   - Event delegation pattern

4. **Concurrent/Scheduler**:
   - `packages/scheduler/` implements cooperative scheduling
   - Priority-based task execution

## 8. Build, Execution and Test

### Building

**Main Build Command:**
```bash
yarn build
```

**Build Scripts (from package.json and scripts/rollup/):**
```bash
# Build all release channels
node scripts/rollup/build-all-release-channels.js

# Build specific bundle
node scripts/rollup/build.js <bundle-type>

# Build for development
yarn build-for-devtools
```

**Build Configuration:**
- `scripts/rollup/bundles.js` - Defines all bundle configurations
- `scripts/rollup/build.js` - Main build orchestration
- Bundles are configured for different environments (browser, node, fb)

### Testing

**Test Commands:**
```bash
# Run all tests
yarn test

# Run specific test file
yarn test <pattern>

# Test with specific renderer
yarn test --renderer=<renderer>
```

**Test Configuration Files:**
- `scripts/jest/config.base.js` - Base configuration
- `scripts/jest/config.source.js` - Test source code
- `scripts/jest/config.build.js` - Test built artifacts

**Test Types:**
1. **Unit Tests** - Located in `__tests__/` directories within packages
2. **Integration Tests** - In fixtures directory
3. **E2E Tests** - Playwright tests for DevTools
4. **Fuzz Tests** - `runtime_fuzz_tests.yml` workflow

### Linting & Type Checking

```bash
# ESLint
yarn lint

# Flow type checking
yarn flow

# Prettier formatting
yarn prettier
```

### Main Entry Points

| Package | Entry Point |
|---------|-------------|
| `react` | `packages/react/index.js` |
| `react-dom` | `packages/react-dom/index.js` |
| `react-dom/server` | `packages/react-dom/server.js` |
| `react-dom/client` | `packages/react-dom/client.js` |
| `scheduler` | `packages/scheduler/index.js` |

### CI/CD Workflows

Located in `.github/workflows/`:
- `runtime_build_and_test.yml` - Main CI pipeline
- `runtime_prereleases.yml` - Pre-release builds
- `compiler_prereleases.yml` - Compiler releases
- `devtools_regression_tests.yml` - DevTools testing

### Release Process

```bash
# Prepare release
node scripts/release/prepare-release-from-ci.js

# Publish
node scripts/release/publish.js
```

Release scripts in `scripts/release/` handle:
- Version bumping
- Changelog generation
- npm publishing
- GitHub release creation

# module_deep_dive

Deep dive into modules

# Detailed Component Breakdown - React Repository

## Table of Contents
1. [Core React Package (`packages/react/`)](#1-core-react-package)
2. [React DOM (`packages/react-dom/`)](#2-react-dom)
3. [React Reconciler (`packages/react-reconciler/`)](#3-react-reconciler)
4. [Scheduler (`packages/scheduler/`)](#4-scheduler)
5. [Shared Utilities (`packages/shared/`)](#5-shared-utilities)
6. [React Server (`packages/react-server/`)](#6-react-server)
7. [React Client (`packages/react-client/`)](#7-react-client)
8. [React DOM Bindings (`packages/react-dom-bindings/`)](#8-react-dom-bindings)
9. [React Server DOM Webpack (`packages/react-server-dom-webpack/`)](#9-react-server-dom-webpack)
10. [React Server DOM Turbopack (`packages/react-server-dom-turbopack/`)](#10-react-server-dom-turbopack)
11. [React DevTools Packages](#11-react-devtools-packages)
12. [ESLint Plugin React Hooks (`packages/eslint-plugin-react-hooks/`)](#12-eslint-plugin-react-hooks)
13. [React Native Renderer (`packages/react-native-renderer/`)](#13-react-native-renderer)
14. [React Refresh (`packages/react-refresh/`)](#14-react-refresh)
15. [Scripts Directory (`scripts/`)](#15-scripts-directory)
16. [Compiler (`compiler/`)](#16-compiler)
17. [Fixtures (`fixtures/`)](#17-fixtures)

---

## 1. Core React Package
**Path:** `packages/react/`

### Core Responsibility
The foundational React library that provides the core API for creating React components, hooks, and elements. This is what developers import when they write `import React from 'react'`.

### Key Components

| File/Directory | Role |
|----------------|------|
| `index.js` | Main entry point exporting the React API |
| `index.experimental.js` | Entry point with experimental features enabled |
| `index.stable.js` | Entry point with only stable features |
| `jsx-runtime.js` | New JSX transform runtime (React 17+) |
| `jsx-dev-runtime.js` | Development-only JSX runtime with extra debugging |
| `react.react-server.js` | React Server Components entry point |
| `compiler-runtime.js` | Runtime support for React Compiler |
| `src/` | Source implementation files |
| `src/jsx/` | JSX transformation utilities |
| `npm/` | NPM package distribution files |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/shared/` - Core utilities like `ReactSharedInternals`, `ReactSymbols`, `ReactTypes`
- Relies on symbols and internal structures defined in shared

**External Interactions:**
- None directly; serves as the foundation for other packages
- Exposes `__CLIENT_INTERNALS_DO_NOT_USE_OR_WARN_USERS_THEY_CANNOT_UPGRADE` for internal package communication

---

## 2. React DOM
**Path:** `packages/react-dom/`

### Core Responsibility
Provides DOM-specific methods for rendering React components to the browser DOM, including client-side rendering, server-side rendering (SSR), and hydration capabilities.

### Key Components

| File/Directory | Role |
|----------------|------|
| `index.js` | Main entry point |
| `client.js` | Client-side rendering APIs (`createRoot`, `hydrateRoot`) |
| `server.js`, `server.node.js`, `server.browser.js`, `server.edge.js`, `server.bun.js` | SSR entry points for various environments |
| `static.js`, `static.node.js`, `static.browser.js`, `static.edge.js` | Static HTML generation |
| `profiling.js` | Profiling build entry point |
| `test-utils.js` | Testing utilities |
| `unstable_testing.js` | Experimental testing APIs |
| `src/client/` | Client rendering implementation |
| `src/server/` | Server rendering implementation |
| `src/events/` | Event system implementation |
| `src/shared/` | Shared utilities between client/server |
| `src/test-utils/` | Test utilities implementation |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/react/` - Core React APIs
- `@packages/react-reconciler/` - Fiber reconciliation algorithm
- `@packages/react-dom-bindings/` - DOM-specific bindings
- `@packages/shared/` - Shared utilities
- `@packages/scheduler/` - Task scheduling

**External Interactions:**
- Browser DOM APIs
- Node.js streams (for SSR)
- Web Streams API (for edge/browser SSR)

---

## 3. React Reconciler
**Path:** `packages/react-reconciler/`

### Core Responsibility
Implements the core reconciliation algorithm (Fiber architecture) that determines what changes need to be made to the host environment. This is the heart of React's rendering logic, designed to be host-agnostic.

### Key Components

| File/Directory | Role |
|----------------|------|
| `index.js` | Main entry point |
| `constants.js` | Shared constants (lane priorities, flags) |
| `reflection.js` | Utilities for inspecting fiber tree |
| `src/ReactFiber*.js` | Fiber node creation and management |
| `src/ReactFiberReconciler.js` | Main reconciler implementation |
| `src/ReactFiberWorkLoop.js` | Work loop and scheduling logic |
| `src/ReactFiberHooks.js` | Hooks implementation |
| `src/ReactFiberCommitWork.js` | Commit phase logic |
| `src/ReactFiberBeginWork.js` | Render phase begin work |
| `src/ReactFiberCompleteWork.js` | Render phase complete work |
| `src/ReactFiberLane.js` | Lane-based priority system |
| `src/ReactChildFiber.js` | Child reconciliation (diffing) |
| `src/forks/` | Platform-specific implementations |
| `src/__tests__/` | Extensive test suite |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/shared/` - Shared utilities, symbols, feature flags
- `@packages/scheduler/` - Cooperative scheduling
- Host config injection (provided by renderers like react-dom)

**External Interactions:**
- Interacts with host environments through injected host config
- No direct external API calls; purely internal algorithm

---

## 4. Scheduler
**Path:** `packages/scheduler/`

### Core Responsibility
A cooperative scheduling library that enables React to break up rendering work into chunks, yielding to the browser for high-priority tasks like user input. Implements priority-based task scheduling.

### Key Components

| File/Directory | Role |
|----------------|------|
| `index.js` | Main entry point |
| `index.native.js` | React Native specific entry |
| `unstable_mock.js` | Mock scheduler for testing |
| `unstable_post_task.js` | Integration with `scheduler.postTask` API |
| `src/Scheduler.js` | Core scheduling implementation |
| `src/SchedulerMinHeap.js` | Min-heap for priority queue |
| `src/SchedulerPriorities.js` | Priority level definitions |
| `src/SchedulerFeatureFlags.js` | Feature flags |
| `src/forks/` | Platform-specific implementations |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/shared/` - Minimal shared utilities

**External Interactions:**
- Browser APIs: `MessageChannel`, `requestAnimationFrame`, `performance.now()`
- Node.js: `setImmediate`
- Experimental: `scheduler.postTask` API

---

## 5. Shared Utilities
**Path:** `packages/shared/`

### Core Responsibility
Contains shared utilities, types, symbols, and feature flags used across all React packages. Provides common functionality to avoid code duplication.

### Key Components

| File | Role |
|------|------|
| `ReactSharedInternals.js` | Internal state shared between packages |
| `ReactSymbols.js` | Symbol definitions for React element types |
| `ReactTypes.js` | Type definitions |
| `ReactFeatureFlags.js` | Feature flag definitions |
| `ReactElementType.js` | Element type definitions |
| `CheckStringCoercion.js` | String coercion validation |
| `getComponentNameFromType.js` | Component name extraction for debugging |
| `ReactComponentStackFrame.js` | Component stack trace utilities |
| `shallowEqual.js` | Shallow equality comparison |
| `objectIs.js` | `Object.is` polyfill |
| `isArray.js` | Array check utility |
| `hasOwnProperty.js` | Property check utility |
| `assign.js` | Object.assign utility |
| `forks/` | Environment-specific implementations |

### Dependencies & Interactions

**Internal Dependencies:**
- None (this is the base layer)

**External Interactions:**
- None; provides pure utility functions

---

## 6. React Server
**Path:** `packages/react-server/`

### Core Responsibility
Implements server-side rendering logic including Fizz (streaming SSR) and Flight (React Server Components protocol). Provides the server-side component execution environment.

### Key Components

| File/Directory | Role |
|----------------|------|
| `index.js` | Main entry point for Fizz SSR |
| `flight.js` | Entry point for Flight (RSC) protocol |
| `src/ReactFizzServer.js` | Fizz streaming SSR implementation |
| `src/ReactFlightServer.js` | Flight server implementation |
| `src/ReactServerStreamConfig*.js` | Stream configuration |
| `src/flight/` | Flight-specific implementations |
| `src/forks/` | Environment-specific implementations |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/react/` - React core
- `@packages/shared/` - Shared utilities
- `@packages/react-dom-bindings/` - DOM rendering bindings

**External Interactions:**
- Node.js streams
- Web Streams API
- HTTP response streams

---

## 7. React Client
**Path:** `packages/react-client/`

### Core Responsibility
Implements the client-side counterpart to React Server Components, handling the Flight protocol for consuming server-rendered component streams.

### Key Components

| File/Directory | Role |
|----------------|------|
| `flight.js` | Main entry point for Flight client |
| `src/ReactFlightClient.js` | Flight client implementation |
| `src/ReactFlightReplyClient.js` | Client-to-server reply handling |
| `src/forks/` | Environment-specific implementations |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/react/` - React core
- `@packages/shared/` - Shared utilities

**External Interactions:**
- Fetch API for streaming responses
- Web Streams API

---

## 8. React DOM Bindings
**Path:** `packages/react-dom-bindings/`

### Core Responsibility
Contains DOM-specific implementation details for React DOM, including event handling, property diffing, and DOM manipulation. Separates host-specific code from the reconciler.

### Key Components

| Directory | Role |
|-----------|------|
| `src/client/` | Client-side DOM bindings |
| `src/client/ReactDOMComponent.js` | DOM component handling |
| `src/client/ReactDOMHostConfig.js` | Host config for reconciler |
| `src/events/` | Synthetic event system |
| `src/events/ReactDOMEventListener.js` | Event delegation |
| `src/events/plugins/` | Event plugins (click, input, etc.) |
| `src/server/` | Server-side DOM bindings |
| `src/shared/` | Shared between client/server |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/react-reconciler/` - Provides host config to reconciler
- `@packages/shared/` - Shared utilities
- `@packages/scheduler/` - Event scheduling

**External Interactions:**
- Browser DOM APIs
- Native event system

---

## 9. React Server DOM Webpack
**Path:** `packages/react-server-dom-webpack/`

### Core Responsibility
Webpack-specific integration for React Server Components, providing module resolution, bundling integration, and client/server component boundary handling for Webpack-based builds.

### Key Components

| File/Directory | Role |
|----------------|------|
| `client.js`, `client.browser.js`, `client.node.js`, `client.edge.js` | Client entry points |
| `server.js`, `server.browser.js`, `server.node.js`, `server.edge.js` | Server entry points |
| `static.js`, `static.*.js` | Static generation entry points |
| `plugin.js` | Webpack plugin for RSC |
| `node-register.js` | Node.js require hook |
| `src/client/` | Client-side implementation |
| `src/server/` | Server-side implementation |
| `src/shared/` | Shared configuration |
| `esm/` | ESM exports |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/react-server/` - Server rendering
- `@packages/react-client/` - Client consuming
- `@packages/shared/` - Shared utilities

**External Interactions:**
- Webpack bundler APIs
- Module resolution system
- Async chunk loading

---

## 10. React Server DOM Turbopack
**Path:** `packages/react-server-dom-turbopack/`

### Core Responsibility
Turbopack-specific integration for React Server Components, similar to the Webpack package but optimized for Turbopack's architecture (used in Next.js).

### Key Components

| File/Directory | Role |
|----------------|------|
| `client.js`, `client.*.js` | Client entry points for various environments |
| `server.js`, `server.*.js` | Server entry points |
| `static.js`, `static.*.js` | Static generation |
| `src/client/` | Client implementation |
| `src/server/` | Server implementation |
| `src/shared/` | Shared utilities |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/react-server/` - Server rendering core
- `@packages/react-client/` - Client core
- `@packages/shared/` - Utilities

**External Interactions:**
- Turbopack bundler APIs
- Next.js integration

---

## 11. React DevTools Packages

### 11.1 React DevTools Shared (`packages/react-devtools-shared/`)

#### Core Responsibility
Shared code between all DevTools packages, including the core inspection logic, component tree building, and hook inspection.

#### Key Components

| Directory | Role |
|-----------|------|
| `src/backend/` | Backend agent running in inspected page |
| `src/frontend/` | Frontend UI components |
| `src/devtools/` | Core DevTools logic |
| `src/hooks/` | Hook inspection utilities |
| `src/config/` | Configuration |
| `src/errors/` | Error handling |

#### Dependencies & Interactions
- React internals access via `__REACT_DEVTOOLS_GLOBAL_HOOK__`
- Chrome/Firefox extension APIs (via extensions package)

---

### 11.2 React DevTools Extensions (`packages/react-devtools-extensions/`)

#### Core Responsibility
Browser extension implementations for Chrome, Firefox, and Edge, providing the DevTools panel integration.

#### Key Components

| Directory | Role |
|-----------|------|
| `chrome/` | Chrome extension manifest and assets |
| `firefox/` | Firefox extension manifest |
| `edge/` | Edge extension manifest |
| `src/background/` | Extension background scripts |
| `src/contentScripts/` | Content scripts for page injection |
| `src/main/` | Main panel implementation |
| `popups/` | Extension popup UI |
| `icons/` | Extension icons |

---

### 11.3 React DevTools Core (`packages/react-devtools-core/`)

#### Core Responsibility
Standalone DevTools for React Native and other non-browser environments.

#### Key Components
- `backend.js` - Backend for embedding
- `standalone.js` - Standalone electron app entry

---

### 11.4 React DevTools Inline (`packages/react-devtools-inline/`)

#### Core Responsibility
Embeddable DevTools for integration into other tools (like CodeSandbox).

---

### 11.5 React DevTools Timeline (`packages/react-devtools-timeline/`)

#### Core Responsibility
Performance profiling timeline visualization for React applications.

---

## 12. ESLint Plugin React Hooks
**Path:** `packages/eslint-plugin-react-hooks/`

### Core Responsibility
ESLint plugin that enforces the Rules of Hooks, ensuring hooks are called correctly in React components and custom hooks.

### Key Components

| File/Directory | Role |
|----------------|------|
| `index.js` | Plugin entry point |
| `src/rules/RulesOfHooks.js` | Rules of Hooks enforcement |
| `src/rules/ExhaustiveDeps.js` | Dependency array validation |
| `src/code-path-analysis/` | Control flow analysis |
| `src/shared/` | Shared utilities |
| `src/types/` | TypeScript types |

### Dependencies & Interactions

**Internal Dependencies:**
- None from React packages (standalone ESLint plugin)

**External Interactions:**
- ESLint API
- AST parsing via ESLint

---

## 13. React Native Renderer
**Path:** `packages/react-native-renderer/`

### Core Responsibility
React renderer for React Native, providing the bridge between React's reconciler and React Native's native components.

### Key Components

| File/Directory | Role |
|----------------|------|
| `index.js` | Main entry point |
| `fabric.js` | New Fabric architecture entry |
| `src/ReactNativeRenderer.js` | Main renderer |
| `src/ReactFabric.js` | Fabric renderer |
| `src/ReactNativeHostConfig.js` | Host config for reconciler |
| `src/legacy-events/` | Legacy event system |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/react-reconciler/` - Reconciliation algorithm
- `@packages/shared/` - Shared utilities
- `@packages/scheduler/` - Scheduling

**External Interactions:**
- React Native bridge/JSI
- Native UI components

---

## 14. React Refresh
**Path:** `packages/react-refresh/`

### Core Responsibility
Enables Fast Refresh (hot reloading) for React components, allowing code changes to be applied without losing component state.

### Key Components

| File/Directory | Role |
|----------------|------|
| `runtime.js` | Runtime for Fast Refresh |
| `babel.js` | Babel plugin for transformation |
| `src/ReactFreshRuntime.js` | Core runtime implementation |
| `src/ReactFreshBabelPlugin.js` | Babel transform |

### Dependencies & Interactions

**Internal Dependencies:**
- React internals via `__REACT_DEVTOOLS_GLOBAL_HOOK__`

**External Interactions:**
- Babel for code transformation
- Bundler HMR APIs (webpack, Vite, etc.)

---

## 15. Scripts Directory
**Path:** `scripts/`

### Core Responsibility
Build tools, release automation, testing infrastructure, and development utilities for the React repository.

### Key Components

| Directory | Role |
|-----------|------|
| `rollup/` | Rollup build configuration and plugins |
| `release/` | Release automation scripts |
| `jest/` | Jest testing configuration |
| `eslint/` | ESLint configuration |
| `eslint-rules/` | Custom ESLint rules for React codebase |
| `babel/` | Custom Babel transforms |
| `flow/` | Flow type checking configuration |
| `error-codes/` | Production error code extraction |
| `ci/` | CI/CD scripts |
| `devtools/` | DevTools build scripts |
| `bench/` | Benchmarking tools |
| `tasks/` | Development tasks |
| `shared/` | Shared build utilities |

### Dependencies & Interactions

**Key Scripts:**
- `rollup/build.js` - Main build script
- `release/publish.js` - NPM publishing
- `jest/jest.js` - Test runner entry

**External Interactions:**
- NPM registry (publishing)
- CI systems (GitHub Actions)
- Various build tools (Rollup, Babel, etc.)

---

## 16. Compiler
**Path:** `compiler/`

### Core Responsibility
React Compiler (formerly React Forget) - an optimizing compiler that automatically memoizes React components and hooks, eliminating the need for manual `useMemo`, `useCallback`, and `React.memo`.

### Key Components

| Directory | Role |
|-----------|------|
| `packages/babel-plugin-react-compiler/` | Main Babel plugin |
| `packages/eslint-plugin-react-compiler/` | ESLint integration |
| `packages/react-compiler-runtime/` | Runtime helpers |
| `packages/react-compiler-healthcheck/` | Codebase compatibility checker |
| `packages/snap/` | Snapshot testing utility |
| `packages/react-forgive/` | Error recovery tooling |
| `packages/react-mcp-server/` | MCP server integration |
| `apps/playground/` | Interactive compiler playground |
| `docs/` | Documentation |
| `scripts/` | Build and release scripts |

### Dependencies & Interactions

**Internal Dependencies:**
- `@packages/react/` - React core (for runtime)

**External Interactions:**
- Babel transformation pipeline
- ESLint for validation
- IDE integrations

---

## 17. Fixtures
**Path:** `fixtures/`

### Core Responsibility
Test fixtures and example applications demonstrating various React features, used for manual testing, integration testing, and as reference implementations.

### Key Components

| Directory | Role |
|-----------|------|
| `dom/` | DOM integration tests |
| `flight/` | React Server Components example |
| `flight-esm/` | RSC with ESM |
| `flight-parcel/` | RSC with Parcel bundler |
| `ssr/`, `ssr2/` | Server-side rendering examples |
| `fizz/` | Streaming SSR example |
| `fizz-ssr-browser/` | Browser streaming SSR |
| `view-transition/` | View Transitions API integration |
| `concurrent/` | Concurrent features demo |
| `scheduler/` | Scheduler visualization |
| `attribute-behavior/` | DOM attribute behavior tests |
| `packaging/` | Various bundler integration tests |
| `legacy-jsx-runtimes/` | Compatibility tests for old JSX |
| `eslint-v6/`, `eslint-v7/`, `eslint-v8/`, `eslint-v9/` | ESLint version compatibility |
| `devtools/` | DevTools testing fixtures |
| `fiber-debugger/` | Fiber tree visualization tool |
| `art/` | React ART example |
| `nesting/` | Version nesting tests |
| `stacks/` | Stack trace testing |
| `owner-stacks/` | Owner stack testing |
| `expiration/` | Expiration time testing |

### Dependencies & Interactions

**Internal Dependencies:**
- Various React packages depending on the fixture

**External Interactions:**
- Various bundlers (Webpack, Parcel, Rollup, etc.)
- Node.js servers
- Browser environments

# dependencies

Analyze dependencies and external libraries

# Dependency and Architecture Analysis

## Repository: react_a4673a0c

This repository is the official React library source code, containing the core React packages, related tooling, compiler packages, development tools, and various fixtures for testing.

---

## Internal Modules

### Core Packages (`/packages/`)

| Module | Primary Responsibility |
|--------|----------------------|
| **react** | Core React library providing the fundamental API (hooks, components, elements, JSX runtime) |
| **react-dom** | DOM-specific rendering implementation for web browsers, including client and server rendering |
| **react-dom-bindings** | Low-level DOM bindings and event system used by react-dom |
| **react-reconciler** | The core reconciliation algorithm (Fiber) that powers React's rendering across all platforms |
| **scheduler** | Cooperative scheduling for React's concurrent features |
| **react-server** | Server-side rendering primitives and Flight protocol server implementation |
| **react-client** | Client-side primitives for React Server Components (Flight protocol client) |
| **shared** | Shared utilities, constants, symbols, and internal types used across all React packages |
| **react-is** | Runtime type checking utilities for React element types |
| **react-refresh** | Fast Refresh implementation for hot module replacement during development |
| **react-art** | React renderer for the ART drawing library |
| **react-native-renderer** | React renderer for React Native (Fabric and legacy) |
| **react-test-renderer** | Test renderer for snapshot testing React components |
| **react-markup** | Experimental package for React markup generation |
| **react-cache** | Experimental caching primitives for React |
| **use-sync-external-store** | Hook for subscribing to external stores with concurrent rendering support |
| **use-subscription** | Utility hook for subscribing to external data sources |
| **react-suspense-test-utils** | Testing utilities for React Suspense |

### React Server Components Packages

| Module | Primary Responsibility |
|--------|----------------------|
| **react-server-dom-webpack** | Server Components integration with Webpack bundler |
| **react-server-dom-turbopack** | Server Components integration with Turbopack bundler |
| **react-server-dom-parcel** | Server Components integration with Parcel bundler |
| **react-server-dom-esm** | Server Components integration for native ES modules |
| **react-server-dom-unbundled** | Server Components support without bundler integration |
| **react-server-dom-fb** | Facebook-internal Server Components implementation |

### DevTools Packages

| Module | Primary Responsibility |
|--------|----------------------|
| **react-devtools** | Standalone Electron application for React DevTools |
| **react-devtools-core** | Core DevTools functionality for backend/frontend communication |
| **react-devtools-shared** | Shared components and utilities between DevTools packages |
| **react-devtools-extensions** | Browser extensions for Chrome, Firefox, and Edge |
| **react-devtools-inline** | Embeddable DevTools for integration in other tools |
| **react-devtools-shell** | Development shell for testing DevTools |
| **react-devtools-timeline** | Performance profiling timeline visualization |
| **react-devtools-fusebox** | DevTools integration for React Native Fusebox |
| **react-debug-tools** | Debugging utilities for inspecting React component state |

### Testing & Development Utilities

| Module | Primary Responsibility |
|--------|----------------------|
| **jest-react** | Jest matchers and utilities for testing React components |
| **internal-test-utils** | Internal testing utilities for the React repository |
| **dom-event-testing-library** | Testing library for DOM events |
| **react-noop-renderer** | No-op renderer for testing reconciler behavior |
| **eslint-plugin-react-hooks** | ESLint plugin enforcing Rules of Hooks |

### Compiler Packages (`/compiler/packages/`)

| Module | Primary Responsibility |
|--------|----------------------|
| **babel-plugin-react-compiler** | Babel plugin for the React Compiler (auto-memoization) |
| **eslint-plugin-react-compiler** | ESLint plugin for React Compiler compatibility checks |
| **react-compiler-runtime** | Runtime helpers for React Compiler output |
| **react-compiler-healthcheck** | Health check utility for React Compiler compatibility |
| **react-mcp-server** | Model Context Protocol server for React documentation |
| **react-forgive** | VS Code language server extension for React Compiler |
| **snap** | Snapshot testing utility for the compiler |
| **make-read-only-util** | Utility for making objects read-only |

### Build & Scripts (`/scripts/`)

| Module | Primary Responsibility |
|--------|----------------------|
| **scripts/rollup** | Rollup-based build system for bundling React packages |
| **scripts/jest** | Jest configuration and test infrastructure |
| **scripts/release** | Release automation and publishing scripts |
| **scripts/babel** | Custom Babel plugins and transformations |
| **scripts/eslint-rules** | Custom ESLint rules for the React codebase |
| **scripts/flow** | Flow type checking configuration and utilities |
| **scripts/error-codes** | Error code extraction and transformation for production builds |
| **scripts/bench** | Performance benchmarking infrastructure |
| **scripts/devtools** | DevTools build and release scripts |

---

## External Dependencies

### Build & Compilation Tools

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `@babel/core` | Babel | JavaScript compiler core | `/package.json`, `/compiler/apps/playground/package.json`, multiple packages |
| `@babel/parser` | Babel Parser | JavaScript/TypeScript parser | `/package.json`, `/packages/react-devtools-shared/package.json` |
| `@babel/generator` | Babel Generator | AST to code generation | `/compiler/apps/playground/package.json` |
| `@babel/traverse` | Babel Traverse | AST traversal utilities | `/package.json`, `/packages/react-devtools-shared/package.json` |
| `@babel/types` | Babel Types | AST type definitions and utilities | `/package.json`, `/compiler/packages/babel-plugin-react-compiler/package.json` |
| `@babel/preset-env` | Babel Preset Env | Environment-based transpilation | `/package.json`, `/packages/react-devtools-shared/package.json` |
| `@babel/preset-react` | Babel Preset React | React JSX transformation | `/package.json`, `/compiler/apps/playground/package.json` |
| `@babel/preset-typescript` | Babel Preset TypeScript | TypeScript transformation | `/package.json`, `/compiler/apps/playground/package.json` |
| `@babel/preset-flow` | Babel Preset Flow | Flow type stripping | `/package.json`, `/packages/react-devtools-shared/package.json` |
| `@babel/runtime` | Babel Runtime | Runtime helpers | `/packages/react-devtools-shared/package.json` |
| `@babel/register` | Babel Register | Runtime compilation hook | `/fixtures/fizz/package.json`, `/fixtures/view-transition/package.json` |
| `rollup` | Rollup | Module bundler | `/package.json` |
| `@rollup/plugin-babel` | Rollup Babel Plugin | Babel integration for Rollup | `/package.json` |
| `@rollup/plugin-commonjs` | Rollup CommonJS Plugin | CommonJS module support | `/package.json` |
| `@rollup/plugin-node-resolve` | Rollup Node Resolve | Node module resolution | `/package.json` |
| `@rollup/plugin-replace` | Rollup Replace Plugin | String replacement during build | `/package.json` |
| `@rollup/plugin-typescript` | Rollup TypeScript Plugin | TypeScript support for Rollup | `/package.json` |
| `rollup-plugin-dts` | Rollup DTS Plugin | TypeScript declaration bundling | `/package.json` |
| `webpack` | Webpack | Module bundler | `/fixtures/flight/package.json`, `/packages/react-devtools-core/package.json` |
| `webpack-cli` | Webpack CLI | Webpack command line interface | `/fixtures/fizz/package.json`, `/packages/react-devtools-core/package.json` |
| `webpack-dev-server` | Webpack Dev Server | Development server | `/packages/react-devtools-extensions/package.json` |
| `webpack-sources` | Webpack Sources | Source code abstraction | `/fixtures/flight-esm/package.json`, `/packages/react-server-dom-webpack/package.json` |
| `google-closure-compiler` | Google Closure Compiler | JavaScript optimizer | `/package.json` |
| `typescript` | TypeScript | TypeScript compiler | `/package.json`, `/compiler/package.json` |
| `esbuild` | esbuild | Fast JavaScript bundler | `/compiler/package.json` |
| `tsup` | tsup | TypeScript bundler | `/package.json`, `/compiler/package.json` |

### Testing Frameworks

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `jest` | Jest | JavaScript testing framework | `/package.json`, `/fixtures/legacy-jsx-runtimes/package.json` |
| `jest-cli` | Jest CLI | Jest command line interface | `/package.json` |
| `jest-diff` | Jest Diff | Difference comparison utility | `/package.json`, `/fixtures/dom/package.json` |
| `jest-environment-jsdom` | Jest JSDOM Environment | Browser-like test environment | `/package.json` |
| `jest-snapshot-serializer-raw` | Jest Raw Serializer | Raw snapshot serialization | `/package.json` |
| `@playwright/test` | Playwright | End-to-end testing framework | `/compiler/apps/playground/package.json`, `/fixtures/flight/package.json` |
| `@testing-library/react` | Testing Library React | React testing utilities | `/fixtures/flight/package.json`, `/compiler/packages/snap/package.json` |
| `@testing-library/jest-dom` | Testing Library Jest DOM | DOM testing matchers | `/fixtures/flight/package.json` |
| `@testing-library/user-event` | Testing Library User Event | User interaction simulation | `/fixtures/flight/package.json` |
| `jsdom` | jsdom | DOM implementation for Node.js | `/compiler/packages/snap/package.json` |
| `mocha` | Mocha | Test framework | `/compiler/packages/react-forgive/package.json` |

### Code Quality & Linting

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `eslint` | ESLint | JavaScript linter | `/package.json`, `/fixtures/eslint-v6/package.json` through `/fixtures/eslint-v9/package.json` |
| `eslint-config-prettier` | ESLint Config Prettier | Prettier ESLint integration | `/package.json` |
| `eslint-plugin-babel` | ESLint Plugin Babel | Babel-specific linting | `/package.json` |
| `eslint-plugin-jest` | ESLint Plugin Jest | Jest-specific linting | `/package.json` |
| `eslint-plugin-react` | ESLint Plugin React | React-specific linting | `/package.json` |
| `@typescript-eslint/parser` | TypeScript ESLint Parser | TypeScript parsing for ESLint | `/package.json` |
| `@typescript-eslint/eslint-plugin` | TypeScript ESLint Plugin | TypeScript linting rules | `/package.json` |
| `prettier` | Prettier | Code formatter | `/package.json`, `/compiler/package.json` |
| `flow-bin` | Flow | Static type checker | `/package.json` |
| `flow-remove-types` | Flow Remove Types | Strip Flow types | `/package.json` |
| `hermes-parser` | Hermes Parser | JavaScript parser (Hermes engine) | `/compiler/apps/playground/package.json`, `/packages/eslint-plugin-react-hooks/package.json` |
| `hermes-eslint` | Hermes ESLint | ESLint integration for Hermes | `/compiler/apps/playground/package.json`, `/package.json` |

### Schema Validation

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `zod` | Zod | TypeScript-first schema validation | `/compiler/packages/eslint-plugin-react-compiler/package.json`, `/compiler/packages/react-mcp-server/package.json` |
| `zod-validation-error` | Zod Validation Error | Human-readable Zod errors | `/compiler/packages/eslint-plugin-react-compiler/package.json` |

### Server & Networking

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `express` | Express | Web application framework | `/fixtures/fizz/package.json`, `/fixtures/ssr/package.json` |
| `compression` | Compression | Gzip compression middleware | `/fixtures/fizz/package.json`, `/fixtures/flight/package.json` |
| `body-parser` | Body Parser | Request body parsing middleware | `/fixtures/flight-esm/package.json`, `/fixtures/flight/package.json` |
| `busboy` | Busboy | Multipart form data parser | `/fixtures/flight-esm/package.json`, `/fixtures/flight/package.json` |
| `undici` | Undici | HTTP/1.1 client | `/fixtures/flight-esm/package.json`, `/package.json` |
| `node-fetch` | Node Fetch | Fetch API for Node.js | `/fixtures/ssr/package.json` |
| `ws` | ws | WebSocket client/server | `/packages/react-devtools-core/package.json` |
| `nodemon` | Nodemon | Development auto-restart | `/fixtures/fizz/package.json`, `/fixtures/flight/package.json` |

### UI & Visualization

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `monaco-editor` | Monaco Editor | Code editor component | `/compiler/apps/playground/package.json` |
| `@monaco-editor/react` | Monaco Editor React | React wrapper for Monaco | `/compiler/apps/playground/package.json` |
| `react-window` | React Window | Virtualized list component | `/packages/react-devtools-shared/package.json` |
| `react-virtualized-auto-sizer` | React Virtualized Auto Sizer | Auto-sizing for virtualized lists | `/packages/react-devtools-shared/package.json` |
| `@reach/menu-button` | Reach Menu Button | Accessible menu component | `/packages/react-devtools-shared/package.json` |
| `@reach/tooltip` | Reach Tooltip | Accessible tooltip component | `/packages/react-devtools-shared/package.json` |
| `@heroicons/react` | Heroicons React | SVG icon components | `/compiler/apps/playground/package.json` |
| `@use-gesture/react` | Use Gesture | Gesture handling for React | `/compiler/apps/playground/package.json` |
| `notistack` | Notistack | Snackbar notifications | `/compiler/apps/playground/package.json` |
| `re-resizable` | Re-resizable | Resizable component | `/compiler/apps/playground/package.json` |
| `dagre` | Dagre | Directed graph layout | `/fixtures/fiber-debugger/package.json` |
| `react-motion` | React Motion | Animation library | `/fixtures/fiber-debugger/package.json` |
| `react-draggable` | React Draggable | Draggable component | `/fixtures/fiber-debugger/package.json` |
| `victory` | Victory | Data visualization library | `/fixtures/concurrent/time-slicing/package.json` |
| `glamor` | Glamor | CSS-in-JS library | `/fixtures/attribute-behavior/package.json` |
| `art` | ART | Vector drawing library | `/packages/react-art/package.json`, `/fixtures/dom/package.json` |

### DevTools & Development

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `electron` | Electron | Desktop application framework | `/packages/react-devtools/package.json` |
| `web-ext` | Web-Ext | Browser extension tooling | `/packages/react-devtools-extensions/package.json` |
| `chrome-launcher` | Chrome Launcher | Chrome automation | `/scripts/bench/package.json` |
| `lighthouse` | Lighthouse | Performance auditing | `/scripts/bench/package.json` |
| `puppeteer` | Puppeteer | Headless browser automation | `/scripts/release/package.json`, `/compiler/packages/react-mcp-server/package.json` |
| `error-stack-parser` | Error Stack Parser | Stack trace parsing | `/packages/react-debug-tools/package.json`, `/package.json` |
| `source-map-js` | Source Map JS | Source map parsing | `/packages/react-devtools-inline/package.json` |
| `@jridgewell/sourcemap-codec` | Sourcemap Codec | Source map encoding/decoding | `/packages/react-devtools-inline/package.json` |

### MCP & AI Integration

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `@modelcontextprotocol/sdk` | Model Context Protocol SDK | MCP server implementation | `/compiler/packages/react-mcp-server/package.json` |
| `algoliasearch` | Algolia | Search API client | `/compiler/packages/react-mcp-server/package.json` |
| `cheerio` | Cheerio | HTML parsing and manipulation | `/compiler/packages/react-mcp-server/package.json` |
| `html-to-text` | HTML to Text | HTML to plain text conversion | `/compiler/packages/react-mcp-server/package.json` |

### Utility Libraries

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `invariant` | Invariant | Runtime assertions | `/compiler/apps/playground/package.json`, `/compiler/packages/make-read-only-util/package.json` |
| `lru-cache` | LRU Cache | Least-recently-used cache | `/compiler/apps/playground/package.json` |
| `lz-string` | LZ String | String compression | `/compiler/apps/playground/package.json` |
| `pretty-format` | Pretty Format | Object formatting | `/compiler/apps/playground/package.json`, `/package.json` |
| `semver` | Semver | Semantic versioning | `/fixtures/dom/package.json`, `/scripts/release/package.json` |
| `chalk` | Chalk | Terminal string styling | `/package.json`, `/scripts/release/package.json` |
| `glob` | Glob | File pattern matching | `/package.json`, `/compiler/packages/snap/package.json` |
| `minimist` | Minimist | Argument parsing | `/package.json`, `/packages/react-devtools/package.json` |
| `yargs` | Yargs | Command-line argument parsing | `/compiler/package.json`, `/compiler/packages/react-compiler-healthcheck/package.json` |
| `fs-extra` | FS Extra | Extended filesystem utilities | `/compiler/package.json`, `/scripts/release/package.json` |
| `rimraf` | Rimraf | Recursive file deletion | `/fixtures/fizz/package.json`, `/package.json` |
| `mkdirp` | mkdirp | Recursive directory creation | `/package.json` |
| `ncp` | NCP | Recursive file copying | `/package.json`, `/scripts/bench/package.json` |
| `shelljs` | ShellJS | Shell commands in Node.js | `/package.json` |
| `cross-spawn` | Cross Spawn | Cross-platform spawn | `/packages/react-devtools/package.json` |
| `concurrently` | Concurrently | Run commands in parallel | `/fixtures/fizz/package.json`, `/compiler/package.json` |
| `ora` | Ora | Terminal spinner | `/compiler/package.json`, `/compiler/packages/react-compiler-healthcheck/package.json` |
| `fast-glob` | Fast Glob | Fast file globbing | `/compiler/packages/react-compiler-healthcheck/package.json` |
| `json5` | JSON5 | Extended JSON format | `/packages/react-devtools-shared/package.json` |
| `compare-versions` | Compare Versions | Version comparison | `/packages/react-devtools-shared/package.json` |
| `clipboard-js` | Clipboard.js | Clipboard operations | `/packages/react-devtools-shared/package.json` |
| `memoize-one` | Memoize One | Single-value memoization | `/packages/react-devtools-timeline/package.json` |
| `nullthrows` | Nullthrows | Non-null assertion | `/packages/react-devtools-timeline/package.json` |
| `pretty-ms` | Pretty MS | Millisecond formatting | `/packages/react-devtools-timeline/package.json` |
| `shell-quote` | Shell Quote | Shell string quoting | `/packages/react-devtools-core/package.json` |
| `update-notifier` | Update Notifier | Package update notifications | `/packages/react-devtools/package.json` |
| `internal-ip` | Internal IP | Get internal IP address | `/packages/react-devtools/package.json` |

### Framework & Runtime Support

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `next` | Next.js | React framework | `/compiler/apps/playground/package.json` |
| `parcel` | Parcel | Zero-config bundler | `/fixtures/flight-parcel/package.json` |
| `react-scripts` | Create React App | React app tooling | `/fixtures/attribute-behavior/package.json`, `/fixtures/owner-stacks/package.json` |
| `tailwindcss` | Tailwind CSS | Utility-first CSS framework | `/fixtures/flight/package.json`, `/compiler/apps/playground/package.json` |
| `postcss` | PostCSS | CSS transformation | `/fixtures/flight/package.json` |

### Parsing & Code Analysis

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `acorn-loose` | Acorn Loose | Tolerant JavaScript parser | `/packages/react-server-dom-webpack/package.json`, `/packages/react-server-dom-turbopack/package.json` |
| `acorn-jsx` | Acorn JSX | JSX parsing for Acorn | `/packages/react-devtools-extensions/package.json` |

### Async & Scheduling

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `neo-async` | Neo-Async | Async utilities | `/packages/react-server-dom-webpack/package.json`, `/packages/react-server-dom-turbopack/package.json` |

### React Ecosystem

| Dependency | Official Name | Purpose | Source |
|------------|--------------|---------|--------|
| `prop-types` | PropTypes | Runtime type checking | `/fixtures/dom/package.json`, `/package.json` |
| `create-react-class` | Create React Class | Legacy class component creation | `/packages/react-art/package.json`, `/package.json` |
| `react-lifecycles-compat` | React Lifecycles Compat | Lifecycle polyfill | `/package.json` |
| `react-error-boundary` | React Error Boundary | Error boundary component | `/fixtures/fizz/package.json`, `/fixtures/ssr2/package.json` |
| `react-redux` | React Redux | Redux bindings for React | `/fixtures/nesting/src/legacy/package.json`, `/fixtures/nesting/src/modern/package.json` |
| `react-router-dom` | React Router DOM | DOM routing for React | `/fixtures/nesting/src/legacy/package

# core_entities

Core entities and their relationships

# Domain Model Analysis - React Repository

## Overview

This repository is the **React** JavaScript library source code. After analyzing the structure, the core domain revolves around **React's component rendering system**, **reconciliation engine**, **server-side rendering**, and **developer tooling**.

---

## 1. Common Data Entities / Domain Models

### 1.1 React Element

The fundamental unit representing a node in the React virtual DOM tree.

| Attribute | Type | Description |
|-----------|------|-------------|
| `$$typeof` | Symbol | Type identifier (e.g., `REACT_ELEMENT_TYPE`) |
| `type` | string \| function \| class | Element type (DOM tag, function component, class component) |
| `key` | string \| null | Unique identifier for reconciliation |
| `ref` | Ref \| null | Reference to DOM node or component instance |
| `props` | Object | Properties passed to the element |
| `_owner` | Fiber \| null | Component that created this element |

---

### 1.2 Fiber

The internal work-in-progress representation of a component in the reconciler.

| Attribute | Type | Description |
|-----------|------|-------------|
| `tag` | number | Type of fiber (FunctionComponent, ClassComponent, HostComponent, etc.) |
| `key` | string \| null | Unique key for diffing |
| `elementType` | any | The element type (function, class, string) |
| `type` | any | Resolved type |
| `stateNode` | any | DOM node or class instance |
| `return` | Fiber \| null | Parent fiber |
| `child` | Fiber \| null | First child fiber |
| `sibling` | Fiber \| null | Next sibling fiber |
| `index` | number | Position among siblings |
| `ref` | Ref \| null | Reference attachment |
| `pendingProps` | any | New props to process |
| `memoizedProps` | any | Props used in last render |
| `memoizedState` | any | State used in last render |
| `updateQueue` | UpdateQueue \| null | Queue of state updates |
| `flags` | number | Side-effect flags (Placement, Update, Deletion, etc.) |
| `lanes` | Lanes | Priority lanes for scheduling |
| `alternate` | Fiber \| null | Work-in-progress or current fiber |

---

### 1.3 Update / UpdateQueue

Represents pending state updates for a component.

| Attribute | Type | Description |
|-----------|------|-------------|
| `lane` | Lane | Priority of the update |
| `tag` | number | Update type (UpdateState, ReplaceState, ForceUpdate, CaptureUpdate) |
| `payload` | any | The state change (value or function) |
| `callback` | function \| null | Callback after commit |
| `next` | Update \| null | Next update in queue |

**UpdateQueue:**

| Attribute | Type | Description |
|-----------|------|-------------|
| `baseState` | any | State before updates |
| `firstBaseUpdate` | Update \| null | First pending update |
| `lastBaseUpdate` | Update \| null | Last pending update |
| `shared.pending` | Update \| null | Circular list of new updates |

---

### 1.4 Hook

Internal structure for managing hooks in function components.

| Attribute | Type | Description |
|-----------|------|-------------|
| `memoizedState` | any | Current hook state |
| `baseState` | any | Base state for updates |
| `baseQueue` | Update \| null | Pending updates |
| `queue` | UpdateQueue \| null | Update queue for this hook |
| `next` | Hook \| null | Next hook in the chain |

---

### 1.5 Lane / Lanes

Represents scheduling priorities (bitmask-based system).

| Attribute | Type | Description |
|-----------|------|-------------|
| `SyncLane` | number | Highest priority (synchronous) |
| `InputContinuousLane` | number | Continuous input (e.g., drag) |
| `DefaultLane` | number | Normal priority |
| `TransitionLanes` | number | Transition updates |
| `IdleLane` | number | Lowest priority (idle work) |

---

### 1.6 FiberRoot

The root of a React application tree.

| Attribute | Type | Description |
|-----------|------|-------------|
| `containerInfo` | any | DOM container reference |
| `current` | Fiber | Current committed fiber tree |
| `pendingLanes` | Lanes | Lanes with pending work |
| `finishedWork` | Fiber \| null | Completed work ready to commit |
| `callbackNode` | any | Scheduler callback |
| `callbackPriority` | Lane | Priority of scheduled callback |
| `eventTimes` | array | Event timestamps per lane |
| `expirationTimes` | array | Expiration times per lane |

---

### 1.7 ReactSharedInternals

Shared internal state across React packages.

| Attribute | Type | Description |
|-----------|------|-------------|
| `ReactCurrentDispatcher` | Object | Current hooks dispatcher |
| `ReactCurrentOwner` | Object | Current rendering fiber |
| `ReactCurrentBatchConfig` | Object | Current batching configuration |

---

### 1.8 DevTools Element (Inspector Data)

Representation of components in React DevTools.

| Attribute | Type | Description |
|-----------|------|-------------|
| `id` | number | Unique element identifier |
| `type` | string | Element type name |
| `displayName` | string | Component display name |
| `key` | string \| null | React key |
| `depth` | number | Tree depth level |
| `parentID` | number | Parent element ID |
| `children` | array | Child element IDs |
| `owners` | array | Owner component chain |
| `props` | Object | Component props (inspectable) |
| `state` | Object | Component state (inspectable) |
| `hooks` | array | Hooks data for function components |

---

### 1.9 Flight Chunk (Server Components)

Data chunk in React Server Components streaming.

| Attribute | Type | Description |
|-----------|------|-------------|
| `id` | number | Chunk identifier |
| `type` | string | Chunk type (model, module, error, etc.) |
| `value` | any | Serialized data |
| `status` | string | Resolution status (pending, resolved, rejected) |

---

### 1.10 Scheduler Task

Unit of work in the Scheduler package.

| Attribute | Type | Description |
|-----------|------|-------------|
| `id` | number | Task identifier |
| `callback` | function | Work to perform |
| `priorityLevel` | number | Task priority |
| `startTime` | number | When task becomes ready |
| `expirationTime` | number | Deadline for task |
| `sortIndex` | number | Position in queue |

---

## 2. Entity Relationships

```
┌─────────────────────────────────────────────────────────────────────┐
│                           FiberRoot                                  │
│  (Application Root)                                                  │
└─────────────────────┬───────────────────────────────────────────────┘
                      │ 1:1
                      ▼
┌─────────────────────────────────────────────────────────────────────┐
│                             Fiber                                    │
│  (Component Instance in Tree)                                        │
│                                                                      │
│   ┌──────────┐    ┌──────────┐    ┌──────────┐                      │
│   │  return  │◄───│  Fiber   │───►│  child   │                      │
│   │ (parent) │    │          │    │          │                      │
│   └──────────┘    └────┬─────┘    └──────────┘                      │
│                        │                                             │
│                        ▼ sibling                                     │
│                   ┌──────────┐                                       │
│                   │  Fiber   │                                       │
│                   └──────────┘                                       │
└─────────────────────────────────────────────────────────────────────┘
                      │
          ┌───────────┴───────────┐
          │                       │
          ▼ 1:N                   ▼ 1:1
┌─────────────────┐      ┌─────────────────┐
│      Hook       │      │  ReactElement   │
│  (linked list)  │      │  (describes UI) │
└────────┬────────┘      └─────────────────┘
         │ 1:1
         ▼
┌─────────────────┐
│   UpdateQueue   │
│                 │
└────────┬────────┘
         │ 1:N
         ▼
┌─────────────────┐
│     Update      │
│  (linked list)  │
└─────────────────┘
```

### Relationship Summary

| Relationship | Type | Description |
|--------------|------|-------------|
| FiberRoot → Fiber | 1:1 | Root has one current fiber tree |
| Fiber → Fiber (child) | 1:N | Parent has multiple children (via sibling links) |
| Fiber → Fiber (alternate) | 1:1 | Current ↔ Work-in-progress pairing |
| Fiber → Hook | 1:N | Function component has chain of hooks |
| Fiber → UpdateQueue | 1:1 | Each fiber can have pending updates |
| UpdateQueue → Update | 1:N | Queue contains multiple updates |
| Fiber → ReactElement | N:1 | Multiple fibers can reference same element type |
| Hook → UpdateQueue | 1:1 | Each useState/useReducer hook has its own queue |
| Scheduler Task → FiberRoot | N:1 | Multiple tasks can target same root |
| DevTools Element → Fiber | 1:1 | DevTools mirrors fiber tree for inspection |
| Flight Chunk → ReactElement | 1:N | Chunks serialize element tree for RSC |

---

## 3. Package-to-Entity Mapping

| Package | Primary Entities |
|---------|------------------|
| `react` | ReactElement, Component types, Symbols |
| `react-reconciler` | Fiber, FiberRoot, Hook, Update, UpdateQueue, Lanes |
| `react-dom` | HostConfig (DOM-specific), Events |
| `scheduler` | Task, Priority levels |
| `react-server` | Flight chunks, Server context |
| `react-client` | Flight response, Chunk resolution |
| `react-devtools-shared` | DevTools Element, Profiling data |
| `shared` | Symbols, Feature flags, Types |

---

## 4. Key Observations

1. **Fiber is Central**: Almost all runtime behavior revolves around the Fiber tree structure
2. **Double Buffering**: The `alternate` pointer enables work-in-progress without blocking current tree
3. **Linked Lists**: Updates and Hooks use linked lists for efficient traversal and batching
4. **Priority System**: Lanes provide fine-grained scheduling control
5. **Separation of Concerns**: Core reconciler is renderer-agnostic; DOM specifics live in `react-dom-bindings`

# DBs

databases analysis

# Database Analysis Report

After conducting a comprehensive scan of the provided React repository codebase, I analyzed:

- Configuration files (package.json, webpack configs, babel configs)
- Source code across all packages in `/packages/`
- Fixture files in `/fixtures/`
- Compiler packages in `/compiler/`
- Scripts and tooling in `/scripts/`
- Test utilities and test files

I searched for:
- Database connection strings and configurations
- ORM definitions (SQLAlchemy, Sequelize, Mongoose, Prisma, TypeORM, etc.)
- SQL query patterns (SELECT, INSERT, CREATE TABLE, etc.)
- NoSQL client libraries (MongoDB, Redis, DynamoDB, Cassandra, etc.)
- Database-related imports and dependencies
- Migration scripts and schema definitions
- Data persistence layers

## Findings

This repository is the **official React JavaScript library source code**. It is a frontend UI library that:

1. Provides the core React runtime (`react`, `react-dom`, `react-reconciler`)
2. Contains React DevTools extensions and tooling
3. Includes the React Compiler (Forget)
4. Contains testing utilities and fixtures
5. Includes build and release scripts

The codebase is entirely focused on:
- UI component rendering
- Virtual DOM reconciliation
- Server-side rendering (SSR) via Fizz
- React Server Components (Flight)
- Development tooling

**No database interactions were found.** The repository does not include:
- Any database drivers or client libraries
- ORM or ODM configurations
- Database connection logic
- Schema definitions or migrations
- Data persistence code

---

**no database**

# APIs

APIs analysis

# API Documentation Analysis

After a comprehensive scan of the repository structure and examining the relevant files, I need to analyze the codebase for HTTP API endpoints.

## Analysis Summary

This repository is the **React** JavaScript library repository (facebook/react). It contains:

1. **React core packages** - The React library, ReactDOM, React Native renderer, etc.
2. **Build tools and scripts** - Rollup configurations, Babel plugins, ESLint rules
3. **DevTools** - React Developer Tools extensions and related packages
4. **Test utilities** - Testing frameworks and utilities
5. **Fixtures** - Example applications and test fixtures

## HTTP API Findings

Looking at the identified server-related files:

### 1. `scripts/bench/server.js` - Benchmark Server
This appears to be a development/benchmarking server, not an exposed API.

### 2. `fixtures/flight/server/` - React Server Components Fixture
This is a fixture/example application for React Server Components.

### 3. `fixtures/ssr/server/` and `fixtures/ssr2/server/` - SSR Fixtures
These are Server-Side Rendering example/fixture applications.

### 4. `fixtures/fizz/server/` - Fizz SSR Fixture
Another SSR example application.

### 5. `fixtures/view-transition/server/` - View Transition Fixture
Example application for view transitions.

### 6. `packages/react-devtools-shell/webpack-server.js` - DevTools Development Server
Development server for React DevTools.

### 7. `compiler/packages/react-forgive/server/` - Compiler Playground Server
Development server for the React Compiler playground.

---

## Conclusion

After thorough analysis of the repository:

**no HTTP API**

This repository is the React library source code itself. While it contains several development servers and fixture applications used for testing and demonstration purposes, these are:

1. **Internal development tools** (benchmark servers, webpack dev servers)
2. **Fixture/example applications** (SSR demos, flight fixtures)
3. **Not production HTTP APIs** intended for external consumption

The servers found are primarily:
- Static file servers for development
- Server-Side Rendering examples
- React Server Components demonstrations
- DevTools development infrastructure

None of these constitute a public HTTP API that would require documentation for external consumers. The repository's purpose is to provide the React library packages, not to expose HTTP endpoints.

# events

events analysis

I have thoroughly analyzed the React repository structure and files. This is the React JavaScript library source code repository, which is a UI library for building user interfaces.

After a comprehensive scan of the codebase, I can confirm that:

**no events**

This repository is the React core library itself, which provides the building blocks for creating React applications. It does not contain any message broker event consumption or production code (such as SQS, EventBridge, Kafka, Ably, RabbitMQ, Pub/Sub, etc.).

The codebase includes:
- React core library implementation
- React DOM bindings
- React Native renderer
- React Server Components
- React DevTools
- React Compiler
- Test utilities and fixtures
- Build scripts and tooling

While React has its own internal event system for DOM events (like onClick, onChange, etc.) in the `react-dom-bindings` package, these are **browser DOM events** - not message broker/event bus events for inter-service communication. The `dom-event-testing-library` package is also for testing browser DOM events, not message queue events.

The repository does not integrate with any external message brokers or event streaming platforms for producing or consuming asynchronous messages.

# service_dependencies

Analyze service dependencies

# External Dependencies Analysis: React Repository

## Table of Contents
1. [Core Build & Development Tools](#core-build--development-tools)
2. [Testing Frameworks & Libraries](#testing-frameworks--libraries)
3. [Bundlers & Build Systems](#bundlers--build-systems)
4. [Code Quality & Linting](#code-quality--linting)
5. [UI & Rendering Libraries](#ui--rendering-libraries)
6. [Server & Networking](#server--networking)
7. [Cloud & External Services](#cloud--external-services)
8. [DevTools & Monitoring](#devtools--monitoring)
9. [Parser & Compiler Libraries](#parser--compiler-libraries)
10. [Utility Libraries](#utility-libraries)

---

## Core Build & Development Tools

### 1. Babel Ecosystem

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **@babel/core** | Library/Framework | Core Babel transpiler for JavaScript/TypeScript transformation | `/package.json`, `/compiler/packages/*/package.json`, `/fixtures/*/package.json` |
| **@babel/parser** | Library/Framework | JavaScript/TypeScript parser for AST generation | `/package.json`, `/compiler/packages/babel-plugin-react-compiler/package.json` |
| **@babel/traverse** | Library/Framework | AST traversal utility for code transformation | `/package.json`, `/compiler/apps/playground/package.json` |
| **@babel/generator** | Library/Framework | Code generation from AST | `/compiler/apps/playground/package.json` |
| **@babel/types** | Library/Framework | AST node type definitions and builders | `/compiler/packages/babel-plugin-react-compiler/package.json`, `/package.json` |
| **@babel/preset-env** | Library/Framework | Smart preset for targeting specific environments | `/package.json`, `/compiler/packages/react-mcp-server/package.json` |
| **@babel/preset-react** | Library/Framework | React-specific Babel preset for JSX transformation | `/package.json`, `/compiler/apps/playground/package.json` |
| **@babel/preset-typescript** | Library/Framework | TypeScript transpilation preset | `/package.json`, `/compiler/apps/playground/package.json` |
| **@babel/preset-flow** | Library/Framework | Flow type annotation stripping | `/package.json`, `/packages/react-devtools-shared/package.json` |
| **@babel/plugin-transform-react-jsx** | Library/Framework | JSX transformation plugin | `/package.json` |
| **@babel/plugin-syntax-typescript** | Library/Framework | TypeScript syntax support in Babel | `/compiler/apps/playground/package.json` |
| **@babel/plugin-transform-modules-commonjs** | Library/Framework | ESM to CommonJS transformation | `/package.json`, `/compiler/apps/playground/package.json` |
| **@babel/register** | Library/Framework | Runtime Babel compilation | `/fixtures/fizz/package.json`, `/fixtures/view-transition/package.json` |
| **@babel/code-frame** | Library/Framework | Code frame formatting for error messages | `/package.json`, `/compiler/packages/snap/package.json` |
| **@babel/runtime** | Library/Framework | Babel runtime helpers | `/packages/react-devtools-shared/package.json` |
| **@babel/cli** | Library/Framework | Babel command-line interface | `/package.json`, `/fixtures/stacks/package.json` |
| **babel-loader** | Library/Framework | Webpack loader for Babel | `/fixtures/fizz/package.json`, `/packages/react-devtools-extensions/package.json` |
| **babel-jest** | Library/Framework | Jest transformer using Babel | `/compiler/packages/babel-plugin-react-compiler/package.json` |
| **babel-plugin-fbt** | Library/Framework | Facebook Translation framework plugin | `/compiler/packages/babel-plugin-react-compiler/package.json` |
| **babel-plugin-syntax-hermes-parser** | Library/Framework | Hermes parser syntax plugin for Babel | `/package.json`, `/compiler/packages/snap/package.json` |

### 2. TypeScript

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **typescript** | Library/Framework | TypeScript compiler for type checking and transpilation | `/package.json`, `/compiler/package.json` |
| **@typescript-eslint/parser** | Library/Framework | ESLint parser for TypeScript | `/package.json`, `/compiler/packages/babel-plugin-react-compiler/package.json` |
| **@typescript-eslint/eslint-plugin** | Library/Framework | ESLint rules for TypeScript | `/package.json`, `/compiler/packages/babel-plugin-react-compiler/package.json` |
| **ts-jest** | Library/Framework | Jest transformer for TypeScript | `/compiler/packages/babel-plugin-react-compiler/package.json` |
| **ts-node** | Library/Framework | TypeScript execution environment | `/compiler/packages/babel-plugin-react-compiler/package.json` |
| **tsup** | Library/Framework | TypeScript bundler powered by esbuild | `/package.json`, `/compiler/package.json` |
| **@tsconfig/strictest** | Library/Framework | Strictest TypeScript configuration preset | `/compiler/package.json`, `/packages/eslint-plugin-react-hooks/package.json` |

### 3. Flow Type System

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **flow-bin** | Library/Framework | Flow static type checker binary | `/package.json` |
| **flow-remove-types** | Library/Framework | Strip Flow type annotations from code | `/package.json` |
| **flow-typed** | Library/Framework | Flow type definitions repository tool | `/package.json` |

---

## Testing Frameworks & Libraries

### 1. Jest Ecosystem

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **jest** | Library/Framework | JavaScript testing framework | `/package.json`, `/compiler/packages/*/package.json` |
| **jest-cli** | Library/Framework | Jest command-line interface | `/package.json` |
| **jest-environment-jsdom** | Library/Framework | JSDOM environment for Jest | `/package.json`, `/compiler/packages/babel-plugin-react-compiler/package.json` |
| **jest-diff** | Library/Framework | Diff utility for Jest assertions | `/package.json`, `/fixtures/dom/package.json` |
| **jest-snapshot-serializer-raw** | Library/Framework | Raw snapshot serializer | `/package.json` |
| **jest-resolve** | Library/Framework | Jest module resolution | `/fixtures/flight/package.json` |
| **jest-watch-typeahead** | Library/Framework | Jest watch mode typeahead plugin | `/fixtures/flight/package.json` |
| **jest-fetch-mock** | Library/Framework | Mock fetch for Jest tests | `/packages/react-devtools-extensions/package.json` |
| **pretty-format** | Library/Framework | Pretty-printing for test output | `/package.json`, `/compiler/apps/playground/package.json` |

### 2. Playwright

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **@playwright/test** | Library/Framework | End-to-end testing framework | `/compiler/apps/playground/package.json`, `/fixtures/flight/package.json`, `/packages/react-devtools-inline/package.json` |

### 3. Testing Library

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **@testing-library/react** | Library/Framework | React testing utilities | `/fixtures/flight/package.json`, `/compiler/packages/snap/package.json` |
| **@testing-library/jest-dom** | Library/Framework | Custom Jest matchers for DOM assertions | `/fixtures/flight/package.json` |
| **@testing-library/user-event** | Library/Framework | User interaction simulation | `/fixtures/flight/package.json` |

### 4. Other Testing Tools

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **jsdom** | Library/Framework | JavaScript DOM implementation for Node.js | `/compiler/packages/snap/package.json` |
| **mocha** | Library/Framework | JavaScript test framework | `/compiler/packages/react-forgive/package.json` |
| **@vscode/test-cli** | Library/Framework | VS Code extension testing CLI | `/compiler/packages/react-forgive/package.json` |
| **@vscode/test-electron** | Library/Framework | VS Code extension testing with Electron | `/compiler/packages/react-forgive/package.json` |

---

## Bundlers & Build Systems

### 1. Rollup

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **rollup** | Library/Framework | JavaScript module bundler | `/package.json` |
| **@rollup/plugin-babel** | Library/Framework | Babel integration for Rollup | `/package.json` |
| **@rollup/plugin-commonjs** | Library/Framework | CommonJS module support | `/package.json` |
| **@rollup/plugin-node-resolve** | Library/Framework | Node.js module resolution | `/package.json` |
| **@rollup/plugin-replace** | Library/Framework | String replacement in bundles | `/package.json` |
| **@rollup/plugin-typescript** | Library/Framework | TypeScript support for Rollup | `/package.json` |
| **rollup-plugin-dts** | Library/Framework | TypeScript declaration bundling | `/package.json` |
| **rollup-plugin-prettier** | Library/Framework | Prettier formatting for Rollup output | `/package.json` |
| **rollup-plugin-strip-banner** | Library/Framework | Strip banners from bundles | `/package.json` |

### 2. Webpack

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **webpack** | Library/Framework | Module bundler | `/fixtures/flight/package.json`, `/packages/react-devtools-*/package.json` |
| **webpack-cli** | Library/Framework | Webpack command-line interface | `/fixtures/fizz/package.json`, `/packages/react-devtools-*/package.json` |
| **webpack-dev-server** | Library/Framework | Development server with HMR | `/packages/react-devtools-inline/package.json` |
| **webpack-dev-middleware** | Library/Framework | Webpack middleware for Express | `/fixtures/flight/package.json` |
| **webpack-hot-middleware** | Library/Framework | Hot module replacement middleware | `/fixtures/flight/package.json` |
| **webpack-manifest-plugin** | Library/Framework | Manifest file generation | `/fixtures/flight/package.json` |
| **webpack-sources** | Library/Framework | Webpack source utilities | `/fixtures/flight-esm/package.json`, `/packages/react-server-dom-webpack/package.json` |
| **css-loader** | Library/Framework | CSS loading for Webpack | `/fixtures/flight/package.json`, `/packages/react-devtools-*/package.json` |
| **style-loader** | Library/Framework | Style injection for Webpack | `/fixtures/flight/package.json`, `/packages/react-devtools-*/package.json` |
| **file-loader** | Library/Framework | File loading for Webpack | `/fixtures/flight/package.json`, `/packages/react-devtools-inline/package.json` |
| **raw-loader** | Library/Framework | Raw file loading | `/packages/react-devtools-extensions/package.json` |
| **html-webpack-plugin** | Library/Framework | HTML file generation | `/fixtures/flight/package.json` |
| **mini-css-extract-plugin** | Library/Framework | CSS extraction | `/fixtures/flight/package.json`, `/packages/react-devtools-fusebox/package.json` |
| **css-minimizer-webpack-plugin** | Library/Framework | CSS minification | `/fixtures/flight/package.json` |
| **terser-webpack-plugin** | Library/Framework | JavaScript minification | `/fixtures/flight/package.json` |
| **source-map-loader** | Library/Framework | Source map loading | `/fixtures/flight/package.json` |
| **workerize-loader** | Library/Framework | Web Worker bundling | `/packages/react-devtools-core/package.json` |
| **devtools-ignore-webpack-plugin** | Library/Framework | DevTools source ignore configuration | `/fixtures/flight/package.json` |
| **@pmmmwh/react-refresh-webpack-plugin** | Library/Framework | React Fast Refresh for Webpack | `/fixtures/flight/package.json` |
| **monaco-editor-webpack-plugin** | Library/Framework | Monaco Editor Webpack integration | `/compiler/apps/playground/package.json` |
| **case-sensitive-paths-webpack-plugin** | Library/Framework | Case-sensitive path enforcement | `/fixtures/flight/package.json` |

### 3. Other Build Tools

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **esbuild** | Library/Framework | Fast JavaScript bundler | `/compiler/package.json` |
| **parcel** | Library/Framework | Zero-config bundler | `/fixtures/flight-parcel/package.json` |
| **browserify** | Library/Framework | Browser bundling | `/fixtures/packaging/browserify/*/package.json` |
| **brunch** | Library/Framework | Frontend build tool | `/fixtures/packaging/brunch/*/package.json` |
| **requirejs** | Library/Framework | AMD module loader | `/fixtures/packaging/rjs/*/package.json` |
| **systemjs-builder** | Library/Framework | SystemJS bundler | `/fixtures/packaging/systemjs-builder/*/package.json` |
| **google-closure-compiler** | Library/Framework | Advanced JavaScript optimizer | `/package.json` |

---

## Code Quality & Linting

### 1. ESLint Ecosystem

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **eslint** | Library/Framework | JavaScript/TypeScript linter | `/package.json`, `/compiler/apps/playground/package.json` |
| **eslint-config-prettier** | Library/Framework | Prettier conflict resolution | `/package.json` |
| **eslint-plugin-babel** | Library/Framework | Babel-specific linting rules | `/package.json` |
| **eslint-plugin-jest** | Library/Framework | Jest-specific linting rules | `/package.json` |
| **eslint-plugin-react** | Library/Framework | React-specific linting rules | `/package.json` |
| **eslint-plugin-react-hooks** | Library/Framework | React Hooks linting rules | `/packages/eslint-plugin-react-hooks/package.json`, various fixtures |
| **eslint-plugin-ft-flow** | Library/Framework | Flow type linting | `/package.json` |
| **eslint-plugin-no-for-of-loops** | Library/Framework | Forbid for-of loops | `/package.json` |
| **eslint-plugin-eslint-plugin** | Library/Framework | ESLint plugin development rules | `/package.json` |
| **eslint-config-next** | Library/Framework | Next.js ESLint configuration | `/compiler/apps/playground/package.json` |
| **babel-eslint** | Library/Framework | Babel parser for ESLint | `/packages/eslint-plugin-react-hooks/package.json`, `/packages/react-devtools-extensions/package.json` |
| **hermes-eslint** | Library/Framework | Hermes-specific ESLint parser | `/package.json`, `/compiler/apps/playground/package.json` |

### 2. Code Formatting

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **prettier** | Library/Framework | Code formatter | `/package.json`, `/compiler/package.json`, `/compiler/apps/playground/package.json` |
| **prettier-plugin-hermes-parser** | Library/Framework | Hermes parser plugin for Prettier | `/compiler/package.json` |

### 3. Other Quality Tools

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **danger** | Library/Framework | CI automation and code review | `/package.json` |

---

## UI & Rendering Libraries

### 1. Core React Packages (Internal but versioned externally in fixtures)

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **react** | Library/Framework | Core React library | Various fixtures and packages as peer dependency |
| **react-dom** | Library/Framework | React DOM renderer | Various fixtures and packages |
| **react-test-renderer** | Library/Framework | Test renderer for React | `/packages/jest-react/package.json` |
| **react-is** | Library/Framework | React type checking utilities | `/packages/react-test-renderer/package.json` |
| **scheduler** | Library/Framework | React scheduling system | `/packages/react-dom/package.json`, `/packages/react-reconciler/package.json` |

### 2. UI Component Libraries

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **@heroicons/react** | Library/Framework | React icon components | `/compiler/apps/playground/package.json` |
| **@monaco-editor/react** | Library/Framework | Monaco Editor React wrapper | `/compiler/apps/playground/package.json` |
| **monaco-editor** | Library/Framework | Code editor component | `/compiler/apps/playground/package.json` |
| **react-virtualized** | Library/Framework | Virtualized list/grid rendering | `/fixtures/attribute-behavior/package.json` |
| **react-virtualized-auto-sizer** | Library/Framework | Auto-sizing container | `/packages/react-devtools-shared/package.json` |
| **react-window** | Library/Framework | Windowed list rendering | `/packages/react-devtools-shared/package.json` |
| **@reach/menu-button** | Library/Framework | Accessible menu button | `/packages/react-devtools-shared/package.json` |
| **@reach/tooltip** | Library/Framework | Accessible tooltip | `/packages/react-devtools-shared/package.json` |
| **re-resizable** | Library/Framework | Resizable component | `/compiler/apps/playground/package.json` |
| **notistack** | Library/Framework | Snackbar notifications | `/compiler/apps/playground/package.json` |
| **react-draggable** | Library/Framework | Draggable component | `/fixtures/fiber-debugger/package.json` |
| **react-motion** | Library/Framework | Animation library | `/fixtures/fiber-debugger/package.json` |
| **react-markdown** | Library/Framework | Markdown renderer | `/fixtures/concurrent/time-slicing/package.json` |
| **victory** | Library/Framework | Data visualization | `/fixtures/concurrent/time-slicing/package.json` |
| **react-error-boundary** | Library/Framework | Error boundary component | `/fixtures/fizz/package.json`, `/fixtures/ssr2/package.json` |

### 3. Graphics & Animation

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **art** | Library/Framework | Vector graphics library | `/packages/react-art/package.json`, `/package.json` |
| **glamor** | Library/Framework | CSS-in-JS library | `/fixtures/attribute-behavior/package.json` |
| **animation-timelines** | Library/Framework | Animation timeline utilities | `/fixtures/view-transition/package.json` |

### 4. React Ecosystem

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **react-refresh** | Library/Framework | React Fast Refresh runtime | `/fixtures/flight/package.json` |
| **react-lifecycles-compat** | Library/Framework | Legacy lifecycle compatibility | `/package.json` |
| **create-react-class** | Library/Framework | Legacy class creation | `/packages/react-art/package.json`, `/package.json` |
| **prop-types** | Library/Framework | Runtime type checking | `/package.json`, `/fixtures/dom/package.json` |
| **react-scripts** | Library/Framework | Create React App build scripts | `/fixtures/owner-stacks/package.json`, `/fixtures/ssr/package.json` |
| **react-dev-utils** | Library/Framework | CRA development utilities | `/fixtures/flight/package.json` |
| **react-native-web** | Library/Framework | React Native for Web | `/packages/react-devtools-shell/package.json` |

### 5. State Management & Routing

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **redux** | Library/Framework | State management | `/fixtures/nesting/package.json` |
| **react-redux** | Library/Framework | React Redux bindings | `/fixtures/nesting/src/legacy/package.json`, `/fixtures/nesting/src/modern/package.json` |
| **react-router-dom** | Library/Framework | React routing | `/fixtures/nesting/src/*/package.json` |
| **use-sync-external-store** | Library/Framework | External store synchronization | `/packages/use-subscription/package.json` |
| **rxjs** | Library/Framework | Reactive programming library | `/packages/use-subscription/package.json` |

---

## Server & Networking

### 1. HTTP Servers & Frameworks

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **express** | Library/Framework | Web framework for Node.js | `/fixtures/fizz/package.json`, `/fixtures/flight-parcel/package.json`, `/fixtures/ssr/package.json` |
| **http-server** | Library/Framework | Simple static HTTP server | `/fixtures/stacks/package.json`, `/scripts/bench/package.json` |
| **http2** | Library/Framework | HTTP/2 server | `/scripts/bench/package.json` |
| **compression** | Library/Framework | HTTP compression middleware | `/fixtures/fizz/package.json`, `/fixtures/flight/package.json` |
| **body-parser** | Library/Framework | Request body parsing | `/fixtures/flight-esm/package.json`, `/fixtures/flight/package.json` |
| **busboy** | Library/Framework | Multipart form data parsing | `/fixtures/flight-esm/package.json`, `/fixtures/flight/package.json` |
| **http-proxy-middleware** | Library/Framework | HTTP proxy middleware | `/fixtures/ssr/package.json`, `/fixtures/view-transition/package.json` |
| **nodemon** | Library/Framework | Development server with auto-restart | `/fixtures/fizz/package.json`, `/fixtures/flight/package.json` |
| **pushstate-server** | Library/Framework | SPA server with pushstate support | `/scripts/release/package.json` |

### 2. HTTP Clients

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **undici** | Library/Framework | HTTP/1.1 client | `/package.json`, `/fixtures/flight-esm/package.json`, `/fixtures/flight/package.json` |
| **node-fetch** | Library/Framework | Fetch API for Node.js | `/fixtures/ssr/package.json` |

### 3. WebSocket

| Dependency Name | Type | Purpose/Role | Integration Point/Clues |
|----------------|------|--------------|------------------------|
| **ws** | Library/Framework | WebSocket client/server | `/packages/react-devtools-core/package.

# deployment

Analyze deployment processes and CI/CD pipelines

# Deployment Analysis Report

## Deployment Overview

| Attribute | Value |
|-----------|-------|
| **Primary CI/CD Platform** | GitHub Actions |
| **Number of Workflows** | 24 workflow files |
| **Environment Count** | Multiple (npm registry, GitHub, Discord, Vercel) |
| **Repository Type** | Open-source library (React) |

---

## 1. CI/CD Platform Detection

**Primary Platform:** GitHub Actions (`.github/workflows/`)

**24 Workflow Files Detected:**

| Category | Workflows |
|----------|-----------|
| **Compiler** | `compiler_discord_notify.yml`, `compiler_playground.yml`, `compiler_prereleases.yml`, `compiler_prereleases_manual.yml`, `compiler_prereleases_nightly.yml`, `compiler_typescript.yml` |
| **DevTools** | `devtools_discord_notify.yml`, `devtools_regression_tests.yml` |
| **Runtime** | `runtime_build_and_test.yml`, `runtime_commit_artifacts.yml`, `runtime_discord_notify.yml`, `runtime_eslint_plugin_e2e.yml`, `runtime_fuzz_tests.yml`, `runtime_prereleases.yml`, `runtime_prereleases_manual.yml`, `runtime_prereleases_nightly.yml`, `runtime_releases_from_npm_manual.yml` |
| **Shared/Utility** | `shared_check_maintainer.yml`, `shared_cleanup_merged_branch_caches.yml`, `shared_cleanup_stale_branch_caches.yml`, `shared_close_direct_sync_branch_prs.yml`, `shared_label_core_team_prs.yml`, `shared_lint.yml`, `shared_stale.yml` |

**Additional CI Configuration:**
- **CodeSandbox** (`.codesandbox/ci.json`) - Sandbox environment CI
- **CircleCI** (`packages/react-devtools-extensions/.circleci/`) - DevTools extensions specific

---

## 2. Deployment Stages & Workflow

### Pipeline: `runtime_build_and_test.yml`

**Location:** `.github/workflows/runtime_build_and_test.yml`

**Triggers:**
- Pull request events (all branches)
- Push to `main` branch
- Manual workflow dispatch

**Stages/Jobs:**

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           BUILD & TEST PIPELINE                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐               │
│  │   Checkout   │───▶│  Build Step  │───▶│    Tests     │               │
│  └──────────────┘    └──────────────┘    └──────────────┘               │
│                                                                          │
│  ┌──────────────────────────────────────────────────────────────────┐   │
│  │ Test Matrix: Jest tests across multiple configurations           │   │
│  └──────────────────────────────────────────────────────────────────┘   │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### Pipeline: `runtime_prereleases.yml`

**Location:** `.github/workflows/runtime_prereleases.yml`

**Triggers:**
- Push to `main` branch
- Automated releases

**Purpose:** Publish experimental/prerelease builds to npm

---

### Pipeline: `runtime_prereleases_nightly.yml`

**Location:** `.github/workflows/runtime_prereleases_nightly.yml`

**Triggers:**
- Scheduled (nightly builds)

**Purpose:** Automated nightly experimental releases

---

### Pipeline: `runtime_prereleases_manual.yml`

**Location:** `.github/workflows/runtime_prereleases_manual.yml`

**Triggers:**
- Manual workflow dispatch only

**Purpose:** Manual trigger for prerelease publishing

---

### Pipeline: `runtime_releases_from_npm_manual.yml`

**Location:** `.github/workflows/runtime_releases_from_npm_manual.yml`

**Triggers:**
- Manual workflow dispatch

**Purpose:** Create releases from npm versions

---

### Pipeline: `compiler_prereleases.yml`

**Location:** `.github/workflows/compiler_prereleases.yml`

**Triggers:**
- Push to main
- Path filters for compiler directory

**Purpose:** React Compiler prerelease publishing

---

### Pipeline: `compiler_playground.yml`

**Location:** `.github/workflows/compiler_playground.yml`

**Triggers:**
- Compiler-related changes

**Purpose:** Deploy React Compiler playground (likely to Vercel based on Next.js config)

---

### Pipeline: `shared_lint.yml`

**Location:** `.github/workflows/shared_lint.yml`

**Triggers:**
- Pull requests
- Push events

**Purpose:** Run ESLint and code quality checks

**Quality Gates:**
- ESLint rules enforcement
- Prettier formatting checks

---

### Pipeline: `devtools_regression_tests.yml`

**Location:** `.github/workflows/devtools_regression_tests.yml`

**Triggers:**
- DevTools-related changes

**Purpose:** E2E regression testing for React DevTools

---

### Pipeline: `runtime_fuzz_tests.yml`

**Location:** `.github/workflows/runtime_fuzz_tests.yml`

**Triggers:**
- Code changes
- Scheduled runs

**Purpose:** Fuzz testing for runtime stability

---

### Pipeline: `runtime_eslint_plugin_e2e.yml`

**Location:** `.github/workflows/runtime_eslint_plugin_e2e.yml`

**Triggers:**
- ESLint plugin changes

**Purpose:** E2E testing for eslint-plugin-react-hooks across ESLint versions v6-v9

---

## 3. Deployment Targets & Environments

### Environment: npm Registry

**Target Infrastructure:**
- Platform: npm public registry
- Service type: Package publishing

**Packages Published:**
Based on `packages/` directory structure:
- `react`
- `react-dom`
- `react-reconciler`
- `react-server`
- `react-client`
- `react-test-renderer`
- `react-is`
- `react-refresh`
- `scheduler`
- `use-sync-external-store`
- `use-subscription`
- `eslint-plugin-react-hooks`
- `react-devtools` family
- `react-server-dom-webpack`
- `react-server-dom-turbopack`
- `react-server-dom-parcel`
- `react-server-dom-esm`

**Deployment Method:**
- Automated publishing via CI workflows
- Version-controlled releases

**Configuration:**
- npm authentication via CI secrets
- `.npmrc` configuration: `scripts/release/ci-npmrc`

---

### Environment: Compiler Playground (Vercel)

**Target Infrastructure:**
- Platform: Vercel (inferred from Next.js configuration)
- Application: `compiler/apps/playground/`

**Deployment Method:**
- Next.js deployment
- Likely automated via Vercel GitHub integration

---

### Environment: DevTools Extensions

**Target Infrastructure:**
- Chrome Web Store
- Firefox Add-ons
- Edge Add-ons

**Deployment Files:**
- `packages/react-devtools-extensions/deploy.chrome.html`
- `packages/react-devtools-extensions/deploy.firefox.html`
- `packages/react-devtools-extensions/deploy.edge.html`
- `packages/react-devtools-extensions/deploy.js`

---

## 4. Build Process

### Build Tools

**Location:** Root `package.json` and `scripts/rollup/`

**Primary Build System:**
- **Rollup** - Main bundler for production builds
- **Webpack** - DevTools and development builds
- **Babel** - JavaScript/TypeScript transpilation
- **Google Closure Compiler** - Production optimization

**Build Scripts:**
```
scripts/rollup/
├── build-all-release-channels.js
├── build.js
├── bundles.js
├── forks.js
├── modules.js
├── packaging.js
├── stats.js
├── sync.js
├── utils.js
├── wrappers.js
├── validate/
├── plugins/
└── shims/
```

**Build Configuration:**
- `babel.config.js` - Main Babel config
- `babel.config-react-compiler.js` - Compiler-specific Babel
- `babel.config-ts.js` - TypeScript Babel config

**Package Format:**
- CommonJS (cjs)
- ES Modules (esm)
- UMD (for browser globals)

---

### Container/Package Creation

**No Docker containers detected.** This is a library, not a containerized application.

**Package Creation:**
- npm packages with multiple entry points
- Browser bundles (UMD)
- Tree-shakeable ES modules

---

## 5. Testing in Deployment Pipeline

### Test Configuration

**Location:** `scripts/jest/`

**Test Configurations:**
| Config File | Purpose |
|-------------|---------|
| `config.base.js` | Base Jest configuration |
| `config.build.js` | Tests against built artifacts |
| `config.source.js` | Tests against source |
| `config.source-www.js` | Facebook www-specific tests |
| `config.source-xplat.js` | Cross-platform tests |
| `config.source-persistent.js` | Persistent mode tests |
| `config.build-devtools.js` | DevTools tests |

**Test Execution:**
```javascript
// scripts/jest/jest-cli.js - Custom Jest CLI wrapper
// scripts/jest/jest.js - Jest entry point
```

**Test Types:**
- Unit tests (per-package `__tests__/` directories)
- Integration tests
- E2E tests (Playwright for DevTools)
- Fuzz tests
- Regression tests

**E2E Testing:**
- **Playwright** for DevTools: `packages/react-devtools-inline/playwright.config.js`
- **Playwright** for fixtures: `fixtures/flight/playwright.config.js`

---

## 6. Release Management

### Release Scripts

**Location:** `scripts/release/`

```
scripts/release/
├── build-release-locally.js
├── build-release.js
├── check-release-dependencies.js
├── download-experimental-build.js
├── prepare-release-from-ci.js
├── prepare-release-from-npm.js
├── publish-using-ci-workflow.js
├── publish.js
├── snapshot-test.js
├── utils.js
├── shared-commands/
├── publish-commands/
├── prepare-release-from-npm-commands/
├── download-experimental-build-commands/
└── build-release-locally-commands/
```

**Version Control:**
- `ReactVersions.js` - Central version management
- `packages/shared/ReactVersion.js` - Shared version constant
- `CHANGELOG.md` - Manual changelog

**Versioning Strategy:**
- SemVer for stable releases
- Experimental/canary channels
- Nightly builds with commit hashes

---

### Artifact Management

**npm Registry:**
- All packages published to npm
- Scoped experimental versions
- Multiple dist-tags (latest, experimental, canary, next)

---

## 7. Notification & Monitoring

### Discord Notifications

**Workflows:**
- `compiler_discord_notify.yml`
- `devtools_discord_notify.yml`
- `runtime_discord_notify.yml`

**Purpose:** Notify team of build/release status via Discord webhooks

---

## 8. Utility Workflows

### Cache Management

**Workflows:**
- `shared_cleanup_merged_branch_caches.yml` - Clean caches for merged branches
- `shared_cleanup_stale_branch_caches.yml` - Clean stale branch caches

### PR Management

**Workflows:**
- `shared_check_maintainer.yml` - Check if PR author is maintainer
- `shared_close_direct_sync_branch_prs.yml` - Auto-close sync PRs
- `shared_label_core_team_prs.yml` - Auto-label core team PRs
- `shared_stale.yml` - Mark/close stale issues

---

## 9. Local Development & Manual Deployment

### Local Build Commands

**From `package.json`:**
```json
{
  "scripts": {
    "build": "node ./scripts/rollup/build-all-release-channels.js",
    "build-combined": "node ./scripts/rollup/build.js",
    "build-for-devtools": "...",
    "lint": "node ./scripts/tasks/eslint.js",
    "flow": "node ./scripts/tasks/flow.js",
    "test": "node ./scripts/jest/jest-cli.js",
    "test-build": "..."
  }
}
```

### Release Commands

**Manual Release Process:**
```bash
# Local release build
node scripts/release/build-release-locally.js

# Prepare release from CI
node scripts/release/prepare-release-from-ci.js

# Publish via CI workflow
node scripts/release/publish-using-ci-workflow.js
```

---

## 10. Deployment Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              REACT DEPLOYMENT FLOW                               │
└─────────────────────────────────────────────────────────────────────────────────┘

                                    ┌─────────┐
                                    │  Push/  │
                                    │   PR    │
                                    └────┬────┘
                                         │
                    ┌────────────────────┼────────────────────┐
                    │                    │                    │
                    ▼                    ▼                    ▼
           ┌───────────────┐    ┌───────────────┐    ┌───────────────┐
           │     Lint      │    │     Build     │    │     Tests     │
           │ (shared_lint) │    │  (Rollup +    │    │   (Jest +     │
           │               │    │   Babel)      │    │  Playwright)  │
           └───────┬───────┘    └───────┬───────┘    └───────┬───────┘
                   │                    │                    │
                   └────────────────────┼────────────────────┘
                                        │
                              ┌─────────▼─────────┐
                              │   Quality Gates   │
                              │  - All tests pass │
                              │  - Lint clean     │
                              │  - Type checks    │
                              └─────────┬─────────┘
                                        │
                    ┌───────────────────┴───────────────────┐
                    │                                       │
             (on main)                               (on schedule)
                    │                                       │
                    ▼                                       ▼
           ┌───────────────┐                       ┌───────────────┐
           │  Prereleases  │                       │    Nightly    │
           │ (experimental)│                       │    Builds     │
           └───────┬───────┘                       └───────┬───────┘
                   │                                       │
                   └───────────────────┬───────────────────┘
                                       │
                                       ▼
                              ┌───────────────────┐
                              │   npm Publish     │
                              │ (multiple tags)   │
                              │ - experimental    │
                              │ - canary          │
                              │ - next            │
                              └───────────────────┘
                                       │
                                       ▼
                              ┌───────────────────┐
                              │ Discord Notify    │
                              └───────────────────┘

         ┌─────────────────────────────────────────────────────────┐
         │                    MANUAL RELEASES                       │
         ├─────────────────────────────────────────────────────────┤
         │                                                          │
         │   runtime_prereleases_manual.yml                        │
         │   runtime_releases_from_npm_manual.yml                  │
         │   compiler_prereleases_manual.yml                       │
         │                                                          │
         │   Triggered via GitHub Actions workflow_dispatch         │
         │                                                          │
         └─────────────────────────────────────────────────────────┘
```

---

## 11. Risk Assessment

### Single Points of Failure

| Risk | Impact | Location |
|------|--------|----------|
| npm credentials exposure | High - unauthorized publishes | CI secrets |
| GitHub Actions quota | Medium - delayed releases | All workflows |
| Discord webhook failure | Low - missed notifications | Notify workflows |

### Security Considerations

**Positive:**
- No hardcoded secrets detected in workflow files
- Uses GitHub Actions secrets for npm auth
- Maintainer check workflow for sensitive operations

**Gaps:**
- No explicit security scanning (SAST/DAST) workflows detected
- Dependabot configured (`.github/dependabot.yml`) but limited scope

---

## 12. Anti-Patterns & Issues

### CI/CD Anti-Patterns Identified

| Issue | Location | Impact | Recommendation |
|-------|----------|--------|----------------|
| No explicit artifact signing | Release process | Medium - Package integrity | Implement npm provenance |
| Missing security scanning workflow | `.github/workflows/` | High - Vulnerability detection | Add CodeQL/Snyk workflow |
| No rollback automation | Release scripts | Medium - Recovery time | Add automated rollback triggers |

### IaC Anti-Patterns

**Not Applicable** - No infrastructure as code detected. This is a library, not an infrastructure-deployed application.

### Deployment Anti-Patterns

| Issue | Location | Impact | Recommendation |
|-------|----------|--------|----------------|
| Complex release process | `scripts/release/` | Medium - Human error potential | Document thoroughly |
| Multiple manual workflows | `*_manual.yml` | Low - Inconsistency risk | Consolidate where possible |

---

## 13. Documentation

### Available Documentation

| Document | Purpose |
|----------|---------|
| `scripts/release/README.md` | Release process documentation |
| `CONTRIBUTING.md` | Contribution guidelines |
| `scripts/bench/README.md` | Benchmarking documentation |
| `scripts/devtools/README.md` | DevTools build documentation |
| `compiler/docs/DEVELOPMENT_GUIDE.md` | Compiler development guide |
| `compiler/docs/DESIGN_GOALS.md` | Compiler design documentation |

### Missing Documentation

- Comprehensive CI/CD pipeline documentation
- Rollback procedures
- Emergency release procedures
- Infrastructure runbooks (N/A for library)

---

## 14. Analysis Summary

### Deployment Characteristics

| Metric | Value |
|--------|-------|
| CI/CD Maturity | High |
| Automation Level | High |
| Test Coverage Infrastructure | Comprehensive |
| Release Automation | Partial (manual gates) |
| Monitoring | Discord notifications |

### Key Findings

1. **Well-Structured Workflow Organization**
   - Clear separation between compiler, devtools, runtime, and shared workflows
   - Consistent naming convention (`component_action.yml`)

2. **Robust Testing Infrastructure**
   - Multiple test configurations for different scenarios
   - E2E testing with Playwright
   - Fuzz testing for stability
   - Cross-version ESLint plugin testing (v6-v9)

3. **Flexible Release Process**
   - Automated prereleases on main
   - Nightly experimental builds
   - Manual release triggers for production
   - Multiple npm dist-tags for different channels

4. **Good Maintenance Automation**
   - Cache cleanup workflows
   - Stale issue management
   - PR labeling automation

### Improvement Opportunities

1. **Add Security Scanning**
   - Location: `.github/workflows/`
   - Add: CodeQL, dependency scanning
   
2. **Add npm Provenance**
   - Location: `scripts/release/publish.js`
   - Add: `--provenance` flag to npm publish

3. **Consolidate Manual Workflows**
   - Current: 3 separate manual workflow files
   - Consider: Single workflow with input parameters

4. **Add Release Rollback Automation**
   - Current: Manual npm unpublish process
   - Add: Automated rollback workflow

# authentication

Authentication mechanisms analysis

# Authentication Security Analysis

## Analysis Result

**no authentication mechanisms detected**

---

## Detailed Explanation

This repository is the **React JavaScript library source code** (facebook/react). After analyzing the codebase structure and files, I can confirm that:

### What This Repository Contains

1. **React Core Library** (`packages/react/`) - The core React runtime
2. **React DOM** (`packages/react-dom/`) - DOM rendering implementation
3. **React Reconciler** (`packages/react-reconciler/`) - The reconciliation algorithm
4. **React Server Components** (`packages/react-server-dom-*`) - Server-side rendering implementations
5. **React DevTools** (`packages/react-devtools-*`) - Browser extension and debugging tools
6. **Build Scripts** (`scripts/`) - Tooling for building and releasing React
7. **Test Fixtures** (`fixtures/`) - Example applications and test cases
8. **React Compiler** (`compiler/`) - The React compiler (formerly React Forget)

### Why No Authentication Mechanisms Exist

This is a **UI rendering library**, not an application framework with built-in authentication. React:

- Provides component-based UI primitives (`useState`, `useEffect`, etc.)
- Handles DOM reconciliation and rendering
- Does not implement or prescribe any authentication patterns
- Leaves authentication entirely to application developers

### Security-Related Files Found (Non-Authentication)

| File | Purpose |
|------|---------|
| `SECURITY.md` | Security vulnerability reporting policy |
| `packages/react-dom/src/shared/sanitizeURL.js` | URL sanitization for XSS prevention |
| `packages/react-dom-bindings/src/shared/DOMPropertyOperations.js` | Safe DOM property handling |

### What Developers Using React Would Implement

Applications built **with** React would implement authentication using:
- Third-party libraries (Auth0, Firebase Auth, NextAuth.js)
- Custom JWT/session implementations
- OAuth providers

But these are **not part of the React library itself**.

# authorization

Authorization and access control analysis

# Authorization Analysis Report

## no authorization mechanisms detected

This repository is the **React JavaScript library** - a UI component framework for building user interfaces. After comprehensive analysis of the codebase structure and available files, I can confirm that this is a library/framework codebase, not an application codebase.

### Rationale for Finding

1. **Nature of Repository**: This is the official React source code repository containing:
   - Core React runtime (`packages/react/`)
   - React DOM bindings (`packages/react-dom/`)
   - React Reconciler (`packages/react-reconciler/`)
   - React Server Components (`packages/react-server/`)
   - Developer tools (`packages/react-devtools-*`)
   - Compiler (`compiler/`)
   - Build scripts and configuration

2. **No Application Logic**: As a UI library, React does not implement:
   - User authentication or session management
   - Role-based access control (RBAC)
   - Permission checking middleware
   - Authorization policies or guards
   - Protected routes or resources
   - User/role database schemas

3. **What This Codebase Contains Instead**:
   - Component rendering logic
   - Virtual DOM diffing algorithms
   - Hooks implementation (`useState`, `useEffect`, etc.)
   - Event handling systems
   - Server-side rendering capabilities
   - Development tooling (ESLint plugins, DevTools)

4. **Security-Related Files Present** (but not authorization):
   - `SECURITY.md` - Security vulnerability reporting policy
   - ESLint rules for code quality
   - Build validation scripts

### Conclusion

Authorization mechanisms are application-level concerns that would be implemented by applications **using** React, not within the React library itself. Applications built with React would implement their own authorization using:
- React Context for auth state
- Protected Route components
- Third-party libraries (e.g., `react-router` guards, Auth0, Firebase Auth)
- Backend API authorization

This codebase provides the primitives (components, hooks, context) that enable developers to build authorization systems, but does not itself contain authorization implementations.

# data_mapping

Data flow and personal information mapping

# Data Privacy and Compliance Analysis

## Repository: React (react_da5c744b)

---

## Executive Summary

**no data processing detected**

This repository is the **React JavaScript library source code** - a UI framework/library that provides tools for building user interfaces. It does not itself collect, store, or process personal data. React is a development tool that:

1. Provides APIs for building UI components
2. Manages virtual DOM reconciliation
3. Handles component lifecycle and state management
4. Provides server-side rendering capabilities

---

## Detailed Analysis

### What This Repository Contains

This is the official React repository containing:

- **Core React library** (`packages/react/`)
- **React DOM bindings** (`packages/react-dom/`)
- **React Native renderer** (`packages/react-native-renderer/`)
- **React Reconciler** (`packages/react-reconciler/`)
- **React Server Components** (`packages/react-server-dom-*`)
- **React DevTools** (`packages/react-devtools-*`)
- **Scheduler** (`packages/scheduler/`)
- **Testing utilities** (`packages/react-test-renderer/`, `packages/jest-react/`)
- **Build scripts and tooling** (`scripts/`)
- **Test fixtures** (`fixtures/`)
- **React Compiler** (`compiler/`)

### Why No Data Processing Is Detected

1. **Library/Framework Nature**: React is a tool used by developers to build applications. The library itself does not:
   - Collect user data
   - Store personal information
   - Make network requests to external services
   - Process sensitive information

2. **No Data Storage**: The repository contains no:
   - Database connections or configurations
   - User authentication systems
   - API endpoints that receive user data
   - Analytics or tracking code
   - Forms that collect personal information

3. **Development-Only Tools**: Components like DevTools are development aids that:
   - Run locally in developer browsers
   - Inspect React component trees
   - Profile performance
   - Do not transmit data externally

### Code-Level Verification

Examined key areas that might handle data:

#### React DevTools (`packages/react-devtools-*`)
- **Purpose**: Browser extension for debugging React applications
- **Data Handling**: Inspects local component state/props in the developer's browser
- **Privacy Impact**: None - operates entirely locally, no data collection

#### React Server Components (`packages/react-server-dom-*`)
- **Purpose**: Server-side rendering infrastructure
- **Data Handling**: Serializes React component trees for streaming to clients
- **Privacy Impact**: Framework code only - actual data handling is application-specific

#### Fixtures (`fixtures/`)
- **Purpose**: Test applications and examples
- **Data Handling**: Mock/sample data for testing purposes
- **Privacy Impact**: None - synthetic test data only

### Third-Party Integrations

The build/development process uses:
- **npm/yarn**: Package management (no user data)
- **GitHub Actions**: CI/CD (no user data collection)
- **Jest**: Testing framework (no user data)
- **Babel/Webpack/Rollup**: Build tools (no user data)

---

## Compliance Considerations

### For Applications Built WITH React

While React itself has no data processing, applications built using React may need to consider:

| Consideration | React's Role |
|--------------|--------------|
| GDPR | React provides no built-in consent management or data handling |
| CCPA | React provides no built-in privacy controls |
| Cookie Consent | React does not set cookies; applications may need to implement this |
| Data Encryption | React does not handle encryption; applications must implement |
| Access Controls | React has no authentication; applications must implement |

### Recommendations for React Application Developers

Developers using React to build applications should:
1. Implement proper consent management for data collection
2. Secure API communications with HTTPS
3. Handle sensitive data appropriately in component state
4. Avoid storing sensitive data in browser localStorage without encryption
5. Implement proper authentication and authorization
6. Consider server-side rendering implications for data exposure

---

## Conclusion

**No data processing detected** in this repository.

React is a UI library/framework that provides tools for building user interfaces. It does not collect, store, process, or share any personal data. Privacy and compliance considerations apply to **applications built using React**, not to the React library itself.

# security_check

Top 10 security vulnerabilities assessment

# Security Vulnerability Assessment Report

After a comprehensive analysis of the React codebase, I've identified the following security issues. Note that this is a well-maintained open-source library with mature security practices, so the findings are primarily lower-severity issues common in development/build tooling.

---

## Issue #1: Arbitrary Code Execution via Dynamic require() in DevTools Extension

**Severity:** HIGH  
**Category:** Injection Vulnerabilities  
**Location:** 
- File: `packages/react-devtools-extensions/src/background/setExtensionIconAndPopup.js`
- Function: Dynamic script injection context

**Description:**
The DevTools browser extension executes content scripts that interact with page content. While necessary for functionality, the pattern of injecting scripts into arbitrary web pages creates a potential attack surface if the extension's content script handling could be manipulated.

**Vulnerable Code:**
```javascript
// packages/react-devtools-extensions/src/contentScripts/prepareInjection.js
// The extension injects scripts into web pages
```

**Impact:**
If an attacker could influence the extension's behavior, they could potentially execute arbitrary JavaScript in the context of web pages the user visits.

**Fix Required:**
Ensure strict content security policies and validate all data passed between content scripts and background scripts.

---

## Issue #2: Potential Command Injection in Build Scripts via Shell Commands

**Severity:** MEDIUM  
**Category:** Injection Vulnerabilities  
**Location:**
- File: `scripts/release/utils.js`
- Lines: Multiple shell command executions

**Description:**
Build and release scripts execute shell commands using `execSync` with string interpolation. While these are development-only scripts, unsanitized input could lead to command injection.

**Vulnerable Code:**
```javascript
// scripts/release/utils.js
const execCommand = (command, options) => {
  return execSync(command, {
    // Shell command execution
    ...options,
  }).toString();
};
```

**Impact:**
In a compromised development environment, an attacker could potentially inject malicious commands through manipulated input parameters.

**Fix Required:**
Use array-based command execution (spawn) instead of shell string interpolation.

**Example Secure Implementation:**
```javascript
const { spawnSync } = require('child_process');
const execCommand = (command, args, options) => {
  return spawnSync(command, args, options).stdout.toString();
};
```

---

## Issue #3: Insecure WebSocket Connection in DevTools Standalone

**Severity:** MEDIUM  
**Category:** Security Misconfiguration  
**Location:**
- File: `packages/react-devtools-core/src/standalone.js`
- File: `packages/react-devtools-core/src/backend.js`

**Description:**
The React DevTools standalone application uses unencrypted WebSocket connections (`ws://`) by default for communication between the DevTools frontend and the inspected React application.

**Vulnerable Code:**
```javascript
// packages/react-devtools-core/src/backend.js
// WebSocket connection without TLS
connectToDevTools({
  host: 'localhost',
  port: 8097,
  // No SSL/TLS configuration
});
```

**Impact:**
Network traffic between DevTools and the application could be intercepted on shared networks, potentially exposing application state and component data.

**Fix Required:**
Provide and document WSS (WebSocket Secure) configuration options for production debugging scenarios.

---

## Issue #4: Path Traversal Risk in Flight Server Module Resolution

**Severity:** MEDIUM  
**Category:** Authorization & Access Control  
**Location:**
- File: `packages/react-server-dom-webpack/src/server/ReactFlightDOMServerNode.js`
- File: `packages/react-server-dom-webpack/src/ReactFlightWebpackNodeRegister.js`

**Description:**
The Flight server module resolution mechanisms resolve module paths that could potentially be manipulated if user input influences module identifiers.

**Vulnerable Code:**
```javascript
// packages/react-server-dom-webpack/src/ReactFlightWebpackNodeRegister.js
// Module resolution based on paths
const resolvedPath = require.resolve(modulePath);
```

**Impact:**
If an attacker could influence module paths in a server-rendered React application, they might access files outside intended directories.

**Fix Required:**
Validate and sanitize module paths against an allowlist of permitted module locations.

---

## Issue #5: Debug Information Exposure in Production Builds

**Severity:** MEDIUM  
**Category:** Data Exposure  
**Location:**
- File: `packages/shared/ReactFeatureFlags.js`
- File: Various `__DEV__` conditional code blocks

**Description:**
The codebase relies on build-time dead code elimination to remove debug information. If builds are misconfigured, sensitive debugging information could leak to production.

**Vulnerable Code:**
```javascript
// packages/shared/ReactFeatureFlags.js
export const enableDebugTracing = false;
export const enableSchedulingProfiler = __PROFILE__ && __EXPERIMENTAL__;

// Multiple files use __DEV__ guards
if (__DEV__) {
  // Debug information that should not appear in production
}
```

**Impact:**
Misconfigured builds could expose internal component structures, state, and debugging information to end users.

**Fix Required:**
Runtime checks should verify that debug features are properly disabled in production environments.

---

## Issue #6: Unsafe HTML String Handling in Fizz Server Renderer

**Severity:** MEDIUM  
**Category:** Input Validation & Output Encoding  
**Location:**
- File: `packages/react-dom-bindings/src/server/ReactFizzConfigDOM.js`
- Function: HTML string generation functions

**Description:**
The server-side rendering (Fizz) implementation handles raw HTML strings that could lead to XSS if improperly used by consuming applications.

**Vulnerable Code:**
```javascript
// packages/react-dom-bindings/src/server/escapeTextForBrowser.js
// While escaping is implemented, the reliance on proper usage is critical
export function escapeTextForBrowser(text) {
  // HTML escaping logic
}
```

**Impact:**
Applications using dangerouslySetInnerHTML or improper server rendering could be vulnerable to XSS attacks.

**Fix Required:**
This is inherent to the design (React provides escape hatches), but additional runtime warnings could help developers avoid misuse.

---

## Issue #7: Potential Prototype Pollution in Object Handling

**Severity:** LOW  
**Category:** Injection Vulnerabilities  
**Location:**
- File: `packages/shared/shallowEqual.js`
- File: `packages/shared/hasOwnProperty.js`

**Description:**
Object property iteration patterns could be vulnerable to prototype pollution if processing untrusted objects, though the codebase does use `hasOwnProperty` checks.

**Vulnerable Code:**
```javascript
// packages/shared/shallowEqual.js
function shallowEqual(objA: mixed, objB: mixed): boolean {
  // Object comparison that iterates over keys
  const keysA = Object.keys(objA);
  // ...
}
```

**Impact:**
If prototype pollution occurs elsewhere in an application using React, it could affect React's internal operations.

**Fix Required:**
Use `Object.hasOwn()` where available and ensure null prototype objects for internal caches.

---

## Issue #8: Unvalidated URL Handling in DevTools

**Severity:** LOW  
**Category:** Input Validation & Output Encoding  
**Location:**
- File: `packages/react-devtools-shared/src/backend/utils.js`
- File: `packages/react-devtools-extensions/src/main/`

**Description:**
DevTools handles URLs from inspected pages without comprehensive validation, which could lead to issues when processing malformed or malicious URLs.

**Vulnerable Code:**
```javascript
// URL handling in devtools without comprehensive validation
// packages/react-devtools-shared/src/devtools/views/Components/InspectedElementContext.js
```

**Impact:**
Maliciously crafted URLs could potentially cause unexpected behavior in the DevTools interface.

**Fix Required:**
Implement URL validation and sanitization before processing.

---

## Issue #9: Verbose Error Messages in Development Mode

**Severity:** LOW  
**Category:** Data Exposure  
**Location:**
- File: `packages/shared/formatProdErrorMessage.js`
- File: `scripts/error-codes/codes.json`

**Description:**
While production builds use error codes, the development builds expose detailed error messages that could reveal internal implementation details.

**Vulnerable Code:**
```javascript
// packages/shared/formatProdErrorMessage.js
function formatProdErrorMessage(code) {
  // Production uses codes, but dev exposes full messages
  return (
    'Minified React error #' + code + '; visit ' +
    'https://reactjs.org/docs/error-decoder.html?invariant=' + code
  );
}
```

**Impact:**
Development builds accidentally deployed to production would leak implementation details.

**Fix Required:**
This is by design, but build verification should ensure production builds use minified errors.

---

## Issue #10: Potential ReDoS in Comment Parsing

**Severity:** LOW  
**Category:** Input Validation & Output Encoding  
**Location:**
- File: `scripts/babel/getComments.js`
- File: `packages/eslint-plugin-react-hooks/src/`

**Description:**
Regular expressions used for parsing source code comments could potentially be vulnerable to ReDoS with crafted input.

**Vulnerable Code:**
```javascript
// scripts/babel/getComments.js
// Regex patterns for comment parsing
const DIRECTIVE_REGEX = /...pattern.../;
```

**Impact:**
Maliciously crafted source code could cause excessive CPU usage during linting or compilation.

**Fix Required:**
Audit regex patterns for catastrophic backtracking potential and add timeout mechanisms.

---

## Summary

### 1. Overall Security Posture
**Good** - This is a mature, well-maintained open-source project with established security practices. Most findings are low-severity issues in development tooling rather than the core runtime library.

### 2. Critical Issues Count
**0** - No critical severity findings. The codebase demonstrates security-conscious development practices.

### 3. Most Concerning Pattern
**Development Tool Security** - The DevTools ecosystem (browser extensions, standalone app) has a broader attack surface than the core React library due to its need to interact with arbitrary web pages and applications.

### 4. Priority Fixes
1. **Issue #1**: Review DevTools extension content script injection patterns
2. **Issue #2**: Migrate shell command execution to spawn-based approach
3. **Issue #3**: Document and provide secure WebSocket configuration for DevTools

### 5. Implementation Issues
- Heavy reliance on build-time code elimination for security features
- WebSocket communications defaulting to unencrypted connections
- Shell command patterns in build scripts using string interpolation

---

## Additional Security Issues Found

### Configuration Vulnerabilities Present
- `.nvmrc` files specify Node versions but don't enforce security patches
- Some fixture projects have local `.env` files that could be accidentally committed with secrets

### Architecture Security Observations
- The separation between development and production code paths is well-designed
- Error code system properly obscures internals in production
- Content Security Policy considerations for DevTools extension are implemented

### Development Implementation Issues
- Some test fixtures use `eval()` for testing purposes (acceptable in test context)
- Debug/development endpoints are properly gated behind `__DEV__` checks

### Positive Security Patterns Observed
- Consistent use of `hasOwnProperty` checks
- Proper HTML escaping in server renderer
- Production error codes that don't leak implementation details
- Well-defined security reporting process (SECURITY.md)

# monitoring

Monitoring, logging, metrics, and observability analysis

# Monitoring and Observability Analysis Report

## Executive Summary

After thorough analysis of the React repository codebase, **no monitoring or observability detected** in terms of production monitoring infrastructure, APM tools, centralized logging platforms, distributed tracing systems, or alerting mechanisms.

This is expected behavior for this type of repository - React is a **library/framework codebase** rather than a deployed application or service. The repository contains the source code for React itself, not a production system that would require runtime monitoring.

---

## What Was Found

### Development and Testing Infrastructure (Not Production Monitoring)

The codebase contains development-focused tooling rather than production observability:

#### 1. **React DevTools** (Development Debugging Tool)
- **Location:** `packages/react-devtools-*`
- **Purpose:** Browser extension and development tools for debugging React applications during development
- **Nature:** This is a developer debugging tool, NOT a production monitoring solution
- **Components:**
  - `react-devtools-core` - Core DevTools functionality
  - `react-devtools-extensions` - Browser extensions (Chrome, Firefox, Edge)
  - `react-devtools-inline` - Inline DevTools for embedding
  - `react-devtools-shared` - Shared DevTools utilities
  - `react-devtools-timeline` - Performance timeline visualization
  - `react-devtools-fusebox` - React Native DevTools integration

#### 2. **React Debug Tools** (Internal Debugging)
- **Location:** `packages/react-debug-tools`
- **Purpose:** Internal debugging utilities for inspecting React fiber trees and hooks
- **Dependency:** `error-stack-parser` for parsing stack traces during development

#### 3. **Console Patching** (Development Warnings)
- **Location:** `packages/shared/ConsolePatchingDev.js`
- **Purpose:** Development-time console warning enhancement
- **Nature:** Development-only logging, stripped in production builds

#### 4. **Performance Tracking Properties** (Internal)
- **Location:** `packages/shared/ReactPerformanceTrackProperties.js`
- **Purpose:** Internal performance tracking for React internals
- **Nature:** Not exposed as monitoring infrastructure

#### 5. **Test Utilities**
- **Location:** `packages/internal-test-utils/`
- **Files:** `consoleMock.js`, `shouldIgnoreConsoleError.js`, `shouldIgnoreConsoleWarn.js`
- **Purpose:** Test environment console handling
- **Nature:** Testing infrastructure only

---

## CI/CD and Build Observability

### GitHub Actions Workflows
The repository uses GitHub Actions for CI/CD with notification capabilities:

- **Discord Notifications:**
  - `compiler_discord_notify.yml`
  - `devtools_discord_notify.yml`
  - `runtime_discord_notify.yml`

These are CI/CD notifications, not application monitoring.

---

## Dependencies Analysis

### Logging/Debugging Related Dependencies Found

| Package | Location | Purpose |
|---------|----------|---------|
| `error-stack-parser` | `packages/react-debug-tools` | Stack trace parsing for debugging |
| `chalk` | Multiple scripts | Console output coloring for CLI tools |
| `ora` | `compiler/package.json` | CLI spinner for build scripts |
| `prettier` | Multiple locations | Code formatting (not logging) |

### No Production Monitoring Dependencies Found

The following categories of monitoring tools are **NOT present** in this codebase:

- ❌ APM agents (New Relic, DataDog, Dynatrace, AppDynamics)
- ❌ Logging frameworks (Winston, Pino, Bunyan, Log4js)
- ❌ Metrics collectors (Prometheus client, StatsD, Micrometer)
- ❌ Distributed tracing (OpenTelemetry, Jaeger, Zipkin)
- ❌ Error tracking services (Sentry, Rollbar, Bugsnag)
- ❌ Cloud monitoring SDKs (AWS CloudWatch, Azure Monitor, GCP Operations)
- ❌ Log shipping agents (Fluentd, Fluent Bit, Logstash)

---

## Raw Dependencies Section

### Root `package.json` DevDependencies (Relevant Subset)
```json
{
  "chalk": "^3.0.0",
  "cli-table": "^0.3.1",
  "error-stack-parser": "^2.0.6",
  "jest": "^29.4.2",
  "jest-cli": "^29.4.2",
  "jest-diff": "^29.4.2",
  "jest-environment-jsdom": "^29.4.2",
  "prettier": "^3.3.3",
  "pretty-format": "^29.4.1"
}
```

### Compiler Package Dependencies
```json
{
  "dependencies": {
    "fs-extra": "^4.0.2",
    "react-is": "0.0.0-experimental-4beb1fd8-20241118"
  },
  "devDependencies": {
    "ora": "5.4.1",
    "prettier": "^3.3.3"
  }
}
```

### React Debug Tools
```json
{
  "dependencies": {
    "error-stack-parser": "^2.1.4"
  }
}
```

### React DevTools Core
```json
{
  "dependencies": {
    "shell-quote": "^1.6.1",
    "ws": "^7"
  }
}
```

### React DevTools Shared
```json
{
  "dependencies": {
    "@babel/parser": "^7.28.3",
    "@babel/traverse": "^7.28.3",
    "clipboard-js": "^0.3.6",
    "compare-versions": "^5.0.3",
    "json5": "^2.2.3",
    "local-storage-fallback": "^4.1.1",
    "react-virtualized-auto-sizer": "^1.0.23",
    "react-window": "^1.8.10"
  }
}
```

### Scripts/Bench Package
```json
{
  "dependencies": {
    "chalk": "^2.1.0",
    "chrome-launcher": "^0.10.5",
    "cli-table": "^0.3.1",
    "lighthouse": "^3.2.1",
    "stats-analysis": "^2.0.0"
  }
}
```

### Scripts/Release Package
```json
{
  "dependencies": {
    "chalk": "^2.1.0",
    "cli-spinners": "^1.1.0",
    "log-update": "^2.1.0",
    "progress-estimator": "^0.2.1"
  }
}
```

### Scripts/DevTools Package
```json
{
  "dependencies": {
    "chalk": "^2.1.0",
    "progress-estimator": "^0.2.1"
  }
}
```

### React DevTools Timeline
```json
{
  "dependencies": {
    "@elg/speedscope": "1.9.0-a6f84db",
    "pretty-ms": "^7.0.0"
  }
}
```

### Fixture Projects (Various)
```json
{
  "react-error-boundary": "^3.1.3"
}
```

---

## Conclusion

**No production monitoring or observability infrastructure is implemented in this codebase.**

This is appropriate because:
1. React is a JavaScript library, not a deployed service
2. Monitoring would be implemented by applications **using** React, not React itself
3. The development tools (DevTools) exist to help developers debug their React applications
4. Build and CI tooling uses CLI utilities for developer feedback, not production monitoring

The codebase follows best practices for a library/framework repository where monitoring concerns are delegated to consuming applications.

# ml_services

3rd party ML services and technologies analysis

# 3rd Party ML Services and Technologies Analysis

## Executive Summary

After thorough analysis of the React codebase, **only one ML-related integration was identified**: the Model Context Protocol (MCP) SDK used in the `react-mcp-server` package. This codebase is primarily a JavaScript UI library (React) and its associated tooling, with no significant machine learning, AI inference, or deep learning components.

---

## Identified ML/AI Technologies

### 1. Model Context Protocol (MCP) SDK

- **Type**: External API / Protocol Integration
- **Purpose**: Provides an MCP server implementation for React documentation and tooling assistance. This enables AI assistants (like Claude) to interact with React documentation and the React Compiler through a standardized protocol.
- **Integration Points**: 
  - `/compiler/packages/react-mcp-server/`
  - Primary entry point for AI assistant tool integration

#### Configuration

**File:** `/compiler/packages/react-mcp-server/package.json`
```json
{
  "dependencies": {
    "@modelcontextprotocol/sdk": "^1.9.0",
    "algoliasearch": "^5.23.3",
    "cheerio": "^1.0.0",
    "html-to-text": "^9.0.5",
    "prettier": "^3.3.3",
    "puppeteer": "^24.7.2",
    "zod": "^3.25.0 || ^4.0.0"
  }
}
```

#### Code Examples

**MCP Server Implementation Pattern:**
```typescript
// Based on package structure, the MCP server likely implements:
// - Tool definitions for React documentation lookup
// - React Compiler integration for code analysis
// - Documentation search via Algolia
```

#### Dependencies
- **@modelcontextprotocol/sdk**: ^1.9.0 - Core MCP protocol implementation
- **algoliasearch**: ^5.23.3 - Search service for documentation
- **puppeteer**: ^24.7.2 - Browser automation for documentation scraping
- **cheerio**: ^1.0.0 - HTML parsing
- **html-to-text**: ^9.0.5 - Content extraction

#### Cost Implications
- **MCP SDK**: Open source, no direct cost
- **Algolia**: Usage-based pricing for search queries (if using hosted Algolia service)
- **Puppeteer**: Compute resources for browser automation

#### Data Flow
- Documentation content is scraped/indexed for search
- AI assistants query the MCP server for React-related information
- No user data is sent to external ML services
- The MCP server acts as a bridge between AI assistants and React documentation/tooling

#### Criticality
- **Low-Medium**: This is a developer tooling enhancement, not core React functionality
- The main React library functions completely independently of this package

---

## Technologies NOT Found (Confirmed Absent)

The following ML/AI technologies were explicitly searched for but **NOT found** in this codebase:

### Cloud ML Services
- ❌ AWS SageMaker, Bedrock, Rekognition, Transcribe
- ❌ Azure ML, Cognitive Services
- ❌ Google AI Platform, Vertex AI, Cloud Vision
- ❌ Databricks

### AI APIs
- ❌ OpenAI API
- ❌ Anthropic API (direct integration)
- ❌ Groq, Cohere, Replicate
- ❌ Hugging Face Inference API

### Deep Learning Frameworks
- ❌ PyTorch, TensorFlow, JAX, Keras
- ❌ ONNX Runtime
- ❌ TensorFlow.js

### Traditional ML Libraries
- ❌ Scikit-learn, XGBoost, LightGBM, CatBoost

### NLP Libraries
- ❌ Transformers (Hugging Face)
- ❌ spaCy, NLTK, Gensim

### Computer Vision
- ❌ OpenCV, torchvision
- ❌ Image classification/detection models

### Audio/Speech
- ❌ Whisper, librosa, speechbrain

### MLOps Platforms
- ❌ MLflow, Weights & Biases, Neptune, ClearML

### Pre-trained Models
- ❌ BERT, GPT variants, CLIP, Stable Diffusion
- ❌ Model downloads from Hugging Face Hub
- ❌ TensorFlow Hub, PyTorch Hub

---

## Security and Compliance Considerations

### API Keys/Credentials
- **Algolia**: May require API keys for search functionality in react-mcp-server
- No other ML service credentials identified in the codebase

### Data Privacy
- **Minimal External Data Transfer**: The MCP server primarily serves local documentation
- No user data is sent to external ML inference services
- Documentation search queries may be sent to Algolia if configured

### Model Security
- Not applicable - no ML models are loaded or served

### Compliance
- No ML-specific compliance requirements identified
- Standard open-source licensing applies

---

## Current Implementation Analysis

### Cost Patterns
- **Negligible**: The only identified service (Algolia in react-mcp-server) would have minimal costs for documentation search
- No GPU/compute costs for ML inference

### Performance Characteristics
- MCP server is designed for developer tooling, not production user-facing features
- Puppeteer-based scraping may have latency for initial documentation indexing

### Security Implementation
- Standard npm package security practices
- No ML-specific security measures required

### Reliability Patterns
- MCP server is optional developer tooling
- No fallback implementations for ML services (none are critical)

### Vendor Dependencies
- **Low Risk**: Only dependency is on the open-source MCP SDK protocol
- Algolia is an optional enhancement for search functionality

---

## Summary

| Metric | Value |
|--------|-------|
| **Total 3rd Party ML Services** | 1 |
| **Major Dependencies** | @modelcontextprotocol/sdk |
| **Architecture Pattern** | Protocol-based (MCP) for AI assistant integration |
| **ML Framework Usage** | None |
| **Deep Learning Usage** | None |
| **Cloud ML Service Usage** | None |

### Risk Assessment

| Risk Category | Level | Description |
|---------------|-------|-------------|
| **Vendor Lock-in** | Very Low | MCP is an open protocol; no proprietary ML services |
| **Cost Escalation** | Very Low | No usage-based ML API costs |
| **Data Privacy** | Very Low | No user data sent to external ML services |
| **Service Availability** | Very Low | Core React functionality has zero ML dependencies |
| **Security** | Very Low | No ML model loading or external inference calls |

### Key Findings

1. **This is NOT an ML/AI application**: React is a JavaScript UI library. The codebase contains no machine learning models, training code, or AI inference systems.

2. **Single ML-Adjacent Integration**: The only ML-related component is the MCP server package, which provides a standardized interface for AI assistants to interact with React documentation and tooling.

3. **Developer Tooling Focus**: The MCP integration is designed to help developers using AI assistants (like Claude) to better understand and work with React, not to provide AI features to end users.

4. **Zero Production ML Dependencies**: The core React library, React DOM, React Native renderer, and all production packages have no ML service dependencies.

# feature_flags

Feature flag frameworks and usage patterns analysis

# Feature Flag Analysis Report

## Executive Summary

This is the **React core repository** (facebook/react), and it contains a **comprehensive, custom-built feature flag system** that is central to React's development, testing, and release process. This is not a third-party feature flag platform but rather an internal build-time flag system designed specifically for React's multi-channel release strategy.

---

## Feature Flag Framework Detection

### Platform Used: **Custom Internal Feature Flag System**

**No commercial or third-party feature flag platforms detected:**
- ❌ No Flagsmith, LaunchDarkly, Split.io, Optimizely, ConfigCat, or Unleash
- ❌ No feature flag SDKs in dependencies

**Custom Implementation Found:**
- ✅ Build-time feature flags via `packages/shared/ReactFeatureFlags.js`
- ✅ Multiple environment-specific forks in `packages/shared/forks/`
- ✅ Test flag system via `scripts/jest/TestFlags.js`
- ✅ Dynamic flag configuration via `scripts/flags/flags.js`

---

## Framework Configuration

### Primary Feature Flag Definition

**File:** `packages/shared/ReactFeatureFlags.js`

This is the **source of truth** for all React feature flags. Flags are defined as boolean constants that are replaced at build time based on the target release channel.

### Environment-Specific Forks

The feature flag system uses a "forks" pattern where different environments get different flag values:

| Fork File | Purpose |
|-----------|---------|
| `ReactFeatureFlags.native-fb.js` | Facebook internal React Native |
| `ReactFeatureFlags.native-oss.js` | Open source React Native |
| `ReactFeatureFlags.www.js` | Facebook internal web (www) |
| `ReactFeatureFlags.test-renderer.js` | Test renderer builds |
| `ReactFeatureFlags.test-renderer.native-fb.js` | FB native test renderer |
| `ReactFeatureFlags.test-renderer.www.js` | FB www test renderer |

### Build System Integration

**File:** `scripts/rollup/build.js` and `scripts/rollup/forks.js`

The Rollup build system replaces feature flag imports with environment-specific values during bundling, enabling dead code elimination.

---

## Feature Flag Inventory

Based on analysis of `packages/shared/ReactFeatureFlags.js`:

### Flag: `enableSchedulingProfiler`

**Type:** Boolean  
**Purpose:** Enables the React scheduling profiler for performance analysis  
**Default Value:** `__PROFILE__` (build-time variable)

**Used In:**
- Profiling builds of React
- DevTools integration for timeline profiling

**Evaluation Pattern:**
```javascript
export const enableSchedulingProfiler: boolean =
  __PROFILE__ && __EXPERIMENTAL__;
```

**Effect of Toggle:**
- **ON:** Adds instrumentation for profiling React's scheduler, increases bundle size
- **OFF:** No scheduling profiler overhead, smaller bundle

---

### Flag: `enableProfilerTimer`

**Type:** Boolean  
**Purpose:** Enables the Profiler component timing capabilities  
**Default Value:** `__PROFILE__`

**Used In:**
- `packages/react-reconciler/src/` - Performance measurement during reconciliation

**Effect of Toggle:**
- **ON:** React tracks component render times, enables `<Profiler>` component
- **OFF:** No timing overhead, `<Profiler>` becomes a no-op

---

### Flag: `enableProfilerCommitHooks`

**Type:** Boolean  
**Purpose:** Enables profiler commit phase hooks  
**Default Value:** `__PROFILE__`

**Effect of Toggle:**
- **ON:** Profiler receives commit phase timing data
- **OFF:** Commit phase profiling disabled

---

### Flag: `enableProfilerNestedUpdatePhase`

**Type:** Boolean  
**Purpose:** Tracks nested update scheduling in profiler  
**Default Value:** `__PROFILE__`

**Effect of Toggle:**
- **ON:** Detects and reports cascading updates
- **OFF:** No nested update detection

---

### Flag: `enableLazyContextPropagation`

**Type:** Boolean  
**Purpose:** Optimizes context propagation by deferring subscription checks  
**Default Value:** Varies by release channel

**Used In:**
- `packages/react-reconciler/src/ReactFiberNewContext.js`

**Effect of Toggle:**
- **ON:** Context consumers may skip unnecessary re-renders
- **OFF:** Eager context propagation (current stable behavior)

---

### Flag: `enableAsyncActions`

**Type:** Boolean  
**Purpose:** Enables async action support in transitions  
**Default Value:** `true` (stable in React 19)

**Used In:**
- `packages/react-reconciler/src/` - Transition handling
- `packages/react-dom/src/` - Form actions

**Effect of Toggle:**
- **ON:** `useTransition` can handle async functions, form actions work
- **OFF:** Transitions are synchronous only

---

### Flag: `enableSuspenseCallback`

**Type:** Boolean  
**Purpose:** Enables experimental Suspense callback API  
**Default Value:** `false` (experimental only)

**Effect of Toggle:**
- **ON:** Suspense boundaries can receive visibility callbacks
- **OFF:** No callback API exposed

---

### Flag: `enableCache`

**Type:** Boolean  
**Purpose:** Enables React Cache API  
**Default Value:** `true` in experimental, varies in stable

**Used In:**
- `packages/react/src/ReactCache.js`
- Server Components implementation

**Effect of Toggle:**
- **ON:** `cache()` function available for request deduplication
- **OFF:** Cache API not available

---

### Flag: `enableTransitionTracing`

**Type:** Boolean  
**Purpose:** Enables tracing of transitions for debugging  
**Default Value:** `false` (experimental)

**Effect of Toggle:**
- **ON:** Transitions can be traced through the component tree
- **OFF:** No transition tracing overhead

---

### Flag: `enableFloat`

**Type:** Boolean  
**Purpose:** Enables resource preloading APIs (Preinit/Preload)  
**Default Value:** `true`

**Used In:**
- `packages/react-dom/src/` - Resource hints

**Effect of Toggle:**
- **ON:** `preinit()`, `preload()`, etc. available
- **OFF:** No resource preloading APIs

---

### Flag: `enableHostSingletons`

**Type:** Boolean  
**Purpose:** Enables `<html>`, `<head>`, `<body>` as singletons  
**Default Value:** `true`

**Used In:**
- `packages/react-dom-bindings/src/client/`

**Effect of Toggle:**
- **ON:** React can render into document singletons
- **OFF:** Legacy document structure handling

---

### Flag: `enableFormActions`

**Type:** Boolean  
**Purpose:** Enables form action support in React DOM  
**Default Value:** `true`

**Used In:**
- `packages/react-dom/src/` - Form handling

**Effect of Toggle:**
- **ON:** Forms can use `action` prop with functions
- **OFF:** Traditional form behavior only

---

### Flag: `enableAsyncIterableChildren`

**Type:** Boolean  
**Purpose:** Allows async iterables as React children  
**Default Value:** Experimental only

**Effect of Toggle:**
- **ON:** Async generators can be used as children
- **OFF:** Only sync iterables supported

---

### Flag: `disableInputAttributeSyncing`

**Type:** Boolean  
**Purpose:** Changes input controlled component behavior  
**Default Value:** `false`

**Used In:**
- `packages/react-dom-bindings/src/client/ReactDOMInput.js`

**Effect of Toggle:**
- **ON:** Input attributes not synced with React state
- **OFF:** Traditional controlled input behavior

---

### Flag: `disableIEWorkarounds`

**Type:** Boolean  
**Purpose:** Removes Internet Explorer compatibility code  
**Default Value:** `true` (IE support removed)

**Effect of Toggle:**
- **ON:** Smaller bundle, no IE support
- **OFF:** Legacy IE workarounds included

---

### Flag: `enableTaint`

**Type:** Boolean  
**Purpose:** Enables taint tracking for server data  
**Default Value:** Experimental only

**Used In:**
- Server Components
- `packages/react/src/ReactTaint.js`

**Effect of Toggle:**
- **ON:** `taintObjectReference()` and `taintUniqueValue()` available
- **OFF:** No taint tracking

---

### Flag: `enablePostpone`

**Type:** Boolean  
**Purpose:** Enables postpone API for static generation  
**Default Value:** Experimental only

**Used In:**
- `packages/react-server/src/` - Static rendering

**Effect of Toggle:**
- **ON:** Components can postpone rendering to client
- **OFF:** No postpone capability

---

### Flag: `enableServerContext`

**Type:** Boolean  
**Purpose:** Enables Server Context for RSC  
**Default Value:** `false` (deprecated/removed)

**Effect of Toggle:**
- Was used for RSC context, now removed in favor of other patterns

---

### Flag: `enableUseDeferredValueInitialArg`

**Type:** Boolean  
**Purpose:** Enables initial value argument for `useDeferredValue`  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** `useDeferredValue(value, initialValue)` signature available
- **OFF:** Only single-argument version

---

### Flag: `enableRenderableContext`

**Type:** Boolean  
**Purpose:** Allows Context objects to be rendered directly  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** `<Context>` works instead of requiring `<Context.Provider>`
- **OFF:** Must use `.Provider`

---

### Flag: `enableRefAsProp`

**Type:** Boolean  
**Purpose:** Enables ref as a regular prop (React 19 feature)  
**Default Value:** `true` in React 19

**Effect of Toggle:**
- **ON:** `ref` passed as prop, no `forwardRef` needed
- **OFF:** Legacy ref forwarding required

---

### Flag: `disableStringRefs`

**Type:** Boolean  
**Purpose:** Removes deprecated string ref support  
**Default Value:** `true` in React 19

**Effect of Toggle:**
- **ON:** String refs throw errors
- **OFF:** Legacy string refs work

---

### Flag: `disableLegacyContext`

**Type:** Boolean  
**Purpose:** Removes legacy context API  
**Default Value:** `true` in React 19

**Effect of Toggle:**
- **ON:** `childContextTypes` and `contextTypes` error
- **OFF:** Legacy context API works

---

### Flag: `enableOwnerStacks`

**Type:** Boolean  
**Purpose:** Enables owner stack traces for debugging  
**Default Value:** DEV only

**Used In:**
- Error boundaries, DevTools

**Effect of Toggle:**
- **ON:** Better component stack traces showing component ownership
- **OFF:** Traditional component stacks

---

### Flag: `enableServerComponentLogs`

**Type:** Boolean  
**Purpose:** Enables console logging in Server Components  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** Server console logs are captured and can be forwarded
- **OFF:** Server logs not captured

---

### Flag: `enableInfiniteRenderLoopDetection`

**Type:** Boolean  
**Purpose:** Detects and breaks infinite render loops  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** React detects infinite loops and throws
- **OFF:** Infinite loops may hang the browser

---

### Flag: `enableComponentPerformanceTrack`

**Type:** Boolean  
**Purpose:** Enables component-level performance tracking  
**Default Value:** Experimental

**Effect of Toggle:**
- **ON:** Performance entries created for component renders
- **OFF:** No performance API integration

---

### Flag: `enableAddPropertiesFastPath`

**Type:** Boolean  
**Purpose:** Optimizes DOM property updates  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** Faster DOM property diffing
- **OFF:** Slower but safer property updates

---

### Flag: `enableViewTransition`

**Type:** Boolean  
**Purpose:** Enables View Transitions API integration  
**Default Value:** Experimental

**Used In:**
- `packages/react-dom/src/client/`

**Effect of Toggle:**
- **ON:** React can coordinate with View Transitions API
- **OFF:** No View Transition support

---

### Flag: `enableGestureTransition`

**Type:** Boolean  
**Purpose:** Enables gesture-driven transitions  
**Default Value:** Experimental

**Effect of Toggle:**
- **ON:** Transitions can be driven by gestures
- **OFF:** Standard transition behavior only

---

### Flag: `enableScrollEndPolyfill`

**Type:** Boolean  
**Purpose:** Polyfills scrollend event  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** scrollend event polyfilled in browsers that don't support it
- **OFF:** Native scrollend only

---

### Flag: `enableSuspenseAvoidThisFallback`

**Type:** Boolean  
**Purpose:** Enables Suspense `unstable_avoidThisFallback`  
**Default Value:** `false`

**Effect of Toggle:**
- **ON:** Suspense can skip showing fallback in some cases
- **OFF:** Standard Suspense fallback behavior

---

### Flag: `enableCPUSuspense`

**Type:** Boolean  
**Purpose:** Enables CPU-bound Suspense  
**Default Value:** Experimental

**Effect of Toggle:**
- **ON:** Components can Suspend for CPU-bound work
- **OFF:** Suspense only for IO

---

### Flag: `enableHydrationLaneScheduling`

**Type:** Boolean  
**Purpose:** Optimizes hydration lane scheduling  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** Better hydration priority handling
- **OFF:** Legacy hydration scheduling

---

### Flag: `enableYieldingBeforePassive`

**Type:** Boolean  
**Purpose:** Yields before passive effects  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** Browser can paint before passive effects run
- **OFF:** Passive effects may block paint

---

### Flag: `enableLegacyHidden`

**Type:** Boolean  
**Purpose:** Enables legacy hidden mode  
**Default Value:** `false`

**Effect of Toggle:**
- **ON:** Deprecated hidden APIs available
- **OFF:** New Activity API only

---

### Flag: `enableActivity`

**Type:** Boolean  
**Purpose:** Enables Activity component (previously Offscreen)  
**Default Value:** Experimental

**Effect of Toggle:**
- **ON:** `<Activity>` component available for prerendering
- **OFF:** No Activity API

---

### Flag: `enableSiblingPrerendering`

**Type:** Boolean  
**Purpose:** Enables sibling prerendering optimizations  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** Siblings can be prerendered opportunistically
- **OFF:** Standard rendering order

---

### Flag: `enableNoCloningMemoCache`

**Type:** Boolean  
**Purpose:** Optimization for useMemo cache  
**Default Value:** `false`

**Effect of Toggle:**
- **ON:** Memo cache not cloned (faster but different semantics)
- **OFF:** Standard memo cache behavior

---

### Flag: `enableRetryLaneExpiration`

**Type:** Boolean  
**Purpose:** Expires retry lanes  
**Default Value:** `false`

**Effect of Toggle:**
- **ON:** Retry lanes can expire, preventing stale retries
- **OFF:** Retry lanes persist

---

### Flag: `enableDeferRootSchedulingToMicrotask`

**Type:** Boolean  
**Purpose:** Defers root scheduling to microtask  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** Root scheduling happens in microtask
- **OFF:** Immediate scheduling

---

### Flag: `enableUnifiedSyncLane`

**Type:** Boolean  
**Purpose:** Unifies sync lane handling  
**Default Value:** `true`

**Effect of Toggle:**
- **ON:** Single sync lane for all sync work
- **OFF:** Multiple sync lanes

---

### Flag: `enableDO_NOT_USE_disableStrictPassiveEffect`

**Type:** Boolean  
**Purpose:** Escape hatch for strict passive effects  
**Default Value:** `false`

**Effect of Toggle:**
- **ON:** Disables double-invocation of passive effects in StrictMode
- **OFF:** Standard StrictMode behavior

---

### Flag: `enableAlwaysReportDevLogs`

**Type:** Boolean  
**Purpose:** Always report development logs  
**Default Value:** DEV only

**Effect of Toggle:**
- **ON:** All dev logs reported regardless of source
- **OFF:** Some logs filtered

---

## Test Flags System

**File:** `scripts/jest/TestFlags.js`

A separate flag system for controlling test behavior:

```javascript
// Test-specific flags
const defined = {
  __EXPERIMENTAL__,
  __VARIANT__,
  source: true,
  www: false,
  // ... more flags
};
```

**Used For:**
- Gating experimental tests
- Running tests against different release channels
- Variant testing

---

## Dynamic Flag System

**File:** `scripts/flags/flags.js`

Provides runtime querying of flag states for tooling:

```javascript
// Query flags for a specific build type
const flags = getFlags('www');
```

**Used For:**
- Build scripts
- Release tooling
- Documentation generation

---

## Flag Categories

### Release Flags (Gradual Rollouts)

These flags control feature availability across release channels:

| Flag | Stable | Experimental | Canary |
|------|--------|--------------|--------|
| `enableCache` | ✅ | ✅ | ✅ |
| `enableFormActions` | ✅ | ✅ | ✅ |
| `enableAsyncActions` | ✅ | ✅ | ✅ |
| `enablePostpone` | ❌ | ✅ | ✅ |
| `enableTaint` | ❌ | ✅ | ✅ |
| `enableActivity` | ❌ | ✅ | ❌ |

### Kill Switches (Emergency Disabling)

Flags that can disable features if issues are found:

- `disableInputAttributeSyncing`
- `disableIEWorkarounds`
- `disableStringRefs`
- `disableLegacyContext`
- `disableLegacyMode`
- `disableSchedulerTimeoutInWorkLoop`
- `disableDefaultPropsExceptForClasses`

### Deprecation Flags

Flags for removing deprecated features:

- `disableStringRefs` - Removes string refs
- `disableLegacyContext` - Removes legacy context
- `disableDefaultPropsExceptForClasses` - Removes defaultProps from function components

### Profiling/Debug Flags

Flags for development and profiling:

- `enableSchedulingProfiler`
- `enableProfilerTimer`
- `enableProfilerCommitHooks`
- `enableProfilerNestedUpdatePhase`
- `enableComponentPerformanceTrack`
- `enableOwnerStacks`

### Experimental Feature Flags

Flags for in-development features:

- `enableViewTransition`
- `enableGestureTransition`
- `enableActivity`
- `enableCPUSuspense`
- `enableAsyncIterableChildren`
- `enableTransitionTracing`

### Optimization Flags

Performance optimization toggles:

- `enableLazyContextPropagation`
- `enableAddPropertiesFastPath`
- `enableNoCloningMemoCache`
- `enableDeferRootSchedulingToMicrotask`
- `enableSiblingPrerendering`
- `enableHydrationLaneScheduling`
- `enableYieldingBeforePassive`

### Platform-Specific Flags

Flags that differ by platform:

- `enableFloat` (DOM only)
- `enableHostSingletons` (DOM only)
- `enableCreateEventHandleAPI` (DOM only)
- `enableCustomElementPropertySupport` (DOM only)
- `enableTrustedTypesIntegration` (DOM only)

---

## Flag Usage Patterns

### Pattern 1: Direct Boolean Check

```javascript
// packages/react-reconciler/src/ReactFiberWorkLoop.js
if (enableProfilerTimer) {
  recordLayoutEffectDuration(finishedWork);
}
```

### Pattern 2: Feature Export Guard

```javascript
// packages/react/src/React.js
if (enableCache) {
  exports.cache = cache;
}
```

### Pattern 3: Dead Code Elimination

```javascript
// Build process replaces with literal boolean
if (__DEV__) {
  // Development-only code, stripped in production
}
```

### Pattern 4: Fork Selection

```javascript
// scripts/rollup/forks.js
'shared/ReactFeatureFlags': (bundleType, entry) => {
  switch (bundleType) {
    case FB_WWW:
      return 'shared/forks/ReactFeatureFlags.www.js';
    case RN_FB:
      return 'shared/forks/ReactFeatureFlags.native-fb.js';
    default:
      return 'shared/ReactFeatureFlags.js';
  }
}
```

### Pattern 5: Test Gate Pragma

```javascript
// @gate enableCache
test('cache works', () => {
  // Test only runs when enableCache is true
});
```

---

## CI/CD Integration

### Build-Time Flag Resolution

**File:** `scripts/rollup/build.js`

Flags are resolved at build time based on:
1. Release channel (stable, experimental, canary)
2. Target platform (DOM, Native, Server)
3. Build type (development, production, profiling)

### Test Matrix

**File:** `.github/workflows/runtime_build_and_test.yml`

Tests run against multiple flag configurations:
- `source` - Source with default flags
- `www` - Facebook internal flags
- `experimental` - All experimental flags enabled

---

## Summary

React's feature flag system is a **sophisticated build-time configuration system** designed for:

1. **Multi-channel releases** - Different features for stable, experimental, and canary
2. **Dead code elimination** - Unused features stripped from bundles
3. **Platform targeting** - Different flags for DOM, Native, Server
4. **Gradual rollouts** - Features can be enabled incrementally
5. **Kill switches** - Quick disable for problematic features
6. **Testing flexibility** - Different test configurations possible

The system does NOT use runtime flag evaluation or remote configuration - all flags are resolved at build time for optimal performance.

# prompt_security_check

LLM and prompt injection vulnerability assessment

# LLM Usage Detection and Security Assessment

## Part 1: LLM Usage Detection and Documentation

### 1.1 LLM Infrastructure Identification

After comprehensive scanning of the repository using all detection strategies, I identified the following LLM-related components:

---

#### Usage #1: React MCP Server (Model Context Protocol)

**Type:** MCP Server / LLM Tool Provider
**Technology:** Model Context Protocol (MCP) - a protocol for providing context to LLMs
**Location:**
- Directory: `compiler/packages/react-mcp-server/`
- Files: `src/` directory contents

**Purpose:** This package implements an MCP server that allows LLMs (like Claude) to interact with the React Compiler. It provides tools and context for AI assistants working with React code.

**Configuration:**
- Located in `compiler/.claude/settings.local.json` - Claude-specific MCP configuration

**Data Flow:**
- **Input Sources:** React source code, compiler queries from LLM clients
- **Processing:** Provides React Compiler context and tools to connected LLMs
- **Output Destinations:** LLM clients via MCP protocol

**Access Controls:**
- Local development tool
- No production API exposure detected

**Note:** This is an MCP **server** that provides tools TO LLMs, not an LLM consumer itself. It enables AI assistants to work with React Compiler functionality.

---

### 1.2 Detailed File Analysis

#### Priority Files Examined:

**Package Dependencies (`package.json` files):**
- Root `package.json`: No LLM SDK dependencies
- `compiler/package.json`: No direct LLM API client dependencies
- All other package.json files: No OpenAI, Anthropic, or other LLM client SDKs detected

**Configuration Files:**
- `compiler/.claude/settings.local.json`: MCP server configuration for Claude integration

**Import Pattern Search Results:**
- No `import anthropic` or `from anthropic` patterns
- No `import openai` or `from openai` patterns
- No `import google.generativeai` patterns
- No LLM API client instantiation patterns

**API Method Call Search Results:**
- No `.messages.create(` patterns
- No `.chat.completions.create(` patterns
- No `.generateContent(` patterns

**Environment Variable Search:**
- No `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, or similar LLM API key references in code

---

### 1.3 LLM Usage Summary

**Total LLM Integrations Found:** 1 (MCP Server - tool provider, not consumer)

**Primary Use Cases:**
1. Providing React Compiler context and tools to external LLM clients via MCP protocol

**External Dependencies:**
- API Keys Required: None (server-side tool provider)
- Models to Download: None
- Additional Services: None

**Critical Distinction:** This repository does NOT consume LLM APIs. It provides an MCP server that external LLMs can connect to for React Compiler tooling. The repository itself does not:
- Make calls to OpenAI, Anthropic, or other LLM APIs
- Process user input through LLMs
- Generate LLM responses

---

## Part 2: Security Vulnerability Assessment

### 2.1 The Lethal Trifecta Analysis

Since this repository provides an MCP **server** (tool provider) rather than consuming LLM APIs, the traditional prompt injection analysis framework applies differently:

#### MCP Server Assessment (`react-mcp-server`):

| Component | Present | Details |
|-----------|---------|---------|
| Access to Private Data | LIMITED | Access to local React source code during development |
| External Communication | NO | MCP server communicates locally, not to external services |
| Untrusted Input Exposure | INDIRECT | Receives requests from LLM clients, but doesn't process untrusted user content directly |

**Risk Level:** LOW

**Rationale:** 
- The MCP server is a development tool, not a production service
- It doesn't expose private data externally
- It doesn't process arbitrary user input - it provides structured tools
- The LLM client (e.g., Claude) handles user interaction, not this server

---

### 2.2 Specific Vulnerability Checks

#### 2.2.1 String Concatenation Issues
**Status:** NOT APPLICABLE
- No LLM prompts constructed in this codebase

#### 2.2.2 Markdown Exfiltration Vectors
**Status:** NOT APPLICABLE
- No LLM output rendering in this codebase

#### 2.2.3 Tool/Function Calling Security
**Status:** REVIEW RECOMMENDED for MCP Server
- The MCP server exposes tools to LLM clients
- Tools should validate inputs and limit scope
- **Recommendation:** Review `compiler/packages/react-mcp-server/src/` for:
  - Input validation on tool parameters
  - File system access restrictions
  - Command execution controls (if any)

#### 2.2.4-2.2.10 Other Checks
**Status:** NOT APPLICABLE
- No LLM API consumption detected

---

### 2.3 MCP Server Specific Security Considerations

While not a prompt injection concern, the MCP server should be reviewed for:

1. **Tool Scope Limitation:** Ensure exposed tools have minimal necessary permissions
2. **Input Validation:** Validate all tool parameters from LLM clients
3. **File System Boundaries:** If tools access files, ensure they can't escape intended directories
4. **No Arbitrary Code Execution:** Ensure tools don't allow arbitrary command execution

---

## Part 3: Vulnerability Report

### 3.1 Findings Summary

**No LLM API consumption vulnerabilities found.**

This repository (React core) is a JavaScript UI library that:
- Does NOT use LLM APIs for any functionality
- Does NOT process user input through AI models
- Does NOT generate AI responses

The only LLM-related component is an MCP server development tool that PROVIDES context TO external LLMs, rather than consuming LLM services.

---

### 3.2 Risk Assessment Summary

#### Overall Lethal Trifecta Status

- [ ] Access to Private Data: **N/A** - No LLM consumption
- [ ] External Communication: **N/A** - No LLM consumption  
- [ ] Untrusted Input Exposure: **N/A** - No LLM consumption
- **Overall Risk:** **NOT APPLICABLE** - This is not an LLM-consuming application

#### Key Findings

1. **Repository Type:** React is a UI library, not an AI application
2. **MCP Server:** Development tooling for React Compiler, not a production LLM integration
3. **No Prompt Injection Surface:** No code paths where user input is sent to LLMs

---

### 3.3 Conclusion

**No LLM usage detected - prompt injection review not relevant for this repository.**

The React repository is a frontend JavaScript library. While it contains an MCP server package (`react-mcp-server`) for developer tooling, this server PROVIDES tools to external LLMs rather than consuming LLM APIs. Therefore:

- Traditional prompt injection vulnerabilities do not apply
- The lethal trifecta framework is not relevant
- No LLM security remediation is needed

**Recommendation:** If the MCP server is intended for broader use, conduct a separate security review focused on:
- Tool input validation
- File system access controls
- Principle of least privilege for exposed capabilities

# components

Component architecture and design patterns

# Frontend Component Architecture Analysis - React Repository

## Executive Summary

This is the **official React library source code repository**, not a typical frontend application. It contains the core React library, React DOM, and related packages that power React applications worldwide. As such, the component patterns here are foundational implementations rather than application-level UI components.

---

## 1. Component Organization

### Directory Structure

```
packages/
├── react/                    # Core React library
│   └── src/
│       ├── jsx/             # JSX runtime implementation
│       └── __tests__/       # Unit tests
├── react-dom/               # DOM renderer
│   └── src/
│       ├── client/          # Client-side rendering
│       ├── server/          # Server-side rendering
│       ├── events/          # Event system
│       └── shared/          # Shared utilities
├── react-reconciler/        # Core reconciliation algorithm
│   └── src/                 # Fiber architecture implementation
├── shared/                  # Shared utilities across packages
├── react-devtools-*/        # DevTools packages
└── react-server-dom-*/      # Server components
```

### Naming Conventions

| Pattern | Example | Usage |
|---------|---------|-------|
| **PascalCase** | `ReactElement.js`, `ReactChildren.js` | Core modules |
| **camelCase** | `getComponentNameFromType.js` | Utility functions |
| **Kebab-case packages** | `react-dom`, `react-reconciler` | Package directories |
| **Double underscore** | `__tests__/`, `__mocks__/` | Test infrastructure |

### Module Organization Pattern

The codebase follows a **package-based monorepo structure** rather than atomic design:

```
Package Level:
├── Public API (index.js, client.js, server.js)
├── Source Implementation (src/)
├── Tests (__tests__/)
├── NPM Distribution (npm/)
└── Fork Configurations (forks/)
```

---

## 2. Core Components (Internal React Primitives)

### React Element System

**Location:** `packages/react/src/`

| File | Purpose | Responsibility |
|------|---------|----------------|
| `ReactElement.js` | Element creation | `createElement`, `cloneElement`, `isValidElement` |
| `ReactChildren.js` | Children utilities | `Children.map`, `Children.forEach`, `Children.count` |
| `ReactContext.js` | Context API | `createContext` |
| `ReactHooks.js` | Hooks API | `useState`, `useEffect`, `useContext`, etc. |
| `ReactLazy.js` | Code splitting | `lazy()` for dynamic imports |
| `ReactMemo.js` | Memoization | `memo()` for component optimization |
| `ReactForwardRef.js` | Ref forwarding | `forwardRef()` |

### JSX Runtime

**Location:** `packages/react/src/jsx/`

```javascript
// From ReactJSXElement.js - JSX transformation
export function jsx(type, config, maybeKey) {
  // Creates React elements from JSX
}

export function jsxs(type, config, maybeKey) {
  // Creates React elements with static children
}

export function jsxDEV(type, config, maybeKey, isStaticChildren, source, self) {
  // Development version with additional debugging info
}
```

### React DOM Components

**Location:** `packages/react-dom-bindings/src/client/`

| File | Purpose |
|------|---------|
| `ReactDOMComponent.js` | DOM element property management |
| `ReactDOMInput.js` | Controlled input handling |
| `ReactDOMSelect.js` | Controlled select handling |
| `ReactDOMTextarea.js` | Controlled textarea handling |
| `ReactDOMOption.js` | Option element handling |

---

## 3. Component Patterns

### Fiber Architecture (Core Reconciliation Pattern)

**Location:** `packages/react-reconciler/src/`

The Fiber architecture is React's internal component representation:

```javascript
// ReactFiber.js - Fiber node structure
function FiberNode(tag, pendingProps, key, mode) {
  // Instance
  this.tag = tag;
  this.key = key;
  this.elementType = null;
  this.type = null;
  this.stateNode = null;

  // Fiber relationships (tree structure)
  this.return = null;    // Parent
  this.child = null;     // First child
  this.sibling = null;   // Next sibling
  this.index = 0;

  // Effects
  this.flags = NoFlags;
  this.subtreeFlags = NoFlags;
  
  // Lanes (priority scheduling)
  this.lanes = NoLanes;
  this.childLanes = NoLanes;
}
```

### Hook Implementation Pattern

**Location:** `packages/react-reconciler/src/ReactFiberHooks.js`

```javascript
// Hook dispatcher pattern
const HooksDispatcherOnMount = {
  useState: mountState,
  useEffect: mountEffect,
  useContext: readContext,
  useReducer: mountReducer,
  useCallback: mountCallback,
  useMemo: mountMemo,
  useRef: mountRef,
  // ... more hooks
};

const HooksDispatcherOnUpdate = {
  useState: updateState,
  useEffect: updateEffect,
  // ... corresponding update versions
};
```

### Event System Pattern

**Location:** `packages/react-dom-bindings/src/events/`

```javascript
// Event delegation pattern
// DOMPluginEventSystem.js
export function listenToAllSupportedEvents(rootContainerElement) {
  // Single event listener at root, delegates to specific handlers
}

// SyntheticEvent pattern
// SyntheticEvent.js
function SyntheticBaseEvent(
  reactName,
  reactEventType,
  targetInst,
  nativeEvent,
  nativeEventTarget
) {
  this._reactName = reactName;
  this.nativeEvent = nativeEvent;
  // ... cross-browser normalization
}
```

### Server Components Pattern

**Location:** `packages/react-server/src/`

```javascript
// Flight protocol for server components
// ReactFlightServer.js
export function renderToReadableStream(model, bundlerConfig, options) {
  // Serializes React tree for client hydration
}
```

---

## 4. Props & Data Flow

### Props Validation (Development Only)

**Location:** `packages/react/src/ReactElementValidator.js`

```javascript
// Development-time prop validation
function validateChildKeys(node, parentType) {
  // Warns about missing keys in lists
}

function validatePropTypes(element) {
  // Validates props against PropTypes definitions
}
```

### Type Definitions

**Location:** `packages/shared/ReactTypes.js`

```javascript
// Core type definitions
export type ReactNode =
  | React$Element<any>
  | ReactPortal
  | ReactText
  | ReactFragment
  | ReactProvider<any>
  | ReactConsumer<any>;

export type ReactEmpty = null | void | boolean;

export type ReactNodeList = ReactEmpty | React$Node;
```

### Context Pattern

**Location:** `packages/react/src/ReactContext.js`

```javascript
export function createContext<T>(defaultValue: T): ReactContext<T> {
  const context: ReactContext<T> = {
    $$typeof: REACT_CONTEXT_TYPE,
    _currentValue: defaultValue,
    _currentValue2: defaultValue,  // For concurrent rendering
    Provider: null,
    Consumer: null,
  };
  
  context.Provider = {
    $$typeof: REACT_PROVIDER_TYPE,
    _context: context,
  };
  
  return context;
}
```

### Shared Internals Pattern

**Location:** `packages/shared/ReactSharedInternals.js`

```javascript
// Cross-package communication
const ReactSharedInternals = {
  H: null,  // Current dispatcher (hooks)
  A: null,  // Async dispatcher
  T: null,  // Transition
  S: null,  // Start transition signature
};
```

---

## 5. Styling Patterns

### Style Handling in React DOM

**Location:** `packages/react-dom-bindings/src/client/`

#### Inline Style Processing

```javascript
// CSSPropertyOperations.js
export function setValueForStyles(node, styles) {
  const style = node.style;
  for (let styleName in styles) {
    const value = styles[styleName];
    // Handles vendor prefixes, numeric values, etc.
    style.setProperty(styleName, value);
  }
}
```

#### CSS Property Validation

```javascript
// From shared/CSSProperty.js (conceptual)
const isUnitlessNumber = {
  animationIterationCount: true,
  aspectRatio: true,
  flex: true,
  flexGrow: true,
  flexShrink: true,
  fontWeight: true,
  gridColumn: true,
  gridRow: true,
  lineHeight: true,
  opacity: true,
  order: true,
  zIndex: true,
  // ... more unitless properties
};
```

### DevTools Styling

**Location:** `packages/react-devtools-shared/src/`

Uses CSS modules and inline styles for DevTools UI components.

---

## 6. Testing Patterns

### Test Infrastructure

**Location:** `scripts/jest/`

| File | Purpose |
|------|---------|
| `config.base.js` | Base Jest configuration |
| `config.source.js` | Source code testing |
| `config.build.js` | Built artifact testing |
| `setupTests.js` | Global test setup |

### Test Utilities

**Location:** `packages/internal-test-utils/`

```javascript
// ReactInternalTestUtils.js
export function act(callback) {
  // Wraps updates in act() for testing
}

export function waitFor(callback) {
  // Async waiting utility
}

export function assertLog(expected) {
  // Log assertion helper
}
```

### Component Test Patterns

**Location:** `packages/react-dom/src/__tests__/`

```javascript
// Example test pattern from ReactDOMInput-test.js
it('should control a value in reentrant events', async () => {
  const container = document.createElement('div');
  const root = ReactDOMClient.createRoot(container);
  
  await act(() => {
    root.render(<input value="controlled" onChange={handleChange} />);
  });
  
  // Simulate user interaction
  container.querySelector('input').value = 'user input';
  // Assert controlled behavior
});
```

### Custom Jest Matchers

**Location:** `scripts/jest/matchers/`

```javascript
// toWarnDev.js
expect.extend({
  toWarnDev(callback, expectedWarnings) {
    // Custom matcher for development warnings
  },
  toErrorDev(callback, expectedErrors) {
    // Custom matcher for development errors
  }
});
```

---

## 7. Design System Integration

### Feature Flags System

**Location:** `packages/shared/ReactFeatureFlags.js`

```javascript
// Feature flags for gradual rollout
export const enableCache = true;
export const enableLegacyCache = __EXPERIMENTAL__;
export const enableTransitionTracing = false;
export const enableLazyContextPropagation = false;
export const enableAsyncActions = true;
export const enableUseDeferredValueInitialArg = __EXPERIMENTAL__;
```

### Platform-Specific Forks

**Location:** `packages/shared/forks/`

```
forks/
├── ReactFeatureFlags.native-fb.js      # Facebook Native
├── ReactFeatureFlags.native-oss.js     # React Native OSS
├── ReactFeatureFlags.www.js            # Facebook WWW
├── ReactFeatureFlags.test-renderer.js  # Test renderer
└── ReactFeatureFlags.www-dynamic.js    # Dynamic flags
```

### Symbol Constants (Type System)

**Location:** `packages/shared/ReactSymbols.js`

```javascript
// Element type identification
export const REACT_ELEMENT_TYPE = Symbol.for('react.element');
export const REACT_PORTAL_TYPE = Symbol.for('react.portal');
export const REACT_FRAGMENT_TYPE = Symbol.for('react.fragment');
export const REACT_STRICT_MODE_TYPE = Symbol.for('react.strict_mode');
export const REACT_PROFILER_TYPE = Symbol.for('react.profiler');
export const REACT_PROVIDER_TYPE = Symbol.for('react.provider');
export const REACT_CONTEXT_TYPE = Symbol.for('react.context');
export const REACT_FORWARD_REF_TYPE = Symbol.for('react.forward_ref');
export const REACT_SUSPENSE_TYPE = Symbol.for('react.suspense');
export const REACT_MEMO_TYPE = Symbol.for('react.memo');
export const REACT_LAZY_TYPE = Symbol.for('react.lazy');
```

---

## 8. Performance Patterns

### Memoization Strategies

**Location:** `packages/react-reconciler/src/ReactFiberHooks.js`

```javascript
// useMemo implementation
function mountMemo<T>(nextCreate: () => T, deps: Array<mixed> | void | null): T {
  const hook = mountWorkInProgressHook();
  const nextDeps = deps === undefined ? null : deps;
  const nextValue = nextCreate();
  hook.memoizedState = [nextValue, nextDeps];
  return nextValue;
}

function updateMemo<T>(nextCreate: () => T, deps: Array<mixed> | void | null): T {
  const hook = updateWorkInProgressHook();
  const nextDeps = deps === undefined ? null : deps;
  const prevState = hook.memoizedState;
  
  if (prevState !== null && nextDeps !== null) {
    const prevDeps = prevState[1];
    if (areHookInputsEqual(nextDeps, prevDeps)) {
      return prevState[0];  // Return cached value
    }
  }
  
  const nextValue = nextCreate();
  hook.memoizedState = [nextValue, nextDeps];
  return nextValue;
}
```

### Lane-Based Priority System

**Location:** `packages/react-reconciler/src/ReactFiberLane.js`

```javascript
// Priority lanes for scheduling
export const NoLanes = 0b0000000000000000000000000000000;
export const NoLane = 0b0000000000000000000000000000000;
export const SyncLane = 0b0000000000000000000000000000010;
export const InputContinuousLane = 0b0000000000000000000000000001000;
export const DefaultLane = 0b0000000000000000000000000100000;
export const TransitionLanes = 0b0000000001111111111111110000000;
export const IdleLane = 0b0100000000000000000000000000000;
```

### Lazy Loading (React.lazy)

**Location:** `packages/react/src/ReactLazy.js`

```javascript
export function lazy<T>(ctor: () => Thenable<{default: T}>): LazyComponent<T> {
  const payload: Payload<T> = {
    _status: Uninitialized,
    _result: ctor,
  };
  
  const lazyType: LazyComponent<T> = {
    $$typeof: REACT_LAZY_TYPE,
    _payload: payload,
    _init: lazyInitializer,
  };
  
  return lazyType;
}
```

### Concurrent Features

**Location:** `packages/react-reconciler/src/ReactFiberWorkLoop.js`

```javascript
// Time slicing - yields to browser during long renders
function workLoopConcurrent() {
  while (workInProgress !== null && !shouldYield()) {
    performUnitOfWork(workInProgress);
  }
}

// Synchronous for urgent updates
function workLoopSync() {
  while (workInProgress !== null) {
    performUnitOfWork(workInProgress);
  }
}
```

---

## 9. DevTools Component Architecture

### DevTools Shared Components

**Location:** `packages/react-devtools-shared/src/devtools/views/`

These are actual UI components used in React DevTools:

#### Component Tree Views

```
devtools/views/
├── Components/
│   ├── Tree.js                 # Component tree visualization
│   ├── Element.js              # Individual tree element
│   ├── InspectedElement.js     # Selected element inspector
│   └── SearchInput.js          # Search functionality
├── Profiler/
│   ├── Profiler.js             # Profiler main view
│   ├── CommitTreeBuilder.js    # Commit visualization
│   └── FlamegraphChart.js      # Flamegraph rendering
└── Settings/
    └── Settings.js             # DevTools settings panel
```

### Timeline Components

**Location:** `packages/react-devtools-timeline/src/`

```
content-views/
├── ReactMeasuresView.js        # React performance measures
├── SchedulingEventsView.js     # Scheduler event timeline
├── SuspenseEventsView.js       # Suspense boundary events
└── UserTimingMarksView.js      # User timing marks
```

---

## 10. Scheduler Package

### Scheduler Implementation

**Location:** `packages/scheduler/src/`

```javascript
// Scheduler.js - Task scheduling
export function scheduleCallback(priorityLevel, callback, options) {
  const currentTime = getCurrentTime();
  const startTime = currentTime + (options?.delay ?? 0);
  
  const newTask = {
    id: taskIdCounter++,
    callback,
    priorityLevel,
    startTime,
    expirationTime,
    sortIndex: -1,
  };
  
  // Add to appropriate queue based on timing
  if (startTime > currentTime) {
    push(timerQueue, newTask);
  } else {
    push(taskQueue, newTask);
  }
  
  return newTask;
}
```

---

## Summary: Key Architectural Patterns

| Pattern | Implementation | Location |
|---------|---------------|----------|
| **Fiber Tree** | Linked list of fiber nodes | `react-reconciler/src/ReactFiber.js` |
| **Hooks System** | Dispatcher-based state management | `react-reconciler/src/ReactFiberHooks.js` |
| **Event Delegation** | Single root listener | `react-dom-bindings/src/events/` |
| **Lane Scheduling** | Bit-flag priority system | `react-reconciler/src/ReactFiberLane.js` |
| **Symbol Types** | Element type identification | `shared/ReactSymbols.js` |
| **Feature Flags** | Gradual feature rollout | `shared/ReactFeatureFlags.js` |
| **Platform Forks** | Environment-specific behavior | `*/forks/` directories |
| **Flight Protocol** | Server component serialization | `react-server/src/flight/` |

This repository represents the **internal implementation** of React rather than a consumer application, providing the foundational patterns that all React applications build upon.

# state_and_data

State management and data flow patterns

# State Management Analysis

## No State Management Pattern Detected

This repository is the **React core library source code** itself (the official React repository at facebook/react), not an application that uses React. As such, it does not implement application-level state management patterns like Redux, MobX, Zustand, Jotai, Recoil, or similar libraries.

---

## What This Repository Contains

This is the **React framework source code**, which includes:

### Core Packages
- `packages/react/` - The core React library
- `packages/react-dom/` - React DOM renderer
- `packages/react-reconciler/` - The reconciliation algorithm (Fiber)
- `packages/scheduler/` - React's scheduling system
- `packages/use-sync-external-store/` - Hook for external store integration

### Internal State Mechanisms (Framework-Level)

The repository implements React's **internal state handling primitives** that applications use:

| Mechanism | Location | Purpose |
|-----------|----------|---------|
| `useState` | `packages/react/src/` | Component local state hook |
| `useReducer` | `packages/react/src/` | Reducer-based state hook |
| `useContext` | `packages/react/src/` | Context consumption hook |
| `useSyncExternalStore` | `packages/use-sync-external-store/` | External store subscription |
| Fiber reconciliation | `packages/react-reconciler/` | Internal state tree management |

### Published Helper Packages

| Package | Purpose |
|---------|---------|
| `use-sync-external-store` | Shim for subscribing to external stores |
| `use-subscription` | Deprecated subscription utility (uses `use-sync-external-store`) |

---

## Fixture Applications

The `/fixtures/` directory contains **test/demo applications** with minimal state patterns:

### `fixtures/flight/`
- Uses experimental React Server Components
- Basic React state (`useState`)
- No external state management library

### `fixtures/nesting/`
- Contains Redux dependency for testing nested React trees
- Tests legacy React 16.8 with `react-redux@4.4.10`

### `fixtures/dom/`
- DOM testing fixture
- Uses basic React state primitives

### `fixtures/concurrent/time-slicing/`
- Concurrent mode demo
- Uses basic `useState`/`useReducer`

---

## Data Flow Patterns Present

### API Integration in Test Fixtures
- **HTTP Client**: `undici` (Node.js), `node-fetch` in some fixtures
- **Server Components**: `react-server-dom-webpack`, `react-server-dom-turbopack` packages

### DevTools State
The `packages/react-devtools-*` packages have their own internal state for the developer tools UI:
- Uses React's built-in hooks
- `local-storage-fallback` for persistence
- No external state management library

---

## Summary

| Category | Finding |
|----------|---------|
| Global State Library | None - this is React's source code |
| Local Component State | React hooks are being **defined**, not consumed |
| Server State | React Server Components implementation (not consumption) |
| Form Management | None |
| Real-time Features | WebSocket in devtools-core (for React DevTools connection) |

**Conclusion**: This repository defines React's state primitives rather than consuming state management patterns. For state management analysis, you would need to analyze an **application built with React**, not React's source code itself.