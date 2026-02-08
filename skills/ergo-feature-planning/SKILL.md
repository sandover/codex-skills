---
name: ergo-feature-planning
description: >-
  Plan multi-step features using the `ergo` CLI (epics, tasks, dependency
  graph). Use when the user explicitly asks for a plan — default to doing the work.
---

# Ergo Feature Planning

Turn a feature request into an in-repo plan tracked by `ergo`: epics, commit-sized tasks, dependency edges, and runnable validation gates.

## When to use

- If the user asked you to **do/implement** something, do it directly.
- Use ergo only when the user asked to **plan** or confirmed they want planning.
- Unsure? Ask: "Want an ergo plan first, or should I just implement?"

## 0 · Orient

1. `ergo` should be globally installed. If missing, ask the user to install it.
2. Run `ergo --help` and `ergo quickstart` to learn the CLI. Do this before creating anything.
3. Confirm you're at the repo root and ergo is initialized (init if not).

## 1 · Plan

Planning surfaces unknowns — that's the point. When a blocking question emerges, pause and ask the user (1–4 targeted questions), record it in the epic body under **Decisions / Open Questions / Assumptions**, and update affected tasks once resolved. Keep building non-dependent work while questions are open. If a task's shape depends on an unresolved decision, make it a spike instead.

### Epics

One per coherent feature area. Body includes scope, non-goals, constraints, and the decisions/questions/assumptions log.

### Tasks

The unit of execution. Each task should be:
- **~One commit** of work — not trivial, not sprawling.
- **Independently verifiable** via acceptance criteria and runnable gates.
- **Split on real boundaries** only (API surface, data model, migration, tests, docs).

**Spikes** produce knowledge, not code. Prefix with `spike:`. Write conclusions into the task body.

Task body template (trim to fit):

```md
## Goal
- <1–3 bullets: concrete outcome>

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

## 3 · Execute

1. Claim a ready task.
2. Implement it.
3. Run its validation gates.
4. Commit using repo conventions. Include ergo state changes in the same commit as the work.
5. Mark done with a result summary. If a task can't be completed, mark it blocked or error — never leave tasks in progress.
