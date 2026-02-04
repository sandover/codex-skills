You are updating this repo to add a small suite of *actionable* local git hooks that improve correctness when working with lower‑trust coding agents.

Goals
- Hooks must be **surgical and stack-aware**: run the repo’s existing fast format/test/lint/CI-parity commands (don’t invent new tooling).
- Hooks must be **actionable**: every message includes a concrete next command or file path.
- Prefer **gates that actually shift behavior**: block commits/pushes when checks fail; warn for “likely drift” situations.
- Keep it lightweight: don’t require installing external hook managers.

What to add
1) `prepare-commit-msg`
- Purely informational.
- Inject a short commented checklist into the commit message *only for editor-driven commits* (avoid polluting `-m`, `--no-edit`, merges/squashes).
- Insert at most once.

2) `pre-commit`
- Format only staged source files (language-appropriate) and re-stage them.
- Fail the commit if staged content contains git conflict markers: `<<<<<<<`, `=======`, `>>>>>>>` (show the offending file + line numbers).
- Fail the commit if a newly-added staged file is suspiciously large or appears binary unless explicitly allowlisted (pick a reasonable default threshold like 1–5 MiB; make the allowlist a small, obvious edit in the hook script).
- Run **quick** checks (e.g. unit tests) only when relevant files are staged.
- Warn (do not fail) when staged changes likely require updating canonical docs/manuals (choose the repo’s equivalents; list exact files and verification commands).
- Warn (do not fail) when important planning/task logs changed but aren’t staged (only if such a file exists in this repo).

3) `pre-push`
- Strong gate: run the repo’s CI-parity command (or closest equivalent) and block push on failure.
- Print a short “repo invariants” reminder.

Where to put them
- Put runnable hooks in `.git/hooks/` that delegate to tracked scripts.
- Put the tracked scripts in `docs/suggested-hooks/` (make them executable).
- Put the reminder banner text in `docs/suggested-hooks/banner.txt` (or equivalent), and have hooks read from it.

Docs
- Add a brief mention in README (or existing contributing/dev doc) pointing to `docs/suggested-hooks/`.
- Do not inline full hook code in README.

Constraints
- Do not enforce Conventional Commits; checklist can *recommend* them.
- Do not add install scripts.
- Use only tools that already exist in the repo (Taskfile/Makefile/package scripts/cargo/etc). If multiple exist, pick the most canonical and explain why in comments.
- Update tests/lint/docs as needed; ensure the repo is clean and checks pass.

Deliverables
- New/updated files under `docs/suggested-hooks/` and `.git/hooks/`.
- Minimal README/docs reference.
- Run the repo’s standard verification commands to confirm clean status.
