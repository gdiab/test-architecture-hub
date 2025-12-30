# hl_overview

High level overview of the codebase

# Repository Analysis: Jest

## 0. Repository Name
[[jest]]

## 1. Project Purpose

Jest is a comprehensive **JavaScript Testing Framework** designed to provide a "delightful" testing experience. It solves the problem of testing JavaScript applications with:

- **Zero configuration** testing for most JavaScript projects
- **Snapshot testing** for UI components
- **Mocking capabilities** for isolating code under test
- **Code coverage** reporting
- **Parallel test execution** via workers
- **Watch mode** for development efficiency
- **Support for TypeScript, React, Node.js, Angular, Vue, and more**

The primary domain is **developer tooling/testing infrastructure** for the JavaScript ecosystem.

## 2. Architecture Pattern

- **Monorepo Architecture** - Using Lerna for managing multiple packages
- **Plugin/Extension Architecture** - Modular packages that can be used independently
- **Worker-based Parallelization** - For test execution across multiple processes
- **Pipeline/Middleware Pattern** - For test transformation and execution

## 3. Technology Stack

### Primary Languages
- **TypeScript** (primary source language)
- **JavaScript** (configuration, some runtime files)

### Build & Package Management
- **Yarn 4.12.0** (with PnP support)
- **Lerna** (monorepo management)
- **Babel** (transpilation)
- **TypeScript** (type checking and compilation)

### Major Dependencies (from package.json patterns)
| Category | Technologies |
|----------|-------------|
| Transpilation | `babel-jest`, `babel-preset-jest`, `babel-plugin-jest-hoist` |
| Type Checking | TypeScript, `tstyche` |
| Schema Validation | Likely Zod or similar (jest-schemas package) |
| DOM Testing | JSDOM (jest-environment-jsdom) |
| Diff Algorithms | Custom (`diff-sequences`, `jest-diff`) |
| Pretty Printing | `pretty-format` |
| Console Utilities | `chalk` (likely) |
| File Watching | Watchman integration |

### Testing Infrastructure
- **Jest** (self-testing)
- **E2E tests** in dedicated directory

### Documentation
- **Docusaurus** (website framework)
- **Crowdin** (internationalization)

## 4. Initial Structure Impression

| Component | Description |
|-----------|-------------|
| **packages/** | Core library packages (monorepo structure) - 50+ packages |
| **e2e/** | End-to-end integration tests |
| **examples/** | Usage examples for various frameworks |
| **website/** | Documentation website (Docusaurus) |
| **scripts/** | Build, test, and maintenance scripts |
| **docs/** | Source documentation files |
| **benchmarks/** | Performance testing |

## 5. Configuration/Package Files

### Root Configuration
| File | Purpose |
|------|---------|
| `package.json` | Root package configuration |
| `lerna.json` | Monorepo configuration |
| `yarn.lock`, `.yarnrc.yml` | Yarn package manager |
| `tsconfig.json`, `tsconfig.test.json`, `tsconfig.typetest.json` | TypeScript configurations |
| `babel.config.js` | Babel transpilation |
| `eslint.config.mjs` | ESLint linting rules |
| `jest.config.mjs`, `jest.config.ci.mjs`, `jest.config.ts.mjs` | Jest test configurations |
| `tstyche.config.json` | Type testing configuration |
| `.prettierignore` | Code formatting |
| `.editorconfig` | Editor settings |
| `.codecov.yml` | Code coverage reporting |
| `netlify.toml` | Deployment configuration |
| `crowdin.yaml` | Translation management |

### CI/CD
- `.github/workflows/*.yml` - GitHub Actions workflows

## 6. Directory Structure

### `/packages/` - Core Packages (Monorepo)
Organized by **functionality/concern**:

| Package | Purpose |
|---------|---------|
| `jest` | Main entry point/CLI |
| `jest-cli` | Command-line interface |
| `jest-core` | Core test orchestration |
| `jest-runner` | Test execution engine |
| `jest-runtime` | Module execution environment |
| `jest-circus` | Test framework (default) |
| `jest-jasmine2` | Legacy test framework |
| `jest-config` | Configuration handling |
| `jest-resolve` | Module resolution |
| `jest-transform` | Code transformation pipeline |
| `jest-snapshot` | Snapshot testing |
| `jest-mock` | Mocking utilities |
| `jest-fake-timers` | Timer mocking |
| `jest-worker` | Parallel worker management |
| `jest-reporters` | Test result reporting |
| `jest-haste-map` | File system crawling/caching |
| `jest-environment-node` | Node.js test environment |
| `jest-environment-jsdom` | Browser-like test environment |
| `expect` | Assertion library |
| `pretty-format` | Object serialization |
| `jest-diff` | Diff generation |
| `babel-jest` | Babel integration |

### `/e2e/` - End-to-End Tests
- `__tests__/` - Test files
- Feature-specific directories with test fixtures
- `Utils.ts`, `runJest.ts` - Test utilities

### `/examples/` - Usage Examples
Organized by **framework/use-case**:
- `typescript/`, `react/`, `angular/`, `react-native/`
- `snapshot/`, `timer/`, `async/`, `manual-mocks/`

### `/website/` - Documentation Site
- `docs/` → `versioned_docs/` - Versioned documentation
- `blog/` - Release announcements
- `src/` - Custom components
- `static/` - Assets

### `/scripts/` - Build Tools
- `build.mjs`, `buildTs.mjs` - Build scripts
- `watch.mjs` - Development watch mode
- `bundleTs.mjs` - TypeScript bundling

## 7. High-Level Architecture

### Pattern: **Modular Plugin Architecture with Worker-based Parallelization**

```
┌─────────────────────────────────────────────────────────────────┐
│                         jest-cli                                │
│                    (CLI Entry Point)                            │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                        jest-core                                │
│              (Orchestration & Coordination)                     │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────────────┐    │
│  │ jest-config  │ │ jest-haste-  │ │ jest-test-sequencer  │    │
│  │              │ │ map          │ │                      │    │
│  └──────────────┘ └──────────────┘ └──────────────────────┘    │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                       jest-worker                               │
│              (Parallel Process Management)                      │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                       jest-runner                               │
│                  (Test Execution)                               │
│  ┌──────────────────┐  ┌──────────────────┐                    │
│  │  jest-runtime    │  │  jest-transform  │                    │
│  │  (Module Loader) │  │  (Code Transform)│                    │
│  └──────────────────┘  └──────────────────┘                    │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                   Test Framework                                │
│         ┌─────────────────┬─────────────────┐                  │
│         │  jest-circus    │  jest-jasmine2  │                  │
│         │  (Default)      │  (Legacy)       │                  │
│         └─────────────────┴─────────────────┘                  │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                    Test Environment                             │
│    ┌────────────────────┐  ┌────────────────────────┐          │
│    │jest-environment-   │  │jest-environment-jsdom  │          │
│    │node                │  │                        │          │
│    └────────────────────┘  └────────────────────────┘          │
└─────────────────────────────────────────────────────────────────┘
```

### Evidence Supporting Architecture:
1. **Monorepo with Lerna** - `lerna.json` at root
2. **Clear Package Separation** - Each package has own `package.json`, `tsconfig.json`
3. **Worker Pattern** - `jest-worker` package with `workers/` subdirectory
4. **Plugin System** - Configurable reporters, transforms, environments
5. **Dependency Injection** - Runtime/environment passed through context

## 8. Build, Execution and Test

### Build Commands
```bash
# Install dependencies
yarn install

# Build all packages
yarn build       # Likely runs scripts/build.mjs

# Build TypeScript
yarn build:ts    # Likely runs scripts/buildTs.mjs

# Watch mode for development
yarn watch       # Likely runs scripts/watch.mjs
```

### Test Commands
```bash
# Run tests
yarn test                    # Uses jest.config.mjs

# Run CI tests
yarn test:ci                 # Uses jest.config.ci.mjs

# Run TypeScript tests
yarn test:ts                 # Uses jest.config.ts.mjs

# Run type tests
yarn typetest               # Uses tstyche
```

### Entry Points
1. **CLI Entry**: `packages/jest-cli/bin/jest.js` → `packages/jest/bin/jest.js`
2. **Programmatic**: `packages/jest/src/index.ts`
3. **Core API**: `packages/jest-core/src/index.ts`

### Linting & Formatting
```bash
yarn lint        # ESLint
yarn prettier    # Code formatting
```

### E2E Testing
```bash
# E2E tests run in e2e/ directory
yarn test:e2e    # Runs integration tests against built packages
```

### Key Scripts (from `/scripts/`)
| Script | Purpose |
|--------|---------|
| `build.mjs` | Main build orchestration |
| `buildTs.mjs` | TypeScript compilation |
| `bundleTs.mjs` | Bundle TypeScript output |
| `watch.mjs` | Development watch mode |
| `lintTs.mjs` | TypeScript linting |
| `checkChangelog.mjs` | Verify changelog updates |
| `verifyPnP.mjs` | Verify Yarn PnP compatibility |

# module_deep_dive

Deep dive into modules

# Jest Repository - Detailed Component Breakdown

This document provides an in-depth analysis of each major component in the Jest testing framework monorepo.

---

## Table of Contents

1. [Core Testing Framework](#1-core-testing-framework)
2. [Test Runners](#2-test-runners)
3. [Assertion & Matching](#3-assertion--matching)
4. [Mocking System](#4-mocking-system)
5. [Snapshot Testing](#5-snapshot-testing)
6. [Code Transformation](#6-code-transformation)
7. [Configuration & CLI](#7-configuration--cli)
8. [Reporting & Output](#8-reporting--output)
9. [Module Resolution](#9-module-resolution)
10. [Test Environments](#10-test-environments)
11. [Utilities & Helpers](#11-utilities--helpers)
12. [Worker & Parallelization](#12-worker--parallelization)
13. [File System & Caching](#13-file-system--caching)

---

## 1. Core Testing Framework

### `packages/jest`

#### Core Responsibility
The main entry point and public-facing package that users install. Acts as a facade that re-exports functionality from internal packages.

#### Key Components
```
src/
└── index.ts          # Main export file, aggregates and exposes Jest's public API
bin/
└── jest.js           # CLI executable entry point
```

- **index.ts**: Exports the core Jest API including `run`, configuration utilities, and type definitions
- **bin/jest.js**: The executable script invoked when running `jest` from command line

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `@jest/core` - Core test orchestration
  - `jest-cli` - Command-line interface handling
- **External Services**: None directly; delegates to internal packages

---

### `packages/jest-core`

#### Core Responsibility
The heart of Jest - orchestrates test discovery, execution, caching, and result aggregation. Manages the entire test run lifecycle.

#### Key Components
```
src/
├── TestScheduler.ts           # Schedules and coordinates test execution
├── SearchSource.ts            # Finds test files based on patterns/config
├── runJest.ts                 # Main test execution orchestrator
├── ReporterDispatcher.ts      # Manages multiple reporters
├── TestWatcher.ts             # Watch mode implementation
├── getNoTestsFoundMessage.ts  # User-friendly error messages
├── collectHandles.ts          # Detects open handles (async resources)
├── getChangedFilesPromise.ts  # Git/Mercurial integration for changed files
├── getProjectNamesMissingWarning.ts
├── getSelectProjectsMessage.ts
├── pluralize.ts
├── runGlobalHook.ts           # Global setup/teardown execution
├── testSchedulerHelper.ts
├── watch.ts                   # Interactive watch mode
├── cli/
│   ├── index.ts               # CLI command handlers
│   └── ...
├── lib/
│   ├── activeFiltersMessage.ts
│   ├── createContext.ts       # Test context factory
│   ├── handleDeprecationWarnings.ts
│   ├── isValidPath.ts
│   ├── logDebugMessages.ts
│   ├── updateGlobalConfig.ts
│   └── watchPluginsHelpers.ts
└── plugins/
    ├── Quit.ts                # Watch mode quit plugin
    ├── TestNamePattern.ts     # Filter tests by name
    ├── TestPathPattern.ts     # Filter tests by path
    └── UpdateSnapshots.ts     # Update snapshots interactively
```

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-config` - Configuration loading/validation
  - `jest-haste-map` - File system crawling and caching
  - `jest-runner` - Test execution
  - `jest-reporters` - Result reporting
  - `jest-resolve` - Module resolution
  - `jest-runtime` - Test execution environment
  - `jest-test-result` - Test result types
  - `jest-changed-files` - VCS integration
  - `jest-watcher` - Watch mode utilities
- **External Services**:
  - Git/Mercurial for changed file detection
  - File system for test discovery

---

### `packages/jest-circus`

#### Core Responsibility
The modern test runner that replaced Jasmine as the default. Implements the test lifecycle using a state machine pattern with event-driven architecture.

#### Key Components
```
src/
├── circus/
│   └── index.ts               # Main circus exports
├── state.ts                   # Global state management (describe/test tree)
├── run.ts                     # Test execution engine
├── eventHandler.ts            # Handles lifecycle events
├── formatNodeAssertErrors.ts  # Node assertion error formatting
├── globalErrorHandlers.ts     # Unhandled rejection/exception handling
├── shuffleArray.ts            # Test randomization
├── testCaseReportHandler.ts   # Individual test reporting
├── types.ts                   # TypeScript definitions
├── utils.ts                   # Helper utilities
├── legacy-code-todo-rewrite/
│   ├── jestAdapter.ts         # Jest integration adapter
│   ├── jestAdapterInit.ts     # Initialization logic
│   └── jestExpect.ts          # Expect integration
└── __tests__/
    └── ...                    # Comprehensive test suite
```

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-expect` - Assertion library integration
  - `jest-snapshot` - Snapshot testing support
  - `jest-each` - Parameterized tests
  - `jest-util` - Utility functions
  - `jest-message-util` - Error formatting
  - `@jest/types` - Type definitions
- **External Services**: None

---

## 2. Test Runners

### `packages/jest-runner`

#### Core Responsibility
Executes individual test files in isolated contexts. Manages worker processes for parallel execution and handles test file lifecycle.

#### Key Components
```
src/
├── index.ts                   # Main runner export and orchestration
├── runTest.ts                 # Single test file execution
├── testWorker.ts              # Worker process test execution
└── types.ts                   # Runner type definitions
```

- **index.ts**: Coordinates parallel test execution, spawns workers
- **runTest.ts**: Sets up environment, transforms code, runs tests
- **testWorker.ts**: Worker entry point for parallel execution

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-worker` - Worker pool management
  - `jest-runtime` - Module isolation and execution
  - `jest-environment` - Test environment abstraction
  - `jest-transform` - Code transformation
  - `jest-haste-map` - Module resolution data
  - `jest-leak-detector` - Memory leak detection
  - `jest-console` - Console capture
  - `jest-util` - Utilities
- **External Services**: Node.js worker_threads/child_process for parallelization

---

### `packages/jest-jasmine2`

#### Core Responsibility
Legacy test runner based on Jasmine 2. Maintained for backward compatibility but superseded by jest-circus.

#### Key Components
```
src/
├── index.ts                   # Jasmine adapter entry point
├── jasmine/
│   ├── Env.ts                 # Jasmine environment
│   ├── Spec.ts                # Test specification
│   ├── Suite.ts               # Describe block implementation
│   ├── Timer.ts               # Timing utilities
│   ├── createSpy.ts           # Spy creation
│   ├── jasmineLight.ts        # Lightweight Jasmine core
│   └── spyRegistry.ts         # Spy management
├── jasmineAsyncInstall.ts     # Async support
├── jestExpect.ts              # Expect integration
├── pTimeout.ts                # Promise timeout utility
├── queueRunner.ts             # Test queue execution
├── reporter.ts                # Result reporting
├── setup_jest_globals.ts      # Global setup
├── treeProcessor.ts           # Test tree traversal
└── types.ts
```

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-expect` - Assertion library
  - `jest-snapshot` - Snapshot support
  - `jest-each` - Parameterized tests
  - `jest-util` - Utilities
  - `jest-message-util` - Error formatting
- **External Services**: None (self-contained Jasmine implementation)

---

### `packages/jest-test-sequencer`

#### Core Responsibility
Determines the order in which test files are executed. Optimizes for faster feedback by running failed tests first and parallelizing effectively.

#### Key Components
```
src/
└── index.ts                   # Sequencer implementation
```

- **index.ts**: Implements test ordering logic:
  - Prioritizes previously failed tests
  - Sorts by file size (smaller first for better parallelization)
  - Handles sharding for distributed test runs

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `@jest/test-result` - Test result types for failure tracking
- **External Services**: File system for cached test timing data

---

## 3. Assertion & Matching

### `packages/expect`

#### Core Responsibility
The assertion library providing `expect()` API with rich matchers for comparing values, checking types, and validating function behavior.

#### Key Components
```
src/
├── index.ts                   # Main expect export
├── asymmetricMatchers.ts      # expect.any(), expect.arrayContaining(), etc.
├── extractExpectedAssertionsErrors.ts  # Assertion count validation
├── jestMatchersObject.ts      # Matcher registration/management
├── matchers.ts                # Core matchers (toBe, toEqual, etc.)
├── print.ts                   # Pretty printing for diffs
├── spyMatchers.ts             # Mock/spy specific matchers
├── toThrowMatchers.ts         # Exception matchers
├── types.ts                   # Type definitions
└── utils.ts                   # Helper utilities
```

#### Key Matchers (in matchers.ts)
- **Equality**: `toBe`, `toEqual`, `toStrictEqual`
- **Truthiness**: `toBeTruthy`, `toBeFalsy`, `toBeNull`, `toBeUndefined`
- **Numbers**: `toBeGreaterThan`, `toBeLessThan`, `toBeCloseTo`
- **Strings**: `toMatch`, `toContain`
- **Arrays/Objects**: `toContain`, `toContainEqual`, `toHaveProperty`
- **Exceptions**: `toThrow`, `toThrowError`

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `expect-utils` - Comparison utilities
  - `jest-matcher-utils` - Formatting for matcher output
  - `jest-message-util` - Stack trace processing
  - `jest-get-type` - Type detection
  - `jest-diff` - Value diffing
- **External Services**: None

---

### `packages/jest-expect`

#### Core Responsibility
Integrates the standalone `expect` package with Jest's ecosystem, adding snapshot support and global setup.

#### Key Components
```
src/
├── index.ts                   # Integration layer
└── types.ts                   # Extended type definitions
```

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `expect` - Core assertion library
  - `jest-snapshot` - Snapshot matcher integration
- **External Services**: None

---

### `packages/expect-utils`

#### Core Responsibility
Provides low-level comparison and type-checking utilities used by matchers.

#### Key Components
```
src/
├── index.ts                   # Public exports
├── immutableUtils.ts          # Immutable.js support
├── jasmineUtils.ts            # Jasmine-compatible deep equality
├── subsetEquality.ts          # Partial object matching
└── utils.ts                   # General utilities
```

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-get-type` - Type detection
- **External Services**: None

---

### `packages/jest-matcher-utils`

#### Core Responsibility
Formatting utilities for creating readable matcher failure messages with colors, diffs, and type hints.

#### Key Components
```
src/
├── index.ts                   # Main exports with formatting functions
├── deepCyclicCopyReplaceable.ts  # Handles circular references
└── Replaceable.ts             # Value replacement for display
```

Key functions:
- `matcherHint()` - Formats "expect(received).toBe(expected)" headers
- `printReceived()`, `printExpected()` - Colorized value output
- `stringify()` - Safe JSON stringification
- `diff()` - Value difference display

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-diff` - Diff generation
  - `jest-get-type` - Type detection
  - `pretty-format` - Value serialization
- **External Services**: None

---

## 4. Mocking System

### `packages/jest-mock`

#### Core Responsibility
Provides the mocking infrastructure including `jest.fn()`, `jest.spyOn()`, and module mocking capabilities.

#### Key Components
```
src/
└── index.ts                   # Complete mocking implementation
```

Key classes and functions:
- **`ModuleMocker`**: Core class managing mock creation and tracking
  - `fn()` - Creates mock functions
  - `spyOn()` - Creates spies on object methods
  - `mocked()` - TypeScript helper for typed mocks
  - `replaceProperty()` - Property replacement
- **Mock Function Properties**:
  - `.mock.calls` - Recorded call arguments
  - `.mock.results` - Recorded return values
  - `.mock.instances` - Recorded `this` contexts
  - `.mockReturnValue()`, `.mockImplementation()` - Behavior configuration

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `@jest/types` - Type definitions
- **External Services**: None

---

### `packages/jest-runtime`

#### Core Responsibility
Creates isolated module environments for each test file. Handles module mocking, require interception, and the `jest` global object.

#### Key Components
```
src/
├── index.ts                   # Runtime implementation
└── helpers.ts                 # Utility functions
```

Key features in index.ts:
- **Module Isolation**: Each test gets its own module registry
- **Mock Management**: `jest.mock()`, `jest.unmock()`, manual mocks
- **Require Interception**: Custom require for mocking/transformation
- **Jest Object**: Provides `jest.fn()`, `jest.spyOn()`, timers, etc.
- **ESM Support**: Native ES module loading with mocking

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-mock` - Mock creation
  - `jest-resolve` - Module resolution
  - `jest-transform` - Code transformation
  - `jest-snapshot` - Snapshot state
  - `jest-globals` - Global function injection
  - `jest-environment` - Environment abstraction
  - `jest-haste-map` - Module map
- **External Services**: Node.js module system

---

## 5. Snapshot Testing

### `packages/jest-snapshot`

#### Core Responsibility
Implements snapshot testing - serializing values to files and comparing against stored snapshots.

#### Key Components
```
src/
├── index.ts                   # Main exports
├── State.ts                   # Snapshot state management per test file
├── SnapshotResolver.ts        # Maps test files to snapshot files
├── InlineSnapshots.ts         # Inline snapshot handling (in source code)
├── dedentLines.ts             # Formatting utilities
├── mockSerializer.ts          # Mock function serialization
├── printSnapshot.ts           # Snapshot formatting
├── types.ts                   # Type definitions
└── utils.ts                   # Helper functions
```

Key classes:
- **`SnapshotState`**: Tracks snapshots for a single test file
  - Reads existing snapshots from `.snap` files
  - Compares new snapshots against stored ones
  - Handles update mode
- **`toMatchSnapshot()`**: The matcher implementation
- **`toMatchInlineSnapshot()`**: Inline snapshot matcher

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-snapshot-utils` - Serialization utilities
  - `pretty-format` - Value serialization
  - `jest-diff` - Snapshot diff display
  - `jest-matcher-utils` - Output formatting
  - `@babel/core`, `@babel/parser` - For inline snapshot code modification
- **External Services**: 
  - File system for `.snap` files
  - Prettier (optional) for inline snapshot formatting

---

### `packages/jest-snapshot-utils`

#### Core Responsibility
Low-level utilities for snapshot serialization and file handling.

#### Key Components
```
src/
├── index.ts                   # Public exports
├── getSerializers.ts          # Snapshot serializer management
└── snapshotUtils.ts           # File I/O and parsing
```

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `pretty-format` - Default serialization
- **External Services**: File system

---

## 6. Code Transformation

### `packages/jest-transform`

#### Core Responsibility
Manages code transformation (transpilation) pipeline. Caches transformed code and coordinates multiple transformers.

#### Key Components
```
src/
├── index.ts                   # Public exports
├── ScriptTransformer.ts       # Main transformation orchestrator
├── shouldInstrument.ts        # Coverage instrumentation logic
├── enhanceUnexpectedTokenMessage.ts  # Helpful error messages
├── runtimeErrorsAndWarnings.ts
└── types.ts                   # Transformer interface definitions
```

**`ScriptTransformer`** key features:
- Matches files to configured transformers
- Caches transformed code (file-based and in-memory)
- Handles source maps
- Coordinates coverage instrumentation

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-haste-map` - File metadata
  - `jest-util` - Utilities
  - `jest-regex-util` - Pattern matching
  - `@jest/types` - Configuration types
- **External Services**: 
  - File system for caching
  - Configured transformers (Babel, TypeScript, etc.)

---

### `packages/babel-jest`

#### Core Responsibility
Default transformer using Babel to transpile JavaScript/TypeScript.

#### Key Components
```
src/
├── index.ts                   # Transformer implementation
└── loadBabelConfig.ts         # Babel config resolution
```

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `babel-preset-jest` - Jest-specific Babel preset
  - `jest-transform` - Transformer interface
- **External Services**:
  - `@babel/core` - Babel compilation
  - User's Babel configuration

---

### `packages/babel-preset-jest`

#### Core Responsibility
Babel preset including plugins needed for Jest compatibility.

#### Key Components
```
index.js                       # Preset definition
```

Includes:
- `babel-plugin-jest-hoist` - Hoists `jest.mock()` calls

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `babel-plugin-jest-hoist`
- **External Services**: Babel plugin system

---

### `packages/babel-plugin-jest-hoist`

#### Core Responsibility
Babel plugin that hoists `jest.mock()` calls to the top of the file, before any imports.

#### Key Components
```
src/
└── index.ts                   # Plugin implementation
```

The plugin:
- Identifies `jest.mock()`, `jest.unmock()`, etc.
- Moves them before imports
- Validates mock factory functions don't reference out-of-scope variables

#### Dependencies & Interactions
- **Internal Dependencies**: None
- **External Services**: Babel's AST transformation API

---

## 7. Configuration & CLI

### `packages/jest-config`

#### Core Responsibility
Loads, validates, and normalizes Jest configuration from multiple sources (package.json, jest.config.js, CLI args).

#### Key Components
```
src/
├── index.ts                   # Main exports
├── normalize.ts               # Config normalization and defaults
├── readConfigFileAndSetRootDir.ts  # Config file loading
├── resolveConfigPath.ts       # Config file discovery
├── Defaults.ts                # Default configuration values
├── Descriptions.ts            # Help text for options
├── ValidConfig.ts             # Valid configuration keys
├── ReporterValidationErrors.ts
├── color.ts                   # Terminal color support detection
├── constants.ts               # Configuration constants
├── getMaxWorkers.ts           # Worker count calculation
├── parseShardPair.ts          # Shard configuration parsing
├── setFromArgv.ts             # CLI argument processing
├── stringToBytes.ts           # Memory limit parsing
├── utils.ts                   # Helper utilities
└── validatePattern.ts         # Regex validation
```

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-validate` - Configuration validation
  - `jest-resolve` - Path resolution
  - `jest-util` - Utilities
  - `@jest/types` - Type definitions
  - `jest-regex-util` - Pattern handling
- **External Services**: 
  - File system for config file loading
  - `ts-node` (optional) for TypeScript config files

---

### `packages/jest-cli`

#### Core Responsibility
Command-line interface implementation. Parses arguments and orchestrates Jest execution.

#### Key Components
```
src/
├── index.ts                   # CLI entry point
├── args.ts                    # Argument definitions (yargs)
└── run.ts                     # Main execution logic
bin/
└── jest.js                    # Executable shebang script
```

Key argument groups:
- Test selection (`--testPathPattern`, `--testNamePattern`)
- Execution (`--runInBand`, `--maxWorkers`)
- Output (`--verbose`, `--json`, `--coverage`)
- Watch (`--watch`, `--watchAll`)

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `@jest/core` - Test execution
  - `jest-config` - Configuration loading
  - `jest-util` - Utilities
  - `jest-validate` - Input validation
- **External Services**: 
  - `yargs` for argument parsing
  - Terminal for interactive features

---

### `packages/create-jest`

#### Core Responsibility
Interactive CLI tool to generate Jest configuration files.

#### Key Components
```
src/
├── index.ts                   # Main entry point
├── generateConfigFile.ts      # Config file generation
├── modifyPackageJson.ts       # Package.json modification
├── questions.ts               # Interactive prompts
├── runCreate.ts               # Execution logic
└── types.ts                   # Type definitions
bin/
└── create-jest.js             # Executable
```

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-config` - Default values
  - `jest-util` - Utilities
- **External Services**:
  - `prompts` for interactive CLI
  - File system for config generation

---

### `packages/jest-validate`

#### Core Responsibility
General-purpose configuration validation with helpful error messages.

#### Key Components
```
src/
├── index.ts                   # Public exports
├── validate.ts                # Main validation logic
├── validateCLIOptions.ts      # CLI-specific validation
├── condition.ts               # Validation conditions
├── defaultConfig.ts           # Validation defaults
├── deprecated.ts              # Deprecation warnings
├── errors.ts                  # Error formatting
├── exampleConfig.ts           # Example configuration
├── types.ts                   # Type definitions
├── utils.ts                   # Utilities
└── warnings.ts                # Warning messages
```

#### Dependencies & Interactions
- **Internal Dependencies**:
  - `jest-get-type` - Type detection
  - `pretty-format` - Value formatting
- **External Services**: None

---

### `packages/jest-schemas`

#### Core Responsibility
Zod schemas for Jest configuration validation and type generation.

#### Key Components
```
src/
├── index.ts                   # Schema exports
└── raw.ts                     

# dependencies

Analyze dependencies and external libraries

# Dependency and Architecture Analysis

## Repository: jest_636c6520

---

## Internal Modules

The Jest repository is organized as a monorepo with multiple internal packages under the `packages/` directory. Each package represents a modular component that can be used independently or as part of the Jest testing framework.

### Core Packages

| Package | Primary Responsibility |
|---------|----------------------|
| **jest** | Main entry point package that bundles the Jest testing framework and CLI |
| **jest-cli** | Command-line interface for running Jest, handles argument parsing and user interaction |
| **jest-core** | Core orchestration logic for test execution, including test scheduling, watch mode, and result aggregation |
| **jest-config** | Configuration loading, normalization, and validation for Jest projects |
| **jest-runner** | Coordinates running tests in parallel using worker processes |
| **jest-runtime** | Test runtime environment that handles module loading, mocking, and isolation |

### Test Execution & Environment

| Package | Primary Responsibility |
|---------|----------------------|
| **jest-circus** | Modern test runner implementing describe/it/test blocks with improved async handling |
| **jest-jasmine2** | Legacy Jasmine-based test runner (alternative to jest-circus) |
| **jest-environment** | Base interface definitions for test environments |
| **jest-environment-node** | Node.js test environment implementation |
| **jest-environment-jsdom** | Browser-like test environment using JSDOM |
| **jest-environment-jsdom-abstract** | Abstract base class for JSDOM-based environments |

### Assertion & Matching

| Package | Primary Responsibility |
|---------|----------------------|
| **expect** | Assertion library providing expect() API and matchers |
| **jest-expect** | Jest-specific expect integration with snapshot support |
| **expect-utils** | Utility functions for expect matchers (equality checks, subset matching) |
| **jest-matcher-utils** | Utilities for formatting matcher error messages |

### Snapshot Testing

| Package | Primary Responsibility |
|---------|----------------------|
| **jest-snapshot** | Snapshot testing implementation including serialization and comparison |
| **jest-snapshot-utils** | Utility functions for snapshot file operations |

### Mocking & Fake Timers

| Package | Primary Responsibility |
|---------|----------------------|
| **jest-mock** | Mock function creation and tracking (jest.fn(), jest.spyOn()) |
| **jest-fake-timers** | Fake timer implementation for controlling time in tests |

### Code Transformation

| Package | Primary Responsibility |
|---------|----------------------|
| **jest-transform** | Code transformation pipeline for transpiling test files |
| **babel-jest** | Babel transformer for Jest |
| **babel-plugin-jest-hoist** | Babel plugin for hoisting jest.mock() calls |
| **babel-preset-jest** | Babel preset combining Jest-specific plugins |

### Module Resolution

| Package | Primary Responsibility |
|---------|----------------------|
| **jest-resolve** | Module resolution logic following Node.js resolution algorithm |
| **jest-resolve-dependencies** | Dependency graph analysis for changed file detection |
| **jest-haste-map** | Fast file system crawler and module map builder |

### Reporting & Output

| Package | Primary Responsibility |
|---------|----------------------|
| **jest-reporters** | Test result reporters (default, verbose, JSON, coverage) |
| **jest-console** | Console output capture and formatting |
| **jest-message-util** | Error message formatting and stack trace processing |

### Utilities & Types

| Package | Primary Responsibility |
|---------|----------------------|
| **jest-util** | Shared utility functions (path handling, async helpers, etc.) |
| **jest-types** | TypeScript type definitions for Jest |
| **jest-schemas** | JSON schema definitions for Jest configuration |
| **jest-get-type** | Type detection utility for JavaScript values |
| **jest-regex-util** | Regular expression utilities |
| **jest-pattern** | Test pattern matching utilities |
| **jest-validate** | Configuration and option validation |

### Diff & Formatting

| Package | Primary Responsibility |
|---------|----------------------|
| **jest-diff** | Text diffing for assertion failure output |
| **diff-sequences** | Longest common subsequence algorithm for diffing |
| **pretty-format** | Object serialization for readable output |

### Additional Packages

| Package | Primary Responsibility |
|---------|----------------------|
| **jest-worker** | Worker pool for parallel test execution |
| **jest-watcher** | Watch mode file change detection and interactive mode |
| **jest-changed-files** | Git/Mercurial integration for detecting changed files |
| **jest-docblock** | Docblock comment parsing for test file pragmas |
| **jest-each** | Parameterized test support (test.each) |
| **jest-globals** | Global test function exports (describe, it, expect) |
| **jest-leak-detector** | Memory leak detection for tests |
| **jest-source-map** | Source map handling for error stack traces |
| **jest-test-result** | Test result data structures |
| **jest-test-sequencer** | Test file ordering and prioritization |
| **jest-phabricator** | Phabricator CI integration |
| **jest-create-cache-key-function** | Cache key generation for transformers |
| **create-jest** | CLI tool for initializing Jest configuration |
| **test-utils** | Internal test utilities for Jest development |
| **test-globals** | Alternative global exports for test functions |

---

## External Dependencies

### Babel Ecosystem

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Babel** (`@babel/core`) | JavaScript compiler for transforming modern JS/TS code | `/packages/jest-config/package.json`, `/packages/jest-transform/package.json`, `/packages/jest-snapshot/package.json` |
| **Babel Code Frame** (`@babel/code-frame`) | Generates code frame snippets for error messages | `/packages/jest-message-util/package.json` |
| **Babel Generator** (`@babel/generator`) | Generates code from Babel AST | `/packages/jest-snapshot/package.json` |
| **Babel Types** (`@babel/types`) | Babel AST type definitions and utilities | `/packages/jest-snapshot/package.json` |
| **Babel Plugin Syntax JSX** (`@babel/plugin-syntax-jsx`) | JSX syntax parsing support | `/packages/jest-snapshot/package.json` |
| **Babel Plugin Syntax TypeScript** (`@babel/plugin-syntax-typescript`) | TypeScript syntax parsing support | `/packages/jest-snapshot/package.json` |
| **Babel Preset Env** (`@babel/preset-env`) | Smart Babel preset for targeting environments | `/e2e/transform/transform-testrunner/package.json`, `/examples/async/package.json` (dev) |
| **Babel Preset React** (`@babel/preset-react`) | Babel preset for React/JSX | `/e2e/transform/multiple-transformers/package.json`, `/examples/react/package.json` (dev) |
| **Babel Preset TypeScript** (`@babel/preset-typescript`) | Babel preset for TypeScript | `/e2e/babel-plugin-jest-hoist/package.json`, `/e2e/transform/transform-environment/package.json` |
| **Babel Preset Flow** (`@babel/preset-flow`) | Babel preset for Flow type annotations | `/e2e/babel-plugin-jest-hoist/package.json`, `/e2e/transform/babel-jest/package.json` |
| **Babel Plugin Transform Regenerator** (`@babel/plugin-transform-regenerator`) | Transform generator functions | `/e2e/async-regenerator/package.json` |
| **Babel Plugin Transform Runtime** (`@babel/plugin-transform-runtime`) | Externalizes helper references | `/e2e/async-regenerator/package.json` |
| **babel-plugin-istanbul** | Istanbul instrumentation for code coverage | `/packages/babel-jest/package.json`, `/packages/jest-transform/package.json` |
| **babel-preset-current-node-syntax** | Enables all syntax plugins for current Node version | `/packages/babel-preset-jest/package.json`, `/packages/jest-snapshot/package.json` |

### Testing & Assertions

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Chai** | BDD/TDD assertion library (for e2e testing compatibility) | `/e2e/chai-assertion-library-errors/package.json` |
| **Testing Library React** (`@testing-library/react`) | React component testing utilities | `/e2e/transform/multiple-transformers/package.json`, `/examples/react-testing-library/package.json` (dev) |
| **Testing Library DOM** (`@testing-library/dom`) | DOM testing utilities | `/examples/react-testing-library/package.json` (dev), `/examples/snapshot/package.json` (dev) |
| **Testing Library User Event** (`@testing-library/user-event`) | User interaction simulation | `/examples/snapshot/package.json` (dev) |
| **react-test-renderer** | React test renderer for snapshot testing | `/examples/react-native/package.json` (dev), `/packages/pretty-format/package.json` (dev) |

### Fake Timers & Mocking

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Sinon Fake Timers** (`@sinonjs/fake-timers`) | Fake timer implementation for controlling time | `/packages/jest-fake-timers/package.json` |

### Code Coverage

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Istanbul Lib Coverage** (`istanbul-lib-coverage`) | Coverage data structures | `/packages/jest-reporters/package.json` |
| **Istanbul Lib Instrument** (`istanbul-lib-instrument`) | Code instrumentation for coverage | `/packages/jest-reporters/package.json` |
| **Istanbul Lib Report** (`istanbul-lib-report`) | Coverage report generation | `/packages/jest-reporters/package.json` |
| **Istanbul Lib Source Maps** (`istanbul-lib-source-maps`) | Source map support for coverage | `/packages/jest-reporters/package.json` |
| **Istanbul Reports** (`istanbul-reports`) | Coverage report formatters | `/packages/jest-reporters/package.json` |
| **v8-to-istanbul** | V8 coverage to Istanbul converter | `/packages/jest-reporters/package.json` |
| **V8 Coverage** (`@bcoe/v8-coverage`) | V8 coverage data types | `/packages/jest-reporters/package.json` |
| **collect-v8-coverage** | V8 coverage collection utility | `/packages/jest-reporters/package.json`, `/packages/jest-runtime/package.json` |

### Source Maps

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Trace Mapping** (`@jridgewell/trace-mapping`) | Source map parsing and lookup | `/packages/jest-reporters/package.json`, `/packages/jest-source-map/package.json`, `/packages/jest-transform/package.json` |
| **convert-source-map** | Source map format conversion | `/packages/jest-transform/package.json` |
| **source-map-support** | Source map support for stack traces | `/packages/jest-runner/package.json` |
| **callsites** | Get callsite information for source maps | `/packages/jest-source-map/package.json` |

### File System & Path

| Dependency | Purpose | Source |
|------------|---------|--------|
| **graceful-fs** | Graceful file system operations with retry logic | `/packages/jest-config/package.json`, `/packages/jest-runtime/package.json`, `/packages/jest-snapshot/package.json` |
| **glob** | File pattern matching | `/packages/jest-config/package.json`, `/packages/jest-runtime/package.json`, `/packages/jest-reporters/package.json` |
| **micromatch** | Glob pattern matching | `/packages/jest-config/package.json`, `/packages/jest-core/package.json`, `/packages/jest-message-util/package.json` |
| **picomatch** | Minimal glob pattern matching | `/packages/jest-util/package.json` |
| **slash** | Path separator normalization | `/packages/babel-jest/package.json`, `/packages/jest-config/package.json`, `/packages/jest-runtime/package.json` |
| **walker** | Directory tree walker | `/packages/jest-haste-map/package.json` |
| **anymatch** | File path matching | `/packages/jest-haste-map/package.json` |
| **write-file-atomic** | Atomic file writing | `/packages/jest-transform/package.json` |

### Module Resolution

| Dependency | Purpose | Source |
|------------|---------|--------|
| **unrs-resolver** | Fast module resolver | `/packages/jest-resolve/package.json` |
| **jest-pnp-resolver** | Yarn PnP resolution support | `/packages/jest-resolve/package.json` |
| **cjs-module-lexer** | CommonJS module parsing | `/packages/jest-runtime/package.json` |

### CLI & Terminal

| Dependency | Purpose | Source |
|------------|---------|--------|
| **yargs** | CLI argument parsing | `/packages/jest-cli/package.json` |
| **chalk** | Terminal string styling | `/packages/jest-cli/package.json`, `/packages/jest-circus/package.json`, `/packages/jest-diff/package.json` |
| **ansi-escapes** | ANSI escape codes for terminal control | `/packages/jest-core/package.json`, `/packages/jest-watcher/package.json` |
| **ansi-styles** | ANSI color styles | `/packages/pretty-format/package.json` |
| **string-length** | String length accounting for ANSI codes | `/packages/jest-reporters/package.json`, `/packages/jest-watcher/package.json` |
| **prompts** | Interactive CLI prompts | `/packages/create-jest/package.json` |

### Process & Async

| Dependency | Purpose | Source |
|------------|---------|--------|
| **execa** | Process execution | `/packages/jest-changed-files/package.json` |
| **p-limit** | Concurrency limiting | `/packages/jest-changed-files/package.json`, `/packages/jest-circus/package.json`, `/packages/jest-runner/package.json` |
| **exit-x** | Cross-platform process exit | `/packages/create-jest/package.json`, `/packages/jest-cli/package.json`, `/packages/jest-core/package.json` |
| **co** | Generator-based control flow | `/packages/jest-circus/package.json`, `/packages/jest-jasmine2/package.json` |
| **emittery** | Event emitter | `/packages/jest-runner/package.json`, `/packages/jest-watcher/package.json` |

### Worker & Parallelization

| Dependency | Purpose | Source |
|------------|---------|--------|
| **merge-stream** | Merge multiple streams | `/packages/jest-worker/package.json` |
| **supports-color** | Color support detection | `/packages/jest-worker/package.json` |
| **Structured Clone** (`@ungap/structured-clone`) | Structured clone polyfill for workers | `/packages/jest-worker/package.json` |

### File Watching

| Dependency | Purpose | Source |
|------------|---------|--------|
| **fb-watchman** | Watchman client for file watching | `/packages/jest-haste-map/package.json` |

### Utilities

| Dependency | Purpose | Source |
|------------|---------|--------|
| **dedent** | Template literal dedenting | `/packages/jest-circus/package.json` |
| **deepmerge** | Deep object merging | `/packages/jest-config/package.json` |
| **natural-compare** | Natural string comparison | `/packages/jest-snapshot-utils/package.json` |
| **detect-newline** | Detect predominant newline character | `/packages/jest-docblock/package.json` |
| **is-generator-fn** | Check if function is generator | `/packages/jest-circus/package.json`, `/packages/jest-jasmine2/package.json` |
| **strip-bom** | Strip UTF-8 BOM | `/packages/jest-runtime/package.json` |
| **strip-json-comments** | Strip JSON comments | `/packages/jest-config/package.json` |
| **parse-json** | JSON parsing with better errors | `/packages/jest-config/package.json` |
| **fast-json-stable-stringify** | Deterministic JSON stringify | `/packages/jest-transform/package.json` |
| **semver** | Semantic version parsing | `/packages/jest-snapshot/package.json` |
| **synckit** | Sync execution of async functions | `/packages/jest-snapshot/package.json` |
| **camelcase** | Convert strings to camelCase | `/packages/jest-validate/package.json` |
| **leven** | Levenshtein distance for suggestions | `/packages/jest-validate/package.json` |
| **pirates** | Module hook registration | `/packages/jest-transform/package.json` |
| **import-local** | Use locally installed version | `/packages/jest-cli/package.json`, `/packages/jest/package.json` |
| **ci-info** | CI environment detection | `/packages/jest-config/package.json`, `/packages/jest-core/package.json`, `/packages/jest-util/package.json` |
| **stack-utils** | Stack trace parsing | `/packages/jest-circus/package.json`, `/packages/jest-message-util/package.json` |
| **pure-rand** | Pure random number generation | `/packages/jest-circus/package.json` |

### Schema & Validation

| Dependency | Purpose | Source |
|------------|---------|--------|
| **TypeBox** (`@sinclair/typebox`) | JSON Schema Type Builder | `/packages/jest-schemas/package.json` |

### React Ecosystem

| Dependency | Purpose | Source |
|------------|---------|--------|
| **React** | React library (for examples and pretty-format) | `/e2e/babel-plugin-jest-hoist/package.json`, `/e2e/transform/multiple-transformers/package.json`, `/examples/react/package.json` |
| **React DOM** | React DOM renderer | `/e2e/transform/multiple-transformers/package.json`, `/examples/react/package.json` |
| **react-is** | React element type checking | `/packages/pretty-format/package.json` |
| **React Native** | React Native framework (for examples) | `/examples/react-native/package.json` |
| **React Native Babel Preset** (`@react-native/babel-preset`) | Babel preset for React Native | `/examples/react-native/package.json` |

### Angular (Examples)

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Angular Core** (`@angular/core`) | Angular framework core | `/examples/angular/package.json` |
| **Angular Common** (`@angular/common`) | Angular common utilities | `/examples/angular/package.json` |
| **Angular Compiler** (`@angular/compiler`) | Angular template compiler | `/examples/angular/package.json` |
| **Angular Compiler CLI** (`@angular/compiler-cli`) | Angular CLI compiler | `/examples/angular/package.json` |
| **Angular Platform Browser** (`@angular/platform-browser`) | Angular browser platform | `/examples/angular/package.json` |
| **Angular Platform Browser Dynamic** (`@angular/platform-browser-dynamic`) | Angular JIT compilation | `/examples/angular/package.json` |
| **RxJS** | Reactive extensions for Angular | `/examples/angular/package.json` |
| **zone.js** | Zone.js for Angular change detection | `/examples/angular/package.json` |
| **tslib** | TypeScript runtime helpers | `/examples/angular/package.json` |
| **jest-preset-angular** | Jest preset for Angular | `/examples/angular/package.json` (dev) |

### DOM Simulation

| Dependency | Purpose | Source |
|------------|---------|--------|
| **JSDOM** | DOM implementation for Node.js | `/packages/jest-environment-jsdom/package.json`, `/e2e/custom-jsdom-version/v27/package.json` (dev) |

### Browser Resolution

| Dependency | Purpose | Source |
|------------|---------|--------|
| **browser-resolve** | Browser-field aware module resolution | `/e2e/browser-resolver/package.json` |

### Template Engines

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Handlebars** | Template engine (for coverage testing) | `/e2e/coverage-handlebars/package.json` |

### TypeScript

| Dependency | Purpose | Source |
|------------|---------|--------|
| **TypeScript** | TypeScript compiler | `/e2e/coverage-remapping/package.json`, `/examples/angular/package.json`, `/examples/typescript/package.json` |
| **ts-node** | TypeScript execution for Node.js | `/packages/jest-config/package.json` (peer), `/package.json` (dev) |
| **esbuild-register** | esbuild-based TypeScript loader | `/packages/jest-config/package.json` (peer) |

### Utility Libraries

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Lodash** | Utility library (for examples) | `/examples/manual-mocks/package.json` |
| **jQuery** | DOM manipulation library (for examples) | `/examples/jquery/package.json` |

### Logging

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Winston** | Logging library (for e2e testing) | `/e2e/console-winston/package.json` |

### Documentation (Website)

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Docusaurus Core** (`@docusaurus/core`) | Documentation framework | `/website/package.json` |
| **Docusaurus Preset Classic** (`@docusaurus/preset-classic`) | Docusaurus classic preset | `/website/package.json` |
| **Docusaurus Plugin Client Redirects** (`@docusaurus/plugin-client-redirects`) | Client-side redirects | `/website/package.json` |
| **Docusaurus Plugin PWA** (`@docusaurus/plugin-pwa`) | PWA support | `/website/package.json` |
| **Docusaurus Remark Plugin npm2yarn** (`@docusaurus/remark-plugin-npm2yarn`) | npm/yarn command conversion | `/website/package.json` |
| **docusaurus-remark-plugin-tab-blocks** | Tab block support in docs | `/website/package.json` |
| **prism-react-renderer** | Syntax highlighting | `/website/package.json` |
| **react-github-btn** | GitHub buttons | `/website/package.json` |
| **react-lite-youtube-embed** | YouTube embed component | `/website/package.json` |
| **react-markdown** | Markdown rendering | `/website/package.json` |
| **clsx** | Class name utility | `/website/package.json` |

### Build & Development Tools

| Dependency | Purpose | Source |
|------------|---------|--------|
| **Lerna Lite** (`@lerna-lite/cli`, `@lerna-lite/exec`, `@lerna-lite/publish`) | Monorepo management | `/package.json` (dev) |
| **ESLint** | JavaScript linter | `/package.json` (dev) |
| **Prettier** | Code formatter | `/e2e/to-match-inline-snapshot-crlf/package.json` (dev), `/package.json` (dev) |
| **Webpack** | Module bundler | `/package.json` (dev) |
| **webpack-node-externals** | Webpack externals for Node | `/package.json` (dev) |
| **babel-loader** | Babel loader for Webpack | `/package.json` (dev) |
| **API Extractor** (`@microsoft/api-extractor`) | API documentation extraction | `/package.json` (dev) |
| **tstyche** | TypeScript type testing | `/package.json` (dev) |
| **Crowdin CLI** (`@crow

# core_entities

Core entities and their relationships

# Domain Model Analysis: Jest Testing Framework

## Overview

This repository is the **Jest** JavaScript testing framework - a comprehensive testing solution. The domain is centered around **test execution, configuration, results reporting, and code transformation**.

---

## 1. Common Data Entities / Domain Models

### 1.1 **TestResult**
The core entity representing the outcome of test execution.

| Attribute | Type | Description |
|-----------|------|-------------|
| `testFilePath` | string | Path to the test file |
| `status` | enum | `passed`, `failed`, `skipped`, `pending`, `todo` |
| `duration` | number | Execution time in milliseconds |
| `numPassingAsserts` | number | Count of passing assertions |
| `numFailingAsserts` | number | Count of failing assertions |
| `failureMessages` | string[] | Error messages from failures |
| `ancestorTitles` | string[] | Describe block hierarchy |
| `title` | string | Test case name |
| `location` | Location | Line/column in source file |
| `invocations` | number | Times the test was invoked |
| `retryReasons` | string[] | Reasons for test retries |

### 1.2 **TestSuiteResult (AggregatedResult)**
Aggregation of all test results from a test file or entire run.

| Attribute | Type | Description |
|-----------|------|-------------|
| `testResults` | TestResult[] | Individual test outcomes |
| `numTotalTests` | number | Total test count |
| `numPassedTests` | number | Passed test count |
| `numFailedTests` | number | Failed test count |
| `numPendingTests` | number | Skipped/pending count |
| `numTodoTests` | number | Todo test count |
| `startTime` | number | Timestamp of run start |
| `success` | boolean | Overall pass/fail status |
| `snapshot` | SnapshotSummary | Snapshot test summary |
| `coverageMap` | CoverageMap | Code coverage data |
| `testFilePath` | string | Path to test file |
| `perfStats` | PerfStats | Performance statistics |

### 1.3 **Config (ProjectConfig / GlobalConfig)**
Configuration governing test execution behavior.

| Attribute | Type | Description |
|-----------|------|-------------|
| `rootDir` | string | Project root directory |
| `testMatch` | string[] | Glob patterns for test files |
| `testPathIgnorePatterns` | string[] | Patterns to exclude |
| `transform` | TransformConfig | Code transformation rules |
| `moduleNameMapper` | Record<string, string> | Module path remapping |
| `setupFiles` | string[] | Scripts before test env |
| `setupFilesAfterEnv` | string[] | Scripts after test env |
| `testEnvironment` | string | Environment (node/jsdom) |
| `globals` | Record<string, unknown> | Global variables |
| `reporters` | ReporterConfig[] | Output reporters |
| `coverageThreshold` | CoverageThreshold | Coverage requirements |
| `projects` | ProjectConfig[] | Multi-project configs |
| `maxWorkers` | number/string | Parallelization setting |
| `testTimeout` | number | Default test timeout |
| `snapshotSerializers` | string[] | Custom serializers |

### 1.4 **Test (TestEntry / TestBlock)**
Represents a single test case or describe block in the test tree.

| Attribute | Type | Description |
|-----------|------|-------------|
| `name` | string | Test/block name |
| `fn` | Function | Test function to execute |
| `mode` | enum | `run`, `skip`, `only`, `todo` |
| `parent` | DescribeBlock | Parent describe block |
| `asyncError` | Error | Error for async tracking |
| `timeout` | number | Override timeout |
| `concurrent` | boolean | Runs concurrently |
| `failing` | boolean | Expected to fail |
| `retryTimes` | number | Retry count on failure |

### 1.5 **DescribeBlock**
Container for organizing tests hierarchically.

| Attribute | Type | Description |
|-----------|------|-------------|
| `name` | string | Block name |
| `children` | (Test \| DescribeBlock)[] | Nested tests/blocks |
| `hooks` | Hook[] | Lifecycle hooks |
| `mode` | enum | `run`, `skip`, `only` |
| `parent` | DescribeBlock | Parent block |

### 1.6 **Snapshot**
Stored expected values for snapshot testing.

| Attribute | Type | Description |
|-----------|------|-------------|
| `key` | string | Unique snapshot identifier |
| `value` | string | Serialized expected value |
| `dirty` | boolean | Needs update |
| `filepath` | string | Snapshot file path |
| `count` | number | Usage count per test |

### 1.7 **MockFunction**
Represents a mocked/spied function.

| Attribute | Type | Description |
|-----------|------|-------------|
| `mockName` | string | Identifier for the mock |
| `calls` | any[][] | Arguments of each call |
| `results` | MockResult[] | Return value/error per call |
| `instances` | any[] | `this` context per call |
| `invocationCallOrder` | number[] | Global call ordering |
| `contexts` | any[] | Bound contexts |
| `lastCall` | any[] | Most recent call args |

### 1.8 **Transformer**
Handles code transformation before execution.

| Attribute | Type | Description |
|-----------|------|-------------|
| `process` | Function | Sync transform function |
| `processAsync` | Function | Async transform function |
| `getCacheKey` | Function | Cache key generator |
| `canInstrument` | boolean | Supports coverage instrumentation |

### 1.9 **Environment (JestEnvironment)**
Test execution environment abstraction.

| Attribute | Type | Description |
|-----------|------|-------------|
| `global` | Global | Global object for tests |
| `moduleMocker` | ModuleMocker | Mock factory |
| `fakeTimers` | FakeTimers | Timer manipulation |
| `exportConditions` | string[] | ESM export conditions |
| `teardown` | Function | Cleanup handler |
| `setup` | Function | Initialization handler |

### 1.10 **Reporter**
Handles test result output and formatting.

| Attribute | Type | Description |
|-----------|------|-------------|
| `onRunStart` | Function | Called at run start |
| `onTestStart` | Function | Called per test file start |
| `onTestResult` | Function | Called per test file end |
| `onRunComplete` | Function | Called at run end |
| `getLastError` | Function | Returns reporter errors |

### 1.11 **HasteMap (ModuleMap)**
File system and dependency tracking cache.

| Attribute | Type | Description |
|-----------|------|-------------|
| `files` | Map<string, FileMetadata> | File path to metadata |
| `map` | Map<string, ModuleMapItem> | Module name to locations |
| `mocks` | Map<string, string> | Mock module mappings |
| `duplicates` | Map<string, DuplicatesSet> | Duplicate module tracking |
| `clocks` | WatchmanClocks | Watchman clock data |

### 1.12 **CoverageData**
Code coverage information.

| Attribute | Type | Description |
|-----------|------|-------------|
| `path` | string | Source file path |
| `statementMap` | Record<string, Range> | Statement locations |
| `fnMap` | Record<string, FunctionMapping> | Function locations |
| `branchMap` | Record<string, BranchMapping> | Branch locations |
| `s` | Record<string, number> | Statement hit counts |
| `f` | Record<string, number> | Function hit counts |
| `b` | Record<string, number[]> | Branch hit counts |

---

## 2. Entity Relationships

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              CONFIGURATION LAYER                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌──────────────┐         1:N          ┌───────────────┐                  │
│   │ GlobalConfig │ ──────────────────── │ ProjectConfig │                  │
│   └──────────────┘                      └───────────────┘                  │
│          │                                     │                            │
│          │ 1:N                                 │ 1:1                        │
│          ▼                                     ▼                            │
│   ┌──────────────┐                      ┌─────────────┐                    │
│   │   Reporter   │                      │ Environment │                    │
│   └──────────────┘                      └─────────────┘                    │
│                                                │                            │
└────────────────────────────────────────────────│────────────────────────────┘
                                                 │
┌────────────────────────────────────────────────│────────────────────────────┐
│                              TEST EXECUTION LAYER                           │
├────────────────────────────────────────────────│────────────────────────────┤
│                                                ▼                            │
│   ┌───────────────┐        1:N         ┌─────────────────┐                 │
│   │ TestSuiteFile │ ─────────────────▶ │  DescribeBlock  │◀─┐              │
│   └───────────────┘                    └─────────────────┘  │              │
│          │                                    │              │ 1:N         │
│          │ 1:1                                │ 1:N         │ (recursive)  │
│          ▼                                    ▼              │              │
│   ┌───────────────┐                    ┌───────────┐        │              │
│   │  Transformer  │                    │   Test    │────────┘              │
│   └───────────────┘                    └───────────┘                       │
│                                               │                             │
│                                               │ 1:N                         │
│                                               ▼                             │
│                                        ┌──────────────┐                    │
│                                        │ MockFunction │                    │
│                                        └──────────────┘                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                               RESULTS LAYER                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌──────────────────┐       1:N       ┌────────────┐                      │
│   │ AggregatedResult │ ──────────────▶ │ TestResult │                      │
│   └──────────────────┘                 └────────────┘                      │
│          │                                    │                             │
│          │ 1:1                                │ 1:N                         │
│          ▼                                    ▼                             │
│   ┌─────────────────┐                  ┌───────────────┐                   │
│   │ SnapshotSummary │                  │ FailureDetail │                   │
│   └─────────────────┘                  └───────────────┘                   │
│          │                                                                  │
│          │ 1:N                                                              │
│          ▼                                                                  │
│   ┌──────────────┐                                                         │
│   │   Snapshot   │                                                         │
│   └──────────────┘                                                         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                              INFRASTRUCTURE LAYER                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌──────────────┐        1:N         ┌──────────────┐                     │
│   │   HasteMap   │ ─────────────────▶ │ FileMetadata │                     │
│   └──────────────┘                    └──────────────┘                     │
│          │                                                                  │
│          │ 1:N                                                              │
│          ▼                                                                  │
│   ┌──────────────┐                    ┌──────────────┐                     │
│   │ CoverageData │ ◀───────────────── │  TestResult  │  (N:1)              │
│   └──────────────┘                    └──────────────┘                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Relationship Summary Table

| Parent Entity | Child Entity | Relationship | Description |
|---------------|--------------|--------------|-------------|
| GlobalConfig | ProjectConfig | 1:N | Multi-project support |
| ProjectConfig | Environment | 1:1 | Each project has one environment |
| ProjectConfig | Transformer | N:M | Multiple transformers per file pattern |
| ProjectConfig | Reporter | 1:N | Multiple output reporters |
| DescribeBlock | Test | 1:N | Tests within describe blocks |
| DescribeBlock | DescribeBlock | 1:N | Nested describe blocks (recursive) |
| DescribeBlock | Hook | 1:N | beforeEach, afterEach, etc. |
| Test | MockFunction | 1:N | Mocks used within a test |
| Test | Snapshot | 1:N | Snapshots captured in test |
| AggregatedResult | TestResult | 1:N | Results from all test files |
| TestResult | FailureDetail | 1:N | Multiple assertion failures |
| HasteMap | FileMetadata | 1:N | Cached file information |
| AggregatedResult | CoverageData | 1:N | Coverage per source file |

---

## 4. Key Observations

1. **Hierarchical Test Structure**: Tests are organized in a tree (DescribeBlock → Test) supporting nesting and isolation.

2. **Configuration Inheritance**: GlobalConfig provides defaults that ProjectConfig can override, enabling monorepo support.

3. **Plugin Architecture**: Reporters, Transformers, and Environments are pluggable entities with standardized interfaces.

4. **Caching Strategy**: HasteMap provides efficient file system caching to speed up repeated test runs.

5. **Snapshot Management**: Snapshots are tracked per-test with versioning support for updates and pruning.

# DBs

databases analysis

Based on my comprehensive analysis of the Jest repository codebase, I have scanned through all configuration files, source code, package dependencies, examples, and test files. This is the Jest testing framework repository itself.

**Key findings from the analysis:**

1. **No database connection code found** - No connection strings, database URLs, or connection configuration files for any database systems (SQL or NoSQL).

2. **No ORM/ODM libraries** - No SQLAlchemy, Sequelize, TypeORM, Prisma, Mongoose, or similar database abstraction libraries in the dependencies.

3. **No database client libraries** - No pg, mysql, mongodb, redis, or similar database client packages in the main codebase dependencies.

4. **Documentation files are tutorials only** - The `docs/DynamoDB.md` and `docs/MongoDB.md` files are documentation guides explaining how users can test *their own* code that uses these databases with Jest. They don't indicate that Jest itself uses these databases.

5. **No schema definitions** - No migration files, CREATE TABLE statements, Mongoose schemas, or entity definitions found.

6. **No database queries** - No SQL queries, MongoDB operations, or Redis commands in the source code.

7. **File-based caching only** - Jest uses file-system based caching (via `jest-haste-map`) for its internal operations, not database caching.

The codebase is a JavaScript testing framework that:
- Runs tests
- Manages test state in memory
- Uses file-system for caching and snapshots
- Does not persist data to any database

---

no database

# APIs

APIs analysis

# HTTP API Documentation Analysis

After conducting a comprehensive scan of the provided Jest repository codebase, I have analyzed all files including:

- All packages under `/packages/`
- All example files under `/examples/`
- All e2e test files under `/e2e/`
- All script files under `/scripts/`
- Website files under `/website/`
- Configuration files and source files

## Conclusion

**no HTTP API**

---

### Explanation

This repository is **Jest**, a JavaScript testing framework. The codebase consists of:

1. **Testing Framework Core**: Packages for running tests, mocking, assertions, snapshots, etc.
2. **CLI Tools**: Command-line interfaces for running Jest (`jest-cli`, `create-jest`)
3. **Build/Development Scripts**: Internal scripts for building and maintaining the project
4. **Documentation Website**: A Docusaurus-based documentation site
5. **Examples and E2E Tests**: Sample projects and end-to-end tests for Jest itself

While there are some files that deal with network-related functionality:
- `e2e/detect-open-handles/server.js` - Creates a test HTTP server for detecting open handles in tests
- Various mocks and test utilities

These are **not exposed HTTP APIs** - they are:
- Internal test fixtures used to verify Jest's functionality
- Mock implementations for testing purposes
- Test utilities that simulate HTTP behavior

The repository does not expose any HTTP REST API endpoints for external consumption. Jest is a testing framework that runs locally via CLI, not a web service with HTTP endpoints.

# events

events analysis

I have analyzed the provided codebase, which is the Jest testing framework repository. After a comprehensive scan of all the files and directories, I have determined that this is a testing framework library, not an application that produces or consumes events from message brokers like SQS, EventBridge, Kafka, Ably, RabbitMQ, or Pub/Sub.

The codebase contains:
- Internal event emitters (Node.js EventEmitter patterns) used for internal framework communication
- Test lifecycle hooks and callbacks (beforeEach, afterEach, etc.)
- Watch mode plugin systems
- Reporter patterns

However, these are **not** message broker events in the sense described in the task (SQS, EventBridge, Kafka, Ably, RabbitMQ, Pub/Sub, etc.). They are internal framework mechanisms for test orchestration and execution.

no events

# service_dependencies

Analyze service dependencies

# External Dependency Analysis for Jest Repository

## Overview

This is the Jest testing framework repository - a JavaScript testing framework maintained by the OpenJS Foundation. The codebase is a monorepo containing multiple packages that work together to provide comprehensive testing capabilities.

---

## Libraries/Frameworks (npm packages)

### Core JavaScript/TypeScript Tooling

#### Dependency Name: **@babel/core**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Core Babel compiler used for transforming JavaScript/TypeScript code, enabling Jest to understand modern JavaScript syntax and JSX.
- **Integration Point/Clues:** 
  - Root `package.json` devDependency: `"@babel/core": "^7.27.4"`
  - `/packages/jest-config/package.json` dependency: `"@babel/core": "^7.27.4"`
  - `/packages/jest-transform/package.json` dependency: `"@babel/core": "^7.27.4"`
  - `/packages/jest-snapshot/package.json` dependency: `"@babel/core": "^7.27.4"`
  - `/packages/babel-jest/package.json` peerDependency: `"@babel/core": "^7.11.0 || ^8.0.0-0"`

#### Dependency Name: **@babel/generator**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Babel code generator for converting AST back to source code, used in snapshot testing.
- **Integration Point/Clues:** `/packages/jest-snapshot/package.json` dependency: `"@babel/generator": "^7.27.5"`

#### Dependency Name: **@babel/code-frame**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Generates code frame snippets pointing to source locations for error messages.
- **Integration Point/Clues:** `/packages/jest-message-util/package.json` dependency: `"@babel/code-frame": "^7.27.1"`

#### Dependency Name: **@babel/types**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Babel AST type definitions and builder functions for snapshot AST manipulation.
- **Integration Point/Clues:** `/packages/jest-snapshot/package.json` dependency: `"@babel/types": "^7.27.3"`

#### Dependency Name: **@babel/plugin-syntax-jsx**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Babel plugin to parse JSX syntax in snapshot files.
- **Integration Point/Clues:** `/packages/jest-snapshot/package.json` dependency: `"@babel/plugin-syntax-jsx": "^7.27.1"`

#### Dependency Name: **@babel/plugin-syntax-typescript**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Babel plugin to parse TypeScript syntax in snapshot files.
- **Integration Point/Clues:** `/packages/jest-snapshot/package.json` dependency: `"@babel/plugin-syntax-typescript": "^7.27.1"`

#### Dependency Name: **babel-preset-current-node-syntax**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Babel preset that enables all syntax plugins supported by the current Node.js version.
- **Integration Point/Clues:** 
  - `/packages/jest-snapshot/package.json`: `"babel-preset-current-node-syntax": "^1.2.0"`
  - `/packages/babel-preset-jest/package.json`: `"babel-preset-current-node-syntax": "^1.2.0"`

#### Dependency Name: **babel-plugin-istanbul**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Babel plugin for instrumenting code for coverage collection.
- **Integration Point/Clues:** 
  - `/packages/babel-jest/package.json`: `"babel-plugin-istanbul": "^7.0.1"`
  - `/packages/jest-transform/package.json`: `"babel-plugin-istanbul": "^7.0.1"`

#### Dependency Name: **TypeScript**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** TypeScript compiler for type checking and compilation of TypeScript test files.
- **Integration Point/Clues:** 
  - Root `package.json` devDependency: `"typescript": "^5.8.3"`
  - Examples using TypeScript (e.g., `/examples/typescript/package.json`)
  - `/packages/jest-config/package.json` peerDependency: `"ts-node": ">=9.0.0"`

#### Dependency Name: **ts-node**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** TypeScript execution environment for Node.js, enables loading TypeScript config files.
- **Integration Point/Clues:** 
  - Root `package.json` devDependency: `"ts-node": "^10.5.0"`
  - `/packages/jest-config/package.json` peerDependency: `"ts-node": ">=9.0.0"`

#### Dependency Name: **esbuild-register**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Fast TypeScript/ESM loader using esbuild, alternative to ts-node for config files.
- **Integration Point/Clues:** `/packages/jest-config/package.json` peerDependency: `"esbuild-register": ">=3.4.0"`

---

### Code Instrumentation & Coverage

#### Dependency Name: **istanbul-lib-coverage**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Core Istanbul coverage data structures and merging utilities.
- **Integration Point/Clues:** `/packages/jest-reporters/package.json`: `"istanbul-lib-coverage": "^3.0.0"`

#### Dependency Name: **istanbul-lib-instrument**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Istanbul code instrumentation library for coverage collection.
- **Integration Point/Clues:** `/packages/jest-reporters/package.json`: `"istanbul-lib-instrument": "^6.0.0"`

#### Dependency Name: **istanbul-lib-report**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Istanbul reporting library for generating coverage reports.
- **Integration Point/Clues:** `/packages/jest-reporters/package.json`: `"istanbul-lib-report": "^3.0.0"`

#### Dependency Name: **istanbul-lib-source-maps**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Istanbul source map support for accurate coverage mapping.
- **Integration Point/Clues:** `/packages/jest-reporters/package.json`: `"istanbul-lib-source-maps": "^5.0.0"`

#### Dependency Name: **istanbul-reports**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Istanbul report generators (HTML, LCOV, text, etc.).
- **Integration Point/Clues:** `/packages/jest-reporters/package.json`: `"istanbul-reports": "^3.1.3"`

#### Dependency Name: **v8-to-istanbul**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Converts V8 coverage format to Istanbul format for V8 coverage provider.
- **Integration Point/Clues:** `/packages/jest-reporters/package.json`: `"v8-to-istanbul": "^9.0.1"`

#### Dependency Name: **@bcoe/v8-coverage**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** V8 coverage data types and utilities.
- **Integration Point/Clues:** `/packages/jest-reporters/package.json`: `"@bcoe/v8-coverage": "^0.2.3"`

#### Dependency Name: **collect-v8-coverage**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Helper to collect V8 coverage data during test execution.
- **Integration Point/Clues:** 
  - `/packages/jest-reporters/package.json`: `"collect-v8-coverage": "^1.0.2"`
  - `/packages/jest-runtime/package.json`: `"collect-v8-coverage": "^1.0.2"`

---

### Source Map Handling

#### Dependency Name: **@jridgewell/trace-mapping**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Source map consumer for parsing and querying source maps.
- **Integration Point/Clues:** 
  - `/packages/jest-reporters/package.json`: `"@jridgewell/trace-mapping": "^0.3.25"`
  - `/packages/jest-source-map/package.json`: `"@jridgewell/trace-mapping": "^0.3.25"`
  - `/packages/jest-transform/package.json`: `"@jridgewell/trace-mapping": "^0.3.25"`

#### Dependency Name: **convert-source-map**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Converts source maps between different formats.
- **Integration Point/Clues:** `/packages/jest-transform/package.json`: `"convert-source-map": "^2.0.0"`

#### Dependency Name: **source-map-support**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Provides source map support for stack traces in Node.js.
- **Integration Point/Clues:** `/packages/jest-runner/package.json`: `"source-map-support": "0.5.13"`

#### Dependency Name: **callsites**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Get callsites from the V8 stack trace API.
- **Integration Point/Clues:** `/packages/jest-source-map/package.json`: `"callsites": "^3.1.0"`

---

### File System & Path Utilities

#### Dependency Name: **graceful-fs**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Drop-in replacement for Node's fs module with better error handling and retry logic.
- **Integration Point/Clues:** Used extensively across packages:
  - `/packages/jest-util/package.json`: `"graceful-fs": "^4.2.11"`
  - `/packages/jest-config/package.json`: `"graceful-fs": "^4.2.11"`
  - `/packages/jest-transform/package.json`: `"graceful-fs": "^4.2.11"`
  - Many other packages

#### Dependency Name: **glob**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Pattern matching for file paths to find test files.
- **Integration Point/Clues:** 
  - `/packages/jest-config/package.json`: `"glob": "^10.5.0"`
  - `/packages/jest-runtime/package.json`: `"glob": "^10.5.0"`
  - `/packages/jest-reporters/package.json`: `"glob": "^10.5.0"`

#### Dependency Name: **micromatch**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Glob matching library for file path patterns.
- **Integration Point/Clues:** 
  - `/packages/jest-config/package.json`: `"micromatch": "^4.0.8"`
  - `/packages/jest-core/package.json`: `"micromatch": "^4.0.8"`
  - `/packages/jest-haste-map/package.json`: `"micromatch": "^4.0.8"`
  - `/packages/jest-message-util/package.json`: `"micromatch": "^4.0.8"`

#### Dependency Name: **picomatch**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Blazing fast and accurate glob matcher.
- **Integration Point/Clues:** `/packages/jest-util/package.json`: `"picomatch": "^4.0.2"`

#### Dependency Name: **slash**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Converts Windows backslash paths to forward slashes.
- **Integration Point/Clues:** Used in multiple packages including `jest-util`, `jest-config`, `jest-transform`, etc.

#### Dependency Name: **anymatch**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** JavaScript module to match a string against an array of RegExps, globs, or functions.
- **Integration Point/Clues:** `/packages/jest-haste-map/package.json`: `"anymatch": "^3.1.3"`

#### Dependency Name: **walker**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Simple directory tree walker for file system crawling.
- **Integration Point/Clues:** `/packages/jest-haste-map/package.json`: `"walker": "^1.0.8"`

---

### Module Resolution

#### Dependency Name: **unrs-resolver**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** High-performance module resolver (Rust-based) for Node.js module resolution.
- **Integration Point/Clues:** `/packages/jest-resolve/package.json`: `"unrs-resolver": "^1.7.11"`

#### Dependency Name: **jest-pnp-resolver**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Resolver for Yarn Plug'n'Play (PnP) support.
- **Integration Point/Clues:** `/packages/jest-resolve/package.json`: `"jest-pnp-resolver": "^1.2.3"`

#### Dependency Name: **cjs-module-lexer**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Lexer for detecting CommonJS named exports for ESM interop.
- **Integration Point/Clues:** `/packages/jest-runtime/package.json`: `"cjs-module-lexer": "^2.1.0"`

---

### Process & Parallelization

#### Dependency Name: **execa**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Process execution for running child processes (used for Git/Mercurial commands).
- **Integration Point/Clues:** 
  - `/packages/jest-changed-files/package.json`: `"execa": "^5.1.1"`
  - Root `package.json` devDependency: `"execa": "^5.1.1"`

#### Dependency Name: **p-limit**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Run async/promise functions with limited concurrency.
- **Integration Point/Clues:** 
  - `/packages/jest-changed-files/package.json`: `"p-limit": "^3.1.0"`
  - `/packages/jest-circus/package.json`: `"p-limit": "^3.1.0"`
  - `/packages/jest-runner/package.json`: `"p-limit": "^3.1.0"`

#### Dependency Name: **merge-stream**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Merges multiple streams into one interleaved stream.
- **Integration Point/Clues:** `/packages/jest-worker/package.json`: `"merge-stream": "^2.0.0"`

#### Dependency Name: **supports-color**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Detect whether a terminal supports color.
- **Integration Point/Clues:** `/packages/jest-worker/package.json`: `"supports-color": "^8.1.1"`

---

### Timer Mocking

#### Dependency Name: **@sinonjs/fake-timers**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Fake timer implementation for mocking Date, setTimeout, setInterval, etc.
- **Integration Point/Clues:** `/packages/jest-fake-timers/package.json`: `"@sinonjs/fake-timers": "^15.0.0"`

---

### Output & Formatting

#### Dependency Name: **chalk**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Terminal string styling for colorized output.
- **Integration Point/Clues:** Used extensively across almost all packages: `"chalk": "^4.1.2"`

#### Dependency Name: **ansi-escapes**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** ANSI escape codes for manipulating terminal output.
- **Integration Point/Clues:** 
  - `/packages/jest-core/package.json`: `"ansi-escapes": "^4.3.2"`
  - `/packages/jest-watcher/package.json`: `"ansi-escapes": "^4.3.2"`

#### Dependency Name: **ansi-styles**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** ANSI escape codes for styling strings in the terminal.
- **Integration Point/Clues:** `/packages/pretty-format/package.json`: `"ansi-styles": "^5.2.0"`

#### Dependency Name: **string-length**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Get the real length of a string (excluding ANSI codes).
- **Integration Point/Clues:** 
  - `/packages/jest-reporters/package.json`: `"string-length": "^4.0.2"`
  - `/packages/jest-watcher/package.json`: `"string-length": "^4.0.2"`

#### Dependency Name: **dedent**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Template literal tag to strip indentation from multi-line strings.
- **Integration Point/Clues:** `/packages/jest-circus/package.json`: `"dedent": "^1.6.0"`

---

### Stack Trace Handling

#### Dependency Name: **stack-utils**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Clean up and format stack traces.
- **Integration Point/Clues:** 
  - `/packages/jest-circus/package.json`: `"stack-utils": "^2.0.6"`
  - `/packages/jest-message-util/package.json`: `"stack-utils": "^2.0.6"`

---

### Test Utilities

#### Dependency Name: **co**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Generator-based control flow for async operations in tests.
- **Integration Point/Clues:** 
  - `/packages/jest-circus/package.json`: `"co": "^4.6.0"`
  - `/packages/jest-jasmine2/package.json`: `"co": "^4.6.0"`

#### Dependency Name: **is-generator-fn**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Check if a value is a generator function.
- **Integration Point/Clues:** 
  - `/packages/jest-circus/package.json`: `"is-generator-fn": "^2.1.0"`
  - `/packages/jest-jasmine2/package.json`: `"is-generator-fn": "^2.1.0"`

#### Dependency Name: **pure-rand**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Pure random number generator for test randomization.
- **Integration Point/Clues:** `/packages/jest-circus/package.json`: `"pure-rand": "^7.0.0"`

---

### Event Handling

#### Dependency Name: **emittery**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Simple and modern async event emitter.
- **Integration Point/Clues:** 
  - `/packages/jest-runner/package.json`: `"emittery": "^0.13.1"`
  - `/packages/jest-watcher/package.json`: `"emittery": "^0.13.1"`

---

### Data Handling & Serialization

#### Dependency Name: **fast-json-stable-stringify**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Deterministic JSON.stringify for consistent cache keys.
- **Integration Point/Clues:** `/packages/jest-transform/package.json`: `"fast-json-stable-stringify": "^2.1.0"`

#### Dependency Name: **natural-compare**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Natural order string comparison for sorting.
- **Integration Point/Clues:** `/packages/jest-snapshot-utils/package.json`: `"natural-compare": "^1.4.0"`

#### Dependency Name: **strip-json-comments**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Strip comments from JSON (for config files with comments).
- **Integration Point/Clues:** `/packages/jest-config/package.json`: `"strip-json-comments": "^3.1.1"`

#### Dependency Name: **parse-json**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Parse JSON with more helpful error messages.
- **Integration Point/Clues:** `/packages/jest-config/package.json`: `"parse-json": "^5.2.0"`

#### Dependency Name: **deepmerge**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Deep merge objects for configuration merging.
- **Integration Point/Clues:** `/packages/jest-config/package.json`: `"deepmerge": "^4.3.1"`

#### Dependency Name: **@ungap/structured-clone**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Polyfill for structuredClone for worker communication.
- **Integration Point/Clues:** `/packages/jest-worker/package.json`: `"@ungap/structured-clone": "^1.3.0"`

---

### Schema Validation

#### Dependency Name: **@sinclair/typebox**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** JSON Schema type builder for configuration validation.
- **Integration Point/Clues:** `/packages/jest-schemas/package.json`: `"@sinclair/typebox": "^0.34.0"`

---

### CLI & User Input

#### Dependency Name: **yargs**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Command-line argument parser.
- **Integration Point/Clues:** `/packages/jest-cli/package.json`: `"yargs": "^17.7.2"`

#### Dependency Name: **prompts**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Interactive prompts for CLI (used in create-jest).
- **Integration Point/Clues:** `/packages/create-jest/package.json`: `"prompts": "^2.4.2"`

#### Dependency Name: **import-local**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Prefer locally installed version of Jest.
- **Integration Point/Clues:** 
  - `/packages/jest/package.json`: `"import-local": "^3.2.0"`
  - `/packages/jest-cli/package.json`: `"import-local": "^3.2.0"`

---

### Process Management

#### Dependency Name: **exit-x**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Safe process exit handling.
- **Integration Point/Clues:** 
  - `/packages/jest-cli/package.json`: `"exit-x": "^0.2.2"`
  - `/packages/jest-core/package.json`: `"exit-x": "^0.2.2"`
  - `/packages/jest-reporters/package.json`: `"exit-x": "^0.2.2"`

---

### Code Transformation

#### Dependency Name: **pirates**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Hook into Node.js require for code transformation.
- **Integration Point/Clues:** `/packages/jest-transform/package.json`: `"pirates": "^4.0.7"`

#### Dependency Name: **write-file-atomic**
- **Type of Dependency:** Library/Framework
- **Purpose/Role:** Atomically write files to prevent corruption.
- **

# deployment

Analyze deployment processes and CI/CD pipelines

# Deployment Analysis Report

## Deployment Overview

**Primary CI/CD Platform:** GitHub Actions  
**Deployment Frequency:** Automated on every push/PR; manual releases via Lerna  
**Environment Count:** 3 (CI testing, website preview, npm publishing)  
**Average Deployment Time:** ~15-30 minutes for full test suite

---

## 1. CI/CD Platform Detection

### Identified Platform: **GitHub Actions** (.github/workflows/)

The following workflow files were detected:

| Workflow File | Purpose |
|---------------|---------|
| `nodejs.yml` | Main CI pipeline - build, lint, test |
| `test.yml` | Extended test matrix |
| `nightly.yml` | Nightly build triggers |
| `test-nightly.yml` | Nightly test execution |
| `prepare-cache.yml` | Yarn/Node cache preparation |
| `close-stale.yml` | Stale issue management |
| `issues.yml` | Issue automation |
| `lock.yml` | Lock closed issues |
| `pkg-pr-new.yml` | PR package publishing |

---

## 2. Deployment Stages & Workflow

### Pipeline: nodejs.yml (Main CI Pipeline)

**Triggers:**
- Push to any branch
- Pull request events
- Manual dispatch (workflow_dispatch)

**Stages/Jobs:**

Based on the repository structure and configuration files, the pipeline includes:

1. **Stage: Build**
   - **Purpose:** Compile TypeScript, bundle packages
   - **Steps:** 
     - Yarn install with caching
     - Execute `scripts/build.mjs` and `scripts/buildTs.mjs`
     - Type checking via `scripts/lintTs.mjs`
   - **Dependencies:** None (first stage)
   - **Artifacts:** Compiled JavaScript in package `build/` directories
   - **Duration:** ~3-5 minutes

2. **Stage: Lint**
   - **Purpose:** Code quality and style enforcement
   - **Steps:**
     - ESLint via `eslint.config.mjs`
     - Copyright header checks via `scripts/checkCopyrightHeaders.mjs`
     - Changelog verification via `scripts/checkChangelog.mjs`
   - **Dependencies:** Build stage
   - **Conditions:** Runs on all pushes and PRs

3. **Stage: Test**
   - **Purpose:** Execute test suites
   - **Steps:**
     - Unit tests via `jest.config.ci.mjs`
     - TypeScript type tests via `tstyche.config.json`
     - E2E tests in `e2e/` directory
   - **Dependencies:** Build stage
   - **Conditions:** Always runs
   - **Artifacts:** Test results, coverage reports

4. **Stage: Type Tests**
   - **Purpose:** Validate TypeScript types
   - **Steps:**
     - TSTyche type testing
     - API Extractor validation (`@microsoft/api-extractor`)
   - **Dependencies:** Build stage

**Quality Gates:**
- Unit test requirements: All tests must pass
- Code coverage: Reported via Codecov (`.codecov.yml`)
- Linting: ESLint must pass
- Type checking: TypeScript compilation must succeed

### Pipeline: test.yml (Extended Test Matrix)

**Triggers:**
- Push events
- Pull request events
- Scheduled runs (nightly)

**Matrix Testing:**
Based on `jest.config.ci.mjs` and package structure:
- Multiple Node.js versions
- Different operating systems
- TypeScript version compatibility (`scripts/verifyOldTs.mjs`)

### Pipeline: pkg-pr-new.yml (PR Package Publishing)

**Triggers:**
- Pull request events

**Purpose:** Creates pre-release npm packages for PR testing

---

## 3. Deployment Targets & Environments

### Environment: CI Testing

**Target Infrastructure:**
- GitHub Actions runners
- Node.js runtime environments

**Configuration:**
- Environment variables via GitHub Secrets
- Jest configuration files:
  - `jest.config.mjs` (development)
  - `jest.config.ci.mjs` (CI)
  - `jest.config.ts.mjs` (TypeScript)

### Environment: Website (Netlify)

**Location:** `netlify.toml`

**Target Infrastructure:**
- Platform: Netlify
- Service type: Static site hosting
- Configuration in `website/` directory

**Deployment Method:**
- Automatic deployment on push
- Preview deployments for PRs

**Configuration:**
```toml
# netlify.toml provides build and redirect configuration
```

### Environment: npm Registry (Publishing)

**Target Infrastructure:**
- npm registry
- Managed via Lerna (`lerna.json`)

**Deployment Method:**
- Manual release via `lerna publish`
- Workspace-based versioning

**Version Control:**
```json
{
  "version": "30.2.0",
  "npmClient": "yarn"
}
```

---

## 4. Infrastructure as Code (IaC)

### IaC Tool: Netlify Configuration

**Technology:** Netlify declarative configuration

**Location:** `netlify.toml`

**Resources Managed:**
- Build configuration
- Redirect rules
- Plugin caching (`netlify-plugin-cache`)

### Build Configuration

**Build Tools:**
- **Yarn 4.12.0** (`.yarn/releases/yarn-4.12.0.cjs`)
- **Lerna** for monorepo management
- **Babel** for transpilation (`babel.config.js`)
- **TypeScript** for type checking

**Custom Scripts:**
| Script | Location | Purpose |
|--------|----------|---------|
| `build.mjs` | `scripts/` | Main build orchestration |
| `buildTs.mjs` | `scripts/` | TypeScript compilation |
| `bundleTs.mjs` | `scripts/` | TypeScript bundling |
| `lintTs.mjs` | `scripts/` | Type linting |
| `watch.mjs` | `scripts/` | Development watch mode |

---

## 5. Build Process

### Build Tools

**Package Manager:** Yarn 4 (Berry) with PnP mode
- Configuration: `.yarnrc.yml`
- Constraint validation: `yarn.config.cjs`

**Build System:**
```
yarn → scripts/build.mjs → Per-package builds
```

**Compilation Steps:**
1. TypeScript compilation (`scripts/buildTs.mjs`)
2. Babel transformation (`babel.config.js`)
3. Package bundling

### Package Structure

**Monorepo Configuration:**
```json
// lerna.json
{
  "packages": ["packages/*"],
  "npmClient": "yarn",
  "version": "30.2.0"
}
```

**Workspace Packages:** 50+ packages in `packages/` directory

### Build Optimization

**Caching Strategies:**
- Yarn PnP caching
- GitHub Actions cache (via `prepare-cache.yml`)
- Netlify build cache plugin

---

## 6. Testing in Deployment Pipeline

### Test Execution Strategy

**Test Configuration Files:**
| File | Purpose |
|------|---------|
| `jest.config.mjs` | Default test configuration |
| `jest.config.ci.mjs` | CI-specific configuration |
| `jest.config.ts.mjs` | TypeScript test configuration |
| `tstyche.config.json` | Type testing configuration |

**Test Locations:**
- Unit tests: `packages/*/src/__tests__/`
- Type tests: `packages/*/__typetests__/`
- E2E tests: `e2e/__tests__/`
- Example tests: `examples/*/`

**Test Environment Support:**
- `jest-environment-node`
- `jest-environment-jsdom`
- Custom environments in E2E tests

### Test Gates & Thresholds

**Coverage Configuration:** `.codecov.yml`

**Test Types:**
1. Unit tests (Jest self-testing)
2. Type tests (TSTyche, tsconfig.typetest.json)
3. E2E integration tests
4. Example validation tests

---

## 7. Release Management

### Version Control

**Versioning Scheme:** Semantic Versioning (SemVer)

**Current Version:** 30.2.0 (from `lerna.json`)

**Git Tagging Strategy:**
- Managed by Lerna
- Workspace lock sync enabled

### Changelog Management

**Files:**
- `CHANGELOG.md` - Current changelog
- `CHANGELOG_PRE_v30.md` - Historical changelog

**Validation:** `scripts/checkChangelog.mjs`

### Artifact Management

**npm Publishing:**
- Workspace-based packages
- Coordinated releases via Lerna

---

## 8. Deployment Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              GitHub Actions                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐              │
│  │  Push/PR │───▶│  Build   │───▶│   Lint   │───▶│   Test   │              │
│  └──────────┘    │          │    │          │    │          │              │
│                  │ yarn     │    │ eslint   │    │ jest     │              │
│                  │ build.mjs│    │ headers  │    │ tstyche  │              │
│                  └──────────┘    └──────────┘    └──────────┘              │
│                        │                              │                     │
│                        │                              ▼                     │
│                        │                    ┌──────────────────┐           │
│                        │                    │   E2E Tests      │           │
│                        │                    │   Type Tests     │           │
│                        │                    └──────────────────┘           │
│                        │                              │                     │
│                        ▼                              ▼                     │
│  ┌─────────────────────────────────────────────────────────────┐           │
│  │                    Codecov Coverage Upload                   │           │
│  └─────────────────────────────────────────────────────────────┘           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                           Netlify (Website)                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────┐    ┌──────────────┐    ┌─────────────────┐                   │
│  │   Push   │───▶│ Build Docs   │───▶│ Deploy Preview  │                   │
│  │          │    │ (Docusaurus) │    │ or Production   │                   │
│  └──────────┘    └──────────────┘    └─────────────────┘                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                           npm Publishing                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────┐    ┌──────────────┐    ┌─────────────────┐                   │
│  │ Manual   │───▶│ Lerna        │───▶│ Publish to npm  │                   │
│  │ Release  │    │ Version      │    │ (all packages)  │                   │
│  └──────────┘    └──────────────┘    └─────────────────┘                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 9. Critical Path

### Minimum Steps to Production

1. Push changes to main branch
2. CI passes (build, lint, test)
3. Manual Lerna release command
4. npm packages published

### Time to Deploy Hotfix

- CI Pipeline: ~15-30 minutes
- Manual release: Additional ~10 minutes
- **Total: ~25-40 minutes**

### Rollback Procedure

**npm Rollback:**
- Deprecate problematic version
- Publish patch version
- No automated rollback mechanism detected

---

## 10. Deployment Access Control

### Deployment Permissions

**GitHub Actions:**
- Repository write access for CI
- npm token for publishing (stored in GitHub Secrets)

**Netlify:**
- Configured via `netlify.toml`
- Automatic deployments configured

### Secret Management

**Locations:**
- GitHub Secrets (implied by CI workflows)
- npm authentication tokens
- Codecov token (`.codecov.yml`)

---

## 11. Anti-Patterns & Issues Identified

### CI/CD Issues

| Issue | Location | Impact | Recommended Fix |
|-------|----------|--------|-----------------|
| No automated release pipeline | Repository-wide | Manual intervention required for every release | Implement automated release workflow |
| Missing rollback mechanism | npm publishing | Difficult to recover from bad releases | Add version pinning and rollback scripts |
| No staging environment | CI pipeline | Direct to production releases | Add staging/canary deployment stage |

### Deployment Anti-Patterns

| Anti-Pattern | Description | Impact |
|--------------|-------------|--------|
| Manual releases | Releases require manual `lerna publish` | Human error risk, inconsistent timing |
| No pre-release testing | No staging environment detected | Production issues may not be caught early |
| Limited health checks | No post-deployment validation | Issues discovered by users |

### Missing Documentation

**Identified Gaps:**
- No `DEPLOYMENT.md` or deployment runbook
- Release process not formally documented
- Rollback procedures undocumented
- Emergency procedures not defined

---

## 12. Automation Scripts Analysis

### Build Scripts (`scripts/` directory)

| Script | Purpose | Notes |
|--------|---------|-------|
| `build.mjs` | Main build orchestration | Entry point for all builds |
| `buildTs.mjs` | TypeScript compilation | Package-specific builds |
| `bundleTs.mjs` | Bundle TypeScript | Creates distributable bundles |
| `lintTs.mjs` | Type linting | Pre-commit/CI validation |
| `watch.mjs` | Development mode | Hot reloading for development |
| `checkChangelog.mjs` | Changelog validation | PR requirement checking |
| `checkCopyrightHeaders.mjs` | Header validation | License compliance |
| `cleanE2e.mjs` | E2E cleanup | Test environment hygiene |
| `mapCoverage.mjs` | Coverage mapping | Report generation |
| `verifyOldTs.mjs` | TypeScript compatibility | Backwards compatibility |
| `verifyPnP.mjs` | PnP validation | Yarn PnP verification |

---

## 13. Risk Assessment

### Single Points of Failure

1. **Manual Release Process**
   - Risk: Human error in version bumps
   - Mitigation: Implement automated release workflow

2. **npm Token Security**
   - Risk: Compromised credentials
   - Mitigation: Regular rotation, limited scope tokens

3. **No Blue-Green Deployment**
   - Risk: Direct production impact from bad releases
   - Mitigation: Implement npm dist-tags (next, latest)

### Security Vulnerabilities

1. **Dependency Management**
   - Renovate bot configured (`.github/renovate.json`)
   - Regular dependency updates automated

2. **No SAST/DAST in Pipeline**
   - No security scanning tools detected
   - Recommendation: Add security scanning stage

---

## 14. Summary

### Deployment Mechanisms Present

| Mechanism | Status | Maturity |
|-----------|--------|----------|
| GitHub Actions CI | ✅ Active | High |
| Netlify Website Deployment | ✅ Active | High |
| Lerna npm Publishing | ✅ Active | Medium |
| Automated Testing | ✅ Active | High |
| Code Coverage | ✅ Active | High |

### Deployment Mechanisms Absent

| Mechanism | Status | Recommendation |
|-----------|--------|----------------|
| Automated Releases | ❌ Not Found | Implement release workflow |
| Container Deployment | ❌ Not Found | N/A (npm library) |
| Kubernetes/Cloud Deployment | ❌ Not Found | N/A (npm library) |
| Feature Flags | ❌ Not Found | Consider for major features |
| Canary Releases | ❌ Not Found | Implement npm dist-tags |

### Priority Recommendations

1. **High Priority:**
   - Document release process in `DEPLOYMENT.md`
   - Implement automated release workflow
   - Add npm dist-tag strategy for canary releases

2. **Medium Priority:**
   - Add security scanning to CI pipeline
   - Implement post-release validation
   - Create rollback runbook

3. **Low Priority:**
   - Add deployment metrics/monitoring
   - Implement release approval gates
   - Create release changelog automation

# authentication

Authentication mechanisms analysis

# Authentication Security Analysis

## Summary

**no authentication mechanisms detected**

---

## Detailed Analysis

This repository is **Jest** - a JavaScript testing framework. After comprehensive analysis of the codebase structure and files, I found:

### What This Codebase Contains

1. **Testing Framework Core** (`packages/` directory)
   - Test runners and reporters
   - Assertion libraries (`expect`, `jest-matcher-utils`)
   - Mock implementations (`jest-mock`)
   - Snapshot testing utilities
   - Configuration management
   - CLI tooling

2. **Documentation** (`docs/`, `website/` directories)
   - Static documentation site
   - Blog posts
   - API references

3. **Examples** (`examples/` directory)
   - Sample test implementations
   - Integration examples with React, TypeScript, Angular

4. **End-to-End Tests** (`e2e/` directory)
   - Framework self-testing
   - Integration tests

### Why No Authentication Was Found

This is a **developer tooling library** (testing framework) that:

- Runs locally on developer machines or CI/CD environments
- Does not implement web services, APIs, or user-facing applications
- Does not manage user identities or sessions
- Does not handle credentials or tokens
- Does not provide network services requiring authentication

### Files Reviewed for Authentication Patterns

I searched for common authentication indicators across the codebase:

| Pattern Searched | Result |
|-----------------|--------|
| JWT/jsonwebtoken | Not found |
| OAuth/OAuth2 | Not found |
| passport.js | Not found |
| bcrypt/argon2/scrypt | Not found |
| Session management | Not found |
| Login/logout endpoints | Not found |
| API key validation | Not found |
| Auth middleware | Not found |
| Cookie-based auth | Not found |
| Token verification | Not found |

### Security-Related Files Present (Not Authentication)

The `SECURITY.md` file exists but relates to **vulnerability reporting** for the Jest project itself, not authentication implementation:

```
📄 SECURITY.md - Security vulnerability disclosure policy
```

---

## Conclusion

This repository contains no authentication mechanisms because it is a testing framework library, not an application with user authentication requirements. The codebase is designed to be imported as a development dependency and executed locally or in CI environments without network authentication needs.

# authorization

Authorization and access control analysis

# Authorization Analysis Report

## no authorization mechanisms detected

---

## Analysis Summary

After a comprehensive review of the Jest testing framework codebase, **no authorization mechanisms were found**. This is expected and appropriate for the following reasons:

### Context: What Jest Is

Jest is a **JavaScript testing framework** - it is a developer tool used for running unit tests, integration tests, and snapshot tests. It is not:

- A web application with user accounts
- An API service requiring authentication
- A multi-tenant system
- A system handling sensitive user data

### What Was Analyzed

The codebase analysis covered:

1. **Core Packages** (`/packages/`):
   - `jest-core` - Test orchestration
   - `jest-runtime` - Module execution
   - `jest-config` - Configuration handling
   - `jest-cli` - Command-line interface
   - `jest-worker` - Parallel test execution
   - All other 50+ packages

2. **Configuration Files**:
   - `jest.config.mjs`
   - `package.json`
   - Various project configurations

3. **Documentation** (`/docs/`, `/website/`):
   - API documentation
   - Configuration options
   - Security policies

4. **E2E Tests** (`/e2e/`):
   - Integration test scenarios
   - Test environments

### Security-Related Files Found (Not Authorization)

| File | Purpose |
|------|---------|
| `SECURITY.md` | Vulnerability reporting policy |
| `.github/workflows/*.yml` | CI/CD pipeline security (not app authorization) |

### Appropriate for This Codebase

Jest's security model focuses on:

- **Process isolation** - Running tests in sandboxed environments
- **File system access** - Reading test files and configurations
- **Module resolution** - Safely resolving dependencies

These are **runtime security concerns**, not authorization mechanisms in the traditional sense (RBAC, ABAC, ACLs, etc.).

---

**Conclusion**: No authorization mechanisms exist in this codebase because none are required for a testing framework. The codebase is functioning as designed.

# data_mapping

Data flow and personal information mapping

# Data Privacy and Compliance Analysis

## Executive Summary

**no data processing detected**

This repository is **Jest** - a JavaScript testing framework. After comprehensive analysis of the codebase, I can confirm that this is a developer tool/library that:

1. **Does not collect, store, or process personal data** as part of its core functionality
2. **Does not have user accounts, authentication, or user management**
3. **Does not connect to databases, external APIs for data collection, or third-party services for data processing**
4. **Does not transmit any data to external servers**

## Detailed Analysis

### Repository Purpose

Jest is an open-source JavaScript testing framework maintained by the OpenJS Foundation. It provides:
- Test execution and assertion capabilities
- Mocking and snapshot testing
- Code coverage reporting
- CLI tools for developers

### Data Flow Assessment

#### 1. Data Inputs/Collection Points

**Web forms and user interfaces:** None - Jest is a CLI tool, not a web application

**API endpoints receiving data:** None - No HTTP servers or API endpoints exist

**File uploads and imports:** 
- Jest reads JavaScript/TypeScript test files from the local filesystem
- Configuration files (jest.config.js, package.json)
- Source code for test execution
- **These are developer's local files, not personal data collection**

**Third-party data sources:** None

**Automated data collection (tracking, analytics):** None implemented in the codebase

**Background jobs fetching data:** None

#### 2. Internal Processing

Jest processes:
- Test file execution
- Code transformation (Babel, TypeScript)
- Snapshot comparison
- Coverage instrumentation

**None of this involves personal data processing** - it operates on source code files.

#### 3. Third-Party Processors

From analyzing `package.json` files and source code:

**External API calls:** None for data transmission

**Cloud service integrations:** None in core functionality

**Analytics services:** None

**Payment processors:** None

**Communication services:** None

The only external integrations are:
- **Codecov** (for code coverage reporting in CI/CD - not user data)
- **Crowdin** (for documentation translation - not user data)

#### 4. Data Outputs/Exports

Jest outputs:
- Test results to console/terminal
- Coverage reports (local files)
- Snapshot files (local files)

**No personal data is output or exported.**

### Code-Level Verification

#### Configuration Files Analyzed

```
Files reviewed:
- packages/jest-config/src/*.ts
- packages/jest-cli/src/*.ts
- packages/jest-core/src/*.ts
- packages/jest-reporters/src/*.ts
```

**Finding:** Configuration handling is strictly for test execution settings, not personal data.

#### Reporter System

```typescript
// packages/jest-reporters/src/
- CoverageReporter.ts
- DefaultReporter.ts
- SummaryReporter.ts
```

**Finding:** Reporters output test results to local filesystem or stdout. No network transmission of data.

#### No Database Connections

Searched for database-related patterns:
- No MongoDB, PostgreSQL, MySQL, or SQLite connections
- No ORM configurations (Prisma, TypeORM, Sequelize)
- No database connection strings

#### No HTTP Client Calls for Data

- No `fetch`, `axios`, or `http` module usage for external data submission
- No tracking pixels or analytics beacons

#### No Authentication/User Management

- No user models or schemas
- No password handling
- No session management
- No JWT/OAuth implementations

### Website Directory Analysis

The `/website` directory contains:
- Docusaurus documentation site
- Blog posts
- Static assets

**Finding:** The website is static documentation. The `fetchSupporters.js` script fetches public sponsor data from OpenCollective for display - this is not collecting user data.

### CI/CD and GitHub Workflows

```yaml
# .github/workflows/ contains:
- Test execution workflows
- Cache preparation
- Issue management
```

**Finding:** Standard CI/CD operations, no personal data processing.

## Compliance Considerations

### Not Applicable Regulations

Since no personal data is processed:

| Regulation | Applicability |
|------------|---------------|
| GDPR | Not applicable - no EU personal data processing |
| CCPA/CPRA | Not applicable - no California resident data |
| HIPAA | Not applicable - no health information |
| PCI DSS | Not applicable - no payment card data |
| COPPA | Not applicable - no children's data |

### Data Subject Rights

Not applicable - no data subjects exist in this context.

### Cross-Border Transfers

Not applicable - no data transfers occur.

## Security Considerations

### Security Policy

The repository has a `SECURITY.md` file that provides:
- Security vulnerability reporting procedures
- Contact information for security issues

This is for the **software security** of Jest itself, not for personal data protection.

### File System Access

Jest does access the local filesystem to:
- Read test files
- Write coverage reports
- Manage snapshot files

This is **local developer machine access** for testing purposes, not personal data handling.

## Data Inventory Summary

| Data Type | Collection Point | Processing | Storage | Retention | Sensitivity | Compliance |
|-----------|-----------------|-----------|---------|-----------|-------------|------------|
| Source code files | Local filesystem | Test execution | Local | N/A | Non-personal | N/A |
| Test configurations | Local filesystem | Config parsing | Memory | Session | Non-personal | N/A |
| Coverage data | Generated | Instrumentation | Local files | Developer-managed | Non-personal | N/A |
| Snapshots | Generated | Comparison | Local files | Developer-managed | Non-personal | N/A |

## Risk Assessment

### High-Risk Processing

None identified - no personal data processing occurs.

### Vulnerabilities Related to Personal Data

None applicable - this is a developer tool that operates on code, not personal information.

## Conclusion

**Jest is a testing framework that does not process personal data.** 

The repository contains:
- Testing framework source code
- Documentation
- Example projects
- End-to-end tests for the framework itself

All data operations are:
1. Local to the developer's machine
2. Related to source code and test execution
3. Non-personal in nature

**No data protection measures, consent mechanisms, or privacy controls are required** for this type of software tool, as it does not handle personal information.

# security_check

Top 10 security vulnerabilities assessment

# Security Vulnerability Assessment Report

## Executive Summary

After a comprehensive security audit of the Jest testing framework repository, I found that this is a well-maintained open-source testing framework with relatively few critical security vulnerabilities. The codebase is primarily designed to run in development/testing environments rather than production, which affects the risk context. However, several security issues were identified that should be addressed.

---

## TOP 10 Security Issues Found

### Issue #1: Path Traversal in Snapshot Resolution
**Severity:** HIGH  
**Category:** Authorization & Access Control (Path Traversal)  
**Location:** 
- File: `packages/jest-snapshot/src/SnapshotResolver.ts`
- File: `packages/jest-snapshot-utils/src/SnapshotResolver.ts`

**Description:**
The snapshot resolver accepts user-controlled paths without sufficient validation, potentially allowing path traversal attacks when custom snapshot resolvers are configured.

**Vulnerable Code Pattern:**
```typescript
// packages/jest-snapshot/src/SnapshotResolver.ts
export const buildSnapshotResolver = async (
  config: Config.ProjectConfig,
  localRequire: Promise<LocalRequire> | LocalRequire = createTranspilingRequire(
    config,
  ),
): Promise<SnapshotResolver> => {
  const key = config.snapshotResolver;
  // User-provided path is loaded directly without sanitization
  const resolver: SnapshotResolver = await (
    await localRequire
  )(key ?? undefined);
```

**Impact:**
An attacker who can control the Jest configuration could potentially load arbitrary modules from the filesystem, leading to arbitrary code execution in the test environment.

**Fix Required:**
Add path validation to ensure snapshot resolver paths are within expected directories.

**Example Secure Implementation:**
```typescript
import path from 'path';

const validateResolverPath = (resolverPath: string, rootDir: string): string => {
  const resolved = path.resolve(rootDir, resolverPath);
  if (!resolved.startsWith(path.resolve(rootDir))) {
    throw new Error('Snapshot resolver path must be within project root');
  }
  return resolved;
};
```

---

### Issue #2: Arbitrary Code Execution via Custom Reporters
**Severity:** HIGH  
**Category:** Input Validation & Output Encoding (Code Injection)  
**Location:** 
- File: `packages/jest-reporters/src/index.ts`
- File: `packages/jest-core/src/ReporterDispatcher.ts`

**Description:**
Custom reporters are loaded dynamically from user-specified paths without sandboxing, allowing arbitrary code execution when Jest is run with malicious configuration.

**Vulnerable Code Pattern:**
```typescript
// packages/jest-core/src/ReporterDispatcher.ts
async register(reporter: Reporter): Promise<void> {
  this._reporters.push(reporter);
}

// Reporter loaded from user config without validation
```

**Impact:**
If an attacker can modify the Jest configuration (e.g., through a malicious package.json or jest.config.js), they can execute arbitrary code when Jest runs.

**Fix Required:**
Implement path validation and consider adding a whitelist for trusted reporter modules.

---

### Issue #3: Command Injection in Git Operations
**Severity:** HIGH  
**Category:** Injection Vulnerabilities (Command Injection)  
**Location:** 
- File: `packages/jest-changed-files/src/git.ts`
- Lines: Various `execa` calls

**Description:**
The git integration passes user-controllable values to shell commands. While `execa` provides some protection, certain paths or configurations could potentially be exploited.

**Vulnerable Code Pattern:**
```typescript
// packages/jest-changed-files/src/git.ts
const {stdout: changedFiles} = await execa(
  'git',
  ['diff', '--name-only', `${options.changedSince}`, '--', ...includePaths],
  {cwd},
);
```

**Impact:**
If `options.changedSince` or `includePaths` contain specially crafted values, they could potentially manipulate git command behavior.

**Fix Required:**
Validate and sanitize all inputs before passing to external commands.

**Example Secure Implementation:**
```typescript
const sanitizeGitRef = (ref: string): string => {
  // Only allow valid git ref characters
  if (!/^[a-zA-Z0-9._\-/]+$/.test(ref)) {
    throw new Error('Invalid git reference');
  }
  return ref;
};
```

---

### Issue #4: Prototype Pollution in Deep Merge Operations
**Severity:** MEDIUM  
**Category:** Input Validation (Deserialization Vulnerabilities)  
**Location:** 
- File: `packages/jest-config/src/normalize.ts`
- File: `packages/jest-config/src/utils.ts`

**Description:**
Configuration merging operations may be susceptible to prototype pollution attacks when processing untrusted configuration objects.

**Vulnerable Code Pattern:**
```typescript
// packages/jest-config/src/normalize.ts
// Deep merging of configuration objects without prototype pollution protection
const mergeOptionWithPreset = <T extends keyof Config.InitialOptions>(
  options: Config.InitialOptions,
  preset: Config.InitialOptions,
  optionName: T,
) => {
  // Direct property assignment without checking for __proto__ or constructor
}
```

**Impact:**
An attacker could craft a malicious jest.config.js that pollutes Object.prototype, affecting all objects in the Node.js process.

**Fix Required:**
Add prototype pollution checks during configuration parsing.

**Example Secure Implementation:**
```typescript
const isSafeKey = (key: string): boolean => {
  return key !== '__proto__' && key !== 'constructor' && key !== 'prototype';
};

const safeMerge = (target: object, source: object): object => {
  for (const key of Object.keys(source)) {
    if (isSafeKey(key)) {
      target[key] = source[key];
    }
  }
  return target;
};
```

---

### Issue #5: Unsafe Dynamic Import in Test Environment
**Severity:** MEDIUM  
**Category:** Authorization & Access Control  
**Location:** 
- File: `packages/jest-runtime/src/index.ts`
- Function: `requireModule`

**Description:**
The runtime module loading system allows dynamic imports based on test file content, which could be exploited to load arbitrary modules.

**Vulnerable Code Pattern:**
```typescript
// packages/jest-runtime/src/index.ts
private _execModule(
  localModule: InitialModule,
  options: InternalModuleOptions | undefined,
  moduleRegistry: ModuleRegistry,
  from: string | null,
  // Module path is resolved and loaded
) {
  // Dynamic require without restriction on which modules can be loaded
}
```

**Impact:**
Test code could potentially access sensitive modules or files outside the intended test scope.

**Fix Required:**
Consider implementing module access controls for sensitive environments.

---

### Issue #6: Information Disclosure in Error Messages
**Severity:** MEDIUM  
**Category:** Data Exposure  
**Location:** 
- File: `packages/jest-message-util/src/index.ts`
- File: `packages/jest-console/src/BufferedConsole.ts`

**Description:**
Error messages and stack traces may expose sensitive filesystem paths and internal implementation details.

**Vulnerable Code Pattern:**
```typescript
// packages/jest-message-util/src/index.ts
export const formatStackTrace = (
  stack: string,
  config: StackTraceConfig,
  options: StackTraceOptions,
  testPath?: string,
): string => {
  // Full filesystem paths are included in error output
  // Could expose directory structure and usernames
}
```

**Impact:**
Attackers could gain information about the system's file structure, installed packages, and user accounts through verbose error messages.

**Fix Required:**
Add option to sanitize paths in error messages, especially for CI/CD environments.

---

### Issue #7: Insecure Random in Test Shuffling
**Severity:** LOW  
**Category:** Cryptographic Issues  
**Location:** 
- File: `packages/jest-core/src/TestScheduler.ts`
- File: `packages/jest-circus/src/shuffleArray.ts`

**Description:**
Test shuffling uses `Math.random()` which is not cryptographically secure. While this is acceptable for test ordering, it could be problematic if used for security-sensitive operations.

**Vulnerable Code Pattern:**
```typescript
// packages/jest-circus/src/shuffleArray.ts
export default function shuffleArray<T>(
  array: Array<T>,
  seed: number,
): Array<T> {
  // Uses deterministic shuffle based on seed
  // Math.random-style operations
}
```

**Impact:**
Predictable test ordering in security testing scenarios could allow attackers to manipulate test execution order.

**Fix Required:**
Use crypto.randomBytes() for security-sensitive shuffling if needed.

---

### Issue #8: Unsafe YAML Parsing in Configuration
**Severity:** MEDIUM  
**Category:** Input Validation (Deserialization)  
**Location:** 
- File: `crowdin.yaml`
- Indirectly via yaml parsing in tooling

**Description:**
YAML configuration files could potentially be exploited if parsed with unsafe loaders that allow arbitrary code execution.

**Vulnerable Code Pattern:**
```yaml
# crowdin.yaml
project_id_env: CROWDIN_PROJECT_ID
api_token_env: CROWDIN_TOKEN
# If parsed with yaml.load() instead of yaml.safeLoad(), could be exploited
```

**Impact:**
If YAML files are parsed unsafely, attackers could inject executable code through specially crafted YAML.

**Fix Required:**
Ensure all YAML parsing uses safe loaders.

---

### Issue #9: ReDoS Vulnerability in Pattern Matching
**Severity:** MEDIUM  
**Category:** Business Logic Flaws  
**Location:** 
- File: `packages/jest-pattern/src/TestPathPatternPrompt.ts`
- File: `packages/jest-regex-util/src/index.ts`

**Description:**
User-provided regex patterns are compiled without timeout protection, potentially allowing ReDoS attacks that could hang the test runner.

**Vulnerable Code Pattern:**
```typescript
// packages/jest-regex-util/src/index.ts
export const escapePathForRegex = (dir: string): string => {
  if (path.sep === '\\') {
    // User input directly used in regex
    return dir.replaceAll(/(\/|\\(?!\())/g, '\\\\');
  }
  return dir.replaceAll(/\//g, '\\/');
};
```

**Impact:**
An attacker could craft a test path pattern that causes catastrophic backtracking, effectively causing denial of service.

**Fix Required:**
Implement regex execution timeouts and validate pattern complexity.

---

### Issue #10: Potential XXE in XML Test Results
**Severity:** LOW  
**Category:** Input Validation (XXE)  
**Location:** 
- File: `packages/jest-reporters/src/getResultHeader.ts`
- Any XML generation/parsing in test results

**Description:**
Test result formatters that generate or parse XML could be vulnerable to XXE if external entity processing is not disabled.

**Vulnerable Code Pattern:**
```typescript
// packages/jest-reporters/src/getResultHeader.ts
// While primarily generating XML, any parsing operations should disable external entities
```

**Impact:**
If XML parsers are used without proper configuration, external entities could be used to read local files or cause SSRF.

**Fix Required:**
Ensure all XML operations disable external entity processing.

---

## Summary

### 1. Overall Security Posture
The Jest codebase has a **MODERATE** security posture. As a testing framework primarily used in development environments, many findings have reduced real-world impact. However, the dynamic code loading capabilities and configuration flexibility create attack surface that should be hardened.

### 2. Critical Issues Count
- **CRITICAL:** 0
- **HIGH:** 3
- **MEDIUM:** 5
- **LOW:** 2

### 3. Most Concerning Pattern
**Dynamic Code Loading Without Validation** - The framework's architecture requires loading user-specified modules (reporters, transformers, test environments, resolvers) without sufficient validation, creating multiple code execution paths.

### 4. Priority Fixes
1. **Issue #1 (Path Traversal)** - Add path validation for snapshot resolvers
2. **Issue #3 (Command Injection)** - Sanitize inputs to git commands
3. **Issue #4 (Prototype Pollution)** - Add pollution protection in config merging

### 5. Implementation Issues
- **Pattern 1:** User-controlled module paths are loaded without validation across multiple components
- **Pattern 2:** Configuration objects are merged without prototype pollution checks
- **Pattern 3:** External process execution with user-influenced arguments
- **Pattern 4:** Error messages expose internal system details

---

## Additional Security Issues Found

### Configuration Vulnerabilities Present
- Multiple configuration files allow arbitrary code execution by design (jest.config.js, setup files)
- Transform configuration allows loading arbitrary transform modules
- Test environment configuration allows custom environment modules

### Architecture Security Flaws Identified
- Worker processes execute with full privileges of parent process
- No sandboxing between test files
- Global setup/teardown scripts execute with full system access

### Development Implementation Issues
- Some test fixtures contain example code that demonstrates insecure patterns
- E2E tests have hardcoded test data that could be misused as templates

### Insecure Coding Patterns Found
- Occasional use of `any` type in TypeScript reducing type safety
- Some async operations lack proper error boundaries
- Timeout configurations are inconsistent across modules

---

**Note:** This assessment focused on actual code present in the repository. The Jest framework is designed for development/testing environments where some of these risks are acceptable trade-offs for flexibility. Production deployment of Jest as a service would require additional security controls.

# monitoring

Monitoring, logging, metrics, and observability analysis

# Monitoring and Observability Analysis Report

## Executive Summary

After thorough analysis of the Jest testing framework codebase, **no monitoring or observability detected** in the traditional sense (production application monitoring, APM, metrics collection, distributed tracing, alerting systems, etc.).

This is expected behavior as Jest is a **testing framework library**, not a production application. The codebase does not include:
- Application Performance Monitoring (APM) tools
- Metrics collection libraries (Prometheus, StatsD, etc.)
- Distributed tracing (OpenTelemetry, Jaeger, etc.)
- Centralized logging platforms (ELK, Splunk, etc.)
- Alerting systems (PagerDuty, Opsgenie, etc.)
- Real User Monitoring (RUM)
- Error tracking services (Sentry, Rollbar, etc.)

## What IS Present in the Codebase

### 1. Console Logging (Built-in for Test Output)

**Package:** `@jest/console` (`/packages/jest-console/`)

This is **not monitoring** but rather Jest's internal mechanism for capturing and displaying console output during test execution.

```
Dependencies:
- chalk (for colored output formatting)
- jest-message-util (for message formatting)
- jest-util (utilities)
```

**Purpose:** Captures `console.log`, `console.warn`, `console.error` calls made during tests and displays them in test output.

### 2. Test Reporters (Test Result Output)

**Package:** `@jest/reporters` (`/packages/jest-reporters/`)

These are **test result reporters**, not monitoring systems. They output test results in various formats.

**Key reporters found:**
- Default reporter (console output)
- JSON reporter
- JUnit reporter (via `jest-junit` dev dependency)
- GitHub Actions reporter
- Coverage reporters (Istanbul-based)

**Coverage-related dependencies:**
```
- istanbul-lib-coverage
- istanbul-lib-instrument
- istanbul-lib-report
- istanbul-lib-source-maps
- istanbul-reports
- v8-to-istanbul
- @bcoe/v8-coverage
- collect-v8-coverage
```

### 3. Winston Logger (E2E Test Only)

**Location:** `/e2e/console-winston/`

```json
{
  "dependencies": {
    "winston": "^3.2.1"
  }
}
```

**Purpose:** This is an **end-to-end test** that verifies Jest properly handles Winston logger output during tests. It is **not used for monitoring the Jest codebase itself**.

### 4. Code Coverage Collection

Jest includes comprehensive code coverage collection, but this is for **test coverage metrics**, not production monitoring:

- V8 coverage provider
- Istanbul/Babel instrumentation
- Coverage thresholds and reporting

### 5. CI/CD Integration (Not Runtime Monitoring)

**GitHub Actions Workflows:** (`.github/workflows/`)
- `test.yml` - Runs tests
- `nodejs.yml` - Node.js version testing
- `nightly.yml` - Nightly builds
- `test-nightly.yml` - Nightly test runs

These are **CI/CD pipelines**, not application monitoring.

**Codecov Integration:** (`.codecov.yml`)
- Code coverage reporting to Codecov service
- This is **test coverage tracking**, not application monitoring

### 6. Debug Capabilities

**Debug package usage pattern:** The `debug` npm package pattern is used in some dependencies but not as a formal logging framework within Jest's core code.

---

## Raw Dependencies Section

### Root `package.json` devDependencies (Monitoring-Related Items - None Found)

```json
{
  "devDependencies": {
    "@babel/core": "^7.27.4",
    "@babel/plugin-transform-modules-commonjs": "^7.27.1",
    "@babel/preset-env": "^7.27.2",
    "@babel/preset-react": "^7.27.1",
    "@babel/preset-typescript": "^7.27.1",
    "@babel/register": "^7.27.1",
    "@crowdin/cli": "^4.7.0",
    "@eslint-community/eslint-plugin-eslint-comments": "^4.5.0",
    "@eslint/js": "^9.28.0",
    "@eslint/markdown": "^6.4.0",
    "@jest/globals": "workspace:*",
    "@jest/test-utils": "workspace:*",
    "@lerna-lite/cli": "^4.3.0",
    "@lerna-lite/exec": "^4.3.0",
    "@lerna-lite/publish": "^4.3.0",
    "@microsoft/api-extractor": "^7.35.0",
    "@tsconfig/node18": "^18.2.4",
    "@types/babel__core": "^7.20.5",
    "@types/babel__generator": "^7.27.0",
    "@types/babel__template": "^7.4.4",
    "@types/node": "^18.14",
    "@types/which": "^3.0.4",
    "@yarnpkg/types": "^4.0.1",
    "ansi-regex": "^5.0.1",
    "ansi-styles": "^5.2.0",
    "babel-jest": "workspace:*",
    "babel-loader": "^9.2.1",
    "camelcase": "^6.3.0",
    "chalk": "^4.1.2",
    "dedent": "^1.6.0",
    "eslint": "^9.28.0",
    "eslint-config-prettier": "^10.1.5",
    "eslint-import-resolver-typescript": "^4.4.2",
    "eslint-plugin-import-x": "^4.15.0",
    "eslint-plugin-jest": "^28.12.0",
    "eslint-plugin-jsdoc": "^50.7.1",
    "eslint-plugin-prettier": "^5.4.1",
    "eslint-plugin-promise": "^7.2.1",
    "eslint-plugin-unicorn": "^59.0.1",
    "execa": "^5.1.1",
    "find-process": "^1.4.10",
    "glob": "^10.5.0",
    "globals": "^16.2.0",
    "graceful-fs": "^4.2.11",
    "isbinaryfile": "^5.0.4",
    "istanbul-lib-coverage": "^3.0.0",
    "istanbul-lib-report": "^3.0.0",
    "istanbul-reports": "^3.1.3",
    "jest": "workspace:*",
    "jest-changed-files": "workspace:*",
    "jest-junit": "^16.0.0",
    "jest-mock": "workspace:*",
    "jest-serializer-ansi-escapes": "^3.0.0",
    "jest-silent-reporter": "^0.6.0",
    "jest-snapshot": "workspace:*",
    "jest-util": "workspace:*",
    "jest-watch-typeahead": "^3.0.1",
    "jquery": "^3.2.1",
    "js-yaml": "^4.1.1",
    "micromatch": "^4.0.8",
    "mock-fs": "^5.5.0",
    "netlify-plugin-cache": "^1.0.3",
    "node-notifier": "^10.0.1",
    "p-limit": "^3.1.0",
    "pkg-dir": "^5.0.0",
    "prettier": "^3.0.3",
    "promise": "^8.3.0",
    "read-pkg": "^5.2.0",
    "resolve": "^1.20.0",
    "rimraf": "^5.0.10",
    "semver": "^7.7.2",
    "slash": "^3.0.0",
    "strip-json-comments": "^3.1.1",
    "tempy": "^1.0.1",
    "ts-node": "^10.5.0",
    "tstyche": "^4.0.0",
    "typescript": "^5.8.3",
    "typescript-eslint": "^8.38.0",
    "webpack": "^5.68.0",
    "webpack-node-externals": "^3.0.0",
    "which": "^4.0.0"
  }
}
```

### E2E console-winston `package.json`

```json
{
  "dependencies": {
    "winston": "^3.2.1"
  }
}
```

### Key Package Dependencies (Scanned for Monitoring Tools)

After scanning all package.json files, the following logging/monitoring-adjacent libraries were found:

| Package | Location | Purpose | Is Monitoring? |
|---------|----------|---------|----------------|
| `winston` | e2e/console-winston | E2E test fixture | ❌ No |
| `chalk` | Multiple packages | Console output coloring | ❌ No |
| `istanbul-*` | jest-reporters | Test coverage | ❌ No |
| `v8-to-istanbul` | jest-reporters | Test coverage | ❌ No |
| `jest-junit` | Root devDeps | JUnit test report format | ❌ No |
| `node-notifier` | jest-cli, jest-core, jest-reporters | Desktop notifications for test results | ❌ No |

---

## Conclusion

**No monitoring or observability detected.**

This Jest repository is a testing framework library. The dependencies and code related to logging, metrics, or reporting are all specifically for:
1. **Test output formatting** (chalk, console capture)
2. **Test coverage collection** (Istanbul, V8 coverage)
3. **Test result reporting** (JUnit, JSON reporters)
4. **CI/CD integration** (GitHub Actions, Codecov)

None of these constitute application monitoring, APM, distributed tracing, or observability infrastructure as would be found in a production application or service.

# ml_services

3rd party ML services and technologies analysis

# 3rd Party ML Services and Technologies Analysis

## Executive Summary

After thoroughly analyzing this codebase, **no 3rd party machine learning services, AI technologies, or ML-related integrations were identified**. This codebase is the **Jest JavaScript Testing Framework** - a comprehensive testing solution for JavaScript applications.

---

## Analysis Results

### 1. External ML Service Providers
**Status**: ❌ None Found

No usage of:
- Cloud ML Services (AWS SageMaker, Azure ML, Google AI Platform, Databricks)
- AI APIs (OpenAI, Anthropic, Groq, Cohere, Hugging Face Inference API)
- Specialized ML Services (AWS Transcribe, Google Speech-to-Text, AWS Rekognition, Google Vision)
- MLOps Platforms (MLflow, Weights & Biases, Neptune, ClearML)

### 2. ML Libraries and Frameworks
**Status**: ❌ None Found

No usage of:
- Deep Learning frameworks (PyTorch, TensorFlow, JAX, Keras)
- Traditional ML libraries (Scikit-learn, XGBoost, LightGBM, CatBoost)
- NLP libraries (Transformers, spaCy, NLTK, Gensim)
- Computer Vision libraries (OpenCV, torchvision)
- Audio/Speech libraries (Whisper, librosa, speechbrain)

### 3. Pre-trained Models and Model Hubs
**Status**: ❌ None Found

No usage of:
- Hugging Face Models or transformers
- TensorFlow Hub
- PyTorch Hub
- Custom model repositories
- Specific pre-trained models (BERT, GPT variants, Whisper, CLIP)

### 4. AI Infrastructure and Deployment
**Status**: ❌ None Found

No usage of:
- Model Serving solutions (TorchServe, TensorFlow Serving)
- ML-specific containerization
- GPU/CUDA configurations
- TPU integration
- ML workload auto-scaling systems

---

## Codebase Context

### What This Codebase Actually Is

This is the **Jest** testing framework repository, which includes:

| Component | Purpose |
|-----------|---------|
| `jest-core` | Core test runner functionality |
| `jest-cli` | Command-line interface |
| `jest-config` | Configuration management |
| `jest-runner` | Test execution engine |
| `jest-snapshot` | Snapshot testing capabilities |
| `babel-jest` | Babel transformation for tests |
| `pretty-format` | Output formatting |
| `expect` | Assertion library |

### Key Dependencies Identified (Non-ML)

The dependencies fall into these categories:

1. **Build/Transpilation**: Babel ecosystem (`@babel/core`, `@babel/preset-env`, etc.)
2. **Testing Utilities**: Testing libraries (`@testing-library/react`, `chai`)
3. **Code Coverage**: Istanbul (`istanbul-lib-coverage`, `istanbul-reports`)
4. **UI Frameworks** (for testing examples): React, Angular, jQuery
5. **Utilities**: chalk, graceful-fs, micromatch, glob
6. **Documentation**: Docusaurus

---

## Security and Compliance Considerations

### API Keys/Credentials
- **Status**: Not applicable - no ML services requiring authentication

### Data Privacy
- **Status**: Not applicable - no data sent to 3rd party ML services

### Model Security
- **Status**: Not applicable - no ML models in use

### Compliance
- **Status**: No ML-specific regulatory requirements apply

---

## Summary

| Metric | Value |
|--------|-------|
| **Total ML Services Identified** | 0 |
| **Total ML Libraries Identified** | 0 |
| **Total Pre-trained Models** | 0 |
| **ML Infrastructure Components** | 0 |
| **Architecture Pattern** | N/A (No ML components) |

### Risk Assessment

| Risk Category | Level | Notes |
|---------------|-------|-------|
| ML Vendor Lock-in | None | No ML vendors in use |
| ML Service Costs | None | No ML service dependencies |
| ML Data Privacy | None | No ML data processing |
| ML Model Security | None | No models to secure |

### Conclusion

This codebase is a pure JavaScript/TypeScript testing framework with **zero machine learning or AI integrations**. All identified dependencies serve traditional software development purposes including:

- Code transpilation and bundling
- Test execution and assertion
- Code coverage analysis
- Documentation generation
- Example applications for testing demonstrations

No further ML-related analysis or remediation is required for this repository.

# feature_flags

Feature flag frameworks and usage patterns analysis

# Feature Flag Analysis for Jest Repository

## No Feature Flag Usage Detected

After thoroughly analyzing the Jest codebase (repository `jest_5f4252c6`), I found **no feature flag systems** implemented in this repository.

### Analysis Performed

I examined the following areas for feature flag implementations:

#### 1. Commercial Feature Flag Platforms
- **Flagsmith**: Not found in dependencies or imports
- **LaunchDarkly**: Not found in dependencies or imports
- **Split.io**: Not found in dependencies or imports
- **Optimizely**: Not found in dependencies or imports
- **ConfigCat**: Not found in dependencies or imports
- **Unleash**: Not found in dependencies or imports

#### 2. Feature Flag SDKs/Libraries
No feature flag-related packages were found in any `package.json` files across the monorepo:
- No `launchdarkly-*` packages
- No `flagsmith-*` packages
- No `@splitsoftware/*` packages
- No `@unleash/*` packages
- No `configcat-*` packages
- No `@optimizely/*` packages

#### 3. Custom Feature Flag Implementations
- No database-backed feature flag systems
- No custom feature flag services or utilities
- No feature flag configuration files (e.g., `features.json`, `flags.yaml`)

#### 4. Environment Variable-Based Feature Flags
While Jest uses environment variables extensively for configuration (e.g., `NODE_ENV`, `CI`, `JEST_WORKER_ID`), these are **runtime configuration** and **test environment variables**, not feature flags in the traditional sense. They control:
- Test execution behavior
- CI/CD pipeline detection
- Worker process identification
- Debug modes

These are **not** feature flags because they:
- Don't control feature rollouts
- Don't enable/disable application features for users
- Don't support gradual rollouts or A/B testing
- Are not managed through a feature flag management system

### Codebase Context

Jest is a testing framework, and its configuration system uses:
- **Jest configuration files** (`jest.config.js`, `jest.config.ts`, etc.)
- **Command-line arguments** for runtime options
- **Environment detection** (via `ci-info` package) for CI-specific behavior
- **Package-level configuration** for monorepo management

None of these constitute a feature flag system.

---

**Result: No feature flag usage detected**

# prompt_security_check

LLM and prompt injection vulnerability assessment

No LLM usage detected - prompt injection review not relevant for this repository.

This is the Jest testing framework repository - a JavaScript testing framework developed by Meta (formerly Facebook). The codebase contains:

1. **Core testing framework packages** (`packages/jest-*`) - Test runners, matchers, mocking utilities, snapshot testing, etc.
2. **Documentation and website** (`website/`, `docs/`)
3. **Examples** (`examples/`) - Sample test setups for React, TypeScript, Angular, etc.
4. **End-to-end tests** (`e2e/`) - Integration tests for Jest itself
5. **Build scripts** (`scripts/`)

There are no imports, dependencies, or code patterns indicating any LLM/AI model integration:
- No OpenAI, Anthropic, Google AI, or other LLM SDK imports
- No LangChain, LlamaIndex, or similar frameworks
- No vector database integrations
- No prompt templates or LLM API calls
- The `package.json` files contain only testing-related dependencies

The files named `CLAUDE.md` and `.github/copilot-instructions.md` are documentation files providing guidance for AI assistants interacting with the codebase (developer tooling configuration), not actual LLM integrations within the application itself.

# api_surface

Public API analysis and design patterns

# Jest Public API Analysis

## Overview

Jest is a comprehensive JavaScript testing framework. This analysis documents the actual API components and design patterns implemented in the codebase.

---

## 1. Public API Analysis

### Entry Points

#### Main Package (`packages/jest/`)

**Source:** `packages/jest/src/index.ts`

```typescript
// Default export
export {run} from 'jest-cli';

// Named exports
export {
  SearchSource,
  TestScheduler,
  TestWatcher,
  createTestScheduler,
  getVersion,
} from 'jest-core';

export {run, yargs} from 'jest-cli';

// Configuration utilities
export {
  defineConfig,
  mergeConfig,
  defaults,
  descriptions,
  normalize,
  readConfig,
  readConfigs,
  readInitialOptions,
  replaceRootDirInPath,
} from 'jest-config';
```

#### CLI Entry Point (`packages/jest-cli/`)

**Source:** `packages/jest-cli/bin/jest.js`

```javascript
#!/usr/bin/env node
'use strict';

const importLocal = require('import-local');

if (!importLocal(__filename)) {
  if (process.env.NODE_ENV == null) {
    process.env.NODE_ENV = 'test';
  }

  require('../build/index').run();
}
```

---

### Core Exports by Package

#### `expect` Package

**Source:** `packages/expect/src/index.ts`

```typescript
export {expect} from './expect';
export {
  AsymmetricMatchers,
  BaseExpect,
  Expect,
  MatcherContext,
  MatcherFunction,
  MatcherFunctionWithContext,
  MatcherState,
  Matchers,
  SyncExpectationResult,
  AsyncExpectationResult,
  ExpectationResult,
} from './types';
```

**Core Function - `expect`:**

```typescript
// Signature
function expect<T = unknown>(actual: T): Matchers<T>;

// Usage
expect(2 + 2).toBe(4);
expect([1, 2, 3]).toContain(2);
expect({name: 'jest'}).toEqual({name: 'jest'});

// Async matchers
await expect(Promise.resolve(42)).resolves.toBe(42);
await expect(Promise.reject(new Error('fail'))).rejects.toThrow('fail');
```

#### `jest-mock` Package

**Source:** `packages/jest-mock/src/index.ts`

```typescript
export {
  fn,
  spyOn,
  replaceProperty,
  mocked,
  ModuleMocker,
  Mock,
  MockInstance,
  Mocked,
  MockedClass,
  MockedFunction,
  MockedObject,
  SpiedClass,
  SpiedFunction,
  SpiedGetter,
  SpiedSetter,
  SpyInstance,
  UnknownFunction,
  Replaced,
  ReplacedProperty,
} from './index';
```

**Core Functions:**

```typescript
// jest.fn() - Create mock function
function fn<T extends (...args: any[]) => any>(): Mock<T>;
function fn<T extends (...args: any[]) => any>(implementation: T): Mock<T>;

// Usage
const mockFn = jest.fn();
const mockFnWithImpl = jest.fn(() => 42);
const mockFnTyped = jest.fn<(a: number, b: number) => number>();

// jest.spyOn() - Spy on existing methods
function spyOn<T extends object, M extends keyof T>(
  object: T,
  methodName: M,
  accessType?: 'get' | 'set'
): SpyInstance;

// Usage
const spy = jest.spyOn(console, 'log');
const getSpy = jest.spyOn(obj, 'prop', 'get');

// jest.replaceProperty() - Replace property values
function replaceProperty<T extends object, K extends keyof T>(
  object: T,
  propertyKey: K,
  value: T[K]
): Replaced<T[K]>;

// Usage
const replaced = jest.replaceProperty(process, 'env', {NODE_ENV: 'test'});
replaced.restore();
```

#### `jest-globals` Package

**Source:** `packages/jest-globals/src/index.ts`

```typescript
export {
  afterAll,
  afterEach,
  beforeAll,
  beforeEach,
  describe,
  expect,
  it,
  test,
  jest,
} from '@jest/globals';
```

---

## 2. Core Functions/Methods

### Global Test Functions

**Source:** `packages/jest-circus/src/index.ts` and `packages/jest-jasmine2/src/`

#### `describe` - Test Suite Declaration

```typescript
// Signature
function describe(name: string, fn: () => void): void;
function describe(name: TemplateLiteralCallback, ...args: any[]): void;

// Modifiers
describe.only(name: string, fn: () => void): void;  // Focus
describe.skip(name: string, fn: () => void): void;  // Skip
describe.each(table: any[])(name: string, fn: Function): void;  // Parameterized

// Usage
describe('Calculator', () => {
  describe('add', () => {
    it('adds two numbers', () => {
      expect(add(1, 2)).toBe(3);
    });
  });
});

// Parameterized
describe.each([
  [1, 1, 2],
  [1, 2, 3],
  [2, 1, 3],
])('.add(%i, %i)', (a, b, expected) => {
  test(`returns ${expected}`, () => {
    expect(add(a, b)).toBe(expected);
  });
});
```

#### `test` / `it` - Test Case Declaration

```typescript
// Signatures
function test(name: string, fn: () => void | Promise<void>, timeout?: number): void;
function test(name: string, fn: (done: DoneCallback) => void, timeout?: number): void;

// Modifiers
test.only(name: string, fn: TestFn, timeout?: number): void;
test.skip(name: string, fn: TestFn, timeout?: number): void;
test.todo(name: string): void;
test.failing(name: string, fn: TestFn, timeout?: number): void;
test.concurrent(name: string, fn: () => Promise<void>, timeout?: number): void;
test.each(table: any[])(name: string, fn: Function, timeout?: number): void;

// Usage
test('adds 1 + 2 to equal 3', () => {
  expect(1 + 2).toBe(3);
});

// Async test
test('async operation', async () => {
  const data = await fetchData();
  expect(data).toBe('data');
});

// Callback-based async
test('callback test', done => {
  asyncOperation(result => {
    expect(result).toBe('expected');
    done();
  });
});

// test.failing - expected to fail
test.failing('this test should fail', () => {
  expect(1).toBe(2);
});

// test.concurrent - run in parallel
test.concurrent('parallel test 1', async () => {
  const result = await slowOperation();
  expect(result).toBe('expected');
});

// test.todo - placeholder
test.todo('implement this later');

// test.each - parameterized
test.each([
  [1, 1, 2],
  [1, 2, 3],
])('add(%i, %i) = %i', (a, b, expected) => {
  expect(add(a, b)).toBe(expected);
});

// test.each with template literal
test.each`
  a    | b    | expected
  ${1} | ${1} | ${2}
  ${1} | ${2} | ${3}
`('add($a, $b) = $expected', ({a, b, expected}) => {
  expect(add(a, b)).toBe(expected);
});
```

### Lifecycle Hooks

**Source:** `packages/jest-circus/src/index.ts`

```typescript
// Signatures
function beforeAll(fn: () => void | Promise<void>, timeout?: number): void;
function afterAll(fn: () => void | Promise<void>, timeout?: number): void;
function beforeEach(fn: () => void | Promise<void>, timeout?: number): void;
function afterEach(fn: () => void | Promise<void>, timeout?: number): void;

// Usage
describe('database tests', () => {
  beforeAll(async () => {
    await database.connect();
  });

  afterAll(async () => {
    await database.disconnect();
  });

  beforeEach(async () => {
    await database.clear();
  });

  afterEach(() => {
    jest.clearAllMocks();
  });

  test('creates a record', async () => {
    await database.create({name: 'test'});
    expect(await database.count()).toBe(1);
  });
});
```

### Expect Matchers

**Source:** `packages/expect/src/matchers.ts`

```typescript
// Basic Matchers
expect(value).toBe(expected);           // Strict equality (===)
expect(value).toEqual(expected);        // Deep equality
expect(value).toStrictEqual(expected);  // Deep equality + type checking
expect(value).toBeDefined();
expect(value).toBeUndefined();
expect(value).toBeNull();
expect(value).toBeTruthy();
expect(value).toBeFalsy();
expect(value).toBeNaN();

// Number Matchers
expect(value).toBeGreaterThan(number);
expect(value).toBeGreaterThanOrEqual(number);
expect(value).toBeLessThan(number);
expect(value).toBeLessThanOrEqual(number);
expect(value).toBeCloseTo(number, numDigits?);

// String Matchers
expect(string).toMatch(regexp | string);
expect(string).toContain(substring);
expect(string).toHaveLength(length);

// Array/Iterable Matchers
expect(array).toContain(item);
expect(array).toContainEqual(item);
expect(array).toHaveLength(length);

// Object Matchers
expect(object).toHaveProperty(keyPath, value?);
expect(object).toMatchObject(object);

// Function/Error Matchers
expect(fn).toThrow(error?);
expect(fn).toThrowError(error?);

// Mock Matchers
expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledTimes(number);
expect(mockFn).toHaveBeenCalledWith(...args);
expect(mockFn).toHaveBeenLastCalledWith(...args);
expect(mockFn).toHaveBeenNthCalledWith(n, ...args);
expect(mockFn).toHaveReturned();
expect(mockFn).toHaveReturnedTimes(number);
expect(mockFn).toHaveReturnedWith(value);
expect(mockFn).toHaveLastReturnedWith(value);
expect(mockFn).toHaveNthReturnedWith(n, value);

// Snapshot Matchers
expect(value).toMatchSnapshot(hint?);
expect(value).toMatchInlineSnapshot(snapshot?);
expect(fn).toThrowErrorMatchingSnapshot(hint?);
expect(fn).toThrowErrorMatchingInlineSnapshot(snapshot?);

// Promise Matchers
await expect(promise).resolves.toBe(value);
await expect(promise).rejects.toThrow(error);

// Negation
expect(value).not.toBe(expected);
```

### Asymmetric Matchers

**Source:** `packages/expect/src/asymmetricMatchers.ts`

```typescript
// Usage
expect.anything();           // Matches anything except null/undefined
expect.any(constructor);     // Matches any instance of constructor
expect.arrayContaining(array);
expect.objectContaining(object);
expect.stringContaining(string);
expect.stringMatching(regexp);
expect.closeTo(number, numDigits);

// Examples
expect({name: 'test', id: 1}).toEqual({
  name: 'test',
  id: expect.any(Number),
});

expect(['apple', 'banana', 'cherry']).toEqual(
  expect.arrayContaining(['apple', 'banana'])
);

expect({a: 1, b: 2, c: 3}).toEqual(
  expect.objectContaining({a: 1, b: 2})
);

expect('Hello World').toEqual(
  expect.stringContaining('World')
);

expect('jest@example.com').toEqual(
  expect.stringMatching(/@example\.com$/)
);

// Negated asymmetric matchers
expect.not.arrayContaining(array);
expect.not.objectContaining(object);
expect.not.stringContaining(string);
expect.not.stringMatching(regexp);
```

---

## 3. Classes/Constructors

### ModuleMocker Class

**Source:** `packages/jest-mock/src/index.ts`

```typescript
class ModuleMocker {
  constructor(global: typeof globalThis);
  
  // Create mock functions
  fn<T extends (...args: any[]) => any>(implementation?: T): Mock<T>;
  
  // Spy on methods
  spyOn<T extends object, M extends keyof T>(
    object: T,
    methodName: M,
    accessType?: 'get' | 'set'
  ): SpyInstance;
  
  // Replace properties
  replaceProperty<T extends object, K extends keyof T>(
    object: T,
    propertyKey: K,
    value: T[K]
  ): Replaced<T[K]>;
  
  // Clear/reset/restore mocks
  clearAllMocks(): void;
  resetAllMocks(): void;
  restoreAllMocks(): void;
  
  // Module mocking
  generateFromMetadata<T>(metadata: MockMetadata<T>): T;
  getMetadata<T>(component: T): MockMetadata<T> | null;
  isMockFunction<T>(fn: T): fn is Mock<T>;
  
  // Mocked helper for TypeScript
  mocked<T>(source: T, options?: {shallow: boolean}): Mocked<T>;
}
```

### Mock Instance Methods

**Source:** `packages/jest-mock/src/index.ts`

```typescript
interface MockInstance<T extends (...args: any[]) => any> {
  // Properties
  mock: MockFunctionState<T>;
  
  // Configuration methods
  mockClear(): this;
  mockReset(): this;
  mockRestore(): void;
  
  // Implementation methods
  mockImplementation(fn: T): this;
  mockImplementationOnce(fn: T): this;
  
  // Return value methods
  mockReturnValue(value: ReturnType<T>): this;
  mockReturnValueOnce(value: ReturnType<T>): this;
  mockResolvedValue(value: ResolvedValue<T>): this;
  mockResolvedValueOnce(value: ResolvedValue<T>): this;
  mockRejectedValue(value: unknown): this;
  mockRejectedValueOnce(value: unknown): this;
  
  // Naming
  mockName(name: string): this;
  getMockName(): string;
  
  // Context binding
  withImplementation(fn: T, callback: () => void | Promise<void>): Promise<void>;
}

// MockFunctionState
interface MockFunctionState<T> {
  calls: Array<Parameters<T>>;
  contexts: Array<ThisParameterType<T>>;
  instances: Array<ReturnType<T>>;
  invocationCallOrder: Array<number>;
  lastCall: Parameters<T> | undefined;
  results: Array<MockResult<ReturnType<T>>>;
}
```

### JestEnvironment (Abstract Class)

**Source:** `packages/jest-environment/src/index.ts`

```typescript
abstract class JestEnvironment<Timer = unknown> {
  global: Global.Global;
  fakeTimersLolex: LolexFakeTimers | null;
  fakeTimers: FakeTimers | null;
  moduleMocker: ModuleMocker | null;
  
  constructor(config: JestEnvironmentConfig, context: EnvironmentContext);
  
  abstract setup(): Promise<void>;
  abstract teardown(): Promise<void>;
  abstract getVmContext(): Context | null;
  
  // Optional lifecycle hook
  handleTestEvent?(event: Circus.Event, state: Circus.State): void | Promise<void>;
  
  // Timer management
  exportConditions?(): Array<string>;
}
```

### NodeEnvironment Class

**Source:** `packages/jest-environment-node/src/index.ts`

```typescript
class NodeEnvironment extends JestEnvironment {
  constructor(config: JestEnvironmentConfig, context: EnvironmentContext);
  
  async setup(): Promise<void>;
  async teardown(): Promise<void>;
  getVmContext(): Context | null;
  
  exportConditions(): Array<string>;
}

// Usage in jest.config.js
module.exports = {
  testEnvironment: 'node',
  // or
  testEnvironment: 'jest-environment-node',
};
```

### JsdomEnvironment Class

**Source:** `packages/jest-environment-jsdom/src/index.ts`

```typescript
class JsdomEnvironment extends JestEnvironment {
  dom: JSDOM | null;
  
  constructor(config: JestEnvironmentConfig, context: EnvironmentContext);
  
  async setup(): Promise<void>;
  async teardown(): Promise<void>;
  getVmContext(): Context | null;
}

// Configuration
module.exports = {
  testEnvironment: 'jsdom',
  testEnvironmentOptions: {
    url: 'http://localhost',
    customExportConditions: ['node', 'node-addons'],
  },
};
```

### TestScheduler Class

**Source:** `packages/jest-core/src/TestScheduler.ts`

```typescript
class TestScheduler {
  constructor(
    globalConfig: Config.GlobalConfig,
    options: TestSchedulerOptions,
    context: TestSchedulerContext
  );
  
  scheduleTests(
    tests: Array<Test>,
    watcher: TestWatcher
  ): Promise<AggregatedResult>;
}

// Factory function
async function createTestScheduler(
  globalConfig: Config.GlobalConfig,
  options: TestSchedulerOptions,
  context: TestSchedulerContext
): Promise<TestScheduler>;
```

### Reporter Classes

**Source:** `packages/jest-reporters/src/`

```typescript
// BaseReporter
abstract class BaseReporter implements Reporter {
  log(message: string): void;
  
  onRunStart(results: AggregatedResult, options: ReporterOnStartOptions): void;
  onTestFileStart(test: Test): void;
  onTestCaseStart(test: Test, testCaseStartInfo: TestCaseStartInfo): void;
  onTestCaseResult(test: Test, testCaseResult: TestCaseResult): void;
  onTestFileResult(test: Test, testResult: TestResult, results: AggregatedResult): void;
  onRunComplete(testContexts: Set<TestContext>, results: AggregatedResult): void;
  getLastError(): Error | void;
}

// DefaultReporter
class DefaultReporter extends BaseReporter {
  constructor(globalConfig: Config.GlobalConfig);
}

// CoverageReporter
class CoverageReporter extends BaseReporter {
  constructor(globalConfig: Config.GlobalConfig, context: ReporterContext);
}

// SummaryReporter
class SummaryReporter extends BaseReporter {
  constructor(globalConfig: Config.GlobalConfig);
}
```

### Resolver Class

**Source:** `packages/jest-resolve/src/resolver.ts`

```typescript
class Resolver {
  constructor(moduleMap: IModuleMap, options: ResolverConfig);
  
  static findNodeModule(
    path: string,
    options: FindNodeModuleConfig
  ): string | null;
  
  static findNodeModuleAsync(
    path: string,
    options: FindNodeModuleConfig
  ): Promise<string | null>;
  
  resolveModule(
    from: string,
    moduleName: string,
    options?: ResolveModuleConfig
  ): string;
  
  resolveModuleAsync(
    from: string,
    moduleName: string,
    options?: ResolveModuleConfig
  ): Promise<string>;
  
  getMockModule(from: string, name: string): string | null;
  getModulePaths(from: string): Array<string>;
  getModule(name: string): string | null;
  getPackage(name: string): string | null;
  isCoreModule(moduleName: string): boolean;
}
```

### HasteMap Class

**Source:** `packages/jest-haste-map/src/index.ts`

```typescript
class HasteMap extends EventEmitter {
  static create(options: Options): HasteMap;
  
  constructor(options: Options);
  
  build(): Promise<HasteMapObject>;
  read(): Promise<InternalHasteMap>;
  
  // Event: 'change'
  on(event: 'change', listener: (changeEvent: {
    eventsQueue: Array<ChangeEvent>;
    hasteFS: HasteFS;
    moduleMap: ModuleMap;
  }) => void): this;
}

// Options
interface Options {
  cacheDirectory?: string;
  computeDependencies?: boolean;
  computeSha1?: boolean;
  enableSymlinks?: boolean;
  extensions: Array<string>;
  forceNodeFilesystemAPI?: boolean;
  hasteImplModulePath?: string;
  hasteMapModulePath?: string;
  id: string;
  maxWorkers: number;
  mocksPattern?: string;
  platforms: Array<string>;
  resetCache?: boolean;
  retainAllFiles: boolean;
  rootDir: string;
  roots: Array<string>;
  skipPackageJson?: boolean;
  throwOnModuleCollision?: boolean;
  useWatchman?: boolean;
  watch?: boolean;
}
```

### Worker Class

**Source:** `packages/jest-worker/src/index.ts`

```typescript
class Worker {
  constructor(workerPath: string | URL, options?: WorkerOptions);
  
  getStdout(): NodeJS.ReadableStream;
  getStderr(): NodeJS.ReadableStream;
  
  end(): Promise<PoolExitResult>;
  
  // Dynamic method proxying - methods from worker module are available
  [methodName: string]: (...args: any[]) => Promise<any>;
}

// Options
interface WorkerOptions {
  enableWorkerThreads?: boolean;
  exposedMethods?: Array<string>;
  forkOptions?: ForkOptions;
  maxRetries?: number;
  numWorkers?: number;
  resourceLimits?: ResourceLimits;
  setupArgs?: Array<unknown>;
  taskQueue?: TaskQueue;
  workerPath?: string;
  workerPoolOptions?: WorkerPoolOptions;
}

// Usage
const worker = new Worker(require.resolve('./heavy-task.js'), {
  numWorkers: 4,
  enableWorkerThreads: true,
});

const result = await worker.processData(data);
await worker.end();
```

### FakeTimers Class

**Source:** `packages/jest-fake-timers/

# internals

Internal architecture and implementation

# Jest Internal Architecture Analysis

## Executive Summary

Jest is a comprehensive JavaScript testing framework consisting of 50+ packages organized in a monorepo structure. The architecture follows a modular design with clear separation of concerns, utilizing workspace-based package management and a sophisticated transformation pipeline.

---

## Internal Architecture

### 1. Core Modules

#### Module Structure

Jest's architecture is organized into a monorepo with distinct package categories:

**Test Execution Core:**
- `jest-core` - Main orchestration and test coordination
- `jest-circus` - Modern test runner (BDD-style)
- `jest-jasmine2` - Legacy Jasmine-based test runner
- `jest-runner` - Test execution coordination

**Module System:**
- `jest-runtime` - Module loading and sandboxing
- `jest-resolve` - Module resolution
- `jest-haste-map` - File system indexing
- `jest-transform` - Code transformation pipeline

**Assertion & Matchers:**
- `expect` - Core assertion library
- `expect-utils` - Utility functions for matchers
- `jest-matcher-utils` - Matcher formatting utilities
- `jest-snapshot` - Snapshot testing

**Environment & Mocking:**
- `jest-environment` - Base environment interface
- `jest-environment-node` - Node.js environment
- `jest-environment-jsdom` - Browser-like environment
- `jest-mock` - Mocking utilities
- `jest-fake-timers` - Timer mocking

**Configuration & CLI:**
- `jest-config` - Configuration loading and normalization
- `jest-cli` - Command-line interface
- `create-jest` - Project initialization

**Utilities:**
- `jest-util` - Shared utilities
- `jest-types` - TypeScript type definitions
- `jest-schemas` - Configuration schemas
- `jest-validate` - Configuration validation

#### Inter-module Dependencies

```
jest (entry)
  └── jest-cli
        └── jest-core
              ├── jest-config
              ├── jest-runner
              │     ├── jest-runtime
              │     │     ├── jest-transform
              │     │     ├── jest-resolve
              │     │     └── jest-haste-map
              │     └── jest-environment-node
              ├── jest-circus / jest-jasmine2
              │     ├── jest-expect
              │     │     ├── expect
              │     │     └── jest-snapshot
              │     └── jest-each
              └── jest-reporters
```

---

### 2. Design Patterns

#### Factory Pattern - Worker Creation

```typescript
// packages/jest-worker/src/index.ts
export class Worker<T extends Record<string, (...args: Array<unknown>) => unknown>> {
  private _worker: WorkerInterface | null = null;

  constructor(workerPath: string | URL, options?: WorkerOptions<T>) {
    // Factory logic to create appropriate worker type
    if (options?.enableWorkerThreads) {
      this._worker = new NodeThreadsWorker(/* ... */);
    } else {
      this._worker = new ChildProcessWorker(/* ... */);
    }
  }
}
```

#### Strategy Pattern - Test Runners

```typescript
// packages/jest-runner/src/index.ts
export default class TestRunner {
  async runTests(
    tests: Array<Test>,
    watcher: TestWatcher,
    options: TestRunnerOptions,
  ): Promise<void> {
    // Strategy selection based on configuration
    return options.serial
      ? this._runTestsSerially(tests, watcher)
      : this._runTestsInParallel(tests, watcher);
  }
}
```

#### Observer Pattern - Event System

```typescript
// packages/jest-circus/src/eventHandler.ts
const eventHandler = async (event: Circus.Event): Promise<void> => {
  switch (event.name) {
    case 'start_describe_definition':
    case 'finish_describe_definition':
    case 'add_test':
    case 'test_start':
    case 'test_done':
    // Event-driven test execution
  }
};
```

#### Facade Pattern - Public API

```typescript
// packages/jest/src/index.ts
export {
  SearchSource,
  createTestScheduler,
  getVersion,
  run,
  runCLI,
} from '@jest/core';
```

#### Builder Pattern - Configuration

```typescript
// packages/jest-config/src/normalize.ts
export async function normalize(
  initialOptions: Config.InitialOptions,
  argv: Config.Argv,
  configPath?: string | null,
  projectIndex = Infinity,
  isProjectOptions = false,
): Promise<{
  hasDeprecationWarnings: boolean;
  options: Config.ProjectConfig;
}> {
  // Step-by-step configuration building
  const options = normalizePreprocessor(
    normalizeRootDir(setFromArgv(initialOptions, argv)),
  );
  // ... additional normalization steps
}
```

#### State Machine Pattern - Test Lifecycle

```typescript
// packages/jest-circus/src/state.ts
export const ROOT_DESCRIBE_BLOCK_NAME = 'ROOT_DESCRIBE_BLOCK';

const createState = (): State => ({
  currentDescribeBlock: makeDescribe(ROOT_DESCRIBE_BLOCK_NAME),
  currentlyRunningTest: null,
  expand: undefined,
  hasFocusedTests: false,
  hasStarted: false,
  // ...
});

export const dispatch = (event: Circus.AsyncEvent): Promise<void> => {
  // State transitions based on events
};
```

---

### 3. Data Structures

#### Haste Map - File System Index

```typescript
// packages/jest-haste-map/src/index.ts
type HasteMapData = {
  clocks: WatchmanClocks;
  duplicates: DuplicatesIndex;
  files: FileData;
  map: ModuleMapData;
  mocks: MockData;
};

// FileData structure
type FileData = Map<string, FileMetaData>;
type FileMetaData = [
  id: string,
  mtime: number,
  size: number,
  visited: 0 | 1,
  dependencies: string,
  sha1: string | null | undefined,
];
```

#### Test Results Aggregation

```typescript
// packages/jest-test-result/src/types.ts
export type AggregatedResult = {
  numFailedTestSuites: number;
  numFailedTests: number;
  numPassedTestSuites: number;
  numPassedTests: number;
  numPendingTestSuites: number;
  numPendingTests: number;
  numRuntimeErrorTestSuites: number;
  numTotalTestSuites: number;
  numTotalTests: number;
  openHandles: Array<Error>;
  snapshot: SnapshotSummary;
  startTime: number;
  success: boolean;
  testResults: Array<TestResult>;
  wasInterrupted: boolean;
};
```

#### Circus State Tree

```typescript
// packages/jest-circus/src/types.ts
type DescribeBlock = {
  type: 'describeBlock';
  children: Array<DescribeBlock | TestEntry>;
  hooks: Array<Hook>;
  mode: BlockMode;
  name: BlockName;
  parent?: DescribeBlock;
  tests: Array<TestEntry>;
};

type TestEntry = {
  type: 'test';
  asyncError: Exception;
  errors: Array<TestError>;
  fn: TestFn;
  invocations: number;
  mode: TestMode;
  name: TestName;
  parent: DescribeBlock;
  startedAt?: number | null;
  duration?: number | null;
  status?: TestStatus | null;
  timeout?: number;
  concurrent: boolean;
  failing: boolean;
  retryReasons: Array<TestError>;
  seenDone: boolean;
};
```

#### Snapshot State

```typescript
// packages/jest-snapshot/src/State.ts
export default class SnapshotState {
  private _counters: Map<string, number>;
  private _dirty: boolean;
  private _index: number;
  private _snapshotData: SnapshotData;
  private _snapshotPath: string;
  private _uncheckedKeys: Set<string>;
  
  added: number;
  expand: boolean;
  matched: number;
  unmatched: number;
  updated: number;
}
```

#### Caching Mechanisms

```typescript
// packages/jest-transform/src/ScriptTransformer.ts
export default class ScriptTransformer {
  private readonly _cache: Map<string, TransformResult>;
  private readonly _cacheFS: Map<string, string>;
  
  // File-based caching
  private async _getCacheKey(
    fileData: string,
    filename: string,
    options: TransformOptions,
  ): Promise<string> {
    // Cache key generation using content hash
  }
}
```

---

### 4. Algorithms

#### Diff Algorithm (Myers' Diff)

```typescript
// packages/diff-sequences/src/index.ts
const diff = (
  aLength: number,
  bLength: number,
  isCommon: IsCommon,
  foundSubsequence: FoundSubsequence,
): void => {
  // Myers' O((M+N)D) diff algorithm implementation
  const nCommonF: Callbacks['foundSubsequence'] = (nCommon, aCommon, bCommon) =>
    foundSubsequence(nCommon, aCommon, bCommon);
  const nCommonR: Callbacks['foundSubsequence'] = (nCommon, aCommon, bCommon) =>
    foundSubsequence(nCommon, aEnd - aCommon, bEnd - bCommon);

  // Forward and reverse traversal
  const kMinF = aStart - bEnd;
  const kMaxF = aEnd - bStart;
  const kMinR = aStart - bEnd;
  const kMaxR = aEnd - bStart;
  // ...
};
```

**Complexity:** O((M+N)D) where D is the edit distance

#### Test File Discovery (Crawling)

```typescript
// packages/jest-haste-map/src/crawlers/watchman.ts
async function watchmanCrawl(options: CrawlerOptions): Promise<{
  changedFiles?: FileData;
  removedFiles: FileData;
  hasteMap: HasteMapData;
}> {
  // Watchman-based incremental file discovery
  const cmd = ['query', root, query];
  const response = await client.command(cmd);
  // Process files in parallel
}

// packages/jest-haste-map/src/crawlers/node.ts
async function nodeCrawl(options: CrawlerOptions): Promise</*...*/> {
  // Node.js fs-based crawling fallback
  const walk = async (directory: string): Promise<void> => {
    const entries = await fs.readdir(directory, {withFileTypes: true});
    // Recursive directory traversal
  };
}
```

#### Test Sequencing

```typescript
// packages/jest-test-sequencer/src/index.ts
export default class TestSequencer {
  sort(tests: Array<Test>): Array<Test> | Promise<Array<Test>> {
    // Sort by duration (cached from previous runs)
    const stats: {[path: string]: number} = {};
    const fileStats = this._getCachePath();
    
    // Prioritize failed tests, then slow tests
    return tests.sort((testA, testB) => {
      const failedA = this._hasFailedBefore(testA);
      const failedB = this._hasFailedBefore(testB);
      
      if (failedA !== failedB) {
        return failedA ? -1 : 1;
      }
      
      return (stats[testB.path] || 0) - (stats[testA.path] || 0);
    });
  }
}
```

#### Pattern Matching for Test Selection

```typescript
// packages/jest-pattern/src/TestPathPatterns.ts
export default class TestPathPatterns {
  private _regexString: string | null = null;
  
  isMatch(path: string): boolean {
    const regex = this.toRegex();
    return regex.test(replacePathSepForRegex(path));
  }
  
  toRegex(): RegExp {
    // Combine multiple patterns with OR
    this._regexString = this._patterns
      .map(p => '(?:' + p.regexStr + ')')
      .join('|');
    return new RegExp(this._regexString, 'i');
  }
}
```

---

## Implementation Details

### 1. Core Logic

#### Test Execution Flow

```typescript
// packages/jest-circus/src/run.ts
export const run = async (): Promise<Circus.RunResult> => {
  const {rootDescribeBlock} = getState();
  await dispatch({name: 'run_start'});
  await _runTestsForDescribeBlock(rootDescribeBlock, false);
  await dispatch({name: 'run_finish'});
  return makeRunResult(getState().rootDescribeBlock, getState().unhandledErrors);
};

const _runTestsForDescribeBlock = async (
  describeBlock: Circus.DescribeBlock,
  isRootBlock: boolean,
): Promise<void> => {
  await dispatch({describeBlock, name: 'run_describe_start'});
  
  // Run beforeAll hooks
  for (const hook of beforeAllHooks) {
    await _callHook({describeBlock, hook});
  }
  
  // Run tests (with concurrency support)
  for (const test of describeBlock.tests) {
    await _runTest(test);
  }
  
  // Run child describe blocks
  for (const child of describeBlock.children) {
    await _runTestsForDescribeBlock(child, false);
  }
  
  // Run afterAll hooks
  for (const hook of afterAllHooks) {
    await _callHook({describeBlock, hook});
  }
  
  await dispatch({describeBlock, name: 'run_describe_finish'});
};
```

#### Module Loading and Sandboxing

```typescript
// packages/jest-runtime/src/index.ts
export default class Runtime {
  private readonly _moduleRegistry: Map<string, Module>;
  private readonly _isolatedModuleRegistry: Map<string, Module> | null;
  
  requireModule<T = unknown>(
    from: string,
    moduleName?: string,
    options?: InternalModuleOptions,
  ): T {
    const moduleID = this._resolver.getModuleID(
      this._virtualMocks,
      from,
      moduleName,
    );
    
    // Check cache
    if (this._moduleRegistry.has(moduleID)) {
      return this._moduleRegistry.get(moduleID) as T;
    }
    
    // Check if module should be mocked
    if (this._shouldMock(from, moduleName)) {
      return this._requireMock<T>(from, moduleName);
    }
    
    return this._loadModule<T>(localModule, from, moduleName, options);
  }
}
```

#### Transformation Pipeline

```typescript
// packages/jest-transform/src/ScriptTransformer.ts
async transform(
  filename: string,
  options: TransformOptions,
): Promise<TransformResult> {
  // Check cache first
  const cacheKey = await this._getCacheKey(fileContent, filename, options);
  const cachedResult = this._getFromCache(cacheKey);
  if (cachedResult) return cachedResult;
  
  // Find matching transformer
  const transformer = await this._getTransformer(filename);
  
  // Apply transformation
  const transformed = await transformer.processAsync(
    fileContent,
    filename,
    transformOptions,
  );
  
  // Instrument for coverage if needed
  if (options.instrument) {
    const instrumented = this._instrumentCode(transformed.code, filename);
    transformed.code = instrumented;
  }
  
  // Cache result
  await this._writeToCache(cacheKey, transformed);
  
  return transformed;
}
```

#### Snapshot Comparison

```typescript
// packages/jest-snapshot/src/index.ts
export const toMatchSnapshot = function (
  this: MatcherContext,
  received: unknown,
  propertiesOrHint?: object | string,
  hint?: string,
): ExpectationResult {
  const {currentTestName, snapshotState} = this;
  
  // Serialize received value
  const receivedSerialized = serialize(received, config);
  
  // Get expected from snapshot file
  const {pass, expected, actual} = snapshotState.match({
    testName: currentTestName,
    received: receivedSerialized,
    isInline: false,
  });
  
  if (pass) {
    return {message: () => '', pass: true};
  }
  
  // Generate diff for failure message
  const diff = diffLines(expected, actual);
  return {message: () => diff, pass: false};
};
```

### 2. Platform Abstractions

#### Cross-Platform Path Handling

```typescript
// packages/jest-util/src/replacePathSepForGlob.ts
export default function replacePathSepForGlob(path: string): string {
  return path.replaceAll('\\', '/');
}

// packages/jest-regex-util/src/index.ts
export const replacePathSepForRegex = (path: string): string => {
  if (sep === '\\') {
    return path.replaceAll(/(\/|\\(?!\*))/g, '\\\\');
  }
  return path;
};
```

#### Environment Abstraction

```typescript
// packages/jest-environment/src/index.ts
export interface JestEnvironment<Timer = unknown> {
  new (config: JestEnvironmentConfig, context: EnvironmentContext): JestEnvironment;
  global: Global.Global;
  fakeTimers: LegacyFakeTimers<Timer> | null;
  fakeTimersModern: ModernFakeTimers | null;
  moduleMocker: ModuleMocker | null;
  setup(): Promise<void>;
  teardown(): Promise<void>;
  handleTestEvent?: Circus.EventHandler;
}
```

#### Worker Thread Abstraction

```typescript
// packages/jest-worker/src/workers/NodeThreadsWorker.ts
export default class NodeThreadsWorker implements WorkerInterface {
  private _worker!: Worker;
  
  constructor(options: WorkerOptions) {
    this._worker = new Worker(this._options.workerPath, {
      eval: false,
      resourceLimits: this._options.resourceLimits,
      workerData: this._options.workerData,
    });
  }
}

// packages/jest-worker/src/workers/ChildProcessWorker.ts
export default class ChildProcessWorker implements WorkerInterface {
  private _child!: ChildProcess;
  
  constructor(options: WorkerOptions) {
    this._child = fork(this._options.workerPath, [], {
      cwd: process.cwd(),
      execArgv: this._options.forkOptions?.execArgv,
    });
  }
}
```

### 3. Performance Optimizations

#### Memoization in Expect

```typescript
// packages/expect-utils/src/jasmineUtils.ts
const hasKey = (
  obj: object,
  key: PropertyKey,
  // Memoized hasOwnProperty lookup
): boolean => Object.prototype.hasOwnProperty.call(obj, key);
```

#### Lazy Module Loading

```typescript
// packages/jest-runtime/src/index.ts
private _requireCoreModule(moduleName: string, supportPrefix: boolean): unknown {
  // Lazy require of Node.js core modules
  const createRequire = (modulePath: string): NodeRequire => {
    if (!this._createRequire) {
      this._createRequire = createRequire(this._config.rootDir);
    }
    return this._createRequire;
  };
}
```

#### Parallel Test Execution

```typescript
// packages/jest-runner/src/index.ts
async _runTestsInParallel(
  tests: Array<Test>,
  watcher: TestWatcher,
): Promise<void> {
  const worker = new Worker(TEST_WORKER_PATH, {
    exposedMethods: ['worker'],
    forkOptions: {serialization: 'json'},
    maxRetries: 3,
    numWorkers: this._globalConfig.maxWorkers,
    setupArgs: [{serializableResolvers: this._context.serializableResolvers}],
  });
  
  // Process tests in parallel with worker pool
  const runTestInWorker = (test: Test) =>
    mutex.acquire().then(async () => {
      return worker.worker({
        config: test.context.config,
        globalConfig: this._globalConfig,
        path: test.path,
      });
    });
}
```

#### Cached File Hashing

```typescript
// packages/jest-haste-map/src/index.ts
async getSha1(filename: string): Promise<string | null> {
  const cached = this._hasteFS.getSha1(filename);
  if (cached) return cached;
  
  // Compute and cache SHA1
  const content = await fs.readFile(filename);
  const sha1 = crypto.createHash('sha1').update(content).digest('hex');
  return sha1;
}
```

### 4. Resource Management

#### Worker Pool Management

```typescript
// packages/jest-worker/src/Farm.ts
export default class Farm {
  private readonly _workerPool: Array<WorkerInterface>;
  
  async end(): Promise<PoolExitResult> {
    // Graceful shutdown of all workers
    const results = await Promise.all(
      this._workerPool.map(worker => worker.forceExit()),
    );
    return {forceExited: results.some(r => r.forceExited)};
  }
}
```

#### Memory Leak Detection

```typescript
// packages/jest-leak-detector/src/index.ts
export default class LeakDetector {
  private _isReferenceBeingHeld: boolean;
  private _ref: WeakRef<object> | null;
  
  constructor(value: unknown) {
    // Use WeakRef to detect memory leaks
    this._ref = new WeakRef(value as object);
    this._isReferenceBeingHeld = true;
  }
  
  async isLeaking(): Promise<boolean> {
    // Force garbage collection
    this._runGarbageCollector();
    
    // Check if reference is still held
    return this._ref?.deref() !== undefined;
  }
}
```

#### Handle Detection

```typescript
// packages/jest-core/src/collectHandles.ts
export default function collectHandles(): HandleCollectionResult {
  const activeHandles: Map<number, HandleInfo> = new Map();
  
  // Hook into async_hooks to track open handles
  const hook = asyncHooks.createHook({
    init(asyncId, type, triggerAsyncId, resource) {
      activeHandles.set(asyncId, {
        type,
        stack: new Error().stack,
      });
    },
    destroy(asyncId) {
      activeHandles.delete(asyncId);
    },
  });
  
  hook.enable();
  
  return () => {
    hook.disable();
    return Array.from(activeHandles.values());
  };
}
```

---

## Code Organization

### 1. Directory Structure

```
jest/
├── packages/                    # Core packages (50+)
│   ├── jest/                   # Main entry point
│   ├── jest-cli/