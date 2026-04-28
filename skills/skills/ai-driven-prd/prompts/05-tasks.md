# Phase 5 — Task Decomposition

**Goal:** Convert the validated PRD into `tasks/tasks-prd-<feature>.md` — a checkbox tree with parent tasks, sub-tasks, and per-task test references.

**Gate:** Two human gates inside this phase: one after parent tasks, one after sub-tasks.

---

## Run this prompt verbatim

```
# Rule: Generate a task list from a PRD

INPUT: the PRD at {{path}}.

PROCESS (two-phase, with human gate):

Phase 1 — Parent tasks
- Analyze the PRD.
- Generate ~5 high-level parent tasks. ALWAYS include task 0.0
  "Create feature branch" as the first.
- Each parent task MUST end in a VISIBLE, TESTABLE outcome in the
  running product (no plumbing-only sequences).
- Order parents by dependency; mark independent ones with [P] for
  parallel execution.
- Present parent tasks ONLY (no sub-tasks yet).
- End with: "I have generated the high-level tasks. Ready to
  generate the sub-tasks? Respond with 'Go' to proceed."
- PAUSE.

Phase 2 — Sub-tasks (only after user says "Go")
- Break each parent into 3–7 sub-tasks.
- Each sub-task: ~1–4 hours of work, touches ≤2 files, ≤~400 LoC diff.
- If a sub-task needs >2 files or changes both schema AND UI, split it.
- For each sub-task, list the test file(s) that prove it done.
- Identify Relevant Files (source + tests) with one-line descriptions.

OUTPUT FORMAT — save as tasks/tasks-prd-<feature>.md:

## Relevant Files
- `path/to/file.ts` — <description>
- `path/to/file.test.ts` — unit tests

## Notes
- Test commands, conventions, feature flag name.

## Tasks
- [ ] 0.0 Create feature branch
  - [ ] 0.1 `git checkout -b feature/<slug>`
- [ ] 1.0 <Parent> — outcome: <visible, testable result>
  - [ ] 1.1 <sub-task> — test: `tests/.../x.spec.ts::"..."`
  - [ ] 1.2 ...
- [ ] 2.0 ...

Target reader: junior developer. Be explicit.
```
