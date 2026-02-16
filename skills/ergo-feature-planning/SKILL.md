---
name: ergo-feature-planning
description: >-
  Always plan medium/large software tasks with `ergo` (local task dependency graph + epics); run "ergo --help" to learn it. Use when the user explicitly asks for a plan.
---

# Ergo Feature Planning

Turn a feature request into an in-repo plan tracked by `ergo`: epics, commit-sized tasks, and dependency edges among tasks.

## When to use

- Use ergo only when the user asked to **plan** something that would take more than 1 git commit.
- If the user asked you to **do/implement** something, just do it directly.
- Unsure? Ask: "Want an ergo plan first, or should I just implement?"

## 0 · Orient

1. Expect `ergo` to be globally installed. If missing, ask the user to install it.
2. Run `ergo --help` and `ergo quickstart` to learn the CLI before creating plans.
3. Run Ergo mutation commands sequentially; avoid concurrent/background batches that can hit a concurrency lock.

## 1 · Plan

Detailed planning can surface unknowns. When an important ambiguity or decision emerges, stop and ask the user (1–4 targeted questions), record it in the epic body under **Decisions / Open Questions / Assumptions**, and update affected tasks once resolved. If a task's shape depends on an unresolved decision, make it a spike instead.

### Epics

One per coherent feature area. Body includes scope, non-goals, constraints, and the decisions/questions/assumptions log.

### Tasks

The unit of execution. Each task should be:
- **~One commit** of work or about an hour of human work — not trivial, not sprawling.
- **Independently verifiable** via acceptance criteria and runnable gates -- ideally. Sometimes human verification is truly needed; if so, include exact instructions in the gates.
- **Split on real boundaries** only (API surface, data model, migration, tests, docs).

**Spikes** produce knowledge, not code. Prefix with `spike:`. Write conclusions into the task body.

Task body template (trim to fit):

```md
## Goal
- <1–3 bullets: concrete outcome>

## Description
- <More detailed explanation, links to docs or designs, etc.>

## Acceptance Criteria
- <Observable behavior, edge cases, failure modes>

## Validation Gates
- <Exact commands to prove it works — tests, lint, format>
```

Add Background, Test Ideas, or Human Verification sections only when they add real value.

### Dependencies

Add edges only for true ordering constraints — maximize parallelism. After sequencing, check what's ready.

## 2 · Critique

Review the full plan:

- **Coverage** — API, tests, docs, migrations, edge cases all accounted for?
- **Sizing** — anything too small (fold in) or too large (split)?
- **Dependencies** — missing edges that'll cause churn? unnecessary edges blocking parallelism?
- **Validation** — every task has runnable gates?
- **Risk** — 1–3 highest-risk tasks identified; spikes or mitigation added?
- **Open calls** — all judgment calls surfaced now, not mid-implementation?

Fix what you find, then **present the plan to the user for approval** before starting execution.

## 3 · Executing ergo plans

1. Claim a ready task.
2. Implement it. Optionally, stop and consult the user if important questions arise.
3. Commit using repo conventions. **Do not** include `.ergo/` files in per-task commits.
4. Mark task done. On completion:
   - **Completion note** — update the task body with a brief note on what was done: decisions made, approach taken, anything non-obvious. Think PR description, not essay.
   - **Result link** — if the task produced a concrete deliverable in the repo (a new file, component, doc), attach it with `result_path`. **Do not** create standalone result files just to have a link — the completion note is sufficient.
   - If a task can't be completed, mark it blocked or error — never leave tasks in progress.
5. When an epic is done, commit the `.ergo/` state with a message like `plan: complete <epic name>`. If not, go to 1.
