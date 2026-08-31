---
name: dependency-updater
description: Safely update a project's dependencies to their latest compatible versions, working with npm, yarn, pnpm, or bun on any OS (Linux, macOS, Windows). Auto-detects the package manager, monorepo layout, and available verification scripts (typecheck/lint/test/build) — never assumes a specific tool. Use this whenever the user asks to "update dependencies", "upgrade packages", "bump versions to latest", "update to latest compatible versions", or similar, no matter which package manager the project uses. Also use it if the user pastes a dependency-update checklist/prompt and wants it executed.
---

# Dependency Updater

Update all dependencies in a project to their latest compatible versions, verify nothing broke, and report clearly — without assuming any particular package manager or operating system.

## Core principle: detect, don't assume

Never hardcode `npm`, `pnpm`, `yarn`, or `bun` in your plan before checking. Every command below is written as a **template per package manager** — resolve which one applies first, then use that column consistently for the whole task. See `references/package-managers.md` for the full command matrix (install, update, audit, lockfile regen, run-script syntax per manager).

## Workflow

### Step 1: Detect the package manager

Check, in this priority order, and stop at the first match:

1. `packageManager` field in `package.json` (e.g. `"packageManager": "pnpm@9.1.0"`) — this is the authoritative source if present.
2. Lockfile present in the project root:
   - `bun.lockb` or `bun.lock` → bun
   - `pnpm-lock.yaml` → pnpm
   - `yarn.lock` → yarn
   - `package-lock.json` or `npm-shrinkwrap.json` → npm
3. If none found, check for the binary available on the system (`command -v pnpm`, `command -v yarn`, `command -v bun`) and ask the user which to use, defaulting to npm if nothing else is installed.

Also detect the **OS** (`uname` on Linux/macOS, otherwise assume Windows) only insofar as it changes shell syntax (e.g. `&&` chaining, path separators, env var syntax). Prefer running each command as a separate tool call rather than chaining with shell-specific operators, so the sequence works identically regardless of OS/shell.

### Step 2: Detect the project layout

- Look for monorepo markers: `turbo.json`, `pnpm-workspace.yaml`, `nx.json`, `lerna.json`, or a `workspaces` field in the root `package.json`.
- If it's a monorepo, updates must run at the root (so the workspace-aware update command touches every package), and verification scripts should run per-workspace or via the monorepo's own task runner (e.g. `turbo run build`) if one exists — check `turbo.json` / root `package.json` scripts for this before falling back to per-package commands.
- If it's a single package, operate directly on the root `package.json`.

### Step 3: Inspect before changing anything

- Read `package.json` (all workspaces if monorepo) to record current `dependencies`, `devDependencies`, `peerDependencies`, and any `engines` / Node version constraint.
- Read the lockfile to confirm currently-resolved versions (don't just trust the semver ranges in package.json).
- Note any pinned/exact versions (no `^`/`~`) — these were likely pinned deliberately (e.g. due to a known bug); flag them for extra care rather than blindly bumping.

### Step 4: Compute the update plan

Use the package manager's own "outdated" command (see reference table) to list current vs. latest versions. Categorize each package as:
- **Patch/minor** — safe to bump automatically.
- **Major** — flag explicitly; check the package's changelog/release notes for breaking changes before applying, especially for framework-level packages (the frontend framework, backend framework, database driver, auth library, build tool). Do not swap out the framework or core architecture regardless of what a "latest" suggests.
- **Deprecated on registry** — only replace if it's actually deprecated (check the registry deprecation notice), and prefer the maintainer-recommended replacement; don't swap packages speculatively.

### Step 5: Apply updates

Use the detected package manager's update command from the reference table. For major-version bumps, install explicitly (`pkg@latest` equivalent) rather than relying on the generic "update" command, since most PMs' plain `update` respects the existing semver range and won't cross major versions.

### Step 6: Resolve peer-dependency conflicts

If the install step reports peer dependency conflicts:
- Identify which package's peer range is unmet.
- Prefer updating the dependent package to a version whose peer range includes the new dependency, over forcing/ignoring the conflict.
- Only use force/legacy flags as a last resort, and call this out explicitly in the final report if you had to.

### Step 7: Regenerate the lockfile

Run the manager's install command (no-args) after all version bumps so the lockfile is regenerated/synced. Confirm the lockfile diff only reflects intended version changes.

### Step 8: Verify — don't stop at package.json

Check the root (and each relevant workspace) `package.json` `scripts` block for whichever of these exist, and run only the ones that are actually defined (script names vary — look for typecheck/type-check/tsc, lint, test, build, or their workspace-runner equivalents):

1. Typecheck
2. Lint
3. Test suite
4. Production build

Run them via the detected package manager's run-script syntax (see reference table). If a script doesn't exist, skip it — don't invent one.

### Step 9: Fix breakages caused by the update

If any verification step fails:
- Diagnose whether the failure is caused by an API/behavior change in an updated dependency (read the error, check the package's changelog for the relevant version range).
- Fix the calling code to match the new API, or pin that one package back to the last working version if a proper fix isn't feasible in scope — note this explicitly in the report rather than silently reverting.
- Re-run the failed verification step after each fix until it passes or you've exhausted reasonable options.
- Do not change application behavior beyond what's needed for compatibility with the new versions.

### Step 10: Report

Always end with a structured report, regardless of package manager/OS:

- **Packages updated** — name, old version → new version
- **Major-version updates** — called out separately, with a one-line note on what changed
- **Breaking changes encountered** — and how each was resolved
- **Files changed** — package.json(s), lockfile, plus any source files touched to fix breakage
- **Deprecated packages found** — and whether/why they were replaced
- **Build status** — pass/fail
- **Test status** — pass/fail (with counts if available)
- **Typecheck status** — pass/fail
- **Lint status** — pass/fail
- **Remaining warnings/errors** — anything left unresolved and why

## Guardrails (apply regardless of package manager)

- Don't downgrade a package unless it's required to resolve an unavoidable peer conflict — and say so explicitly if you do.
- Don't swap the framework, database, auth library, or other core architecture pieces — only update their versions.
- Don't introduce new packages that weren't already present unless replacing a genuinely deprecated one, or a peer conflict truly requires a new peer package.
- Don't change application behavior/config beyond what compatibility with the new versions requires.
- If uncertain whether a major bump is safe (e.g. no changelog found, ambiguous breaking-change scope), flag it in the report rather than guessing silently.

## Reference

See `references/package-managers.md` for the exact command to use, per package manager, for: listing outdated packages, updating within range, forcing latest/major, installing, regenerating the lockfile, running scripts, and handling peer-dependency flags.
