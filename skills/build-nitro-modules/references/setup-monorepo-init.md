---
title: Monorepo Setup and Nitrogen Scaffold
impact: CRITICAL
tags: monorepo, nitrogen, init, workspace, bun, scaffold, package-json
---

# Skill: Monorepo Setup and Nitrogen Scaffold

Covers Steps 1–3: setting up the monorepo structure, getting the library name, and scaffolding with Nitrogen.

## Quick Commands

```bash
# Ask user for library name (e.g. react-native-math)
# Then scaffold:
npx nitrogen@latest init react-native-math

# After scaffold, install from root:
bun install
```

## When to Use

- Starting any new Nitro module from scratch
- Setting up the monorepo workspace before writing specs

## Prerequisites

- Node.js and Bun installed
- A root directory that will serve as the monorepo root

## Step-by-Step

### 1. Always prefer a monorepo setup

Nitro modules work best in a monorepo structure with:
- `packages/` — the native library
- `example/` — the example app for testing
- Root `package.json` managing workspaces

### 2. Ask the user for the library name

The library name should:
- Follow npm naming convention: `react-native-<domain>` (e.g. `react-native-math`, `react-native-camera`)
- Be lowercase, hyphen-separated
- Reflect the module's purpose clearly

### 3. Scaffold with Nitrogen

Run from the monorepo root:

```bash
npx nitrogen@latest init react-native-math
```

This creates `packages/react-native-math/` with the full library structure.

### 4. Verify the generated folder structure

```
packages/react-native-math/
├── android/
│   ├── src/main/java/com/margelo/nitro/<namespace>/
│   └── CMakeLists.txt
├── ios/
│   └── ReactNativeMath.podspec
├── src/
│   └── specs/
│       └── Example.nitro.ts    ← delete this
├── nitrogen/
│   └── generated/              ← populated after running nitrogen
├── nitro.json
└── package.json
```

### 5. Add to root workspace

In the monorepo root `package.json`:

```json
{
  "name": "react-native-math-root",
  "private": true,
  "workspaces": [
    "packages/*",
    "example"
  ],
  "scripts": {
    "specs": "bun --cwd packages/react-native-math run specs",
    "example": "bun --cwd example"
  }
}
```

### 6. Install from root

```bash
bun install
```

## Code Examples

### Root `package.json`

```json
{
  "name": "react-native-math-root",
  "private": true,
  "workspaces": [
    "packages/*",
    "example"
  ],
  "scripts": {
    "specs": "bun --cwd packages/react-native-math run specs",
    "example": "bun --cwd example"
  },
  "devDependencies": {
    "typescript": "^5.0.0"
  }
}
```

### Package `package.json` (generated, key fields)

```json
{
  "name": "react-native-math",
  "version": "0.1.0",
  "description": "A React Native Math module built with Nitro",
  "main": "lib/commonjs/index.js",
  "module": "lib/module/index.js",
  "react-native": "src/index.ts",
  "types": "lib/typescript/index.d.ts",
  "peerDependencies": {
    "react": "*",
    "react-native": "*",
    "react-native-nitro-modules": "*"
  }
}
```

## Common Pitfalls

- **Running `nitrogen init` from wrong directory** — Run from the monorepo root, not inside `packages/`
- **Forgetting to add package to workspaces** — Without this, `bun install` won't link the package
- **Name collisions** — Check npm before choosing a name (`npm info react-native-<name>`)
- **Not running `bun install` after scaffold** — Dependencies won't be linked until you do

## Related Skills

- [spec-hybrid-object.md](spec-hybrid-object.md) — Next step: write the TypeScript spec
- [spec-nitro-json.md](spec-nitro-json.md) — Configure autolinking before running nitrogen
