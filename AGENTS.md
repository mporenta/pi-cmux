# AGENTS.md

## Repo overview

This repository contains `pi-cmux`, a small Pi package that adds cmux-powered terminal workflows to Pi.

Current extensions (wired up in `extensions/index.ts`):
- `extensions/cmux-notify.ts` — sends `cmux notify` alerts when Pi finishes, waits for input, or ends in an error/abort state
- `extensions/cmux-sidebar.ts` — updates cmux status, progress, logs, and surface flash while Pi runs
- `extensions/cmux-split.ts` — adds split commands that open a new cmux pane and start a fresh Pi session in the same working directory
- `extensions/cmux-zoxide.ts` — adds zoxide-based split commands that jump to a matched directory and start Pi there
- `extensions/cmux-review.ts` — starts a focused review session in a split
- `extensions/cmux-continue.ts` — opens a handoff/continuation session in a split, optionally in a new git worktree
- `extensions/cmux-open.ts` — split/tab commands that run a shell command, plus the agent-callable `cmux_open_terminal` tool
- `extensions/cmux-core.ts`, `extensions/git-core.ts` — shared cmux/git helpers used by the above

Other important files:
- `README.md` — user-facing package documentation
- `CHANGELOG.md` — unreleased and released changes
- `install.mjs` — installer/removal entrypoint used by `npx pi-cmux`
- `package.json` — package metadata for npm and Pi
- `skills/`, `prompts/` — development-reference only. They are intentionally **not** listed in `package.json` `files` or the `pi` manifest, so they are never published to npm or loaded by Pi (see the "Bundled resources" note in `README.md`). Do not add them to `files`/`pi` unless the package deliberately starts owning those command names.

## How the repo works

- This is a TypeScript-based Pi package. Pi loads the extensions directly via its jiti loader, so there is no build step; `npm run typecheck` (`tsc --noEmit`) is used for validation only.
- Extensions are loaded from `./extensions` via the `pi.extensions` entry in `package.json`.
- The package is published to npm and installed in Pi via `pi install npm:pi-cmux` or `npx pi-cmux`.

## Editing guidelines

- Keep README examples and behavior descriptions aligned with the extension behavior.
- Update `CHANGELOG.md` for user-visible changes.
- Prefer small, focused edits.
- Preserve the existing style: concise docs, simple utilities, minimal dependencies.

## Release / push checklist

Before pushing changes:
- bump the npm version
- update `CHANGELOG.md` if behavior changed
- make sure `README.md` matches the current behavior
- review the git diff for accidental changes

## Notes for future agents

- Validate changes with `npm ci && npm run typecheck && npm run pack:check` — the same commands CI runs on Node 22 (`.github/workflows/ci.yml`). `typescript` and `typebox` are devDependencies used only for typechecking; `typebox` is also declared as a `"*"` peer dependency because Pi provides it at runtime.
- If you change publishable package metadata or release behavior, check `package.json`, `README.md`, and `CHANGELOG.md` together.
