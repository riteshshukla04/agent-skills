# Agent Skills

A collection of agent skills for building React Native libraries — designed for use with Claude Code, Cursor, and other AI coding assistants.

## What are Agent Skills?

Agent skills are structured markdown guides that teach AI assistants how to perform complex, multi-step engineering tasks. Each skill contains:

- A `SKILL.md` — the entry point with an overview, quick reference, and problem→solution mapping
- A `references/` folder — detailed step-by-step guides for each sub-task

AI assistants load the `SKILL.md` first, then drill into specific reference files as needed.

## Skills

| Skill | Description |
|-------|-------------|
| [build-nitro-modules](skills/build-nitro-modules/SKILL.md) | Build React Native Nitro Modules end-to-end in a monorepo — from Nitrogen scaffold to native implementation (Swift/Kotlin/C++) to example app and npm publishing |

---

## Installation

### Claude Code (Plugin Marketplace)

The fastest way — install directly from the Claude Code plugin marketplace:

```bash
# 1. Add the marketplace source
/plugin marketplace add riteshshukla04/agent-skills

# 2. Install the skill
/plugin install build-nitro-modules@agent-skills
```

The skill is now active in your Claude Code session.

**Alternative — manual clone:**

```bash
# Clone into your project root
git clone https://github.com/riteshshukla04/agent-skills.git .agent-skills

# Or as a git submodule
git submodule add https://github.com/riteshshukla04/agent-skills.git .agent-skills
```

Then reference in your `CLAUDE.md`:

```markdown
## Skills

When building a Nitro Module, follow:
@.agent-skills/skills/build-nitro-modules/SKILL.md
```

---

### Cursor

**Option 1 — Remote rule (recommended):**

In Cursor, go to **Settings → Rules** and add a new rule pointing to this repo. When working on a Nitro Module, type `/` in the Agent chat to search and activate the skill.

**Option 2 — Local clone:**

```bash
# Project-level (affects only this project)
git clone https://github.com/riteshshukla04/agent-skills.git .cursor/skills/agent-skills

# User-level (available in all projects)
git clone https://github.com/riteshshukla04/agent-skills.git ~/.cursor/skills/agent-skills
```

Then reference in `.cursorrules`:

```
When building a React Native Nitro Module, read and follow:
skills/build-nitro-modules/SKILL.md
```

---

### Other AI Assistants (Copilot, ChatGPT, Gemini, etc.)

Point your assistant directly to the skill file:

```
Read skills/build-nitro-modules/SKILL.md and help me build a new Nitro Module.
```

Or paste the contents of `SKILL.md` directly into the chat context. The skill is designed to be self-contained.

---

## Using the `build-nitro-modules` Skill

This skill guides an AI agent through all 21 steps of building a React Native Nitro Module:

1. **Scaffold** — monorepo setup, `npx nitrogen@latest init <name>`
2. **Spec** — write `*.nitro.ts` HybridObject interface, delete the default stub
3. **Configure** — set up `nitro.json` autolinking (cxxNamespace, iosModuleName, etc.)
4. **Codegen** — run `npx nitrogen`, verify `nitrogen/generated/` output
5. **Implement** — fill in native implementations:
   - Swift (`ios/HybridMath.swift`)
   - Kotlin (`android/.../HybridMath.kt`)
   - C++ (`cpp/HybridMath.cpp`) — optional cross-platform
6. **Export** — expose the HybridObject via `NitroModules.createHybridObject<T>('Key')`
7. **Example app** — create with `npx @react-native-community/cli@latest init --skip-install`
8. **Wire Android** — fix `settings.gradle` and `build.gradle` paths for monorepo
9. **Wire Metro** — add `watchFolders`, install library with `bun add ../packages/<name>`
10. **Test** — `bun example android` / `bun example ios`
11. **Publish** — update `files`, author, and metadata in `package.json`

### Quick start prompt

Give your AI assistant this prompt to kick off a session:

```
Read skills/build-nitro-modules/SKILL.md and help me build a new Nitro Module.
```

---

## What are Nitro Modules?

[Nitro Modules](https://github.com/mrousavy/nitro) is a framework for building high-performance React Native native modules using a TypeScript-first codegen pipeline. You write a `*.nitro.ts` spec, run `nitrogen` to generate C++/Swift/Kotlin boilerplate, then fill in your implementation.

- Documentation: https://nitro.margelo.com
- GitHub: https://github.com/mrousavy/nitro

---

## Inspiration
This is inspired from [callstackincubator/agent-skills](https://github.com/callstackincubator/agent-skills):


Each reference file includes:
- **Quick Command/Pattern/Config** — copy-paste ready
- **When to Use** — trigger conditions
- **Step-by-Step** — numbered, actionable instructions
- **Code Examples** — with type tables verified against canonical Nitro examples
- **Common Pitfalls** — the mistakes agents (and humans) make most
- **Related Skills** — cross-links to adjacent reference files

## Contributing

To add a new skill:

1. Create `skills/<skill-name>/SKILL.md` with YAML frontmatter (`name`, `description`, `license`, `metadata`)
2. Add reference files under `skills/<skill-name>/references/` using the format above
3. Update this README's Skills table

See `skills/build-nitro-modules/` as the reference implementation.

## License

MIT
