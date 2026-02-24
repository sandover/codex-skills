---
name: ergo-feature-planning
description: >-
  Plan software tasks with `ergo` (local task dependency graph + epics) when the work spans 3+ commits or involves multiple concerns. Run "ergo --help" to learn it.
---

# Ergo Feature Planning

Turn a feature request into a repo-local plan tracked by `ergo`: a backlog of well-scoped tasks, with dependency edges, grouped into epics.

## When to use

**Use ergo when** the work would span 3+ commits, touches multiple concerns (API + UI + tests + docs), or has ambiguity that needs resolving before implementation.

**Skip ergo when** the change is straightforward enough to just implement — a bug fix, a single-concern feature, routine refactoring. Don't plan what you can just do.

**Unsure?** Ask: "Want an ergo plan first, or should I just implement?"

## Bootstrap

1. Expect `ergo` to be globally installed. If missing, ask the user to install it.
2. Run `ergo --help` and `ergo quickstart` to learn the CLI before creating plans.

## Planning

Detailed planning naturally surfaces unknowns, ambiguities, and decisions. When these arise, interview the user thoroughly. Present options clearly with tradeoffs. Record decisions in the relevant epic body or task(s). If a task's shape depends on an unresolved decision, make it a spike instead.

Critique continuously as you build the plan — when writing one task reveals that earlier tasks should be merged or split, fix it immediately rather than deferring to a review pass.

### Epics

One per coherent feature area. Body includes scope, non-goals, constraints, and if it makes sense, key decisions and assumptions. Tasks are grouped into epics, but dependencies can cross epic boundaries when needed.

Tasks that don't fit a larger feature area can be left ungrouped. Use judgment.

### Tasks

The unit of execution. Each task should be:
- **One atomic, reviewable change** — completable in a single session. Not trivial, not sprawling.
- **Ideally, automatically verifiable** via acceptance criteria and runnable gates. When human verification is truly needed, include exact instructions so implementing agents know precisely how to verify.
- **Split on real boundaries** only (API surface, data model, migration, tests, docs).
- **Friendly to smaller models** — the implementing model might not be as smart as you. Before finalizing a task, consider it from the perspective of a less capable model without the context of the full user conversation or the whole backlog. Will they succeed?

**Spikes** produce knowledge, not code. Prefix with `spike:`. Dependent tasks should note what they're waiting to learn from the spike.

Task body template (trim to fit):

```md
## Goal
- <1–3 bullets: concrete outcome and why it matters>

## Context
- <Background, links to docs/designs — only when not obvious from the goal>

## Acceptance Criteria
- <Observable behavior, edge cases, definition of done>

## Validation Gates
- <Exact commands to prove it works — tests, lint, format>
```

### Dependencies

Add edges only for true ordering constraints — maximize parallelism and task independence.

## Plan review

Before presenting to the user, do a final confirmation pass:

- **Coverage** — API, tests, docs, migrations, edge cases all accounted for?
- **Sizing** — anything too small (fold in) or too large (split)?
- **Dependencies** — missing edges that'll cause churn? unnecessary edges blocking parallelism?
- **Validation** — every task has runnable gates?
- **Risk** — 1–3 highest-risk tasks identified; spikes or mitigation added?
- **Open calls** — all judgment calls surfaced now, not mid-implementation?
- **Cruft** — will the planned work leave behind unowned debt? Consider cleanup tasks.

Fix what you find, then **present an executive summary to the user for approval**. Concise overview of epics, key tasks, and major decisions, expressed in high-level language, minimizing jargon. Get buy-in before investing time in implementation.

## Executing ergo plans

1. Claim a ready task.
2. Implement it. Stop and consult the user if important questions arise.
3. Commit using repo conventions. **Do not** include `.ergo/` files in per-task commits.
4. Mark task done. On completion:
   - **Completion note** — update the task body with a brief note on what was done: decisions made, approach taken, anything non-obvious. Think PR description, not essay.
   - **Result link** — if the task produced a concrete deliverable (a new file, component, doc), attach it with `result_path`. **Do not** create standalone result files just to have a link.
   - **After completing a spike** — update dependent tasks with what was learned so the knowledge flows forward.
   - If a task can't be completed, mark it blocked or error — never leave tasks in progress.
5. **If the plan needs to change** — a task is unnecessary, scope shifted, new work emerged — update the plan and note why. Plans are living documents, not contracts.
6. When an epic is done, commit the `.ergo/` state with a message like `plan: complete <epic name>`. Otherwise, go to 1.
