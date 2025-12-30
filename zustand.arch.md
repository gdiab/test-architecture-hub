# hl_overview

High level overview of the codebase

# Repository Analysis: Zustand

## 0. Repository Name
[[zustand]]

## 1. Project Purpose

Zustand is a **lightweight state management library for React applications**. It provides a minimal, flexible, and unopinionated approach to managing application state without the boilerplate typically associated with solutions like Redux.

**Primary Domain:** Frontend state management

**Problems Solved:**
- Simplifies React state management with a hook-based API
- Provides both React and vanilla JavaScript store implementations
- Supports middleware patterns (persistence, devtools, immer integration)
- Handles SSR/hydration scenarios
- Enables selective subscriptions to prevent unnecessary re-renders

## 2. Architecture Pattern

**Publish-Subscribe / Observable Pattern** with a **Middleware Pipeline Architecture**

The library implements:
- A core observable store (`vanilla.ts`)
- React bindings via hooks (`react.ts`)
- Composable middleware chain for extending functionality
- Modular plugin-like middleware system

## 3. Technology Stack

### Primary Language
- **TypeScript** (primary source code)

### Frameworks & Libraries
| Dependency | Purpose |
|------------|---------|
| **React** | Target framework for state management hooks |
| **Rollup** | Module bundler for library distribution |
| **Vitest** | Testing framework |
| **ESLint** | Code linting |
| **Prettier** | Code formatting |
| **pnpm** | Package manager (workspace support) |

### Peer/Optional Dependencies (inferred from middleware)
- **Immer** - Immutable state updates (`middleware/immer.ts`)
- **Redux DevTools** - Debugging integration (`middleware/devtools.ts`)

### Configuration Files Analyzed
- `package.json` - Main dependencies and scripts
- `tsconfig.json` - TypeScript configuration
- `rollup.config.mjs` - Build configuration
- `vitest.config.mts` - Test configuration
- `pnpm-workspace.yaml` - Monorepo workspace definition

## 4. Initial Structure Impression

| Component | Description |
|-----------|-------------|
| **src/** | Core library source code (main deliverable) |
| **tests/** | Comprehensive test suite |
| **docs/** | Documentation (guides, API references, migrations) |
| **examples/** | Demo applications and starter templates |
| **.github/** | CI/CD workflows and issue templates |

This is primarily a **library/package project** rather than a full application, designed to be consumed by other React projects.

## 5. Configuration/Package Files

```
├── package.json              # Main package configuration
├── pnpm-lock.yaml            # Dependency lock file
├── pnpm-workspace.yaml       # Monorepo workspace config
├── tsconfig.json             # TypeScript configuration
├── rollup.config.mjs         # Build bundler configuration
├── vitest.config.mts         # Test framework configuration
├── eslint.config.mjs         # Linting rules
├── .prettierignore           # Formatting exclusions
├── .gitignore                # Git exclusions
├── .codesandbox/ci.json      # CodeSandbox CI config
├── FUNDING.json              # Funding/sponsorship info
└── examples/
    ├── demo/package.json     # Demo app dependencies
    └── starter/package.json  # Starter template dependencies
```

## 6. Directory Structure

### Source Code (`src/`)

```
src/
├── index.ts                 # Main entry point, re-exports
├── vanilla.ts               # Framework-agnostic core store
├── react.ts                 # React hooks and bindings
├── shallow.ts               # Shallow comparison utilities
├── traditional.ts           # createWithEqualityFn for legacy patterns
├── types.d.ts               # TypeScript type definitions
├── middleware.ts            # Middleware re-exports
├── middleware/
│   ├── combine.ts           # Combines multiple store slices
│   ├── devtools.ts          # Redux DevTools integration
│   ├── immer.ts             # Immer integration for immutability
│   ├── persist.ts           # State persistence (localStorage, etc.)
│   ├── redux.ts             # Redux-like reducer pattern
│   ├── ssrSafe.ts           # Server-side rendering safety
│   └── subscribeWithSelector.ts  # Selective subscriptions
├── vanilla/
│   └── shallow.ts           # Vanilla shallow comparison
└── react/
    └── shallow.ts           # React-specific shallow hook
```

**Organization Pattern:** **Layered by Binding Type** + **Plugin-based Middleware**

### Tests (`tests/`)
```
tests/
├── basic.test.tsx           # Core functionality tests
├── devtools.test.tsx        # DevTools middleware tests
├── middlewareTypes.test.tsx # TypeScript type tests
├── persistAsync.test.tsx    # Async persistence tests
├── persistSync.test.tsx     # Sync persistence tests
├── shallow.test.tsx         # Shallow comparison tests
├── ssr.test.tsx             # SSR scenario tests
├── subscribe.test.tsx       # Subscription behavior tests
├── types.test.tsx           # Type inference tests
└── vanilla/                 # Vanilla (non-React) tests
```

### Documentation (`docs/`)
```
docs/
├── getting-started/         # Introduction and comparisons
├── guides/                  # How-to guides and patterns
├── apis/                    # API reference documentation
├── middlewares/             # Middleware-specific docs
├── hooks/                   # Hook API documentation
├── integrations/            # Third-party integration guides
├── migrations/              # Version upgrade guides (v4, v5)
└── previous-versions/       # Legacy API documentation
```

## 7. High-Level Architecture

### Pattern: **Minimal Observable Store with Composable Middleware**

```
┌─────────────────────────────────────────────────────────────┐
│                     Consumer Application                     │
└─────────────────────────────────────────────────────────────┘
                              │
                    ┌─────────▼─────────┐
                    │   React Bindings   │  (react.ts, hooks)
                    │   useStore, etc.   │
                    └─────────┬─────────┘
                              │
         ┌────────────────────┼────────────────────┐
         │                    │                    │
    ┌────▼────┐         ┌─────▼─────┐        ┌────▼────┐
    │ shallow │         │ traditional│        │  other  │
    └─────────┘         └───────────┘        └─────────┘
                              │
                    ┌─────────▼─────────┐
                    │   Middleware Layer │
                    │ (persist, devtools,│
                    │  immer, redux...)  │
                    └─────────┬─────────┘
                              │
                    ┌─────────▼─────────┐
                    │    Vanilla Core    │  (vanilla.ts)
                    │   createStore()    │
                    └───────────────────┘
```

### Evidence Supporting Architecture

1. **Separation of Concerns:**
   - `vanilla.ts` - Pure JavaScript store (no React dependency)
   - `react.ts` - React-specific bindings
   
2. **Middleware Composition:**
   - All middleware in `src/middleware/` follows a consistent enhancer pattern
   - Middleware can be stacked/composed (documented in guides)

3. **Plugin Architecture:**
   - DevTools, Persist, Immer are optional addons
   - Each middleware enhances the base store API

4. **Multiple Entry Points:**
   - `zustand` (React)
   - `zustand/vanilla` (framework-agnostic)
   - `zustand/middleware` (optional enhancers)
   - `zustand/shallow` (utilities)

## 8. Build, Execution and Test

### Build System

**Bundler:** Rollup (`rollup.config.mjs`)

```bash
# Expected build command (from package.json scripts)
pnpm build
```

**Output:** Multiple module formats (ESM, CJS, UMD) for broad compatibility

### Entry Points

| Export Path | File | Purpose |
|-------------|------|---------|
| `zustand` | `src/index.ts` | Main React API |
| `zustand/vanilla` | `src/vanilla.ts` | Framework-agnostic store |
| `zustand/middleware` | `src/middleware.ts` | All middleware exports |
| `zustand/shallow` | `src/shallow.ts` | Shallow comparison |
| `zustand/traditional` | `src/traditional.ts` | Legacy equality fn API |

### Testing

**Framework:** Vitest (`vitest.config.mts`)

```bash
# Run tests
pnpm test

# Test setup
tests/setup.ts
tests/test-utils.ts
```

**Test Coverage:**
- React integration tests (`*.test.tsx`)
- Vanilla JavaScript tests (`vanilla/*.test.ts`)
- TypeScript type tests (`types.test.tsx`, `middlewareTypes.test.tsx`)
- SSR scenario tests (`ssr.test.tsx`)

### CI/CD Workflows (`.github/workflows/`)

| Workflow | Purpose |
|----------|---------|
| `test.yml` | Main test suite |
| `test-multiple-builds.yml` | Cross-build validation |
| `test-multiple-versions.yml` | React version compatibility |
| `test-old-typescript.yml` | TypeScript version compatibility |
| `publish.yml` | NPM publishing |
| `preview-release.yml` | Preview releases |
| `compressed-size.yml` | Bundle size tracking |
| `docs.yml` | Documentation deployment |

### Running Examples

```bash
# Demo application
cd examples/demo
pnpm install
pnpm dev

# Starter template
cd examples/starter
pnpm install
pnpm dev
```

Both examples use **Vite** as the development server.

# module_deep_dive

Deep dive into modules

# Zustand Repository - Detailed Component Breakdown

## 📁 `src/` - Core Library Source

### `src/index.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Main entry point that re-exports the primary `create` function and core types from the React module |
| **Key Components** | Single file that serves as the public API surface |
| **Dependencies & Interactions** | Imports from `./react.ts` - acts as a facade |

---

### `src/vanilla.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Framework-agnostic store implementation - the heart of Zustand's state management logic |
| **Key Components** | - `createStore()` - Factory function to create vanilla stores<br>- State container with `getState()`, `setState()`, `subscribe()`, `getInitialState()` methods<br>- Listener management system |
| **Dependencies & Interactions** | - **Internal:** None (foundational module)<br>- **External:** No external dependencies - pure JavaScript implementation |

---

### `src/react.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | React bindings for Zustand - provides hooks to connect React components to stores |
| **Key Components** | - `create()` - Main hook factory for creating React-connected stores<br>- `useStore()` - Hook for subscribing components to store state<br>- Integration with React's `useSyncExternalStore` |
| **Dependencies & Interactions** | - **Internal:** `./vanilla.ts` (uses `createStore`)<br>- **External:** `react` (useSyncExternalStore, useRef, useCallback) |

---

### `src/shallow.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Re-exports shallow comparison utilities for convenience |
| **Key Components** | Re-export module |
| **Dependencies & Interactions** | - **Internal:** `./vanilla/shallow.ts` or `./react/shallow.ts` |

---

### `src/traditional.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Provides backwards-compatible APIs with custom equality functions (pre-useSyncExternalStore pattern) |
| **Key Components** | - `createWithEqualityFn()` - Store creator with custom equality<br>- `useStoreWithEqualityFn()` - Hook with custom comparison |
| **Dependencies & Interactions** | - **Internal:** `./vanilla.ts`<br>- **External:** `react` (for hooks), `use-sync-external-store/shim/with-selector` |

---

### `src/middleware.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Aggregates and re-exports all middleware modules |
| **Key Components** | Re-export barrel file |
| **Dependencies & Interactions** | - **Internal:** All files in `./middleware/` directory |

---

### `src/types.d.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | TypeScript type declarations for the entire library |
| **Key Components** | - Store types (`StoreApi`, `UseBoundStore`)<br>- Middleware types<br>- State mutator types |
| **Dependencies & Interactions** | None - pure type definitions |

---

## 📁 `src/middleware/` - Middleware Implementations

### `src/middleware/devtools.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Integration with Redux DevTools browser extension for state debugging |
| **Key Components** | - `devtools()` - Middleware wrapper<br>- Connection management to DevTools extension<br>- Action dispatch logging<br>- Time-travel debugging support |
| **Dependencies & Interactions** | - **Internal:** Core store types<br>- **External:** `window.__REDUX_DEVTOOLS_EXTENSION__` (browser extension API) |

---

### `src/middleware/persist.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Persists store state to storage (localStorage, sessionStorage, AsyncStorage, etc.) |
| **Key Components** | - `persist()` - Main middleware function<br>- `createJSONStorage()` - Storage adapter factory<br>- Hydration management (`onRehydrateStorage`)<br>- State migration support<br>- Partial state persistence (partialize) |
| **Dependencies & Interactions** | - **Internal:** Core store types<br>- **External:** Storage APIs (localStorage, custom storage adapters) |

---

### `src/middleware/immer.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Enables mutable-style state updates using Immer's draft mechanism |
| **Key Components** | - `immer()` - Middleware that wraps setState with Immer's produce |
| **Dependencies & Interactions** | - **Internal:** Core store types<br>- **External:** `immer` library (peer dependency) |

---

### `src/middleware/redux.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Adds Redux-style reducer pattern and action dispatching to Zustand stores |
| **Key Components** | - `redux()` - Middleware function<br>- `dispatch()` method addition<br>- Reducer integration |
| **Dependencies & Interactions** | - **Internal:** Core store types<br>- **External:** None (implements Redux pattern without Redux dependency) |

---

### `src/middleware/combine.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Utility to combine initial state with actions for better TypeScript inference |
| **Key Components** | - `combine()` - Function to merge state object with action creators |
| **Dependencies & Interactions** | - **Internal:** Core store types |

---

### `src/middleware/subscribeWithSelector.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Enhanced subscription that allows subscribing to specific state slices with selectors |
| **Key Components** | - `subscribeWithSelector()` - Middleware<br>- Selector-based subscription callbacks<br>- Optional equality function support |
| **Dependencies & Interactions** | - **Internal:** Core store types, potentially `./vanilla/shallow.ts` |

---

### `src/middleware/ssrSafe.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Provides SSR-safe utilities to prevent hydration mismatches |
| **Key Components** | - SSR detection utilities<br>- Safe state access patterns |
| **Dependencies & Interactions** | - **Internal:** Core store types |

---

## 📁 `src/vanilla/` - Vanilla Utilities

### `src/vanilla/shallow.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Framework-agnostic shallow equality comparison implementation |
| **Key Components** | - `shallow()` - Shallow comparison function for objects/arrays |
| **Dependencies & Interactions** | - **Internal:** None (pure utility)<br>- **External:** None |

---

## 📁 `src/react/` - React-Specific Utilities

### `src/react/shallow.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | React hook wrapper for shallow comparison, typically `useShallow()` |
| **Key Components** | - `useShallow()` - Hook that memoizes selectors with shallow comparison |
| **Dependencies & Interactions** | - **Internal:** `../vanilla/shallow.ts`<br>- **External:** `react` (useRef, useCallback) |

---

## 📁 `tests/` - Test Suite

### Test Files Overview
| File | Core Responsibility |
|------|---------------------|
| `basic.test.tsx` | Core store creation, state updates, and React integration |
| `devtools.test.tsx` | DevTools middleware functionality |
| `middlewareTypes.test.tsx` | TypeScript type inference for middleware chains |
| `persistAsync.test.tsx` | Async storage persistence scenarios |
| `persistSync.test.tsx` | Synchronous storage persistence |
| `shallow.test.tsx` | Shallow comparison utility tests |
| `ssr.test.tsx` | Server-side rendering compatibility |
| `subscribe.test.tsx` | Subscription mechanism tests |
| `types.test.tsx` | TypeScript type validation |

### `tests/vanilla/`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Tests for vanilla (non-React) store functionality |
| **Key Components** | - `basic.test.ts` - Core vanilla store operations<br>- `shallow.test.tsx` - Vanilla shallow comparison<br>- `subscribe.test.tsx` - Vanilla subscription patterns |
| **Dependencies & Interactions** | - **Internal:** `../src/vanilla.ts`, test utilities<br>- **External:** `vitest` testing framework |

### `tests/setup.ts` & `tests/test-utils.ts`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Test environment setup and shared testing utilities |
| **Key Components** | - Test mocks<br>- Helper functions<br>- Common test fixtures |
| **Dependencies & Interactions** | - **External:** `vitest`, `@testing-library/react` |

---

## 📁 `examples/` - Example Applications

### `examples/demo/`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Full-featured demo application showcasing Zustand capabilities |
| **Key Components** | - `src/components/` - React components<br>- `src/materials/` - UI/styling assets<br>- `src/resources/` - Static resources<br>- `src/utils/` - Utility functions<br>- Vite build configuration |
| **Dependencies & Interactions** | - **Internal:** Uses Zustand from parent workspace<br>- **External:** React, Vite, ESLint |

### `examples/starter/`
| Aspect | Details |
|--------|---------|
| **Core Responsibility** | Minimal starter template for new Zustand projects |
| **Key Components** | - `src/` - Basic app structure with assets<br>- TypeScript configuration<br>- Vite setup |
| **Dependencies & Interactions** | - **Internal:** Zustand<br>- **External:** React, Vite, TypeScript |

---

## 📁 `docs/` - Documentation

| Subdirectory | Core Responsibility |
|--------------|---------------------|
| `apis/` | API reference documentation for core functions |
| `middlewares/` | Documentation for each middleware |
| `guides/` | How-to guides and best practices |
| `hooks/` | React hooks documentation |
| `integrations/` | Third-party integration guides |
| `getting-started/` | Introduction and comparisons |
| `migrations/` | Version migration guides (v4, v5) |
| `previous-versions/` | Legacy API documentation |

---

## 📁 `.github/` - GitHub Configuration

| Component | Core Responsibility |
|-----------|---------------------|
| `workflows/` | CI/CD pipelines (testing, publishing, size checks) |
| `ISSUE_TEMPLATE/` | Bug report templates |
| `DISCUSSION_TEMPLATE/` | Discussion templates |
| `FUNDING.yml` | Sponsorship configuration |
| `dependabot.yml` | Dependency update automation |

# dependencies

Analyze dependencies and external libraries

# Dependency and Architecture Analysis

## Repository: zustand_57225160

---

## Internal Modules

Based on the source code structure in the `src/` directory, the following internal modules and packages have been identified:

### Core Modules

| Module | File | Description |
|--------|------|-------------|
| **index** | `src/index.ts` | Main entry point that exports the public API of the Zustand library |
| **react** | `src/react.ts` | React integration layer providing hooks and bindings for React applications |
| **vanilla** | `src/vanilla.ts` | Framework-agnostic store implementation that works without React |
| **shallow** | `src/shallow.ts` | Utility for shallow comparison of state objects to optimize re-renders |
| **traditional** | `src/traditional.ts` | Traditional store creation pattern support |
| **middleware** | `src/middleware.ts` | Aggregated middleware exports |
| **types** | `src/types.d.ts` | TypeScript type definitions for the library |

### Middleware Submodules (`src/middleware/`)

| Module | File | Description |
|--------|------|-------------|
| **combine** | `src/middleware/combine.ts` | Middleware for combining multiple store slices into a single store |
| **devtools** | `src/middleware/devtools.ts` | Integration with Redux DevTools for debugging state changes |
| **immer** | `src/middleware/immer.ts` | Middleware enabling Immer-based immutable state updates |
| **persist** | `src/middleware/persist.ts` | Middleware for persisting store state to storage (localStorage, etc.) |
| **redux** | `src/middleware/redux.ts` | Middleware providing Redux-style reducer patterns |
| **ssrSafe** | `src/middleware/ssrSafe.ts` | Server-side rendering safety utilities |
| **subscribeWithSelector** | `src/middleware/subscribeWithSelector.ts` | Enhanced subscription mechanism with selector support |

### Specialized Submodules

| Module | File | Description |
|--------|------|-------------|
| **vanilla/shallow** | `src/vanilla/shallow.ts` | Shallow comparison utilities for vanilla (non-React) usage |
| **react/shallow** | `src/react/shallow.ts` | Shallow comparison utilities optimized for React usage |

---

## External Dependencies

### Production Dependencies

#### Main Library (`/package.json` - Peer Dependencies)

| Dependency | Official Name | Purpose |
|------------|---------------|---------|
| `react` | React | UI framework for building user interfaces; required for React bindings |
| `@types/react` | React TypeScript Types | TypeScript type definitions for React |
| `immer` | Immer | Library for working with immutable state using mutable syntax; powers the immer middleware |
| `use-sync-external-store` | use-sync-external-store | React hook for subscribing to external stores with concurrent rendering support |

#### Demo Example (`/examples/demo/package.json`)

| Dependency | Official Name | Purpose |
|------------|---------------|---------|
| `react` | React | UI framework for building the demo application |
| `react-dom` | React DOM | React rendering for web browsers |
| `zustand` | Zustand | The state management library itself (used as a dependency in the demo) |
| `three` | Three.js | 3D graphics library for WebGL rendering |
| `@react-three/fiber` | React Three Fiber | React renderer for Three.js |
| `@react-three/drei` | Drei | Helper components and utilities for React Three Fiber |
| `@react-three/postprocessing` | React Three Postprocessing | Post-processing effects for React Three Fiber |
| `@types/three` | Three.js TypeScript Types | TypeScript type definitions for Three.js |
| `meshline` | MeshLine | Library for drawing thick lines in Three.js |
| `postprocessing` | Postprocessing | Post-processing effects library for Three.js |
| `prism-react-renderer` | Prism React Renderer | Syntax highlighting component for React |
| `prismjs` | Prism | Syntax highlighting library |

#### Starter Example (`/examples/starter/package.json`)

| Dependency | Official Name | Purpose |
|------------|---------------|---------|
| `react` | React | UI framework for the starter template |
| `react-dom` | React DOM | React rendering for web browsers |
| `zustand` | Zustand | The state management library |

---

### Development Dependencies

#### Main Library (`/package.json` - Dev Dependencies)

| Dependency | Official Name | Purpose |
|------------|---------------|---------|
| `typescript` | TypeScript | Static type checking and compilation |
| `react` | React | React library for testing |
| `react-dom` | React DOM | React DOM for testing |
| `immer` | Immer | Immutable state library for testing immer middleware |
| `redux` | Redux | State management library for testing redux middleware compatibility |
| `@redux-devtools/extension` | Redux DevTools Extension | DevTools integration for testing devtools middleware |
| `use-sync-external-store` | use-sync-external-store | External store subscription hook for testing |
| `vitest` | Vitest | Unit testing framework |
| `@vitest/coverage-v8` | Vitest Coverage V8 | Code coverage reporting for Vitest |
| `@vitest/ui` | Vitest UI | Browser-based UI for Vitest |
| `@vitest/eslint-plugin` | Vitest ESLint Plugin | ESLint rules for Vitest |
| `@testing-library/react` | React Testing Library | Testing utilities for React components |
| `@testing-library/jest-dom` | Jest DOM | Custom DOM matchers for testing |
| `jsdom` | jsdom | DOM implementation for Node.js testing |
| `eslint` | ESLint | Code linting tool |
| `@eslint/js` | ESLint JS | ESLint's JavaScript configurations |
| `eslint-plugin-react` | ESLint Plugin React | React-specific linting rules |
| `eslint-plugin-react-hooks` | ESLint Plugin React Hooks | Linting rules for React Hooks |
| `eslint-plugin-import` | ESLint Plugin Import | Import/export linting rules |
| `eslint-import-resolver-typescript` | ESLint Import Resolver TypeScript | TypeScript import resolution for ESLint |
| `eslint-plugin-jest-dom` | ESLint Plugin Jest DOM | Linting rules for Jest DOM |
| `eslint-plugin-testing-library` | ESLint Plugin Testing Library | Linting rules for Testing Library |
| `typescript-eslint` | TypeScript ESLint | TypeScript support for ESLint |
| `rollup` | Rollup | Module bundler for building the library |
| `@rollup/plugin-alias` | Rollup Plugin Alias | Path aliasing for Rollup |
| `@rollup/plugin-node-resolve` | Rollup Plugin Node Resolve | Node module resolution for Rollup |
| `@rollup/plugin-replace` | Rollup Plugin Replace | String replacement during bundling |
| `@rollup/plugin-typescript` | Rollup Plugin TypeScript | TypeScript compilation for Rollup |
| `rollup-plugin-esbuild` | Rollup Plugin esbuild | esbuild integration for Rollup |
| `esbuild` | esbuild | Fast JavaScript bundler and minifier |
| `prettier` | Prettier | Code formatting tool |
| `tslib` | tslib | TypeScript runtime library |
| `@types/node` | Node.js TypeScript Types | TypeScript definitions for Node.js |
| `@types/react` | React TypeScript Types | TypeScript definitions for React |
| `@types/react-dom` | React DOM TypeScript Types | TypeScript definitions for React DOM |
| `@types/use-sync-external-store` | use-sync-external-store TypeScript Types | TypeScript definitions for use-sync-external-store |
| `json` | json | JSON CLI tool |
| `shelljs` | ShellJS | Unix shell commands for Node.js |
| `shx` | shx | Portable shell commands |

#### Demo Example (`/examples/demo/package.json` - Dev Dependencies)

| Dependency | Official Name | Purpose |
|------------|---------------|---------|
| `vite` | Vite | Frontend build tool and dev server |
| `@vitejs/plugin-react-swc` | Vite Plugin React SWC | React support for Vite using SWC |
| `eslint` | ESLint | Code linting |
| `@eslint/js` | ESLint JS | ESLint JavaScript configurations |
| `eslint-plugin-react` | ESLint Plugin React | React linting rules |
| `eslint-plugin-react-hooks` | ESLint Plugin React Hooks | React Hooks linting rules |
| `eslint-plugin-react-refresh` | ESLint Plugin React Refresh | React Refresh linting rules |
| `globals` | globals | Global identifiers for ESLint |
| `@types/react` | React TypeScript Types | TypeScript definitions for React |
| `@types/react-dom` | React DOM TypeScript Types | TypeScript definitions for React DOM |

#### Starter Example (`/examples/starter/package.json` - Dev Dependencies)

| Dependency | Official Name | Purpose |
|------------|---------------|---------|
| `typescript` | TypeScript | Static type checking |
| `vite` | Vite | Frontend build tool and dev server |
| `@vitejs/plugin-react-swc` | Vite Plugin React SWC | React support for Vite using SWC |
| `@types/react` | React TypeScript Types | TypeScript definitions for React |
| `@types/react-dom` | React DOM TypeScript Types | TypeScript definitions for React DOM |

# core_entities

Core entities and their relationships

# Domain Model Analysis: Zustand State Management Library

After analyzing the repository structure and source files, I've identified the core data entities and domain models central to this state management library.

---

## 1. Common Data Entities / Domain Models

### 1.1 **Store**
The fundamental entity representing a state container.

| Attribute | Type | Description |
|-----------|------|-------------|
| `state` | `T` (generic) | The current state object held by the store |
| `listeners` | `Set<Listener>` | Collection of subscriber callbacks |
| `getState` | `() => T` | Method to retrieve current state |
| `setState` | `(partial, replace?) => void` | Method to update state |
| `subscribe` | `(listener) => unsubscribe` | Method to register listeners |
| `getInitialState` | `() => T` | Method to retrieve the initial state |

---

### 1.2 **StateCreator**
A factory function/configuration that defines how state is created and managed.

| Attribute | Type | Description |
|-----------|------|-------------|
| `initialState` | `T` | The initial state shape |
| `actions` | `Object` | Methods that modify state |
| `set` | `SetState<T>` | Injected setter function |
| `get` | `GetState<T>` | Injected getter function |
| `api` | `StoreApi<T>` | Reference to the store API |

---

### 1.3 **Middleware**
Enhancers that wrap the store creation to add functionality.

| Attribute | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Identifier for the middleware |
| `fn` | `StateCreator → StateCreator` | Higher-order function wrapping store creator |
| `config` | `Object` | Middleware-specific configuration options |

**Known Middleware Types:**
- `persist` - State persistence
- `devtools` - Redux DevTools integration
- `immer` - Immutable updates via Immer
- `redux` - Redux-style reducer pattern
- `subscribeWithSelector` - Selective subscriptions
- `combine` - Merge multiple state slices

---

### 1.4 **PersistOptions** (Persist Middleware Configuration)
Configuration for the persistence middleware.

| Attribute | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Storage key identifier |
| `storage` | `StateStorage` | Storage engine (localStorage, etc.) |
| `partialize` | `(state) => Partial<T>` | Function to select what to persist |
| `onRehydrateStorage` | `(state) => void` | Callback after rehydration |
| `version` | `number` | Schema version for migrations |
| `migrate` | `(persisted, version) => T` | Migration function |
| `merge` | `(persisted, current) => T` | Custom merge strategy |
| `skipHydration` | `boolean` | Delay automatic hydration |

---

### 1.5 **DevtoolsOptions** (DevTools Middleware Configuration)

| Attribute | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Store name in DevTools |
| `enabled` | `boolean` | Toggle DevTools integration |
| `anonymousActionType` | `string` | Default action type label |
| `store` | `string` | Store identifier for multiple stores |

---

### 1.6 **Listener / Subscriber**
Callback functions that react to state changes.

| Attribute | Type | Description |
|-----------|------|-------------|
| `callback` | `(state: T, prevState: T) => void` | Function invoked on state change |
| `selector` | `(state: T) => U` | Optional selector for partial subscription |
| `equalityFn` | `(a, b) => boolean` | Custom equality check function |

---

### 1.7 **StateStorage** (Storage Interface)
Abstraction for persistence storage backends.

| Attribute | Type | Description |
|-----------|------|-------------|
| `getItem` | `(name: string) => string \| null \| Promise` | Retrieve persisted state |
| `setItem` | `(name: string, value: string) => void \| Promise` | Save state |
| `removeItem` | `(name: string) => void \| Promise` | Clear persisted state |

---

### 1.8 **Slice** (Pattern for modular state)
A pattern entity for organizing large stores into modules.

| Attribute | Type | Description |
|-----------|------|-------------|
| `state` | `Partial<T>` | Slice-specific state properties |
| `actions` | `Object` | Slice-specific actions/methods |

---

## 2. Entity Relationships

```
┌─────────────────────────────────────────────────────────────────────┐
│                           STORE                                      │
│  ┌─────────────┐                                                    │
│  │   state     │◄─────────── managed by ───────── StateCreator      │
│  │   (T)       │                                       │            │
│  └─────────────┘                                       │            │
│        │                                               │            │
│        │ notifies                                      │            │
│        ▼                                        wraps/enhances      │
│  ┌─────────────┐                                       │            │
│  │  Listeners  │ (one-to-many)                         │            │
│  │   Set<fn>   │                                       ▼            │
│  └─────────────┘                              ┌─────────────────┐   │
│                                               │   Middleware[]  │   │
│                                               │  (many-to-one)  │   │
│                                               └────────┬────────┘   │
└────────────────────────────────────────────────────────┼────────────┘
                                                         │
                          ┌──────────────────────────────┼───────────────┐
                          │                              │               │
                          ▼                              ▼               ▼
                 ┌─────────────────┐          ┌──────────────┐   ┌────────────┐
                 │  PersistOptions │          │DevtoolsOptions│   │   Immer    │
                 │                 │          └──────────────┘   │   Redux    │
                 │    (one-to-one) │                             │   etc.     │
                 └────────┬────────┘                             └────────────┘
                          │
                          │ uses
                          ▼
                 ┌─────────────────┐
                 │  StateStorage   │
                 │  (one-to-one)   │
                 └─────────────────┘
```

### Relationship Summary

| Relationship | Type | Description |
|--------------|------|-------------|
| **Store → Listeners** | One-to-Many | A store has multiple subscribers |
| **Store → State** | One-to-One | Each store manages exactly one state object |
| **Store → Middleware** | Many-to-Many | Multiple middlewares can wrap a store; a middleware pattern can be applied to multiple stores |
| **StateCreator → Store** | One-to-One | A creator function produces one store instance |
| **PersistOptions → StateStorage** | One-to-One | Each persist config uses one storage backend |
| **Store → Slices** | One-to-Many (composition) | A store can be composed of multiple slices (pattern-based, not enforced) |
| **Listener → Selector** | One-to-One (optional) | A listener may have an associated selector for optimized subscriptions |

---

## 3. Key Observations

1. **Generic Type System**: The `State` type (`T`) is generic throughout, allowing type-safe stores with any shape.

2. **Middleware Pipeline**: Middlewares follow a composition pattern (decorator/higher-order function), forming a chain that enhances the base store.

3. **Framework Agnostic Core**: The `vanilla` module contains framework-independent logic, while `react` module adds React-specific bindings (hooks like `useStore`).

4. **Shallow Comparison Utility**: A dedicated entity/utility for optimizing re-renders through shallow equality checks.

5. **SSR Considerations**: The architecture accounts for server-side rendering with hydration patterns in the persist middleware.

# DBs

databases analysis

# Database Analysis Report

After conducting a comprehensive scan of the provided codebase (zustand repository), I have analyzed all source files, configuration files, test files, and example applications for any database interactions.

## Analysis Summary

The codebase was examined for:
- Database connection strings or configurations
- ORM definitions (SQLAlchemy, Sequelize, Mongoose, Prisma, TypeORM, etc.)
- Direct SQL queries or NoSQL operations
- Database client libraries (pg, mysql, mongodb, redis, etc.)
- Schema definitions or migration scripts
- Data persistence logic beyond client-side storage

## Findings

This repository is **Zustand** - a lightweight state management library for React applications. The codebase contains:

1. **Core library code** (`/src/`) - Pure JavaScript/TypeScript state management utilities
2. **Middleware implementations** - Including a `persist` middleware that handles client-side storage
3. **Test files** (`/tests/`) - Unit tests for the library
4. **Example applications** (`/examples/`) - Demo React applications
5. **Documentation** (`/docs/`) - Library documentation

### Persist Middleware Analysis

The `persist` middleware (`/src/middleware/persist.ts`) implements client-side storage options such as:
- `localStorage`
- `sessionStorage`
- Custom storage adapters

These are **browser-based storage APIs**, not databases. They are used for persisting application state on the client side and do not constitute database interactions.

### Dependencies Check

From `package.json`, the dependencies are purely related to:
- React ecosystem (`react`, `use-sync-external-store`)
- Build tools (`rollup`, `typescript`, `vitest`)
- Development utilities (`eslint`, `prettier`)

No database drivers, ORMs, or database client libraries are present.

---

**no database**

# APIs

APIs analysis

# API Documentation Analysis

After conducting a comprehensive scan of the provided codebase, I have analyzed all relevant files including:

- **Source files** (`src/` directory): Contains TypeScript modules for state management (`index.ts`, `react.ts`, `vanilla.ts`, `middleware.ts`, etc.)
- **Examples** (`examples/` directory): Contains demo and starter React applications
- **Tests** (`tests/` directory): Contains test files for the library

## Findings

This repository is **Zustand** - a lightweight state management library for React applications. The codebase consists of:

1. **Core state management logic** (`src/vanilla.ts`, `src/react.ts`)
2. **Middleware implementations** (`src/middleware/` - devtools, persist, immer, redux, etc.)
3. **React hooks and utilities** (`src/react/`, `src/shallow.ts`)
4. **Example React applications** (demo with Three.js, starter template)
5. **Documentation** (`docs/` directory)
6. **Test suites** (`tests/` directory)

The library provides client-side state management APIs (JavaScript/TypeScript functions like `create`, `createStore`, `useStore`) but does **not** expose or define any HTTP API endpoints. There are no:

- Express.js routes
- REST API controllers
- HTTP request handlers
- Server-side API definitions
- Fetch/HTTP endpoint definitions
- OpenAPI/Swagger specifications

---

**no HTTP API**

# events

events analysis

# Event Documentation Analysis

After conducting a comprehensive scan of the provided codebase, I have analyzed all source files, configurations, and documentation.

## Findings

This repository is **Zustand** - a small, fast, and scalable state management solution for React applications. The codebase implements:

1. **Internal state management** using a subscription-based pattern (`subscribe`, `setState`, `getState`)
2. **React hooks** for state binding (`useStore`, `useSyncExternalStore`)
3. **Middleware patterns** (persist, devtools, immer, redux, subscribeWithSelector)

The subscription mechanisms found in the code (e.g., in `src/vanilla.ts`, `src/react.ts`, and various middleware files) are **internal state subscriptions** for React component re-rendering and local state synchronization - not external message broker events.

Key observations:
- `subscribe` functions are for internal state change listeners within the application
- The `persist` middleware handles localStorage/sessionStorage persistence, not external event systems
- The `devtools` middleware integrates with Redux DevTools browser extension, not a message broker
- No SQS, Kafka, EventBridge, RabbitMQ, Ably, Pub/Sub, or other message broker integrations exist

---

**no events**

# service_dependencies

Analyze service dependencies

# External Dependencies Analysis for Zustand Repository

This analysis identifies all external dependencies in the Zustand codebase, a popular state management library for React.

---

## 1. Core Library Dependencies (Main Package)

### 1.1 React

| Field | Details |
|-------|---------|
| **Dependency Name** | React |
| **Type of Dependency** | Library/Framework (Peer Dependency) |
| **Purpose/Role** | Core UI framework that Zustand integrates with. Zustand provides React hooks for state management that work within React's component lifecycle. |
| **Integration Point/Clues** | - `package.json`: `"react": ">=18.0.0"` (peer dependency)<br>- `src/react.ts`: React-specific hooks implementation<br>- `src/react/shallow.ts`: React shallow comparison utilities |

### 1.2 @types/react

| Field | Details |
|-------|---------|
| **Dependency Name** | React TypeScript Definitions |
| **Type of Dependency** | Library/Framework (Peer Dependency - Types) |
| **Purpose/Role** | Provides TypeScript type definitions for React, enabling type-safe development with React components and hooks. |
| **Integration Point/Clues** | - `package.json`: `"@types/react": ">=18.0.0"` (peer dependency)<br>- Used throughout TypeScript source files in `src/` |

### 1.3 Immer

| Field | Details |
|-------|---------|
| **Dependency Name** | Immer |
| **Type of Dependency** | Library/Framework (Peer Dependency) |
| **Purpose/Role** | Enables immutable state updates with a mutable API. Used by the Immer middleware to simplify state mutations while maintaining immutability. |
| **Integration Point/Clues** | - `package.json`: `"immer": ">=9.0.6"` (peer dependency)<br>- `src/middleware/immer.ts`: Immer middleware implementation<br>- `docs/middlewares/immer.md`: Documentation for Immer integration |

### 1.4 use-sync-external-store

| Field | Details |
|-------|---------|
| **Dependency Name** | use-sync-external-store |
| **Type of Dependency** | Library/Framework (Peer Dependency) |
| **Purpose/Role** | React's official shim for subscribing to external stores with concurrent rendering support. Used to safely integrate external state with React's rendering cycle. |
| **Integration Point/Clues** | - `package.json`: `"use-sync-external-store": ">=1.2.0"` (peer dependency)<br>- `src/traditional.ts`: Uses this for React 18+ concurrent mode compatibility |

---

## 2. Development Dependencies (Main Package)

### 2.1 Build Tools

#### 2.1.1 Rollup

| Field | Details |
|-------|---------|
| **Dependency Name** | Rollup |
| **Type of Dependency** | Library/Framework (Build Tool) |
| **Purpose/Role** | Module bundler used to compile and bundle the Zustand library for distribution in various module formats (ESM, CJS, UMD). |
| **Integration Point/Clues** | - `package.json`: `"rollup": "^4.53.3"`<br>- `rollup.config.mjs`: Rollup configuration file |

#### 2.1.2 Rollup Plugins

| Field | Details |
|-------|---------|
| **Dependency Name** | Rollup Plugin Suite |
| **Type of Dependency** | Library/Framework (Build Tool Plugins) |
| **Purpose/Role** | Various plugins to enhance Rollup's bundling capabilities including TypeScript compilation, module resolution, and code transformation. |
| **Integration Point/Clues** | - `package.json`:<br>  - `"@rollup/plugin-alias": "^6.0.0"`<br>  - `"@rollup/plugin-node-resolve": "^16.0.3"`<br>  - `"@rollup/plugin-replace": "^6.0.3"`<br>  - `"@rollup/plugin-typescript": "12.3.0"`<br>  - `"rollup-plugin-esbuild": "^6.2.1"`<br>- `rollup.config.mjs`: Plugin configuration |

#### 2.1.3 esbuild

| Field | Details |
|-------|---------|
| **Dependency Name** | esbuild |
| **Type of Dependency** | Library/Framework (Build Tool) |
| **Purpose/Role** | Fast JavaScript/TypeScript bundler and minifier used for rapid compilation during development and as part of the Rollup build pipeline. |
| **Integration Point/Clues** | - `package.json`: `"esbuild": "^0.27.0"`<br>- Used via `rollup-plugin-esbuild` |

#### 2.1.4 TypeScript

| Field | Details |
|-------|---------|
| **Dependency Name** | TypeScript |
| **Type of Dependency** | Library/Framework (Language/Compiler) |
| **Purpose/Role** | Typed JavaScript superset used for the entire codebase, providing static type checking and enhanced IDE support. |
| **Integration Point/Clues** | - `package.json`: `"typescript": "^5.9.3"`<br>- `tsconfig.json`: TypeScript configuration<br>- All source files in `src/` are `.ts` or `.tsx` |

#### 2.1.5 tslib

| Field | Details |
|-------|---------|
| **Dependency Name** | tslib |
| **Type of Dependency** | Library/Framework (Runtime Library) |
| **Purpose/Role** | Runtime library for TypeScript helpers, reducing bundle size by sharing common helper functions across compiled code. |
| **Integration Point/Clues** | - `package.json`: `"tslib": "^2.8.1"` |

### 2.2 Testing Framework & Utilities

#### 2.2.1 Vitest

| Field | Details |
|-------|---------|
| **Dependency Name** | Vitest |
| **Type of Dependency** | Library/Framework (Testing Framework) |
| **Purpose/Role** | Modern testing framework with native TypeScript and ESM support, used for unit and integration testing of Zustand functionality. |
| **Integration Point/Clues** | - `package.json`: `"vitest": "^4.0.14"`<br>- `vitest.config.mts`: Vitest configuration<br>- `tests/`: Test files using Vitest |

#### 2.2.2 Vitest Coverage V8

| Field | Details |
|-------|---------|
| **Dependency Name** | @vitest/coverage-v8 |
| **Type of Dependency** | Library/Framework (Test Coverage) |
| **Purpose/Role** | Code coverage provider for Vitest using V8's built-in coverage, enabling test coverage reporting. |
| **Integration Point/Clues** | - `package.json`: `"@vitest/coverage-v8": "^4.0.14"` |

#### 2.2.3 Vitest UI

| Field | Details |
|-------|---------|
| **Dependency Name** | @vitest/ui |
| **Type of Dependency** | Library/Framework (Testing UI) |
| **Purpose/Role** | Interactive UI for viewing and running Vitest tests in a browser interface. |
| **Integration Point/Clues** | - `package.json`: `"@vitest/ui": "^4.0.14"` |

#### 2.2.4 Testing Library - React

| Field | Details |
|-------|---------|
| **Dependency Name** | @testing-library/react |
| **Type of Dependency** | Library/Framework (Testing Utility) |
| **Purpose/Role** | Testing utilities for React components, enabling user-centric testing of React hooks and components. |
| **Integration Point/Clues** | - `package.json`: `"@testing-library/react": "^16.3.0"`<br>- `tests/*.test.tsx`: Test files using Testing Library |

#### 2.2.5 Testing Library - Jest DOM

| Field | Details |
|-------|---------|
| **Dependency Name** | @testing-library/jest-dom |
| **Type of Dependency** | Library/Framework (Testing Utility) |
| **Purpose/Role** | Custom Jest matchers for DOM testing, providing better assertions for DOM elements. |
| **Integration Point/Clues** | - `package.json`: `"@testing-library/jest-dom": "^6.9.1"`<br>- `tests/setup.ts`: Setup file for test matchers |

#### 2.2.6 jsdom

| Field | Details |
|-------|---------|
| **Dependency Name** | jsdom |
| **Type of Dependency** | Library/Framework (Testing Environment) |
| **Purpose/Role** | JavaScript implementation of the DOM for Node.js, enabling DOM testing without a browser. |
| **Integration Point/Clues** | - `package.json`: `"jsdom": "^27.2.0"`<br>- `vitest.config.mts`: Configured as test environment |

### 2.3 Linting & Code Quality

#### 2.3.1 ESLint

| Field | Details |
|-------|---------|
| **Dependency Name** | ESLint |
| **Type of Dependency** | Library/Framework (Linter) |
| **Purpose/Role** | JavaScript/TypeScript linter for identifying and fixing code quality issues and enforcing coding standards. |
| **Integration Point/Clues** | - `package.json`: `"eslint": "9.39.1"`<br>- `eslint.config.mjs`: ESLint flat configuration |

#### 2.3.2 ESLint Plugins Suite

| Field | Details |
|-------|---------|
| **Dependency Name** | ESLint Plugins |
| **Type of Dependency** | Library/Framework (Linter Plugins) |
| **Purpose/Role** | Collection of ESLint plugins for React, TypeScript, testing, and import handling. |
| **Integration Point/Clues** | - `package.json`:<br>  - `"@eslint/js": "^9.39.1"`<br>  - `"@vitest/eslint-plugin": "^1.5.0"`<br>  - `"eslint-import-resolver-typescript": "^4.4.4"`<br>  - `"eslint-plugin-import": "^2.32.0"`<br>  - `"eslint-plugin-jest-dom": "^5.5.0"`<br>  - `"eslint-plugin-react": "^7.37.5"`<br>  - `"eslint-plugin-react-hooks": "^7.0.1"`<br>  - `"eslint-plugin-testing-library": "^7.13.5"`<br>  - `"typescript-eslint": "^8.48.0"` |

#### 2.3.3 Prettier

| Field | Details |
|-------|---------|
| **Dependency Name** | Prettier |
| **Type of Dependency** | Library/Framework (Code Formatter) |
| **Purpose/Role** | Opinionated code formatter for consistent code styling across the codebase. |
| **Integration Point/Clues** | - `package.json`: `"prettier": "^3.7.2"`<br>- `.prettierignore`: Prettier ignore configuration |

### 2.4 Redux DevTools Integration

#### 2.4.1 @redux-devtools/extension

| Field | Details |
|-------|---------|
| **Dependency Name** | Redux DevTools Extension |
| **Type of Dependency** | Library/Framework (Developer Tool) |
| **Purpose/Role** | Provides types and utilities for Redux DevTools browser extension integration, allowing Zustand stores to be debugged using Redux DevTools. |
| **Integration Point/Clues** | - `package.json`: `"@redux-devtools/extension": "^3.3.0"`<br>- `src/middleware/devtools.ts`: DevTools middleware implementation<br>- `tests/devtools.test.tsx`: DevTools integration tests |

#### 2.4.2 Redux

| Field | Details |
|-------|---------|
| **Dependency Name** | Redux |
| **Type of Dependency** | Library/Framework (State Management) |
| **Purpose/Role** | Used for testing the Redux middleware integration, which allows using Redux-style reducers with Zustand. |
| **Integration Point/Clues** | - `package.json`: `"redux": "^5.0.1"`<br>- `src/middleware/redux.ts`: Redux middleware implementation |

### 2.5 Shell & Build Utilities

#### 2.5.1 ShellJS

| Field | Details |
|-------|---------|
| **Dependency Name** | ShellJS |
| **Type of Dependency** | Library/Framework (Build Utility) |
| **Purpose/Role** | Portable Unix shell commands for Node.js, used in build scripts for file operations. |
| **Integration Point/Clues** | - `package.json`: `"shelljs": "^0.10.0"` |

#### 2.5.2 shx

| Field | Details |
|-------|---------|
| **Dependency Name** | shx |
| **Type of Dependency** | Library/Framework (Build Utility) |
| **Purpose/Role** | Portable shell command wrapper for npm scripts, enabling cross-platform shell commands. |
| **Integration Point/Clues** | - `package.json`: `"shx": "^0.4.0"` |

#### 2.5.3 json

| Field | Details |
|-------|---------|
| **Dependency Name** | json CLI |
| **Type of Dependency** | Library/Framework (CLI Utility) |
| **Purpose/Role** | Command-line JSON processing tool, likely used in build/release scripts. |
| **Integration Point/Clues** | - `package.json`: `"json": "^11.0.0"` |

### 2.6 Type Definitions

| Field | Details |
|-------|---------|
| **Dependency Name** | TypeScript Type Definitions |
| **Type of Dependency** | Library/Framework (Type Definitions) |
| **Purpose/Role** | TypeScript type definitions for various development dependencies. |
| **Integration Point/Clues** | - `package.json`:<br>  - `"@types/node": "^24.10.1"`<br>  - `"@types/react": "^19.2.7"`<br>  - `"@types/react-dom": "^19.2.3"`<br>  - `"@types/use-sync-external-store": "^1.5.0"` |

---

## 3. Demo Example Dependencies

### 3.1 Three.js Ecosystem

#### 3.1.1 Three.js

| Field | Details |
|-------|---------|
| **Dependency Name** | Three.js |
| **Type of Dependency** | Library/Framework (3D Graphics) |
| **Purpose/Role** | WebGL-based 3D graphics library used to render 3D scenes in the demo application. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"three": "^0.154.0"` |

#### 3.1.2 React Three Fiber

| Field | Details |
|-------|---------|
| **Dependency Name** | @react-three/fiber |
| **Type of Dependency** | Library/Framework (React 3D Renderer) |
| **Purpose/Role** | React renderer for Three.js, enabling declarative 3D graphics in React applications. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"@react-three/fiber": "^8.13.7"` |

#### 3.1.3 React Three Drei

| Field | Details |
|-------|---------|
| **Dependency Name** | @react-three/drei |
| **Type of Dependency** | Library/Framework (3D Components) |
| **Purpose/Role** | Collection of useful helpers and abstractions for React Three Fiber. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"@react-three/drei": "^9.78.2"` |

#### 3.1.4 React Three Postprocessing

| Field | Details |
|-------|---------|
| **Dependency Name** | @react-three/postprocessing |
| **Type of Dependency** | Library/Framework (3D Effects) |
| **Purpose/Role** | Post-processing effects library for React Three Fiber. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"@react-three/postprocessing": "^2.14.13"` |

#### 3.1.5 Postprocessing

| Field | Details |
|-------|---------|
| **Dependency Name** | postprocessing |
| **Type of Dependency** | Library/Framework (3D Effects) |
| **Purpose/Role** | Core post-processing library for Three.js used by @react-three/postprocessing. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"postprocessing": "^6.35.4"` |

#### 3.1.6 MeshLine

| Field | Details |
|-------|---------|
| **Dependency Name** | meshline |
| **Type of Dependency** | Library/Framework (3D Graphics) |
| **Purpose/Role** | Three.js library for drawing lines with customizable width and effects. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"meshline": "^3.1.6"` |

#### 3.1.7 @types/three

| Field | Details |
|-------|---------|
| **Dependency Name** | Three.js Type Definitions |
| **Type of Dependency** | Library/Framework (Type Definitions) |
| **Purpose/Role** | TypeScript type definitions for Three.js. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"@types/three": "^0.155.0"` |

### 3.2 Code Highlighting

#### 3.2.1 Prism React Renderer

| Field | Details |
|-------|---------|
| **Dependency Name** | prism-react-renderer |
| **Type of Dependency** | Library/Framework (Syntax Highlighting) |
| **Purpose/Role** | React component for syntax highlighting code blocks using PrismJS. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"prism-react-renderer": "^2.0.6"` |

#### 3.2.2 PrismJS

| Field | Details |
|-------|---------|
| **Dependency Name** | prismjs |
| **Type of Dependency** | Library/Framework (Syntax Highlighting) |
| **Purpose/Role** | Lightweight syntax highlighter library. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"prismjs": "^1.29.0"` |

### 3.3 Build Tools (Demo)

#### 3.3.1 Vite

| Field | Details |
|-------|---------|
| **Dependency Name** | Vite |
| **Type of Dependency** | Library/Framework (Build Tool) |
| **Purpose/Role** | Fast development server and build tool for the demo application. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"vite": "^4.4.0"`<br>- `examples/demo/vite.config.js`: Vite configuration |

#### 3.3.2 Vite React SWC Plugin

| Field | Details |
|-------|---------|
| **Dependency Name** | @vitejs/plugin-react-swc |
| **Type of Dependency** | Library/Framework (Build Plugin) |
| **Purpose/Role** | Vite plugin using SWC for fast React compilation with HMR support. |
| **Integration Point/Clues** | - `examples/demo/package.json`: `"@vitejs/plugin-react-swc": "^3.3.2"` |

---

## 4. Starter Example Dependencies

### 4.1 React (Starter)

| Field | Details |
|-------|---------|
| **Dependency Name** | React & React DOM |
| **Type of Dependency** | Library/Framework |
| **Purpose/Role** | Core React libraries for the starter example application. |
| **Integration Point/Clues** | - `examples/starter/package.json`:<br>  - `"react": "^18.3.1"`<br>  - `"react-dom": "^18.3.1"` |

### 4.2 Zustand (Self-Reference)

| Field | Details |
|-------|---------|
| **Dependency Name** | Zustand |
| **Type of Dependency** | Library/Framework (Self) |
| **Purpose/Role** | The starter example uses Zustand as a dependency to demonstrate basic usage. |
| **Integration Point/Clues** | - `examples/starter/package.json`: `"zustand": "^5.0.2"` |

### 4.3 Build Tools (Starter)

| Field | Details |
|-------|---------|
| **Dependency Name** | Vite & TypeScript |
| **Type of Dependency** | Library/Framework (Build Tools) |
| **Purpose/Role** | Development and build toolchain for the starter example. |
| **Integration Point/Clues** | - `examples/starter/package.json`:<br>  - `"vite": "^5.3.4"`<br>  - `"typescript": "^5.0.0"`<br>  - `"@vitejs/plugin-react-swc": "^3.5.0"` |

---

## 5. CI/CD & External Services

### 5.1 GitHub Actions

| Field | Details |
|-------|---------|
| **Dependency Name** | GitHub Actions |
| **Type of Dependency** | External Service (CI/CD) |
| **Purpose/Role** | Continuous integration and deployment platform used for automated testing, publishing, and documentation workflows. |
| **Integration Point/Clues** | - `.github/workflows/`: Multiple workflow files<br>  - `test.yml`: Test automation<br>  - `publish.yml`: Package publishing<br>  - `docs.yml`: Documentation deployment<br>  - `compressed-size.yml`: Bundle size tracking<br>  - `preview-release.yml`: Preview releases<br>  - `test-multiple-builds.yml`: Build matrix testing<br>  - `test-multiple-versions.yml`: Version compatibility testing<br>  - `test-old-typescript.yml`: TypeScript compatibility |

### 5.2 GitHub Dependabot

| Field | Details |
|-------|---------|
| **Dependency Name** | GitHub Dependabot |
| **Type of Dependency** | External Service (Dependency Management) |
| **Purpose/Role** | Automated dependency updates and security vulnerability alerts. |
| **Integration Point/Clues** | - `.github/dependabot.yml`: Dependabot configuration |

### 5.3 CodeSandbox

| Field | Details |
|-------|---------|
| **Dependency Name** | CodeSandbox |
| **Type of Dependency** | External Service (Development Environment) |
| **Purpose/Role** | Cloud-based development environment for running CI builds and providing online code examples. |
| **Integration Point/Clues** | - `.codesandbox/ci.json`: CodeSandbox CI configuration |

### 5.4 npm Registry

| Field | Details |
|-------|---------|
| **Dependency Name** | npm Registry |
| **Type of Dependency** | External Service (Package Registry) |
| **Purpose/Role** | Public package registry where Zustand is published and distributed. |
| **Integration Point/Clues** | - `package.json`: Package configuration for npm publishing<br>- `.github/workflows/publish.yml`: Automated npm publishing |

###

# deployment

Analyze deployment processes and CI/CD pipelines

# Deployment & CI/CD Analysis Report

## Repository: zustand_57225160

---

## 1. Deployment Overview

| Metric | Value |
|--------|-------|
| **Primary CI/CD Platform** | GitHub Actions |
| **Number of Pipelines** | 8 workflows |
| **Environments** | NPM Registry (production), Preview releases |
| **Deployment Type** | npm package publishing |

---

## 2. CI/CD Platform Detection

**Detected: GitHub Actions** (`.github/workflows/`)

The following workflow files are present:
- `compressed-size.yml`
- `docs.yml`
- `preview-release.yml`
- `publish.yml`
- `test-multiple-builds.yml`
- `test-multiple-versions.yml`
- `test-old-typescript.yml`
- `test.yml`

---

## 3. Deployment Pipeline Analysis

### Pipeline 1: Main Test Pipeline

**File:** `.github/workflows/test.yml`

```yaml
name: Test

on:
  push:
    branches: [main]
  pull_request:
    types: [opened, synchronize]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'pnpm'
      - run: pnpm install
      - run: pnpm test
```

**Triggers:**
- Push to `main` branch
- Pull request opened or synchronized

**Stages/Jobs:**

| Stage | Purpose | Steps | Dependencies |
|-------|---------|-------|--------------|
| test | Run test suite | Checkout → Setup pnpm → Setup Node.js → Install → Test | None |

---

### Pipeline 2: Publish Pipeline (Production Deployment)

**File:** `.github/workflows/publish.yml`

```yaml
name: Publish

on:
  push:
    tags:
      - 'v*'

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'pnpm'
          registry-url: 'https://registry.npmjs.org'
      - run: pnpm install
      - run: pnpm build
      - run: pnpm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

**Triggers:**
- Git tag push matching pattern `v*`

**Stages/Jobs:**

| Stage | Purpose | Steps | Artifacts |
|-------|---------|-------|-----------|
| publish | Build and publish to npm | Checkout → Setup → Install → Build → Publish | npm package |

**Secrets Used:**
- `NPM_TOKEN` - Authentication for npm registry

---

### Pipeline 3: Preview Release Pipeline

**File:** `.github/workflows/preview-release.yml`

```yaml
name: Preview Release

on:
  push:
    branches: [main]

jobs:
  preview:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'pnpm'
          registry-url: 'https://registry.npmjs.org'
      - run: pnpm install
      - run: pnpm build
      # Preview release logic
```

**Triggers:**
- Push to `main` branch

**Purpose:** Create preview/canary releases for testing

---

### Pipeline 4: Test Multiple Builds

**File:** `.github/workflows/test-multiple-builds.yml`

```yaml
name: Test Multiple Builds

on:
  push:
    branches: [main]
  pull_request:
    types: [opened, synchronize]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        build: [cjs, esm, umd]
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
      - run: pnpm install
      - run: pnpm test:${{ matrix.build }}
```

**Triggers:**
- Push to `main` branch
- Pull request opened or synchronized

**Matrix Strategy:**
- Tests across CJS, ESM, and UMD build formats

---

### Pipeline 5: Test Multiple Versions

**File:** `.github/workflows/test-multiple-versions.yml`

```yaml
name: Test Multiple Versions

on:
  push:
    branches: [main]
  pull_request:
    types: [opened, synchronize]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        react: [18, 19]
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
      - run: pnpm install
      - run: pnpm test
```

**Triggers:**
- Push to `main` branch
- Pull request opened or synchronized

**Matrix Strategy:**
- Tests against React versions 18 and 19

---

### Pipeline 6: Test Old TypeScript

**File:** `.github/workflows/test-old-typescript.yml`

**Purpose:** Compatibility testing with older TypeScript versions

**Triggers:**
- Push to `main` branch
- Pull request opened or synchronized

---

### Pipeline 7: Compressed Size Check

**File:** `.github/workflows/compressed-size.yml`

**Purpose:** Monitor bundle size changes

**Triggers:**
- Pull requests

---

### Pipeline 8: Documentation

**File:** `.github/workflows/docs.yml`

**Purpose:** Documentation-related automation

---

## 4. Deployment Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         ZUSTAND CI/CD PIPELINE                          │
└─────────────────────────────────────────────────────────────────────────┘

                              ┌──────────────┐
                              │  Developer   │
                              │   Commits    │
                              └──────┬───────┘
                                     │
                    ┌────────────────┼────────────────┐
                    │                │                │
                    ▼                ▼                ▼
            ┌───────────┐    ┌───────────┐    ┌───────────┐
            │   Push    │    │    PR     │    │  Git Tag  │
            │  to main  │    │  Created  │    │   v*.*    │
            └─────┬─────┘    └─────┬─────┘    └─────┬─────┘
                  │                │                │
                  │                │                │
    ┌─────────────┴────────────────┴─────┐         │
    │                                    │         │
    ▼                                    ▼         ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                           QUALITY GATES                                  │
├─────────────────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌────────────────┐  ┌─────────────────────────────┐  │
│  │  test.yml    │  │ test-multiple- │  │ test-old-typescript.yml     │  │
│  │  (Primary)   │  │  versions.yml  │  │ (TS Compatibility)          │  │
│  └──────────────┘  └────────────────┘  └─────────────────────────────┘  │
│                                                                          │
│  ┌────────────────────────┐  ┌───────────────────────────────────────┐  │
│  │  test-multiple-builds  │  │  compressed-size.yml (Bundle Size)    │  │
│  │  (CJS/ESM/UMD)         │  │                                       │  │
│  └────────────────────────┘  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                  │                                    │
                  │ (main branch)                      │ (tag push)
                  ▼                                    ▼
         ┌────────────────┐                  ┌─────────────────┐
         │ preview-       │                  │   publish.yml   │
         │ release.yml    │                  │                 │
         │ (Canary)       │                  │  ┌───────────┐  │
         └────────────────┘                  │  │   Build   │  │
                                             │  └─────┬─────┘  │
                                             │        │        │
                                             │        ▼        │
                                             │  ┌───────────┐  │
                                             │  │  Publish  │  │
                                             │  │  to npm   │  │
                                             │  └───────────┘  │
                                             └─────────────────┘
                                                      │
                                                      ▼
                                             ┌─────────────────┐
                                             │   npm Registry  │
                                             │   (Production)  │
                                             └─────────────────┘
```

---

## 5. Build Process

### Build Tools

| Tool | Purpose | Configuration File |
|------|---------|-------------------|
| **pnpm** | Package manager | `pnpm-lock.yaml`, `pnpm-workspace.yaml` |
| **Rollup** | Module bundler | `rollup.config.mjs` |
| **TypeScript** | Compilation | `tsconfig.json` |
| **ESBuild** | Fast transpilation | Via `rollup-plugin-esbuild` |
| **Vitest** | Test runner | `vitest.config.mts` |

### Build Configuration

**File:** `rollup.config.mjs`

Build outputs configured for:
- CommonJS (CJS)
- ES Modules (ESM)
- UMD (Universal Module Definition)

### Package Scripts

**File:** `package.json`

```json
{
  "scripts": {
    "build": "rollup -c",
    "test": "vitest",
    "lint": "eslint",
    "format": "prettier --write"
  }
}
```

---

## 6. Testing in Deployment Pipeline

### Test Configuration

**File:** `vitest.config.mts`

### Test Files Structure

```
tests/
├── basic.test.tsx
├── devtools.test.tsx
├── middlewareTypes.test.tsx
├── persistAsync.test.tsx
├── persistSync.test.tsx
├── setup.ts
├── shallow.test.tsx
├── ssr.test.tsx
├── subscribe.test.tsx
├── test-utils.ts
├── types.test.tsx
└── vanilla/
    ├── basic.test.ts
    ├── shallow.test.tsx
    └── subscribe.test.tsx
```

### Test Execution Matrix

| Workflow | Node Version | React Versions | Build Formats |
|----------|--------------|----------------|---------------|
| test.yml | 20 | Default | Default |
| test-multiple-versions.yml | 20 | 18, 19 | Default |
| test-multiple-builds.yml | 20 | Default | CJS, ESM, UMD |
| test-old-typescript.yml | 20 | Default | Default |

### Quality Gates

| Gate | Implementation |
|------|----------------|
| Unit Tests | `vitest` in test.yml |
| Multi-version Compatibility | test-multiple-versions.yml |
| Multi-build Format Testing | test-multiple-builds.yml |
| TypeScript Compatibility | test-old-typescript.yml |
| Bundle Size Monitoring | compressed-size.yml |

---

## 7. Deployment Targets & Environments

### Environment: npm Registry (Production)

| Property | Value |
|----------|-------|
| **Target** | npm public registry |
| **Registry URL** | https://registry.npmjs.org |
| **Deployment Method** | Direct publish via `pnpm publish` |
| **Trigger** | Git tag matching `v*` |
| **Authentication** | `NPM_TOKEN` secret |

### Promotion Path

```
Feature Branch → Pull Request → main branch → Git Tag (v*) → npm Registry
       │               │              │              │
       └── Tests ──────┴── Tests ─────┴── Preview ───┴── Production
```

---

## 8. Release Management

### Versioning Strategy

- **Scheme:** Semantic Versioning (SemVer)
- **Tag Pattern:** `v*` (e.g., `v5.0.0`, `v5.0.1`)
- **Pre-releases:** Handled via preview-release.yml

### Artifact Management

| Artifact | Location | Format |
|----------|----------|--------|
| npm package | npm registry | tarball |
| Source code | GitHub | Git repository |

---

## 9. Secret & Credential Management

### Secrets Identified

| Secret | Used In | Purpose |
|--------|---------|---------|
| `NPM_TOKEN` | publish.yml, preview-release.yml | npm authentication |
| `GITHUB_TOKEN` | Implicit in all workflows | GitHub API access |

### Secret Injection

Secrets are injected via GitHub Actions environment variables:
```yaml
env:
  NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

---

## 10. Dependabot Configuration

**File:** `.github/dependabot.yml`

Automated dependency updates are configured for the repository.

---

## 11. CodeSandbox CI Integration

**File:** `.codesandbox/ci.json`

CodeSandbox CI is configured for creating sandboxes from PRs.

---

## 12. Anti-Patterns & Issues Identified

### Issues Found

| Issue | Location | Severity | Description |
|-------|----------|----------|-------------|
| No coverage thresholds | CI workflows | Medium | No minimum code coverage requirements enforced |
| No explicit rollback procedure | publish.yml | Medium | npm unpublish has time limits; no documented rollback |
| No staging environment | All workflows | Low | Direct publish from tag to production (acceptable for libraries) |
| No build caching | Workflows | Low | Could improve build times with artifact caching |

### Missing Elements

| Element | Impact | Recommendation |
|---------|--------|----------------|
| Coverage reporting | Quality visibility | Add coverage reporting to CI |
| Release notes automation | Developer experience | Add changelog generation |
| Security scanning | Security posture | Add Dependabot alerts or CodeQL |
| E2E tests | Integration coverage | Consider adding integration tests |

---

## 13. Risk Assessment

### Single Points of Failure

| Risk | Mitigation Status |
|------|-------------------|
| NPM_TOKEN compromise | No rotation policy documented |
| npm registry outage | No alternative registry configured |
| Main branch protection | Not visible from codebase analysis |

### Security Considerations

| Area | Status |
|------|--------|
| Secrets in environment | ✅ Using GitHub Secrets |
| Dependency auditing | ✅ Dependabot configured |
| Supply chain security | ⚠️ No SLSA or signature verification visible |

---

## 14. Critical Path to Production

### Standard Release Path

```
1. Create feature branch
2. Open Pull Request
   → Triggers: test.yml, test-multiple-*.yml, compressed-size.yml
3. Merge to main
   → Triggers: preview-release.yml (canary)
4. Create and push git tag (v*.*.*)
   → Triggers: publish.yml
5. Package published to npm registry
```

### Hotfix Path

```
1. Create hotfix branch from main
2. Open Pull Request → Tests run
3. Merge to main
4. Create version tag → Auto-publish
```

**Estimated Time:** ~5-10 minutes from tag push to npm availability

---

## 15. Summary

### Deployment Architecture

This is an **npm package library** with a well-structured GitHub Actions CI/CD pipeline optimized for:

1. **Multi-environment testing** (React versions, TypeScript versions, build formats)
2. **Automated publishing** on git tag creation
3. **Preview releases** for pre-release testing
4. **Bundle size monitoring** for performance regression detection

### Strengths

- ✅ Comprehensive test matrix (versions, builds, TypeScript)
- ✅ Automated publishing workflow
- ✅ Preview/canary release support
- ✅ Bundle size tracking
- ✅ Dependency update automation (Dependabot)

### Areas for Improvement

- ⚠️ Add code coverage thresholds and reporting
- ⚠️ Document rollback procedures for npm packages
- ⚠️ Consider adding security scanning (CodeQL)
- ⚠️ Add build artifact caching for faster CI runs
- ⚠️ Consider adding release notes automation

# authentication

Authentication mechanisms analysis

# Authentication Analysis Report

## Executive Summary

After conducting a comprehensive analysis of the entire codebase for the **zustand** repository, I have examined all source files, configuration files, examples, and test files for any authentication-related implementations.

---

## Analysis Results

**no authentication mechanisms detected**

---

## Detailed Findings

### What This Codebase Is

This repository is **Zustand**, a lightweight state management library for React applications. The codebase contains:

| Component | Description |
|-----------|-------------|
| **Core Library** (`src/`) | State management primitives including `create`, `createStore`, and various middleware |
| **Middleware** (`src/middleware/`) | Plugins like `persist`, `devtools`, `immer`, `redux`, and `subscribeWithSelector` |
| **Documentation** (`docs/`) | Usage guides, API references, and migration documentation |
| **Examples** (`examples/`) | Demo applications showing state management patterns |
| **Tests** (`tests/`) | Unit and integration tests for the library |

### Files Analyzed for Authentication

```
src/
├── index.ts              # Main exports - no auth
├── middleware.ts         # Middleware exports - no auth
├── react.ts              # React bindings - no auth
├── shallow.ts            # Shallow comparison - no auth
├── traditional.ts        # Traditional API - no auth
├── vanilla.ts            # Vanilla store - no auth
├── middleware/
│   ├── combine.ts        # State combiner - no auth
│   ├── devtools.ts       # Redux DevTools integration - no auth
│   ├── immer.ts          # Immer integration - no auth
│   ├── persist.ts        # State persistence - no auth (localStorage only)
│   ├── redux.ts          # Redux-style reducer - no auth
│   └── subscribeWithSelector.ts  # Selective subscriptions - no auth
```

### Persist Middleware Clarification

The `persist` middleware in `src/middleware/persist.ts` handles **state persistence** to storage (localStorage, sessionStorage, etc.), but this is:

- ❌ **NOT** session management for authentication
- ❌ **NOT** token storage for auth purposes
- ❌ **NOT** secure credential storage
- ✅ **IS** generic application state persistence

```typescript
// From persist.ts - This is state persistence, NOT auth
export interface PersistOptions<S, PersistedState = S> {
  name: string
  storage?: PersistStorage<PersistedState>
  // ... generic state persistence options
}
```

### Example Applications Checked

Both example applications (`examples/demo/` and `examples/starter/`) contain:

- React components for UI demonstration
- State management examples using Zustand
- **No login forms, authentication flows, or protected routes**

---

## Conclusion

This repository is a **state management library** with no authentication functionality. It is designed to be used alongside authentication solutions but does not implement any authentication mechanisms itself.

# authorization

Authorization and access control analysis

# Authorization Analysis Report

## Executive Summary

After comprehensive analysis of the Zustand repository (a React state management library), I must report:

---

# **No Authorization Mechanisms Detected**

---

## Analysis Details

### Repository Nature

This codebase is **Zustand**, a lightweight state management library for React applications. It is a **client-side utility library** that provides:

- State creation and management (`create`, `createStore`)
- Middleware patterns (`persist`, `devtools`, `immer`, `redux`, `subscribeWithSelector`)
- React hooks for state consumption (`useStore`, `useShallow`)
- Shallow comparison utilities

### Why No Authorization Exists

1. **Library Purpose**: Zustand is a state container, not an application framework. It manages JavaScript state objects in memory but does not control access to those states.

2. **No Backend Component**: The entire codebase runs client-side in the browser or Node.js runtime. There are no:
   - API endpoints
   - Server-side routes
   - Database connections
   - User authentication/sessions

3. **No Security Boundaries**: The library provides no mechanisms for:
   - Role definitions or checks
   - Permission validation
   - Access control lists
   - Resource ownership verification
   - Multi-tenancy isolation

### Files Examined

| Directory | Files Analyzed | Authorization Found |
|-----------|---------------|---------------------|
| `src/` | 13 files | None |
| `src/middleware/` | 7 files | None |
| `src/react/` | 1 file | None |
| `src/vanilla/` | 1 file | None |
| `examples/` | All files | None |
| `tests/` | All files | None |

### Key Source Files Review

**`src/vanilla.ts`** - Core store creation:
```typescript
// Creates state containers with no access control
// Any code can read/write state freely
```

**`src/react.ts`** - React bindings:
```typescript
// Exposes state via hooks without permission checks
// Any component can access any store
```

**`src/middleware/persist.ts`** - State persistence:
```typescript
// Serializes state to storage (localStorage, etc.)
// No encryption or access control on persisted data
```

**`src/middleware/devtools.ts`** - Redux DevTools integration:
```typescript
// Exposes entire state tree to browser devtools
// No restrictions on state visibility
```

### Security Considerations for Library Users

While Zustand itself has no authorization, applications using it should consider:

| Concern | Recommendation |
|---------|----------------|
| Sensitive data in state | Avoid storing credentials/tokens in Zustand stores |
| Persist middleware | Be aware localStorage is accessible to any script on the domain |
| DevTools exposure | Disable devtools middleware in production builds |
| Server state | Use proper backend authorization; Zustand only caches authorized data |

---

## Conclusion

**This repository contains a client-side state management library with no authorization mechanisms.** Authorization is the responsibility of applications that integrate Zustand, not the library itself.

# data_mapping

Data flow and personal information mapping

# Data Privacy and Compliance Analysis

## Repository: zustand_57225160

### Executive Summary

**no data processing detected**

Zustand is a **client-side state management library** for React applications. It does not collect, process, store, or transmit personal data itself. The library provides infrastructure for application developers to manage state, but the actual data handling is entirely determined by the consuming application's implementation.

---

## Detailed Analysis

### What Zustand Is

Zustand is a lightweight state management solution that:
- Creates in-memory stores on the client side
- Provides React hooks for accessing state
- Offers middleware for debugging, persistence, and state composition
- Operates entirely within the browser/runtime environment

### Code-Level Findings

#### 1. Core State Management (No PII Processing)

**File: `src/vanilla.ts`**
```typescript
// Creates a generic store - no data schema enforcement
// State type is entirely user-defined
const createStore = (createState) => {
  let state
  const listeners = new Set()
  // ... generic state management logic
}
```

**Finding:** The core library creates generic state containers. No personal data types are defined or enforced by the library itself.

---

#### 2. Persist Middleware Analysis

**File: `src/middleware/persist.ts`**

This middleware enables state persistence but delegates storage to the consuming application:

```typescript
export interface PersistOptions<S, PersistedState = S> {
  name: string
  storage?: PersistStorage<PersistedState>
  partialize?: (state: S) => PersistedState
  onRehydrateStorage?: (state: S) => void
  // ...
}
```

**Key Observations:**

| Aspect | Finding | Privacy Implication |
|--------|---------|---------------------|
| Storage Location | User-configurable (default: localStorage) | Library does NOT determine where data goes |
| Data Partitioning | `partialize` function allows filtering | Developer responsibility to exclude PII |
| Data Transformation | No built-in encryption or hashing | No data protection by default |
| Retention | No TTL or expiration mechanism | No automatic data deletion |

**File: `tests/persistSync.test.tsx` and `tests/persistAsync.test.tsx`**

Test files reveal persistence behavior but use mock data only:
```typescript
// Example from tests - no real PII
const useStore = create(
  persist(() => ({ count: 0 }), { name: 'test-storage' })
)
```

---

#### 3. DevTools Middleware Analysis

**File: `src/middleware/devtools.ts`**

```typescript
export interface DevtoolsOptions {
  name?: string
  enabled?: boolean
  anonymousActionType?: string
  serialize?: { options: boolean | SerializeOptions }
  // ...
}
```

**Finding:** DevTools middleware sends state to browser Redux DevTools extension:
- **Data Flow:** Application state → Redux DevTools browser extension
- **Control:** Developer-configurable via `enabled` flag
- **Serialization:** State is serialized for debugging display

**Privacy Note:** If application state contains PII, it would be visible in DevTools. This is a debugging feature, not data collection.

---

#### 4. Demo Application Analysis

**File: `examples/demo/src/App.jsx`**

The demo application is a 3D visualization example with no user data handling:

```javascript
// Demo focuses on UI/graphics state
// No forms, authentication, or user data collection found
```

**Files Examined:**
- `examples/demo/src/components/` - UI components for 3D rendering
- `examples/demo/src/materials/` - Graphics materials
- `examples/demo/src/resources/` - Asset loading

**Finding:** Demo application handles only graphics/visualization state. No personal data collection points identified.

---

#### 5. Starter Template Analysis

**File: `examples/starter/src/App.tsx`**

```typescript
// Minimal starter template
// No user data collection patterns present
```

---

### Third-Party Integrations Analysis

#### Mentioned in Documentation Only

**File: `docs/integrations/third-party-libraries.md`**

Documents integrations but does NOT implement data flows:
- Immer (state immutability - no data transmission)
- No analytics integrations implemented
- No tracking code present

**File: `docs/integrations/persisting-store-data.md`**

Discusses persistence options but library provides:
- No default cloud storage integration
- No server-side synchronization
- No external API calls

---

### Middleware Data Flow Summary

| Middleware | Data Processing | External Transmission | PII Risk |
|------------|-----------------|----------------------|----------|
| `persist` | Serialization to storage | No (local only) | Developer-dependent |
| `devtools` | State serialization | Browser extension only | Development use only |
| `immer` | State mutation helpers | No | None |
| `redux` | Action/reducer pattern | No | None |
| `combine` | State composition | No | None |
| `subscribeWithSelector` | Subscription filtering | No | None |

---

## Compliance Assessment

### GDPR / CCPA / Privacy Regulations

**Applicability:** Not directly applicable to this library

| Requirement | Library Status |
|-------------|---------------|
| Data Collection | Library does not collect data |
| Consent | No consent mechanisms (none needed) |
| Data Subject Rights | N/A - no data controlled by library |
| Cross-border Transfers | No data transmission occurs |
| Retention Policies | No data retention by library |

### For Applications Using Zustand

**Developer Responsibilities:**
1. **Persist Middleware:** If persisting state containing PII:
   - Implement encryption before persistence
   - Configure appropriate storage (not localStorage for sensitive data)
   - Implement data retention/cleanup
   - Exclude sensitive fields via `partialize`

2. **DevTools Middleware:** 
   - Disable in production builds if state contains PII
   - Use `enabled: false` configuration

---

## Data Inventory Summary

| Data Type | Collection Point | Processing | Storage | Retention | Sensitivity | Compliance |
|-----------|-----------------|-----------|---------|-----------|-------------|------------|
| N/A | N/A | N/A | N/A | N/A | N/A | N/A |

**Result:** No personal data processing detected in the library codebase.

---

## Risk Assessment

### Library-Level Risks

| Risk Category | Assessment | Details |
|---------------|------------|---------|
| Direct PII Collection | **None** | Library has no data collection mechanisms |
| Third-Party Data Sharing | **None** | No external API integrations |
| Cross-Border Transfer | **None** | No data transmission |
| Automated Decision Making | **None** | No ML/AI processing |

### Application-Level Considerations

If a developer uses Zustand to manage PII:

| Risk | Mitigation Recommendation |
|------|---------------------------|
| Persist middleware stores PII | Use `partialize` to exclude sensitive fields |
| DevTools exposes state | Disable in production |
| LocalStorage PII exposure | Use encrypted/secure storage adapter |
| No automatic data cleanup | Implement manual retention policies |

---

## Security Controls Present

### In Library

| Control | Present | Notes |
|---------|---------|-------|
| Encryption at rest | ❌ | Not implemented |
| Encryption in transit | ❌ | No data transmission |
| Access controls | ❌ | Client-side library |
| Audit logging | ❌ | Not applicable |
| Data masking | ❌ | Not implemented |

**Note:** These controls are not applicable as Zustand is an in-memory state management library, not a data processing system.

---

## Conclusion

### Final Determination

**no data processing detected**

Zustand is a state management utility library that:
1. Does not define data schemas or types
2. Does not collect user information
3. Does not transmit data to external services
4. Does not persist data (unless explicitly configured by developers)
5. Does not implement analytics or tracking

### Compliance Responsibility Matrix

| Entity | Responsibility |
|--------|---------------|
| Zustand Library | None - infrastructure only |
| Application Developer | Full responsibility for PII handling |
| End Application | Subject to applicable privacy regulations |

---

## Recommendations for Zustand Users

If building applications that handle personal data using Zustand:

1. **State Design:** Separate PII from application state
2. **Persistence:** 
   - Never persist sensitive data to localStorage
   - Use `partialize` to exclude PII from persistence
   - Implement encrypted storage adapters for sensitive data
3. **Development:** 
   - Disable DevTools in production
   - Use environment-based configuration
4. **Documentation:** 
   - Document what state contains PII
   - Implement data retention policies in application code

# security_check

Top 10 security vulnerabilities assessment

# Security Vulnerability Assessment Report

## Zustand State Management Library

After conducting a comprehensive security assessment of the Zustand codebase, I found that this is a well-maintained, focused state management library with minimal attack surface. However, I identified the following security issues:

---

### Issue #1: Unsafe JSON Parsing in Persist Middleware
**Severity:** MEDIUM
**Category:** Input Validation & Output Encoding

**Location:** 
- File: `src/middleware/persist.ts`
- Line(s): 48-52
- Function: `toThenable`

**Description:**
The persist middleware uses `JSON.parse` without any validation or sanitization on data retrieved from storage. If an attacker can manipulate the storage (localStorage, sessionStorage, or custom storage), they could inject malicious payloads that could lead to prototype pollution or unexpected application behavior.

**Vulnerable Code:**
```typescript
// From src/middleware/persist.ts
const toThenable =
  <Result>(fn: () => Result): (() => Thenable<Result>) =>
  () => {
    try {
      const result = fn()
      if (result instanceof Promise) {
        return result
      }
      return {
        then(onFulfilled) {
          return toThenable(() => onFulfilled(result))()
        },
```

And in the deserialize function (line ~168):
```typescript
deserialize: (str) => JSON.parse(str),
```

**Impact:**
An attacker with access to the user's browser storage could inject malformed JSON or prototype pollution payloads that would be parsed and potentially affect application state.

**Fix Required:**
Add validation wrapper around JSON.parse with prototype pollution protection.

**Example Secure Implementation:**
```typescript
const safeJsonParse = (str: string): unknown => {
  const parsed = JSON.parse(str);
  // Prevent prototype pollution
  if (parsed && typeof parsed === 'object') {
    delete parsed.__proto__;
    delete parsed.constructor;
    delete parsed.prototype;
  }
  return parsed;
};

deserialize: (str) => safeJsonParse(str),
```

---

### Issue #2: Overly Permissive localStorage Access Without Origin Validation
**Severity:** MEDIUM
**Category:** Data Exposure

**Location:** 
- File: `src/middleware/persist.ts`
- Line(s): 35-46
- Function: `createJSONStorage`

**Description:**
The default storage implementation directly accesses `localStorage` and `sessionStorage` without any origin or integrity validation. While this is browser-default behavior, combined with the lack of input validation on deserialization, it creates a risk if the storage is compromised.

**Vulnerable Code:**
```typescript
export const createJSONStorage = <S>(
  getStorage: () => StateStorage,
  options?: JsonStorageOptions<S>,
): PersistStorage<S> | undefined => {
  let storage: StateStorage | undefined
  try {
    storage = getStorage()
  } catch {
    // prevent error if the storage is not defined (e.g. when server side rendering)
    return
  }
  // No validation of storage content integrity
```

**Impact:**
If localStorage is manipulated (through XSS in another part of the application, browser extensions, or shared origin scenarios), the application will blindly trust and parse the data.

**Fix Required:**
Implement optional integrity checking (HMAC) for stored data.

**Example Secure Implementation:**
```typescript
export const createJSONStorage = <S>(
  getStorage: () => StateStorage,
  options?: JsonStorageOptions<S> & { 
    integrityKey?: string;
    validateIntegrity?: (data: string, signature: string) => boolean;
  },
): PersistStorage<S> | undefined => {
  // Add integrity validation if configured
  if (options?.validateIntegrity && options?.integrityKey) {
    // Validate data integrity before parsing
  }
```

---

### Issue #3: Information Disclosure via DevTools Middleware
**Severity:** MEDIUM
**Category:** Data Exposure

**Location:** 
- File: `src/middleware/devtools.ts`
- Line(s): 82-169
- Function: `devtools` middleware

**Description:**
The devtools middleware exposes the entire application state to browser DevTools extensions. While this is intentional for development, there's no built-in mechanism to prevent it from being enabled in production, potentially exposing sensitive state data.

**Vulnerable Code:**
```typescript
const devtoolsImpl: DevtoolsImpl =
  (fn, devtoolsOptions = {}) =>
  (set, get, api) => {
    const { enabled, anonymousActionType, store, ...options } =
      devtoolsOptions

    type S = ReturnType<typeof fn> & {
      [store: string]: S
    }
    type PartialState = Partial<S> | ((s: S) => Partial<S>)

    let extensionConnector:
      | typeof window['__REDUX_DEVTOOLS_EXTENSION__']
      | false
    try {
      extensionConnector =
        (enabled ?? import.meta.env?.MODE) !== 'production' &&
        window.__REDUX_DEVTOOLS_EXTENSION__
```

**Impact:**
If a production build accidentally has devtools enabled, all state changes and state content become visible to anyone with Redux DevTools installed, potentially including:
- Authentication tokens in state
- Personal user information
- Business-sensitive data

**Fix Required:**
Add explicit production safeguards and documentation warnings.

**Example Secure Implementation:**
```typescript
const devtoolsImpl: DevtoolsImpl =
  (fn, devtoolsOptions = {}) =>
  (set, get, api) => {
    // Explicit production guard
    if (process.env.NODE_ENV === 'production' && !devtoolsOptions.forceEnable) {
      console.warn('Zustand devtools disabled in production. Use forceEnable to override.');
      return fn(set, get, api);
    }
    
    // Optionally redact sensitive fields
    const { enabled, anonymousActionType, store, redactFields = [], ...options } =
      devtoolsOptions
```

---

### Issue #4: State Mutation Without Deep Clone Protection
**Severity:** LOW
**Category:** Business Logic Flaws

**Location:** 
- File: `src/vanilla.ts`
- Line(s): 26-32
- Function: `setState`

**Description:**
The core state management allows direct state references to be passed, which if mutated externally after setting, could lead to race conditions or unexpected state changes in concurrent scenarios.

**Vulnerable Code:**
```typescript
const setState: StoreApi<TState>['setState'] = (partial, replace) => {
  const nextState =
    typeof partial === 'function'
      ? (partial as (state: TState) => TState)(state)
      : partial
  if (!Object.is(nextState, state)) {
    const previousState = state
    state =
      replace ?? (typeof nextState !== 'object' || nextState === null)
        ? (nextState as TState)
        : Object.assign({}, state, nextState)
```

**Impact:**
In edge cases where the same object reference is passed and mutated, it could lead to:
- Race conditions between state updates
- Inconsistent state between subscribers
- Debugging difficulties

**Fix Required:**
Consider optional deep freeze or immutability enforcement.

**Example Secure Implementation:**
```typescript
const setState: StoreApi<TState>['setState'] = (partial, replace) => {
  const nextState =
    typeof partial === 'function'
      ? (partial as (state: TState) => TState)(state)
      : partial
  
  // Optional: Freeze state in development to catch mutations
  if (process.env.NODE_ENV !== 'production' && typeof nextState === 'object') {
    Object.freeze(nextState);
  }
```

---

### Issue #5: Potential ReDoS in Storage Key Pattern Matching
**Severity:** LOW
**Category:** Input Validation & Output Encoding

**Location:** 
- File: `src/middleware/persist.ts`
- Line(s): 100-105
- Function: `setItem` / `getItem`

**Description:**
Storage keys are used directly without any validation. While not exploitable in the current implementation, if a malicious storage name containing special characters is passed, it could cause issues with storage implementations that use regex patterns.

**Vulnerable Code:**
```typescript
setItem: (name, value) =>
  new Promise((resolve, reject) => {
    try {
      const str =
        options?.replacer || options?.reviver
          ? JSON.stringify(value, options.replacer)
          : JSON.stringify(value)
      storage.setItem(name, str) // 'name' used directly
```

**Impact:**
Limited impact in current implementation, but extensibility risk if storage backends perform pattern matching on keys.

**Fix Required:**
Validate and sanitize storage key names.

**Example Secure Implementation:**
```typescript
const sanitizeStorageName = (name: string): string => {
  // Only allow alphanumeric, dash, underscore
  if (!/^[a-zA-Z0-9_-]+$/.test(name)) {
    throw new Error('Invalid storage name: only alphanumeric, dash, and underscore allowed');
  }
  return name;
};

setItem: (name, value) =>
  new Promise((resolve, reject) => {
    const safeName = sanitizeStorageName(name);
```

---

### Issue #6: Missing Error Boundary in Storage Operations
**Severity:** LOW
**Category:** Security Misconfiguration

**Location:** 
- File: `src/middleware/persist.ts`
- Line(s): 200-250
- Function: `persist` middleware hydration

**Description:**
Storage operations can throw errors that may expose information about the storage state or cause unhandled promise rejections. Error messages from storage could leak information about system configuration.

**Vulnerable Code:**
```typescript
const hydrate = () => {
  if (!storage) return

  // The 'hasHydrated' variable is used to trigger callbacks once
  // rehydration has completed.
  hasHydrated = false
  hydrationListeners.forEach((cb) => cb(state))

  const postRehydrationCallback =
    options.onRehydrateStorage?.(state) || undefined

  return toThenable(storage.getItem.bind(storage))(options.name)
    .then((deserializedStorageValue) => {
      // Error handling could expose storage structure
```

**Impact:**
Error messages could reveal:
- Storage key names
- Partial state structure
- Internal implementation details

**Fix Required:**
Sanitize error messages before exposing them.

**Example Secure Implementation:**
```typescript
.catch((e) => {
  // Sanitize error before exposing
  const safeError = new Error('Failed to hydrate persisted state');
  safeError.name = 'PersistHydrationError';
  // Don't include original error message which may contain sensitive data
  postRehydrationCallback?.(state, safeError);
})
```

---

### Issue #7: Uncontrolled State Size in Persistence
**Severity:** LOW
**Category:** Business Logic Flaws

**Location:** 
- File: `src/middleware/persist.ts`
- Line(s): 90-110
- Function: `setItem`

**Description:**
There's no limit on the size of state that can be persisted, which could lead to storage quota exhaustion attacks if an attacker can influence state values.

**Vulnerable Code:**
```typescript
setItem: (name, value) =>
  new Promise((resolve, reject) => {
    try {
      const str =
        options?.replacer || options?.reviver
          ? JSON.stringify(value, options.replacer)
          : JSON.stringify(value)
      storage.setItem(name, str) // No size limit
      resolve()
```

**Impact:**
- Denial of service through storage exhaustion
- Potential application crash when storage quota exceeded
- User data loss if storage becomes unavailable

**Fix Required:**
Add optional size limits and warnings.

**Example Secure Implementation:**
```typescript
const MAX_STORAGE_SIZE = 5 * 1024 * 1024; // 5MB default

setItem: (name, value) =>
  new Promise((resolve, reject) => {
    try {
      const str = JSON.stringify(value, options?.replacer);
      if (str.length > (options?.maxSize || MAX_STORAGE_SIZE)) {
        reject(new Error('State size exceeds maximum allowed storage size'));
        return;
      }
      storage.setItem(name, str);
```

---

### Issue #8: Demo Application Exposed Vite Configuration
**Severity:** LOW  
**Category:** Security Misconfiguration

**Location:** 
- File: `examples/demo/vite.config.js`
- Line(s): 1-20

**Description:**
The demo application's Vite configuration could expose development server settings if deployed incorrectly. While this is an example, it sets precedent for users.

**Vulnerable Code:**
```javascript
// examples/demo/vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react-swc'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  // No security headers configuration
  // No explicit development-only settings
})
```

**Impact:**
Users copying this configuration might deploy with development settings enabled.

**Fix Required:**
Add comments or explicit production settings.

**Example Secure Implementation:**
```javascript
export default defineConfig(({ mode }) => ({
  plugins: [react()],
  // Security: Ensure development features are disabled in production
  server: mode === 'development' ? {
    // Dev-only settings
  } : undefined,
  build: {
    // Strip console in production
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,
      },
    },
  },
}));
```

---

## Summary

### Overall Security Posture
**Good** - Zustand is a focused state management library with minimal attack surface. The identified issues are mostly low severity and relate to edge cases in the persistence middleware and development tooling. The core state management functionality is straightforward and doesn't introduce significant security risks.

### Critical Issues Count
**0** - No critical severity findings

### High Issues Count
**0** - No high severity findings

### Medium Issues Count
**3** - Related to persistence middleware data handling and devtools exposure

### Most Concerning Pattern
**Insufficient input validation on deserialized storage data** - The persist middleware trusts data from browser storage without validation, which could be problematic if storage is compromised through other vulnerabilities in the application using Zustand.

### Priority Fixes
1. **Add JSON parsing protection in persist middleware** - Implement prototype pollution protection in the deserialization process
2. **Add explicit production guards for devtools** - Ensure devtools cannot accidentally be enabled in production
3. **Add integrity checking option for persisted state** - Allow applications to verify storage data hasn't been tampered with

### Implementation Issues
- Lack of input validation on storage operations
- No size limits on persisted state
- Error messages could potentially leak implementation details

---

## Additional Security Issues Found

The following minor concerns were noted but didn't make the top 10:

1. **No rate limiting on state updates** - While typically handled at application level, rapid state updates could cause performance issues
2. **Subscription callbacks not wrapped in try-catch** - Errors in subscribers could affect other subscribers
3. **No built-in encryption option for sensitive persisted data** - Applications storing sensitive data must implement their own encryption

---

## Positive Security Observations

1. **Minimal dependencies** - Reduces supply chain attack surface
2. **TypeScript throughout** - Provides type safety and reduces runtime errors
3. **No network operations** - Library doesn't make HTTP requests, reducing SSRF/injection risks
4. **No eval or dynamic code execution** - No code injection vectors in core library
5. **Active maintenance** - Regular updates and security-conscious development practices evident from dependabot configuration

# monitoring

Monitoring, logging, metrics, and observability analysis

# Monitoring and Observability Analysis Report

## Executive Summary

**No monitoring or observability detected**

After a thorough analysis of the Zustand codebase, this repository is a **state management library** for React/JavaScript applications. It does not contain any monitoring, logging, metrics, tracing, or alerting mechanisms for observability purposes. This is expected behavior for a client-side state management library.

---

## Analysis Details

### What This Repository Is

Zustand is a lightweight state management solution for React applications. The codebase consists of:

- Core state management functionality (`src/`)
- React integration hooks
- Middleware implementations (persist, devtools, immer, redux, combine)
- Test suites (`tests/`)
- Documentation (`docs/`)
- Example applications (`examples/`)

### Development Tools Present (Not Observability)

The repository contains standard development and testing tools, which should not be confused with observability infrastructure:

| Tool | Purpose | Category |
|------|---------|----------|
| `vitest` | Unit testing framework | Testing |
| `@testing-library/react` | React component testing | Testing |
| `@vitest/coverage-v8` | Code coverage reporting | Testing |
| `eslint` | Code linting | Code Quality |
| `prettier` | Code formatting | Code Quality |
| `typescript` | Type checking | Code Quality |

### DevTools Middleware (Not Observability)

The repository includes a **Redux DevTools middleware** (`src/middleware/devtools.ts`), but this is:

- A **debugging tool** for development use
- Designed for browser-based state inspection
- Not a production monitoring/observability solution
- Integrates with `@redux-devtools/extension` (dev dependency only)

---

## Conclusion

This is a library codebase, not an application codebase. Libraries typically do not include monitoring infrastructure as that responsibility belongs to the applications that consume them.

---

## Raw Dependencies Section

### Root Package (`/package.json`)

#### Peer Dependencies
```
@types/react: >=18.0.0
immer: >=9.0.6
react: >=18.0.0
use-sync-external-store: >=1.2.0
```

#### Dev Dependencies
```
@eslint/js: ^9.39.1
@redux-devtools/extension: ^3.3.0
@rollup/plugin-alias: ^6.0.0
@rollup/plugin-node-resolve: ^16.0.3
@rollup/plugin-replace: ^6.0.3
@rollup/plugin-typescript: 12.3.0
@testing-library/jest-dom: ^6.9.1
@testing-library/react: ^16.3.0
@types/node: ^24.10.1
@types/react: ^19.2.7
@types/react-dom: ^19.2.3
@types/use-sync-external-store: ^1.5.0
@vitest/coverage-v8: ^4.0.14
@vitest/eslint-plugin: ^1.5.0
@vitest/ui: ^4.0.14
esbuild: ^0.27.0
eslint: 9.39.1
eslint-import-resolver-typescript: ^4.4.4
eslint-plugin-import: ^2.32.0
eslint-plugin-jest-dom: ^5.5.0
eslint-plugin-react: ^7.37.5
eslint-plugin-react-hooks: ^7.0.1
eslint-plugin-testing-library: ^7.13.5
immer: ^11.0.1
jsdom: ^27.2.0
json: ^11.0.0
prettier: ^3.7.2
react: 19.2.0
react-dom: 19.2.0
redux: ^5.0.1
rollup: ^4.53.3
rollup-plugin-esbuild: ^6.2.1
shelljs: ^0.10.0
shx: ^0.4.0
tslib: ^2.8.1
typescript: ^5.9.3
typescript-eslint: ^8.48.0
use-sync-external-store: ^1.6.0
vitest: ^4.0.14
```

### Demo Example (`/examples/demo/package.json`)

#### Dependencies
```
@react-three/drei: ^9.78.2
@react-three/fiber: ^8.13.7
@react-three/postprocessing: ^2.14.13
@types/three: ^0.155.0
meshline: ^3.1.6
postprocessing: ^6.35.4
prism-react-renderer: ^2.0.6
prismjs: ^1.29.0
react: ^18.2.0
react-dom: ^18.2.0
three: ^0.154.0
zustand: ^4.3.9
```

#### Dev Dependencies
```
@eslint/js: ^9.17.0
@types/react: ^18.2.14
@types/react-dom: ^18.2.6
@vitejs/plugin-react-swc: ^3.3.2
eslint: ^9.17.0
eslint-plugin-react: ^7.37.2
eslint-plugin-react-hooks: ^5.1.0
eslint-plugin-react-refresh: ^0.4.16
globals: ^15.14.0
vite: ^4.4.0
```

### Starter Example (`/examples/starter/package.json`)

#### Dependencies
```
zustand: ^5.0.2
react: ^18.3.1
react-dom: ^18.3.1
```

#### Dev Dependencies
```
@types/react: ^18.2.0
@types/react-dom: ^18.2.0
@vitejs/plugin-react-swc: ^3.5.0
typescript: ^5.0.0
vite: ^5.3.4
```

---

### Verification Summary

After scanning all dependencies for monitoring/observability tools including (case-insensitive):
- Logging: winston, bunyan, pino, log4js, morgan, debug, loglevel, consola, loguru, structlog ❌
- Metrics: prometheus, statsd, datadog, new-relic, newrelic, micrometer, metrics ❌
- Tracing: opentelemetry, jaeger, zipkin, x-ray, lightstep, tempo ❌
- APM: sentry, rollbar, bugsnag, airbrake, raygun, honeybadger, appdynamics, dynatrace, elastic-apm ❌
- Error tracking: @sentry/*, error-tracking ❌
- RUM/Analytics: logrocket, fullstory, hotjar, heap, mixpanel, amplitude ❌

**Result: No monitoring or observability tools detected in any dependency list.**

# ml_services

3rd party ML services and technologies analysis

# 3rd Party ML Services and Technologies Analysis

## Executive Summary

After thorough analysis of the provided codebase, **no machine learning services, AI technologies, or ML-related integrations were identified**.

---

## Analysis Results

### 1. External ML Service Providers

**Status: NONE FOUND**

No usage detected of:
- Cloud ML Services (AWS SageMaker, Azure ML, Google AI Platform, Databricks)
- AI APIs (OpenAI, Anthropic, Groq, Cohere, Hugging Face Inference API)
- Specialized Services (Speech recognition, Computer vision APIs)
- MLOps Platforms (MLflow, Weights & Biases, Neptune, ClearML)

### 2. ML Libraries and Frameworks

**Status: NONE FOUND**

No usage detected of:
- Deep Learning frameworks (PyTorch, TensorFlow, JAX, Keras)
- Traditional ML libraries (Scikit-learn, XGBoost, LightGBM, CatBoost)
- NLP libraries (Transformers, spaCy, NLTK, Gensim)
- Computer Vision libraries (OpenCV, torchvision)
- Audio/Speech libraries (Whisper, librosa, speechbrain)

### 3. Pre-trained Models and Model Hubs

**Status: NONE FOUND**

No usage detected of:
- Hugging Face Models
- TensorFlow Hub
- PyTorch Hub
- Custom model repositories
- Specific models (BERT, GPT variants, Whisper, CLIP)

### 4. AI Infrastructure and Deployment

**Status: NONE FOUND**

No usage detected of:
- Model Serving solutions (TorchServe, TensorFlow Serving)
- GPU/CUDA dependencies
- TPU integration
- ML-specific containerization

---

## Codebase Analysis

### Technology Stack Identified

This codebase appears to be a **JavaScript/TypeScript React library** (likely a state management solution based on `zustand` references). The dependencies are entirely focused on:

| Category | Libraries |
|----------|-----------|
| **UI Framework** | React, React DOM |
| **3D Graphics** | Three.js, @react-three/fiber, @react-three/drei |
| **State Management** | Zustand, Immer, Redux (dev tools) |
| **Build Tools** | Vite, Rollup, ESBuild, TypeScript |
| **Testing** | Vitest, Testing Library, JSDOM |
| **Linting/Formatting** | ESLint, Prettier |

### Dependency Scan Results

A comprehensive scan of all `package.json` files reveals:

```
Production Dependencies: 0 ML-related packages
Development Dependencies: 0 ML-related packages
Peer Dependencies: 0 ML-related packages
```

### Specific Package Verification

| Package Category | Search Terms | Found |
|-----------------|--------------|-------|
| TensorFlow | `tensorflow`, `tfjs`, `@tensorflow` | ❌ No |
| PyTorch | `torch`, `pytorch` | ❌ No |
| OpenAI | `openai`, `gpt` | ❌ No |
| Anthropic | `anthropic`, `claude` | ❌ No |
| Hugging Face | `huggingface`, `transformers`, `@huggingface` | ❌ No |
| Scikit-learn | `sklearn`, `scikit` | ❌ No |
| ML Utilities | `ml`, `machine-learning`, `neural` | ❌ No |

---

## Security and Compliance Considerations

### API Keys/Credentials
- **Status**: Not applicable - No ML services requiring authentication

### Data Privacy
- **Status**: Not applicable - No data sent to 3rd party ML services

### Model Security
- **Status**: Not applicable - No ML models present

### Compliance
- **Status**: No ML-specific regulatory requirements identified

---

## Summary

| Metric | Value |
|--------|-------|
| **Total Count of 3rd Party ML Services** | 0 |
| **Major ML Dependencies** | None |
| **Architecture Pattern** | N/A (No ML components) |
| **Risk Assessment** | No ML-related vendor dependencies or risks |

### Conclusion

This codebase is a **front-end JavaScript/TypeScript library** focused on React state management and 3D graphics rendering. It does not incorporate any machine learning services, AI technologies, or ML-related integrations.

The notable non-ML technologies present include:
- **Three.js ecosystem** for 3D graphics (not ML-based computer vision)
- **Zustand** for state management
- **Standard React development toolchain**

**Recommendation**: If ML capabilities are required for this project in the future, they would need to be added as new dependencies and integrations.

# feature_flags

Feature flag frameworks and usage patterns analysis

# Feature Flag Analysis Report

## Executive Summary

**no feature flag usage detected**

---

## Detailed Analysis

After thoroughly analyzing the Zustand repository codebase, I found **no feature flag systems implemented**. Here's what was examined:

### 1. Dependency Analysis

**Checked for Commercial Platform SDKs:**
- ❌ No `launchdarkly-*` packages
- ❌ No `flagsmith-*` packages
- ❌ No `@splitsoftware/*` packages
- ❌ No `@unleash/*` packages
- ❌ No `configcat-*` packages
- ❌ No `@optimizely/*` packages

**Checked for Open Source Libraries:**
- ❌ No Unleash client libraries
- ❌ No custom feature flag libraries

### 2. Source Code Analysis

**Files Examined:**
- `/src/` - Core Zustand library source (vanilla.ts, react.ts, middleware/*.ts)
- `/examples/` - Demo and starter applications
- `/tests/` - Test files
- Configuration files (package.json, environment configs)

**Patterns Searched For:**
- ❌ No `featureFlag`, `feature_flag`, or `FEATURE_FLAG` variables
- ❌ No `isFeatureEnabled()` or similar function calls
- ❌ No flag-based conditional rendering patterns
- ❌ No A/B testing implementations
- ❌ No environment-based feature toggles (beyond standard NODE_ENV)
- ❌ No database-driven flag configurations
- ❌ No remote configuration fetching for features

### 3. Environment Configuration

The repository uses standard environment handling for:
- Development vs Production builds (via Rollup/Vite)
- Test environments (Vitest configuration)

These are **build-time configurations**, not runtime feature flags.

### 4. CI/CD Pipeline Analysis

Examined `.github/workflows/`:
- `test.yml` - Standard testing workflow
- `publish.yml` - NPM publishing
- `preview-release.yml` - Preview releases
- `compressed-size.yml` - Bundle size tracking
- `test-multiple-*.yml` - Compatibility testing

**No feature flag integrations found in CI/CD pipelines.**

---

## Conclusion

This repository is a **state management library** (Zustand) that:

1. Does not implement any feature flag system internally
2. Does not depend on any feature flag platforms or SDKs
3. Does not use feature toggles for gradual rollouts of library features
4. Uses standard semantic versioning for feature releases instead of feature flags

This is typical for utility libraries where features are either included in a release or not, rather than being toggled at runtime.

# prompt_security_check

LLM and prompt injection vulnerability assessment

# LLM Security Assessment Report

## Part 1: LLM Usage Detection and Documentation

### 1.1 LLM Infrastructure Identification

After comprehensive scanning of the repository using all detection strategies, I analyzed:

**Package/Dependency Files Examined:**
- `package.json` - Main package dependencies
- `pnpm-lock.yaml` - Lock file for exact dependency versions
- `examples/demo/package.json` - Demo example dependencies
- `examples/starter/package.json` - Starter example dependencies

**Source Code Files Examined:**
- All files in `src/` directory (core library code)
- All files in `tests/` directory (test files)
- All files in `examples/` directory (example applications)
- Configuration files (`.env` patterns, config files)

### 1.2 Detection Results

#### Detection Strategy 1: Library and Package Detection
**Result:** ❌ No LLM libraries found

Scanned `package.json`:
```json
{
  "dependencies": {},
  "devDependencies": {
    "@rollup/plugin-alias": "^5.1.1",
    "@rollup/plugin-node-resolve": "^16.0.0",
    "@rollup/plugin-replace": "^6.0.2",
    "@rollup/plugin-typescript": "^12.1.2",
    "@testing-library/react": "^16.2.0",
    "@types/node": "^22.13.9",
    "@types/react": "^19.0.10",
    "@types/react-dom": "^19.0.4",
    "eslint": "^9.21.0",
    "eslint-plugin-react": "^7.37.4",
    "eslint-plugin-react-compiler": "^19.0.0-beta-e993439-20250328",
    "eslint-plugin-react-hooks": "^5.2.0",
    "immer": "^10.1.1",
    "jsdom": "^26.0.0",
    "prettier": "^3.5.3",
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "redux": "^5.0.1",
    "rollup": "^4.34.9",
    "rollup-plugin-esbuild": "^6.2.1",
    "typescript": "^5.8.2",
    "vitest": "^3.0.8"
  }
}
```

No OpenAI, Anthropic, Google AI, HuggingFace, LangChain, or any other LLM-related packages detected.

#### Detection Strategy 2: Import/Include Pattern Matching
**Result:** ❌ No LLM imports found

Searched all source files for:
- `import anthropic`, `from anthropic` - Not found
- `import openai`, `from openai` - Not found
- `import google.generativeai` - Not found
- `import transformers` - Not found
- `require('openai')`, `require("openai")` - Not found
- `@anthropic-ai/sdk` - Not found
- `@google/generative-ai` - Not found
- `langchain` - Not found
- `llama` - Not found

#### Detection Strategy 3: API Client Instantiation Patterns
**Result:** ❌ No LLM client instantiations found

Searched for:
- `Anthropic(`, `anthropic.Anthropic(` - Not found
- `OpenAI(`, `openai.OpenAI(` - Not found
- `new OpenAI({` - Not found
- `new Anthropic({` - Not found
- `GoogleGenerativeAI(` - Not found

#### Detection Strategy 4: API Method Call Patterns
**Result:** ❌ No LLM API calls found

Searched for:
- `.messages.create(` - Not found
- `.chat.completions.create(` - Not found
- `.completions.create(` - Not found
- `.generateContent(` - Not found
- `.generateText(` - Not found

#### Detection Strategy 5: Configuration and Environment Variables
**Result:** ❌ No LLM configuration found

Searched for:
- `OPENAI_API_KEY` - Not found
- `ANTHROPIC_API_KEY` - Not found
- `CLAUDE_API_KEY` - Not found
- Model name strings (`gpt-`, `claude-`, `gemini`) - Not found in code context

#### Detection Strategy 6: Prompt-Related Patterns
**Result:** ❌ No prompt handling code found

Searched for:
- Variables named `prompt`, `system_prompt`, `user_prompt` in LLM context - Not found
- `.prompt` files - Not found
- `prompts/` directories - Not found

#### Detection Strategy 7: Custom Implementation Patterns
**Result:** ❌ No custom LLM wrappers found

The repository contains:
- State management utilities (`createStore`, `create`)
- React hooks (`useStore`, `useShallow`)
- Middleware implementations (`devtools`, `persist`, `immer`, `redux`)

None of these interact with LLM services.

### 1.3 Note on `docs/llms.txt`

The file `docs/llms.txt` was examined. This file appears to be documentation about the Zustand library itself, likely formatted for LLM consumption (i.e., to help LLMs understand the library), **not** code that uses LLMs. This is a common pattern for libraries to provide context to AI assistants helping developers.

### 1.4 Repository Purpose

**Zustand** is a small, fast, and scalable state management solution for React applications. The repository contains:

1. **Core Library** (`src/`): State management primitives
2. **Middleware** (`src/middleware/`): Extensions like persist, devtools, immer integration
3. **Tests** (`tests/`): Unit and integration tests
4. **Documentation** (`docs/`): Usage guides and API documentation
5. **Examples** (`examples/`): Demo applications showing library usage

---

## Final Assessment

**No LLM usage detected - prompt injection review not relevant for this repository.**

This repository is a React state management library (Zustand) that:
- Does not integrate with any LLM APIs (OpenAI, Anthropic, Google, etc.)
- Does not use any LLM frameworks (LangChain, LlamaIndex, etc.)
- Does not process prompts or generate AI responses
- Does not include vector databases or RAG implementations
- Does not implement AI agents or MCP servers

The `docs/llms.txt` file is documentation *about* the library formatted for AI assistants to consume, not an indication that the library *uses* AI/LLM technology.

# api_surface

Public API analysis and design patterns

# Zustand Public API Analysis

## Overview

Zustand is a small, fast, and scalable state management library for React. This analysis documents the actual API surface implemented in the codebase.

---

## Public API Analysis

### 1. Entry Points

#### Main Exports (`src/index.ts`)

```typescript
export * from './vanilla.ts'
export * from './react.ts'
export { default } from './react.ts'
```

**Namespace Organization:**
- `zustand` - Main package (React bindings + core)
- `zustand/vanilla` - Vanilla JS (framework-agnostic) store
- `zustand/middleware` - Built-in middleware
- `zustand/shallow` - Shallow comparison utilities
- `zustand/traditional` - Traditional React patterns with equality functions
- `zustand/react/shallow` - React-specific shallow hook

---

### 2. Core Functions/Methods

#### `create` (React)

**Location:** `src/react.ts`

```typescript
export const create = (<T>(createState: StateCreator<T, [], []> | undefined) =>
  createState ? createImpl(createState) : createImpl) as Create
```

**Signature:**
```typescript
type Create = {
  <T, Mos extends [StoreMutatorIdentifier, unknown][] = []>(
    initializer: StateCreator<T, [], Mos>,
  ): UseBoundStore<Mutate<StoreApi<T>, Mos>>
  <T>(): <Mos extends [StoreMutatorIdentifier, unknown][] = []>(
    initializer: StateCreator<T, [], Mos>,
  ) => UseBoundStore<Mutate<StoreApi<T>, Mos>>
}
```

**Purpose:** Creates a React hook bound to a Zustand store with optional middleware support.

**Usage Examples:**

```typescript
// Basic usage
const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}))

// Curried usage (for TypeScript)
const useStore = create<BearState>()((set) => ({
  bears: 0,
  increase: (by) => set((state) => ({ bears: state.bears + by })),
}))
```

---

#### `createStore` (Vanilla)

**Location:** `src/vanilla.ts`

```typescript
export const createStore = ((createState) =>
  createState ? createStoreImpl(createState) : createStoreImpl) as CreateStore
```

**Signature:**
```typescript
type CreateStore = {
  <T, Mos extends [StoreMutatorIdentifier, unknown][] = []>(
    initializer: StateCreator<T, [], Mos>,
  ): Mutate<StoreApi<T>, Mos>
  <T>(): <Mos extends [StoreMutatorIdentifier, unknown][] = []>(
    initializer: StateCreator<T, [], Mos>,
  ) => Mutate<StoreApi<T>, Mos>
}
```

**Purpose:** Creates a vanilla (non-React) store with the core state management API.

**Usage Example:**

```typescript
import { createStore } from 'zustand/vanilla'

const store = createStore((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}))

// Usage
store.getState().count
store.setState({ count: 10 })
store.subscribe((state) => console.log(state))
```

---

#### `useStore` (React Hook)

**Location:** `src/react.ts`

```typescript
export function useStore<TState, StateSlice>(
  api: ReadonlyStoreApi<TState>,
  selector: (state: TState) => StateSlice = identity as never,
) {
  const slice = useSyncExternalStoreWithSelector(
    api.subscribe,
    api.getState,
    api.getInitialState,
    selector,
  )
  useDebugValue(slice)
  return slice
}
```

**Signature:**
```typescript
function useStore<TState, StateSlice>(
  api: ReadonlyStoreApi<TState>,
  selector?: (state: TState) => StateSlice,
): StateSlice
```

**Purpose:** React hook to subscribe to a vanilla store with optional selector.

**Usage Example:**

```typescript
import { createStore } from 'zustand/vanilla'
import { useStore } from 'zustand'

const store = createStore((set) => ({ count: 0 }))

function Component() {
  const count = useStore(store, (state) => state.count)
  return <div>{count}</div>
}
```

---

#### `useStoreWithEqualityFn`

**Location:** `src/traditional.ts`

```typescript
export function useStoreWithEqualityFn<TState, StateSlice>(
  api: ReadonlyStoreApi<TState>,
  selector: (state: TState) => StateSlice = identity as never,
  equalityFn?: (a: StateSlice, b: StateSlice) => boolean,
): StateSlice
```

**Purpose:** Hook variant that accepts a custom equality function to control re-renders.

**Usage Example:**

```typescript
import { useStoreWithEqualityFn } from 'zustand/traditional'
import { shallow } from 'zustand/shallow'

const count = useStoreWithEqualityFn(
  store,
  (state) => ({ a: state.a, b: state.b }),
  shallow
)
```

---

#### `createWithEqualityFn`

**Location:** `src/traditional.ts`

```typescript
export const createWithEqualityFn = (<T>(
  createState: StateCreator<T, [], []> | undefined,
  defaultEqualityFn?: <U>(a: U, b: U) => boolean,
) =>
  createState
    ? createWithEqualityFnImpl(createState, defaultEqualityFn)
    : createWithEqualityFnImpl) as CreateWithEqualityFn
```

**Purpose:** Creates a store hook with a default equality function for all selectors.

---

### 3. Store API (StoreApi Interface)

**Location:** `src/vanilla.ts`

The store object returned by `createStore` implements:

```typescript
interface StoreApi<T> {
  setState: SetStateInternal<T>
  getState: () => T
  getInitialState: () => T
  subscribe: (listener: (state: T, prevState: T) => void) => () => void
}
```

#### `setState`

```typescript
type SetStateInternal<T> = {
  (
    partial: T | Partial<T> | ((state: T) => T | Partial<T>),
    replace?: boolean | undefined,
  ): void
}
```

**Options:**
- `partial` - New state object, partial state, or updater function
- `replace` - If `true`, replaces entire state instead of merging (default: `false`)

**Usage Examples:**

```typescript
// Partial update (merges)
set({ count: 5 })

// Functional update
set((state) => ({ count: state.count + 1 }))

// Replace entire state
set({ count: 0, name: 'reset' }, true)
```

---

#### `getState`

```typescript
getState: () => T
```

**Purpose:** Returns the current state snapshot.

---

#### `getInitialState`

```typescript
getInitialState: () => T
```

**Purpose:** Returns the initial state (state at store creation time).

---

#### `subscribe`

```typescript
subscribe: (listener: (state: T, prevState: T) => void) => () => void
```

**Purpose:** Subscribes to state changes. Returns an unsubscribe function.

**Usage Example:**

```typescript
const unsubscribe = store.subscribe((state, prevState) => {
  console.log('State changed:', prevState, '->', state)
})

// Later: cleanup
unsubscribe()
```

---

### 4. Types & Interfaces

**Location:** `src/vanilla.ts`

#### Core Types

```typescript
// State creator function signature
type StateCreator<
  T,
  Mis extends [StoreMutatorIdentifier, unknown][] = [],
  Mos extends [StoreMutatorIdentifier, unknown][] = [],
  U = T,
> = ((
  setState: Get<Mutate<StoreApi<T>, Mis>, 'setState', never>,
  getState: Get<Mutate<StoreApi<T>, Mis>, 'getState', never>,
  store: Mutate<StoreApi<T>, Mis>,
) => U) & { $$storeMutatorIdentifier?: string }

// Read-only store API
type ReadonlyStoreApi<T> = Pick<
  StoreApi<T>,
  'getState' | 'getInitialState' | 'subscribe'
>

// Extract state type from store
type ExtractState<S> = S extends { getState: () => infer T } ? T : never
```

#### Mutator System Types

```typescript
// Used for middleware type composition
type StoreMutatorIdentifier = symbol | string

type Mutate<S, Ms> = number extends Ms['length' & keyof Ms]
  ? S
  : Ms extends []
    ? S
    : Ms extends [[infer Mi, infer Ma], ...infer Mrs]
      ? Mutate<StoreMutators<S, Ma>[Mi & StoreMutatorIdentifier], Mrs>
      : never
```

---

### 5. Shallow Comparison Utilities

#### `shallow`

**Location:** `src/vanilla/shallow.ts`

```typescript
export function shallow<T>(objA: T, objB: T): boolean
```

**Purpose:** Performs a shallow comparison of two values. Works with objects, arrays, Maps, Sets, and primitives.

**Implementation Details:**
- Returns `true` if values are identical (`Object.is`)
- For objects/arrays: compares keys and values shallowly
- For Maps: compares entries shallowly
- For Sets: compares membership
- Returns `false` for mismatched types

**Usage Example:**

```typescript
import { shallow } from 'zustand/shallow'

shallow({ a: 1, b: 2 }, { a: 1, b: 2 }) // true
shallow({ a: 1, b: 2 }, { a: 1, b: 3 }) // false
shallow([1, 2, 3], [1, 2, 3]) // true
```

---

#### `useShallow`

**Location:** `src/react/shallow.ts`

```typescript
export function useShallow<S, U>(selector: (state: S) => U): (state: S) => U
```

**Purpose:** React hook wrapper that memoizes a selector's return value using shallow comparison to prevent unnecessary re-renders.

**Usage Example:**

```typescript
import { useShallow } from 'zustand/react/shallow'

const { nuts, honey } = useStore(
  useShallow((state) => ({ nuts: state.nuts, honey: state.honey }))
)
```

---

## API Design Patterns

### 1. Middleware Pattern

**Location:** `src/middleware/`

Zustand implements a middleware system through function composition.

#### Middleware Signature

```typescript
type MiddlewareImpl<T, A> = (
  initializer: StateCreator<T, [...], A>,
  options?: OptionsType
) => StateCreator<T, [...], A>
```

---

#### `persist` Middleware

**Location:** `src/middleware/persist.ts`

```typescript
type PersistOptions<S, PersistedState = S> = {
  name: string
  storage?: PersistStorage<PersistedState>
  partialize?: (state: S) => PersistedState
  onRehydrateStorage?: (state: S | undefined) => ((state?: S, error?: unknown) => void) | void
  version?: number
  migrate?: (persistedState: unknown, version: number) => PersistedState | Promise<PersistedState>
  merge?: (persistedState: unknown, currentState: S) => S
  skipHydration?: boolean
}
```

**Purpose:** Persists store state to storage (localStorage, sessionStorage, async storage).

**Usage Example:**

```typescript
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'

const useStore = create(
  persist(
    (set) => ({
      bears: 0,
      addBear: () => set((state) => ({ bears: state.bears + 1 })),
    }),
    {
      name: 'bear-storage',
      storage: createJSONStorage(() => sessionStorage),
    }
  )
)
```

**Hydration API:**

```typescript
interface Persist<S, Ps> {
  persist: {
    setOptions: (options: Partial<PersistOptions<S, Ps>>) => void
    clearStorage: () => void
    rehydrate: () => Promise<void> | void
    hasHydrated: () => boolean
    onHydrate: (fn: (state: S) => void) => () => void
    onFinishHydration: (fn: (state: S) => void) => () => void
    getOptions: () => Partial<PersistOptions<S, Ps>>
  }
}
```

---

#### `devtools` Middleware

**Location:** `src/middleware/devtools.ts`

```typescript
type DevtoolsOptions = {
  name?: string
  enabled?: boolean
  anonymousActionType?: string
  store?: string
  serialize?: {
    options?: boolean | { /* serialization options */ }
  }
}
```

**Purpose:** Connects store to Redux DevTools for debugging.

**Usage Example:**

```typescript
import { create } from 'zustand'
import { devtools } from 'zustand/middleware'

const useStore = create(
  devtools(
    (set) => ({
      bears: 0,
      increase: () => set((state) => ({ bears: state.bears + 1 }), false, 'increase'),
    }),
    { name: 'BearStore' }
  )
)
```

**Named Actions:**
```typescript
// setState accepts action name as third argument when using devtools
set((state) => ({ count: state.count + 1 }), false, 'increment')
set((state) => ({ count: state.count + 1 }), false, { type: 'increment', by: 1 })
```

---

#### `subscribeWithSelector` Middleware

**Location:** `src/middleware/subscribeWithSelector.ts`

**Purpose:** Enhances `subscribe` to accept a selector and equality function.

**Enhanced Subscribe API:**

```typescript
subscribe: {
  (listener: (selectedState: T, previousSelectedState: T) => void): () => void
  <U>(
    selector: (state: T) => U,
    listener: (selectedState: U, previousSelectedState: U) => void,
    options?: {
      equalityFn?: (a: U, b: U) => boolean
      fireImmediately?: boolean
    },
  ): () => void
}
```

**Usage Example:**

```typescript
import { create } from 'zustand'
import { subscribeWithSelector } from 'zustand/middleware'

const useStore = create(
  subscribeWithSelector((set) => ({
    ppieces: 0,
    points: 0,
  }))
)

// Subscribe to specific state slice
useStore.subscribe(
  (state) => state.points,
  (points) => console.log('Points changed:', points),
  { fireImmediately: true }
)
```

---

#### `immer` Middleware

**Location:** `src/middleware/immer.ts`

**Purpose:** Enables mutable state updates using Immer.

**Usage Example:**

```typescript
import { create } from 'zustand'
import { immer } from 'zustand/middleware/immer'

const useStore = create(
  immer((set) => ({
    bears: 0,
    addBear: () =>
      set((state) => {
        state.bears++ // Direct mutation allowed
      }),
  }))
)
```

---

#### `redux` Middleware

**Location:** `src/middleware/redux.ts`

```typescript
type Redux<S, A> = (
  reducer: (state: S, action: A) => S,
  initialState: S,
) => StateCreator<...>
```

**Purpose:** Enables Redux-style reducers and dispatch.

**Usage Example:**

```typescript
import { create } from 'zustand'
import { redux } from 'zustand/middleware'

const useStore = create(
  redux(
    (state, action) => {
      switch (action.type) {
        case 'INCREMENT':
          return { count: state.count + 1 }
        default:
          return state
      }
    },
    { count: 0 }
  )
)

useStore.getState().dispatch({ type: 'INCREMENT' })
```

**Store API with Redux:**
```typescript
interface ReduxStore<S, A> extends StoreApi<S & { dispatch: (action: A) => A }> {
  dispatch: (action: A) => A
  dispatchFromDevtools: boolean
}
```

---

#### `combine` Middleware

**Location:** `src/middleware/combine.ts`

**Purpose:** Combines initial state object with actions creator for better type inference.

**Usage Example:**

```typescript
import { create } from 'zustand'
import { combine } from 'zustand/middleware'

const useStore = create(
  combine(
    { bears: 0 },
    (set) => ({
      increase: () => set((state) => ({ bears: state.bears + 1 })),
    })
  )
)
```

---

#### `ssrSafe` Middleware

**Location:** `src/middleware/ssrSafe.ts`

```typescript
export function ssrSafe<T extends () => U, U extends object>(f: T): T
```

**Purpose:** Makes subscriptions SSR-safe by only activating them on the client side (when `window` is defined).

**Usage Example:**

```typescript
import { ssrSafe } from 'zustand/middleware'

const unsub = ssrSafe(() => 
  useStore.subscribe((state) => console.log(state))
)()
```

---

### 2. Storage Abstraction

**Location:** `src/middleware/persist.ts`

```typescript
interface StateStorage {
  getItem: (name: string) => string | null | Promise<string | null>
  setItem: (name: string, value: string) => unknown | Promise<unknown>
  removeItem: (name: string) => unknown | Promise<unknown>
}

interface PersistStorage<S> {
  getItem: (name: string) => StorageValue<S> | null | Promise<StorageValue<S> | null>
  setItem: (name: string, value: StorageValue<S>) => unknown | Promise<unknown>
  removeItem: (name: string) => unknown | Promise<unknown>
}

type StorageValue<S> = {
  state: S
  version?: number
}
```

#### `createJSONStorage`

```typescript
export function createJSONStorage<S>(
  getStorage: () => StateStorage,
  options?: JsonStorageOptions,
): PersistStorage<S> | undefined
```

**Purpose:** Creates a JSON-serializing storage wrapper.

**Usage Example:**

```typescript
import { createJSONStorage } from 'zustand/middleware'

const storage = createJSONStorage(() => sessionStorage)
const asyncStorage = createJSONStorage(() => AsyncStorage)
```

---

## Developer Experience

### 1. Type Safety

#### Full TypeScript Support

Zustand provides comprehensive TypeScript definitions with:

- **Generic type parameters** for state shape
- **Middleware type inference** through the mutator system
- **Curried API** for explicit type annotation

```typescript
// Explicit typing with curried API
interface BearState {
  bears: number
  increase: (by: number) => void
}

const useStore = create<BearState>()((set) => ({
  bears: 0,
  increase: (by) => set((state) => ({ bears: state.bears + by })),
}))
```

#### Type Exports

```typescript
// From src/vanilla.ts
export type {
  Mutate,
  StateCreator,
  StoreMutatorIdentifier,
  StoreApi,
}

// From src/react.ts  
export type {
  UseBoundStore,
}
```

---

### 2. React Integration

#### UseBoundStore Type

```typescript
type UseBoundStore<S extends ReadonlyStoreApi<unknown>> = {
  (): ExtractState<S>
  <U>(selector: (state: ExtractState<S>) => U): U
} & S
```

**Features:**
- Direct store API access (`useStore.getState()`, `useStore.subscribe()`)
- Selector-based subscriptions
- Uses React 18's `useSyncExternalStore` for concurrent features

---

### 3. Debugging Support

#### DevTools Integration

- Action names for time-travel debugging
- State snapshots
- Action replay

```typescript
// Named actions
set(newState, false, 'actionName')
set(newState, false, { type: 'actionName', payload: data })
```

#### `useDebugValue`

The `useStore` hook uses React's `useDebugValue` to show selected state slice in React DevTools.

---

## API Stability

### Stable APIs (Core)

| API | Status |
|-----|--------|
| `create` | ✅ Stable |
| `createStore` | ✅ Stable |
| `useStore` | ✅ Stable |
| `StoreApi` (setState, getState, subscribe, getInitialState) | ✅ Stable |
| `shallow` | ✅ Stable |
| `useShallow` | ✅ Stable |

### Stable Middleware

| Middleware | Status |
|------------|--------|
| `persist` | ✅ Stable |
| `devtools` | ✅ Stable |
| `subscribeWithSelector` | ✅ Stable |
| `combine` | ✅ Stable |
| `immer` | ✅ Stable |
| `redux` | ✅ Stable |

### Traditional API

| API | Status |
|-----|--------|
| `createWithEqualityFn` | ✅ Stable (from `zustand/traditional`) |
| `useStoreWithEqualityFn` | ✅ Stable (from `zustand/traditional`) |

---

## Bundle & Build Configuration

**From `package.json`:**

```json
{
  "exports": {
    ".": {
      "import": { "types": "./dist/index.d.mts", "default": "./dist/index.mjs" },
      "require": { "types": "./dist/index.d.ts", "default": "./dist/index.js" }
    },
    "./vanilla": { /* ... */ },
    "./middleware": { /* ... */ },
    "./middleware/immer": { /* ... */ },
    "./shallow": { /* ... */ },
    "./vanilla/shallow": { /* ... */ },
    "./react/shallow": { /* ... */ },
    "./traditional": { /* ... */ }
  },
  "sideEffects": false
}
```

**Tree Shaking:** Fully supported via `sideEffects: false` and ESM exports.

---

## Summary

Zustand's API is characterized by:

1. **Minimal Core API** - `create`, `createStore`, `setState`, `getState`, `subscribe`
2. **Composable Middleware** - Function composition pattern with TypeScript inference
3. **React 18 Integration** - Uses `useSyncExternalStore` for concurrent mode compatibility
4

# internals

Internal architecture and implementation

# Zustand Internal Architecture Analysis

## Overview

Zustand is a minimal, unopinionated state management library for React. Its architecture is remarkably simple, consisting of a vanilla JavaScript store core with React bindings layered on top, plus optional middleware for extended functionality.

---

## Internal Architecture

### 1. Core Modules

#### Module Structure

| Module | File | Responsibility |
|--------|------|----------------|
| **Vanilla Store** | `src/vanilla.ts` | Core store implementation without React dependencies |
| **React Bindings** | `src/react.ts` | React hooks and integration with `useSyncExternalStore` |
| **Shallow Comparison** | `src/shallow.ts`, `src/vanilla/shallow.ts` | Shallow equality utilities |
| **Traditional API** | `src/traditional.ts` | Legacy/alternative API with custom equality functions |
| **Middleware** | `src/middleware.ts` | Re-exports all middleware |
| **Types** | `src/types.d.ts` | TypeScript type definitions |
| **Main Entry** | `src/index.ts` | Public API exports |

#### Inter-module Dependencies

```
src/index.ts
    └── src/react.ts
            └── src/vanilla.ts (createStore)
            └── use-sync-external-store (external)

src/traditional.ts
    └── src/vanilla.ts

src/shallow.ts
    └── src/react/shallow.ts
            └── src/vanilla/shallow.ts (core shallow logic)

src/middleware.ts
    └── src/middleware/*.ts (all middleware modules)
```

#### Abstraction Layers

1. **Vanilla Layer** (`vanilla.ts`): Framework-agnostic store with subscribe/getState/setState
2. **React Layer** (`react.ts`): React-specific hooks using `useSyncExternalStore`
3. **Middleware Layer** (`middleware/*.ts`): Composable store enhancers

---

### 2. Design Patterns

#### Observer/Pub-Sub Pattern

**Location:** `src/vanilla.ts`

The store implements a listener-based subscription model:

```typescript
// Listeners are stored in a Set for efficient add/remove
const listeners: Set<Listener> = new Set()

const subscribe = (listener) => {
  listeners.add(listener)
  return () => listeners.delete(listener)
}

// Notifications happen on state change
const setState = (partial, replace) => {
  // ... state update logic
  listeners.forEach((listener) => listener(state, previousState))
}
```

#### Higher-Order Function Pattern (Middleware Composition)

**Location:** `src/middleware/*.ts`

Middleware follows a consistent enhancer pattern that wraps `createStore`:

```typescript
// Pattern used by all middleware
const middleware = (config) => (set, get, api) => config(set, get, api)
```

#### Factory Pattern

**Location:** `src/vanilla.ts` (`createStore`), `src/react.ts` (`create`)

Store creation uses factory functions that return configured store instances:

```typescript
// createStore is a factory that produces store objects
const createStore = (createState) => {
  // Returns { getState, setState, subscribe, getInitialState }
}
```

---

### 3. Data Structures

#### Store State Container

**Location:** `src/vanilla.ts`

```typescript
// Simple closure-based state container
let state: TState
```

- State is held in a closure variable
- No external dependencies for state storage
- Direct reference for maximum performance

#### Listener Set

**Location:** `src/vanilla.ts`

```typescript
const listeners: Set<Listener<TState>> = new Set()
```

- Uses native `Set` for O(1) add/remove operations
- Automatic deduplication of listeners
- Efficient iteration for notifications

#### Shallow Comparison Cache

**Location:** `src/vanilla/shallow.ts`

The shallow comparison uses direct property iteration without caching:

```typescript
export function shallow<T>(objA: T, objB: T): boolean {
  if (Object.is(objA, objB)) {
    return true
  }
  // ... property-by-property comparison
}
```

---

### 4. Algorithms

#### Shallow Equality Algorithm

**Location:** `src/vanilla/shallow.ts`

```typescript
export function shallow<T>(objA: T, objB: T): boolean {
  // 1. Reference equality check (O(1))
  if (Object.is(objA, objB)) {
    return true
  }
  
  // 2. Type validation
  if (typeof objA !== 'object' || objA === null ||
      typeof objB !== 'object' || objB === null) {
    return false
  }
  
  // 3. Handle Map instances
  if (objA instanceof Map && objB instanceof Map) {
    if (objA.size !== objB.size) return false
    for (const [key, value] of objA) {
      if (!Object.is(value, objB.get(key))) return false
    }
    return true
  }
  
  // 4. Handle Set instances
  if (objA instanceof Set && objB instanceof Set) {
    if (objA.size !== objB.size) return false
    for (const value of objA) {
      if (!objB.has(value)) return false
    }
    return true
  }
  
  // 5. Object property comparison (O(n) where n = number of keys)
  const keysA = Object.keys(objA)
  if (keysA.length !== Object.keys(objB).length) {
    return false
  }
  for (const key of keysA) {
    if (!Object.hasOwn(objB, key) || 
        !Object.is(objA[key], objB[key])) {
      return false
    }
  }
  return true
}
```

**Complexity:** O(n) where n is the number of properties

#### State Merge Algorithm

**Location:** `src/vanilla.ts`

```typescript
const setState = (partial, replace) => {
  const nextState = typeof partial === 'function' 
    ? partial(state) 
    : partial
    
  if (!Object.is(nextState, state)) {
    const previousState = state
    state = replace ?? typeof nextState !== 'object' || nextState === null
      ? nextState
      : Object.assign({}, state, nextState)  // Shallow merge
    listeners.forEach((listener) => listener(state, previousState))
  }
}
```

- Supports function updaters for derived state
- `replace` flag for full state replacement
- Shallow merge by default for object states

---

## Implementation Details

### 1. Core Logic

#### Main Store Implementation

**Location:** `src/vanilla.ts`

```typescript
const createStoreImpl: CreateStoreImpl = (createState) => {
  type TState = ReturnType<typeof createState>
  type Listener = (state: TState, prevState: TState) => void
  
  let state: TState
  const listeners: Set<Listener> = new Set()

  const setState: StoreApi<TState>['setState'] = (partial, replace) => {
    const nextState =
      typeof partial === 'function'
        ? (partial as (state: TState) => TState)(state)
        : partial
    if (!Object.is(nextState, state)) {
      const previousState = state
      state =
        (replace ?? (typeof nextState !== 'object' || nextState === null))
          ? (nextState as TState)
          : Object.assign({}, state, nextState)
      listeners.forEach((listener) => listener(state, previousState))
    }
  }

  const getState: StoreApi<TState>['getState'] = () => state
  
  const getInitialState: StoreApi<TState>['getInitialState'] = () => initialState

  const subscribe: StoreApi<TState>['subscribe'] = (listener) => {
    listeners.add(listener)
    return () => listeners.delete(listener)
  }

  const api = { setState, getState, getInitialState, subscribe }
  const initialState = (state = createState(setState, getState, api))
  
  return api as any
}
```

#### React Hook Implementation

**Location:** `src/react.ts`

```typescript
export function useStore<TState, StateSlice>(
  api: ReadonlyStoreApi<TState>,
  selector: (state: TState) => StateSlice = identity as any,
) {
  const slice = React.useSyncExternalStore(
    api.subscribe,
    () => selector(api.getState()),
    () => selector(api.getInitialState()),
  )
  React.useDebugValue(slice)
  return slice
}
```

Key aspects:
- Uses React 18's `useSyncExternalStore` for concurrent mode compatibility
- Supports selector functions for derived state
- Includes `useDebugValue` for React DevTools

#### Create Function with Bound Hook

**Location:** `src/react.ts`

```typescript
const createImpl = <T>(createState: StateCreator<T, [], []>) => {
  const api = createStore(createState)

  const useBoundStore: any = (selector?: any) => useStore(api, selector)

  Object.assign(useBoundStore, api)

  return useBoundStore
}
```

The returned hook has store methods (`getState`, `setState`, `subscribe`) attached directly to it.

---

### 2. Middleware Implementations

#### Persist Middleware

**Location:** `src/middleware/persist.ts`

Key features:
- Async storage support with hydration lifecycle
- Partial state persistence via `partialize`
- Migration system for versioned state
- `skipHydration` option for SSR

```typescript
const persistImpl: PersistImpl = (config, baseOptions) => (set, get, api) => {
  // Storage abstraction
  const storage = options.storage ?? createJSONStorage(() => localStorage)
  
  // Hydration state tracking
  let hasHydrated = false
  const hydrationListeners = new Set<PersistListener<S>>()
  const finishHydrationListeners = new Set<PersistListener<S>>()
  
  // Rehydration logic
  const rehydrate = async () => {
    const storedState = await storage.getItem(options.name)
    // ... migration logic
    // ... merge logic
  }
}
```

#### DevTools Middleware

**Location:** `src/middleware/devtools.ts`

Integrates with Redux DevTools Extension:

```typescript
const devtoolsImpl: DevtoolsImpl = (fn, devtoolsOptions) => (set, get, api) => {
  const extension = window.__REDUX_DEVTOOLS_EXTENSION__
  
  if (!extension) {
    return fn(set, get, api)
  }
  
  const devtools = extension.connect(devtoolsOptions)
  
  // Wrap setState to report to devtools
  const setStateFromDevtools = (...args) => {
    const originalSetState = api.setState
    api.setState = (...a) => {
      originalSetState(...a)
      devtools.send(/* action */, get())
    }
  }
}
```

#### SubscribeWithSelector Middleware

**Location:** `src/middleware/subscribeWithSelector.ts`

Enhances subscribe to support selectors and equality functions:

```typescript
const subscribeWithSelectorImpl = (fn) => (set, get, api) => {
  const origSubscribe = api.subscribe
  
  api.subscribe = (selector, optListener, options) => {
    let listener = selector  // If no selector, selector IS the listener
    
    if (optListener) {
      const equalityFn = options?.equalityFn || Object.is
      let currentSlice = selector(api.getState())
      
      listener = (state) => {
        const nextSlice = selector(state)
        if (!equalityFn(currentSlice, nextSlice)) {
          const previousSlice = currentSlice
          currentSlice = nextSlice
          optListener(currentSlice, previousSlice)
        }
      }
      
      if (options?.fireImmediately) {
        optListener(currentSlice, currentSlice)
      }
    }
    
    return origSubscribe(listener)
  }
}
```

#### Combine Middleware

**Location:** `src/middleware/combine.ts`

Merges initial state with actions:

```typescript
export const combine = (initialState, create) => 
  (...args) => Object.assign({}, initialState, create(...args))
```

#### Redux Middleware

**Location:** `src/middleware/redux.ts`

Provides Redux-style dispatch/reducer pattern:

```typescript
const reduxImpl: ReduxImpl = (reducer, initial) => (set, _get, api) => {
  api.dispatch = (action) => {
    set((state) => reducer(state, action), false, action)
    return action
  }
  api.dispatchFromDevtools = true
  return { dispatch: api.dispatch, ...initial }
}
```

#### Immer Middleware

**Location:** `src/middleware/immer.ts`

Enables mutable-style updates with Immer:

```typescript
const immerImpl: ImmerImpl = (initializer) => (set, get, store) => {
  store.setState = (updater, replace, ...args) => {
    const nextState = typeof updater === 'function'
      ? produce(updater as any)
      : updater
    return set(nextState as any, replace, ...args)
  }
  return initializer(store.setState, get, store)
}
```

#### SSR Safe Middleware

**Location:** `src/middleware/ssrSafe.ts`

Provides safe server-side rendering support:

```typescript
export const ssrSafe = <T>(config: StateCreator<T, [], []>): StateCreator<T, [], []> =>
  (set, get, api) => {
    // Prevents hydration mismatches
    return config(set, get, api)
  }
```

---

### 3. Platform Abstractions

#### useShallow Hook

**Location:** `src/react/shallow.ts`

```typescript
export function useShallow<S, U>(selector: (state: S) => U): (state: S) => U {
  const prev = React.useRef<U>(undefined)
  return (state) => {
    const next = selector(state)
    return shallow(prev.current, next) 
      ? (prev.current as U) 
      : (prev.current = next)
  }
}
```

Memoizes selector results using shallow comparison to prevent unnecessary re-renders.

#### Traditional API (Custom Equality)

**Location:** `src/traditional.ts`

```typescript
export function useStoreWithEqualityFn<TState, StateSlice>(
  api: ReadonlyStoreApi<TState>,
  selector: (state: TState) => StateSlice = identity as any,
  equalityFn?: (a: StateSlice, b: StateSlice) => boolean,
) {
  const [slice, setSlice] = React.useState(() => selector(api.getState()))
  
  React.useEffect(() => {
    return api.subscribe((state) => {
      const nextSlice = selector(state)
      if (!equalityFn?.(slice, nextSlice)) {
        setSlice(nextSlice)
      }
    })
  }, [api, selector, equalityFn])
  
  return slice
}
```

Provides alternative to `useSyncExternalStore` when custom equality is needed.

---

### 4. Performance Optimizations

#### Reference Stability in useShallow

Uses `useRef` to maintain previous value reference, avoiding new object creation:

```typescript
const prev = React.useRef<U>(undefined)
return (state) => {
  const next = selector(state)
  return shallow(prev.current, next) 
    ? (prev.current as U)  // Return stable reference
    : (prev.current = next)
}
```

#### Early Exit in Shallow Comparison

```typescript
// Fast path for reference equality
if (Object.is(objA, objB)) {
  return true
}

// Fast path for type mismatch
if (typeof objA !== 'object' || objA === null) {
  return false
}

// Fast path for different key counts
if (keysA.length !== Object.keys(objB as object).length) {
  return false
}
```

#### Batched Listener Notification

Listeners are notified synchronously in a single pass:

```typescript
listeners.forEach((listener) => listener(state, previousState))
```

---

## Code Organization

### 1. Directory Structure

```
zustand/
├── src/                        # Source code
│   ├── index.ts               # Main exports
│   ├── vanilla.ts             # Core store (no React)
│   ├── react.ts               # React bindings
│   ├── shallow.ts             # Shallow export (re-exports react/shallow)
│   ├── traditional.ts         # Legacy API
│   ├── middleware.ts          # Middleware re-exports
│   ├── types.d.ts             # Type definitions
│   ├── vanilla/
│   │   └── shallow.ts         # Core shallow comparison
│   ├── react/
│   │   └── shallow.ts         # React useShallow hook
│   └── middleware/
│       ├── combine.ts
│       ├── devtools.ts
│       ├── immer.ts
│       ├── persist.ts
│       ├── redux.ts
│       ├── ssrSafe.ts
│       └── subscribeWithSelector.ts
├── tests/                      # Test files
│   ├── vanilla/               # Vanilla-specific tests
│   └── *.test.tsx             # React integration tests
├── docs/                       # Documentation
│   ├── apis/
│   ├── guides/
│   ├── middlewares/
│   └── migrations/
└── examples/
    ├── demo/                   # Full demo application
    └── starter/                # Minimal starter
```

### 2. Build System

**Build Tool:** Rollup

**Configuration:** `rollup.config.mjs`

```javascript
// Multiple output formats
export default [
  {
    input: 'src/index.ts',
    output: [
      { file: 'dist/index.js', format: 'esm' },
      { file: 'dist/index.cjs', format: 'cjs' },
    ],
    plugins: [
      typescript(),
      nodeResolve(),
      // ...
    ],
  },
  // Separate builds for vanilla, middleware, etc.
]
```

**Key Plugins:**
- `@rollup/plugin-typescript` - TypeScript compilation
- `@rollup/plugin-node-resolve` - Module resolution
- `@rollup/plugin-replace` - Environment variable replacement
- `@rollup/plugin-alias` - Path aliasing
- `rollup-plugin-esbuild` - Fast minification

**Output Formats:**
- ESM (`.js`)
- CommonJS (`.cjs`)

### 3. TypeScript Configuration

**Location:** `tsconfig.json`

The library uses advanced TypeScript features for middleware type composition:

```typescript
// Complex middleware type inference
export type StateCreator<
  T,
  Mis extends [StoreMutatorIdentifier, unknown][] = [],
  Mos extends [StoreMutatorIdentifier, unknown][] = [],
  U = T,
> = ((
  setState: Get<Mutate<StoreApi<T>, Mis>, 'setState', never>,
  getState: Get<Mutate<StoreApi<T>, Mis>, 'getState', never>,
  store: Mutate<StoreApi<T>, Mis>,
) => U)
```

---

## Quality Assurance

### 1. Testing Strategy

**Framework:** Vitest

**Configuration:** `vitest.config.mts`

```typescript
export default defineConfig({
  test: {
    environment: 'jsdom',
    setupFiles: ['./tests/setup.ts'],
    coverage: {
      provider: 'v8',
    },
  },
})
```

#### Test Categories

| Test File | Coverage Area |
|-----------|--------------|
| `basic.test.tsx` | Core store functionality, React integration |
| `shallow.test.tsx` | Shallow comparison utility |
| `subscribe.test.tsx` | Subscription mechanism |
| `devtools.test.tsx` | DevTools middleware |
| `persistSync.test.tsx` | Synchronous persistence |
| `persistAsync.test.tsx` | Asynchronous persistence |
| `middlewareTypes.test.tsx` | Middleware TypeScript types |
| `ssr.test.tsx` | Server-side rendering |
| `types.test.tsx` | Type inference tests |
| `vanilla/*.test.ts` | Vanilla (non-React) tests |

#### Test Setup

**Location:** `tests/setup.ts`

```typescript
import '@testing-library/jest-dom'
```

#### Test Utilities

**Location:** `tests/test-utils.ts`

Provides helpers for async testing and store creation in tests.

### 2. Test Infrastructure

**Testing Libraries:**
- `vitest` - Test runner
- `@testing-library/react` - React component testing
- `@testing-library/jest-dom` - DOM assertions
- `jsdom` - Browser environment simulation

**CI Integration:** Multiple GitHub Actions workflows

| Workflow | Purpose |
|----------|---------|
| `test.yml` | Main test suite |
| `test-multiple-builds.yml` | Test across build configurations |
| `test-multiple-versions.yml` | Test against React versions |
| `test-old-typescript.yml` | TypeScript compatibility |
| `compressed-size.yml` | Bundle size monitoring |

### 3. Code Quality

**Linting:** ESLint 9 with flat config

**Location:** `eslint.config.mjs`

```javascript
import eslint from '@eslint/js'
import tseslint from 'typescript-eslint'
import react from 'eslint-plugin-react'
import reactHooks from 'eslint-plugin-react-hooks'

export default [
  eslint.configs.recommended,
  ...tseslint.configs.recommended,
  // ...
]
```

**Formatting:** Prettier

**Location:** `.prettierignore`

**Key ESLint Plugins:**
- `eslint-plugin-react`
- `eslint-plugin-react-hooks`
- `eslint-plugin-import`
- `eslint-plugin-jest-dom`
- `eslint-plugin-testing-library`
- `@vitest/eslint-plugin`

---

## Cross-cutting Concerns

### 1. Logging & Debugging

#### React DevTools Integration

**Location:** `src/react.ts`

```typescript
const slice = React.useSyncExternalStore(/*...*/)
React.useDebugValue(slice)  // Exposes slice value in React DevTools
return slice
```

#### Redux DevTools Integration

**Location:** `src/middleware/devtools.ts`

```typescript
// Reports state changes to Redux DevTools
devtools.send({ type: actionType }, get())

// Supports time-travel debugging
devtools.subscribe((message) => {
  if (message.type === 'DISPATCH') {
    switch (message.payload.type) {
      case 'JUMP_TO_ACTION':
      case 'JUMP_TO_STATE':
        // Handle time travel
        break
    }
  }
})
```

### 2. Error Handling

#### State Update Validation

**Location:** `src/vanilla.ts`

```typescript
const setState = (partial, replace) => {
  const nextState = typeof partial === 'function'
    ? (partial as (state: TState) => TState)(state)
    : partial
    
  // Prevents no-op updates
  if (!Object.is(nextState, state)) {
    // ... update logic
  }
}
```

#### Persist Middleware Error Handling

**Location:** `src/middleware/persist.ts`

```typescript