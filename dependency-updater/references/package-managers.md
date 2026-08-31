# Package manager command reference

Resolve the package manager once (Step 1 of SKILL.md), then use only the matching column below for the rest of the task.

| Action | npm | yarn (classic v1) | yarn (berry v2+) | pnpm | bun |
|---|---|---|---|---|---|
| Detect version | `npm -v` | `yarn -v` | `yarn -v` | `pnpm -v` | `bun -v` |
| Install (sync lockfile) | `npm install` | `yarn install` | `yarn install` | `pnpm install` | `bun install` |
| List outdated | `npm outdated` | `yarn outdated` | `yarn outdated` | `pnpm outdated` | `bunx npm-check-updates` (bun has no native outdated) |
| Update within semver range | `npm update` | `yarn upgrade` | `yarn up` | `pnpm update` | `bun update` |
| Force to latest incl. majors (single pkg) | `npm install pkg@latest` | `yarn add pkg@latest` | `yarn add pkg@latest` | `pnpm add pkg@latest` | `bun add pkg@latest` |
| Force to latest incl. majors (all, interactive) | `npx npm-check-updates -u && npm install` | `yarn upgrade-interactive --latest` | `yarn upgrade-interactive` | `pnpm up --latest` | `bunx npm-check-updates -u && bun install` |
| Regenerate lockfile only | `npm install --package-lock-only` | `yarn install` | `yarn install` | `pnpm install --lockfile-only` | `bun install` |
| Run a script | `npm run <script>` | `yarn <script>` | `yarn <script>` | `pnpm <script>` or `pnpm run <script>` | `bun run <script>` |
| Install ignoring peer conflicts (last resort) | `npm install --legacy-peer-deps` | `yarn install --ignore-engines` | n/a (strict by default) | `pnpm install --no-strict-peer-dependencies` | `bun install --no-verify` (rarely needed; bun is lenient by default) |
| Audit/check deprecated | `npm outdated` + registry deprecation warnings surface on install | `yarn outdated` | `yarn npm audit` | `pnpm outdated` | check registry manually — bun doesn't surface deprecation warnings well |
| Monorepo: update across all workspaces | `npm update --workspaces` | `yarn workspaces run <script>` (no built-in bulk upgrade; use `yarn upgrade-interactive` at root) | `yarn up -R '*'` | `pnpm update -r` | `bun update` (bun workspaces update together by default) |
| Lockfile filename | `package-lock.json` | `yarn.lock` | `yarn.lock` | `pnpm-lock.yaml` | `bun.lockb` / `bun.lock` |

## Notes

- **npm-check-updates (`ncu`)** is a useful cross-manager tool for *computing* what "latest" means (it just rewrites version ranges in package.json), but the actual install/lockfile step should still go through the detected package manager so the lockfile format matches.
- **Turborepo monorepos**: check the root `turbo.json` for a `build`/`lint`/`test`/`typecheck` pipeline task — if present, prefer `<pm> turbo run build` / `<pm> turbo run typecheck` etc. (or `turbo run build` directly if `turbo` is a global/root devDependency) over guessing per-package script names, since Turborepo will fan the task out to every workspace correctly and cache results.
- **Windows shells**: avoid `&&` chaining across PowerShell vs. bash differences — issue each command as its own step. All commands in the table above are shell-agnostic otherwise (no OS-specific flags).
- **Corepack**: if `packageManager` is set in `package.json` and `corepack` is available, prefer invoking the manager via `corepack <manager> ...` (or just ensure `corepack enable` has been run) so the exact pinned version is used rather than whatever happens to be globally installed.
