---
title: Configuring nitro.json for Autolinking
impact: CRITICAL
tags: nitro-json, autolinking, cxxNamespace, iosModuleName, androidNamespace, androidCxxLibName, configuration
---

# Skill: Configuring nitro.json for Autolinking

Covers Step 5: updating `nitro.json` with all required fields for Nitrogen codegen and autolinking.

## Quick Config

```json
{
  "$schema": "https://nitro.margelo.com/nitro.schema.json",
  "cxxNamespace": ["math"],
  "ios": {
    "iosModuleName": "ReactNativeMath"
  },
  "android": {
    "androidNamespace": ["math"],
    "androidCxxLibName": "ReactNativeMath"
  },
  "autolinking": {
    "Math": {
      "swift": "HybridMath",
      "kotlin": "HybridMath"
    }
  }
}
```

## When to Use

- After writing the `*.nitro.ts` spec, before running `npx nitrogen`
- When adding new HybridObjects to an existing module
- When autolinking fails at runtime

## Prerequisites

- `nitro.json` exists in the package root (created by `nitrogen init`)
- `*.nitro.ts` spec file written with interface name known

## Step-by-Step

### 1. Set `cxxNamespace`

Defines the C++ namespace for all generated code. Use a lowercase array:

```json
"cxxNamespace": ["math"]
// generates: namespace margelo::nitro::math { ... }

"cxxNamespace": ["math", "extra"]
// generates: namespace margelo::nitro::math::extra { ... }
```

### 2. Set iOS module name

Must match the CocoaPod's podspec name exactly:

```json
"ios": {
  "iosModuleName": "ReactNativeMath"
}
```

Check `ios/ReactNativeMath.podspec` — the `s.name` field must match.

### 3. Set Android configuration

```json
"android": {
  "androidNamespace": ["math"],
  "androidCxxLibName": "ReactNativeMath"
}
```

- `androidNamespace`: Kotlin package suffix appended to `com.margelo.nitro`
  - `["math"]` → package `com.margelo.nitro.math`
- `androidCxxLibName`: Name of the native C++ library loaded via JNI
  - Must match the library name in `android/CMakeLists.txt`: `add_library(ReactNativeMath SHARED ...)`

### 4. Configure `autolinking`

This is how Nitro knows which native class handles each HybridObject:

```json
"autolinking": {
  "Math": {
    "swift": "HybridMath",
    "kotlin": "HybridMath"
  }
}
```

Rules:
- The key (`"Math"`) must **exactly match** the string passed to `NitroModules.createHybridObject<Math>('Math')`
- `"swift"` / `"kotlin"` values are the native class names that implement the spec
- For C++ (cross-platform): use `"cpp": "HybridMath"` instead
- The native classes must be **default-constructible** (no required constructor arguments)

### 5. Multiple HybridObjects

```json
"autolinking": {
  "Math": {
    "swift": "HybridMath",
    "kotlin": "HybridMath"
  },
  "Crypto": {
    "swift": "HybridCrypto",
    "kotlin": "HybridCrypto"
  }
}
```

### 6. Optional fields

```json
{
  "ignorePaths": ["node_modules", "example"],
  "gitAttributesGeneratedFlag": true
}
```

- `ignorePaths`: Directories Nitrogen skips when scanning for `.nitro.ts` files
- `gitAttributesGeneratedFlag`: Marks generated files as `linguist-generated` on GitHub

## Code Examples

### C++ only (cross-platform) config

```json
{
  "$schema": "https://nitro.margelo.com/nitro.schema.json",
  "cxxNamespace": ["math"],
  "ios": {
    "iosModuleName": "ReactNativeMath"
  },
  "android": {
    "androidNamespace": ["math"],
    "androidCxxLibName": "ReactNativeMath"
  },
  "autolinking": {
    "Math": {
      "cpp": "HybridMath"
    }
  }
}
```

### Full config with all options

```json
{
  "$schema": "https://nitro.margelo.com/nitro.schema.json",
  "cxxNamespace": ["math"],
  "ios": {
    "iosModuleName": "ReactNativeMath"
  },
  "android": {
    "androidNamespace": ["math"],
    "androidCxxLibName": "ReactNativeMath"
  },
  "autolinking": {
    "Math": {
      "swift": "HybridMath",
      "kotlin": "HybridMath"
    }
  },
  "ignorePaths": ["node_modules", "example"],
  "gitAttributesGeneratedFlag": true
}
```

## Common Pitfalls

- **Autolinking key mismatch** — `"Math"` in `nitro.json` must match `createHybridObject('Math')` exactly (case-sensitive)
- **`androidCxxLibName` mismatch** — Must match `add_library(<name> SHARED ...)` in `CMakeLists.txt`
- **`iosModuleName` mismatch** — Must match `s.name` in the `.podspec` file
- **Missing `$schema`** — Without the schema reference, some validators won't catch errors
- **Non-default-constructible class in autolinking** — Native classes registered via autolinking must have a no-argument constructor

## Related Skills

- [spec-hybrid-object.md](spec-hybrid-object.md) — Write the spec before configuring nitro.json
- [native-nitrogen-codegen.md](native-nitrogen-codegen.md) — Run nitrogen after configuring nitro.json
