# 🛠️ Engineering Skills for AI Coding Agents

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Skills Standard](https://img.shields.io/badge/Standard-SKILL.md-success.svg)](#skill-format--structure)
[![Built with skills.sh](https://img.shields.io/badge/Built%20with-skills.sh-6E56CF.svg)](https://skills.sh/rohant302/engineering-skills)
[![Supported PMs](https://img.shields.io/badge/Package%20Managers-npm%20%7C%20yarn%20%7C%20pnpm%20%7C%20bun-orange.svg)](#supported-package-managers)
[![Cross-Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey.svg)](#cross-platform--monorepo-support)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](#-contributing-new-skills)

A collection of battle-tested, deterministic, and safe **AI Agent Skills** designed for modern AI coding assistants (such as Google Antigravity, Claude Code, Cursor, and other agentic LLM workflows).

These skills give AI assistants clear guardrails, structured multi-step workflows, command references, and domain knowledge to execute complex engineering tasks autonomously and reliably without breaking codebases.

> Compatible with the open [SKILL.md standard](https://skills.sh) — any agent that reads `SKILL.md` files can load these directly.

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Available Skills](#-available-skills)
  - [1. `dependency-updater`](#1-dependency-updater)
- [Skill Format & Structure](#-skill-format--structure)
- [Installation & Setup](#-installation--setup)
  - [Google Antigravity](#google-antigravity)
  - [Claude Code](#claude-code)
  - [Cursor / Windsurf / Generic AI Agents](#cursor--windsurf--generic-ai-agents)
  - [skills.sh](#skillssh)
- [Design Principles](#-design-principles)
- [Contributing New Skills](#-contributing-new-skills)
- [License](#-license)

---

## 🌟 Overview

Large Language Models (LLMs) often make risky assumptions when performing engineering tasks guessing package managers, hallucinating upgrade commands, ignoring peer-dependency conflicts, or skipping build/test verifications.

This repository provides standardized, executable **Agent Skills** that:

- **Detect, Don't Assume**: Dynamically detect environments, lockfiles, monorepos, and scripts.
- **Enforce Verification**: Require automated testing, typechecking, linting, and build verification before concluding.
- **Provide Guardrails**: Strictly forbid breaking application architecture, unintended downgrades, or silent failures.
- **Deliver Structured Reports**: Ensure clean, consistent summaries of all actions taken and issues resolved.
- **Stay Portable**: Plain `SKILL.md` + `references/` — no proprietary format, works anywhere the standard is supported, including registries like [skills.sh](https://skills.sh).

---

## 📦 Available Skills

| Skill                                        | Description                                                                                                                                                   | Supported Ecosystems                  | Status   |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- | -------- |
| [`dependency-updater`](./dependency-updater) | Safely updates project dependencies to latest compatible versions with automated verification, peer-dependency resolution, and cross-package-manager support. | `npm`, `yarn` (v1/v2+), `pnpm`, `bun` | ✅ Ready |

Have a skill idea? See [Contributing New Skills](#-contributing-new-skills) below — the table above grows with every PR.

---

### 1. `dependency-updater`

> **Path**: [`dependency-updater/`](./dependency-updater)
> **Reference**: [`dependency-updater/references/package-managers.md`](./dependency-updater/references/package-managers.md)

A production-grade skill for upgrading JavaScript/TypeScript/Node.js dependencies without breaking builds or guessing tools.

#### 🔑 Key Features

- **Zero Assumptions**: Auto-detects package manager via `packageManager` in `package.json`, lockfiles (`bun.lock`, `pnpm-lock.yaml`, `yarn.lock`, `package-lock.json`), or system binaries.
- **Monorepo Native**: Recognizes Turborepo, Nx, pnpm workspaces, Lerna, and Yarn/npm workspaces — updating at the root level and running workspace-aware verification pipelines.
- **10-Step Safe Workflow**:
  1. **Detect Package Manager** (`npm`, `yarn`, `pnpm`, `bun`) & OS.
  2. **Detect Project Layout** (Single package vs. Monorepo).
  3. **Inspect Before Modifying** (Pinned versions, engine constraints, semver ranges).
  4. **Compute Update Plan** (Categorize into patch/minor vs. major vs. deprecated).
  5. **Apply Updates** (In-range updates or explicit `@latest` installs).
  6. **Resolve Peer Conflicts** (Fix dependent ranges; fallback flags only if strictly required).
  7. **Regenerate Lockfile** (Lockfile sync verification).
  8. **Full Verification** (Typecheck `tsc`, Lint, Test suite, Production build).
  9. **Auto-Fix Breakages** (Diagnose breaking changes, update calling code, or pin safely).
  10. **Structured Report** (Markdown report detailing updated packages, major changes, test results, and unresolved items).
- **Comprehensive Command Matrix**: Includes a detailed command reference for every package manager covering installs, outdated checks, interactive upgrades, script execution, and peer dependency flags.

---

## 🏗️ Skill Format & Structure

Each skill in this repository follows the standard Agent Skill directory layout:

```text
skills-repository/
├── LICENSE
├── README.md
└── <skill-name>/
    ├── SKILL.md                 # Required: YAML frontmatter + structured instructions
    ├── references/              # Optional: In-depth reference docs, tables, syntax guides
    │   └── package-managers.md
    ├── scripts/                 # Optional: Helper scripts and automation tools
    └── examples/                # Optional: Example inputs, outputs, or test fixtures
```

### `SKILL.md` Specification

Every `SKILL.md` begins with YAML frontmatter specifying:

- `name`: Unique identifier for the skill (e.g. `dependency-updater`).
- `description`: Detailed description indicating what the skill does and exact triggers/prompts when the AI should activate it.

The body that follows is plain Markdown — numbered workflow steps, explicit guardrails, and pointers to any `references/` files the agent should consult mid-task rather than loading everything up front.

---

## 🚀 Installation & Setup

You can load these skills into your preferred AI agent environment:

### Google Antigravity

**Option A: Workspace-Specific (Recommended for project repos)**
Copy the desired skill folder into your workspace's `.agents/skills/` directory:

```bash
# In your project root
mkdir -p .agents/skills
cp -r /path/to/engineering-skills/dependency-updater .agents/skills/
```

**Option B: Global User Skills**
Copy the skill into your global Antigravity configuration directory:

- **Windows**: `%USERPROFILE%\.gemini\config\skills\`
- **macOS / Linux**: `~/.gemini/config/skills/`

```bash
cp -r dependency-updater ~/.gemini/config/skills/
```

---

### Claude Code

Copy or symlink the skill directory to your project's `.claude/skills/` or personal configuration:

```bash
mkdir -p .claude/skills
cp -r /path/to/engineering-skills/dependency-updater .claude/skills/
```

---

### Cursor / Windsurf / Generic AI Agents

You can reference the skill in `.cursorrules`, `.windsurfrules`, or your agent's system prompt by pointing to the `SKILL.md`:

```markdown
When updating dependencies, follow the rules and workflow in:
@dependency-updater/SKILL.md and @dependency-updater/references/package-managers.md
```

---

### skills.sh

This repository follows the open `SKILL.md` standard used by [skills.sh](https://skills.sh), so skills here can be installed from the registry with its CLI:

Install any skill from this repository:

```bash
npx skills add rohant302/engineering-skills --skill <skill-name>
```

> Exact command syntax may evolve with the skills.sh CLI — check `npx skills --help` if a command above doesn't match your installed version.

---

## 🧭 Design Principles

Every skill in this repo is expected to follow the same house rules:

- **Detect, don't assume** — probe the environment (lockfiles, config files, binaries) before picking a tool or command.
- **Verify before declaring done** — run the project's own typecheck/lint/test/build, don't just assume the change worked.
- **Guardrails over cleverness** — explicitly list what the agent must _not_ do (swap frameworks, force-downgrade, silently drop errors) rather than relying on the model to infer restraint.
- **Progressive disclosure** — keep `SKILL.md` itself short and workflow-focused; push large command matrices, edge-case tables, and rarely-needed detail into `references/`.
- **Struc­tured, honest reporting** — every skill ends with a consistent report format, including what it _couldn't_ resolve, not just what succeeded.

---

## 🤝 Contributing New Skills

Contributions of new engineering skills are welcome! When creating a new skill:

1. **Create a Directory**: Create a descriptive folder name (e.g., `db-migration-verifier`, `dockerfile-optimizer`).
2. **Author `SKILL.md`**: Include YAML frontmatter (`name`, `description`) and a comprehensive step-by-step workflow.
3. **Include Guardrails**: Explicitly list actions the agent must _not_ take.
4. **Provide References**: Place cheat sheets, CLI command matrices, and error-handling matrices into a `references/` subdirectory.
5. **Update README.md**: Add your new skill to the [Available Skills](#-available-skills) table above.

---

## 📄 License

This repository is licensed under the [MIT License](LICENSE).
Copyright (c) 2026 Rohan T.
