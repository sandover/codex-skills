---
name: ergo-feature-planning
description: >-
  Plan software features using the ergo CLI, storing plans in-repo as epics and
  right-sized tasks with explicit dependencies. Use when asked to create or
  revise an implementation plan (especially multi-step work) that should become
  `ergo` tasks, including task/epic creation and sequencing,
  acceptance criteria, automated validation gates, up-front escalation of
  judgment calls before tasks are created, and a second-pass staff-level critique
  of the full plan before execution.
---

# Ergo Feature Planning

Use this workflow to turn a feature request into an in-repo plan tracked by `ergo` (epics + tasks + dependency graph), with tasks sized ~one commit each and validation gates the agent can run.

## 0) Bootstrap (always)

1) Get oriented:
   - If you’re rusty, consult the ergo CLI help and quickstart.
2) Confirm you are operating in the correct repo root and the repo is initialized for ergo:
   - If the repo is not initialized for ergo yet, initialize it before planning.
3) Discover project-specific planning constraints:
   - Read any `AGENTS.md` / CONTRIBUTING docs in the repo.
   - Note required CI hygiene gates (format/lint/test) and any doc-update expectations.

## 1) Escalate judgment calls as they emerge (required)

Judgment calls often *emerge while writing the plan itself* (as you attempt to break work into tasks and realize ambiguities, tradeoffs, or missing constraints). Don’t force all questions to be answered before you write anything; instead:

1) Start drafting the plan (epics + tasks) to expose unknowns.
2) The moment a blocking judgment call appears, pause and ask the user.
3) Record the call centrally as “Decisions / Open Questions / Assumptions” and then update the affected tasks once resolved.

When you do ask, prefer 1–4 targeted questions that unblock planning, e.g.:
- “What’s the desired public API / UX?”
- “What invariants must hold (compat, durability, performance, security)?”
- “Any migration/backward-compat constraints?”
- “What’s the definition of done, and what tests prove it?”

If the user wants you to choose, capture it as “Decisions / Open Questions” (not per-task), propose a default with rationale, and wait for confirmation before continuing to write *dependent* tasks.

Recommended structure (capture inside `ergo` itself, typically in the epic body, so the plan lives in `ergo` unless the user explicitly asks for a separate planning doc):
- **Decisions**: choices already made (with rationale)
- **Open Questions**: unresolved items that block planning
- **Assumptions**: defaults you will plan against if not answered

Keep planning moving:
- It is fine to create epics and non-dependent tasks while a question is open.
- Avoid writing (or sequence-block) tasks whose shape depends on unresolved decisions; prefer a small “spike” task to resolve unknowns early.

## 2) Create the plan as epics + tasks

### 2a) Create an epic (when work is multi-area)

- Create 1 epic per coherent feature area (avoid “mega-epics”):
  - Use ergo to create an epic with a clear title and an overview (scope, non-goals, constraints, decisions/open-questions/assumptions).

### 2b) Create tasks (the unit of execution)

Task sizing guidelines:
- A task should be “about one commit” worth of work (not trivial, not sprawling).
- Each task must be independently verifiable via explicit acceptance criteria and automated gates.
- Prefer fewer, stronger tasks over many tiny ones; split only on real boundaries (API surface, data model, migration, tests, docs).

Spike tasks:
- Spikes are welcome: they produce knowledge/decisions/analysis rather than working code.
- Consider prefixing spike task titles with `spike:` (for example, `spike: choose durability UX`).
- Default spike output: write conclusions directly into the task body and mark done.
- Only create standalone docs/results files when the user explicitly asks for them or the repo needs durable documentation.

Create tasks in ergo with clear titles and multi-line bodies (use whatever ergo-supported input method is most ergonomic in your environment).

Use this task body template as a starting point (edit as needed; keep the headings when it helps consistency):

```md
## Goal
- <1–3 bullets describing the concrete outcome>

## Background / Rationale
- <Why this work exists; link to docs/specs/issues if present>

## Acceptance Criteria
- <Observable behavior; include edge cases and failure modes>

## Validation Gates
- <Exact commands the agent will run to prove it works, e.g. tests/lint/format>
- <Include any “no diff after fmt” constraints>

## Git Commit (optional; preferred for code changes)
- <If this task changes repo code/docs, prefer committing the result as part of “done” unless the user says not to>
- <Prefer 1 logical commit for this task; if multiple commits are needed, say so explicitly>
- <Use the repo’s commit conventions (often Conventional Commits: `type(scope): summary`) and include why/how in the body when helpful>
- <Prefer including `.ergo/events.jsonl` updates in the SAME commit as the related work (avoid standalone “ergo-only” commits unless needed)>

## Test Ideas (optional)
- <Extra tests to add/run beyond the required gates (unit/integration/property/fuzz/bench)>

## Human Verification (only if needed)
- <Exact manual review/verification steps; keep minimal and explain why automation isn’t sufficient>
```

If tasks have artifacts (specs, docs, screenshots, benchmarks), note them in acceptance criteria and attach them as results when marking done. If a task produces no file artifact (common for spikes), capture outcomes in the task body.

## 3) Encode dependencies (sequence)

Use ergo’s dependency/sequencing features to express real ordering constraints and unblock parallelism:
- Add edges only when there is a true ordering constraint.
- Prefer enabling parallel work by avoiding unnecessary edges.

After sequencing, sanity-check which work is “ready” per ergo.

## 4) Second-pass critique (required)

After the first planning pass is complete (all tasks + epics exist and are sequenced), do a staff-level review of the *entire* plan:

Checklist:
- Coverage: Are API, tests, docs, migrations, observability, and edge cases accounted for?
- Correctness: Are invariants/constraints explicitly stated somewhere actionable?
- Sizing: Any task too small (fold in) or too large (split at a real seam)?
- Dependencies: Any missing edges that will cause churn? Any unnecessary edges blocking parallelism?
- Validation: Do all tasks have runnable gates? Are gates strong enough to catch regressions?
- Risk: Identify the 1–3 highest-risk tasks; add mitigation tasks or earlier spikes if needed.
- Consult-me: Ensure judgment calls are surfaced *now*, not mid-implementation.

Then improve the plan:
- Update epic/task bodies in ergo as needed.
- Add/remove tasks (prefer adding a “spike” task for unknowns rather than embedding guesswork).
- Adjust dependency edges using ergo’s sequencing/dependency editing features.

Prefer not to start implementation until the plan passes this critique.

## 5) Execution loop (after planning)

1) Claim ready work:
   - If your workflow uses claiming/assignment, claim or assign a ready task in ergo.
2) Implement the task.
3) Run the task’s validation gates.
3b) If the task includes a Git Commit expectation, commit the change(s) before marking done.
   - Prefer including `.ergo/events.jsonl` updates in the same commit as the code/doc changes.
4) Attach results (docs/specs/notes) and mark state:
   - Use ergo to mark state (`done`, `blocked`, `error`, `canceled`) and attach result summaries/paths when applicable.
5) Never leave tasks in `doing`; always set `done`, `blocked`, `error`, or `canceled`.

Maintenance:
- Prefer committing `.ergo/events.jsonl` alongside the related work commit, rather than creating “ergo-only” commits.
- Avoid ergo history/compaction maintenance unless a human explicitly asks for it.
