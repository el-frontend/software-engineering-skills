# Phase 3 — Write Plan

You have a Phase 1 requirement bundle and Phase 2 investigation notes. Now write the plan file.

## Inputs

- Approved requirement bundle (Phase 1).
- Approved investigation notes (Phase 2).
- The template at [`templates/plan.md.tmpl`](../templates/plan.md.tmpl).

## What to do

### Step 1: Determine the file path

`docs/plans/<REQUIREMENT_ID>.md` — verbatim ID, no prefix, no version. Examples:

- `docs/plans/FR-001.md`
- `docs/plans/FR-AUTH-003.md`

If the file already exists, STOP. Show the user the existing file and ask: *replace / merge / abort*.

### Step 2: Fill the template

Copy [`templates/plan.md.tmpl`](../templates/plan.md.tmpl). Fill every placeholder:

- `{{REQUIREMENT_ID}}` and `{{REQUIREMENT_TITLE}}` — from Phase 1.
- `{{PRD_PATH}}` — relative path to the PRD file.
- `{{STATEMENT}}` — the requirement statement, verbatim.
- `{{ACCEPTANCE_CRITERIA}}` — bulleted list of AC-IDs.
- `{{DEPENDENCIES}}` — other FR-IDs that must ship first (or "None").
- `{{TASKS}}` — see Step 3.

### Step 3: Decompose into tasks

Use the **dependency graph from Phase 2** to order tasks. Each task is one line:

```markdown
- [ ] FR-001.1 — <what gets done> · files: `<path1>`, `<path2>` · AC: AC-001.1 · test: `<test name>`
```

**Hard task rules:**

1. **Vertical slices.** Each task ends `runnable + tested`. No "scaffold types" or "stub handler" tasks alone.
2. **Sub-IDs.** `FR-001.1`, `FR-001.2`, … in the order they should be tackled. If two are parallelizable, mark both with the same number suffixed `a/b` (`FR-001.3a`, `FR-001.3b`) and a note: *"can run in parallel"*.
3. **Files named.** Every task lists the files it adds or modifies. Vague tasks ("update auth module") are forbidden.
4. **AC reference.** Every task points to the AC-N(s) it satisfies. If a task doesn't satisfy an AC, it's probably plumbing — fold it into the next task.
5. **Test reference.** Every task either adds a new test (name it) or modifies an existing one (name the file).
6. **Sized for one focused session.** If a task feels >2 hours, split it. If you can't split, you under-investigated.

### Step 4: Add the meta sections

The template includes these — fill them:

- **Out of scope** — explicit list of things this plan does NOT cover. Pull from PRD's DO NOT list + anything Phase 2 surfaced as adjacent-but-deferred.
- **Reuse** — files / functions Phase 2 found should be reused, not reinvented.
- **Open risks** — known risks the implementer should keep in view (not blockers — those should have been resolved in Phase 2).

### Step 5: Final pass

Score against [`references/checklist.md`](../references/checklist.md). All 7 items MUST pass. If any fail, fix before handoff.

If you cannot make all 7 pass with the current investigation, return to Phase 2 — the gap is in your understanding, not the plan format.

### Step 6: Hand to user

Write `docs/plans/<FR-N>.md`. Then:

> *Plan ready at `docs/plans/<FR-N>.md` — execute now / refine `<task>` / abort*

Suggest the user run `skills/execute-plan/` next (Phase 5 of the framework) to drive implementation against this plan.

## Anti-patterns (forbidden)

- ❌ Bundling multiple requirements in one plan file.
- ❌ Plumbing-only tasks (`[ ] add types`, `[ ] add empty interface`) standing alone.
- ❌ Task lines longer than ~100 chars. If you need more space, the task is too big — split.
- ❌ Inventing tests that don't connect to an AC. Every test traces to AC-N.
- ❌ Writing the plan when Phase 2 had unresolved `⚠️ MISSING:` items.
- ❌ Generating a "Phase 1 / Phase 2 / Phase 3 of implementation" structure inside the plan — the plan is a flat task list. Internal phasing belongs in the PRD's own §17 (implementation phases).
