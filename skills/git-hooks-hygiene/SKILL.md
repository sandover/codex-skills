---
name: git-hooks-hygiene
description: Create actionable local git hooks to improve repo hygiene and correctness when using lower-trust coding agents. Use for adding or updating prepare-commit-msg, pre-commit, and pre-push hooks that run the repo’s existing format/lint/test/CI-parity commands, keep docs/manuals in sync, and store tracked hook copies under docs/suggested-hooks with a brief README mention.
---

# Git Hooks Hygiene

Create a small, homologous suite of git hooks that measurably shifts agent behavior toward repo invariants.

## Guardrails

- Do not invent new tooling or add new format/lint/test tasks; use what the repo already has.
- Do not add hook managers (e.g. pre-commit framework, husky) unless the repo already uses them.
- Do not inline full hook code in README; only add a short pointer to `docs/suggested-hooks/`.

## Workflow

Keep `pre-commit` fast and local; put CI-parity checks in `pre-push`.

1) Identify the repo’s **canonical** commands
- Formatting: e.g. `task fmt`, `make fmt`, `cargo fmt`, `npm run fmt`
- Lint: e.g. `task lint`, `cargo clippy`, `npm run lint`
- Tests: e.g. `task test`, `go test ./...`, `cargo test`, `npm test`
- CI parity: e.g. `task ci`, `make ci`, `just ci`

Pick the most canonical command that already exists. If multiple exist, prefer the one the repo already treats as CI parity (often `ci`). Do not create new tasks/targets to satisfy this.

2) Implement three hooks
- **`prepare-commit-msg`** (informational): inject a short commented checklist into the commit message buffer **only for editor-driven commits**. Never pollute `-m`/`--no-edit`/merge/squash messages. Insert at most once.
- **`pre-commit`** (light gate): format staged files + restage; run only quick checks conditionally; print warnings with specific next actions (files + commands). Add two universal hard guards:
  - Fail if staged content contains git conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
  - Fail if a newly-added staged file is suspiciously large or looks binary, unless explicitly allowlisted.
- **`pre-push`** (strong gate): run CI-parity command and block push on failure.

3) Store hooks twice
- Tracked copies in `docs/suggested-hooks/`
- Delegating scripts in `.git/hooks/` that exec the tracked copies

The `.git/hooks/*` scripts must be tiny delegators; the tracked scripts are the source of truth.

4) Make every message actionable
- For every warning: include explicit file paths and exact commands to run next.
- Always warn on doc/manual drift risk when staged changes are likely user-facing but canonical manuals/docs are not staged.

5) Verify
- Run the repo’s CI-parity command.
- Ensure `git status` is clean.

## Prompt template
Use `references/prompt.md` as the single prompt to run in other repos.
