# Phase 2 — Task Loop

You are executing **one** task from the plan. End-to-end: implement, test, typecheck, commit, mark `[x]`. After this task, return to the user — do not auto-chain to the next.

## Inputs

- The next `[ ]` task (from Phase 1's context summary).
- The full plan + PRD + designs (already loaded in Phase 1).

## What to do

### Step 1: Re-read the task

Pull the line from the plan. Confirm you have:

- Sub-ID (e.g. FR-001.1)
- One-line description
- File list — every file the task touches
- AC reference — what AC(s) it satisfies
- Test reference — which test it adds or modifies

If any of these is missing or vague, STOP. The plan needs fixing in `generate-dev-plan` first.

### Step 2: Write the failing test first (when applicable)

For tasks that add or modify behavior, write the test before the implementation. Run the test command to confirm it fails for the right reason. (If the task is purely structural — e.g. a schema migration with a generated test — skip the red-bar step.)

This is the framework's red-green principle. Adapted from the spirit of TDD; if a TDD/Superpowers skill is installed, defer to its loop.

### Step 3: Implement

Write the code. Stay inside the file list named in the task. If you find yourself wanting to touch a file the task didn't list:

- If it's a one-line obvious change (import, type), do it and note in the commit body.
- If it's larger, STOP and tell the user — the plan was wrong, fix the plan first.

### Step 4: Verify locally

Run, in order:

1. The test command — `{{TEST_CMD}}`. The new/modified test MUST pass. No other tests MAY regress.
2. The typecheck — `{{TYPECHECK_CMD}}`. Zero errors.
3. Lint, if `CLAUDE.md` listed a lint command.

If anything fails:

- **Once or twice:** fix and retry.
- **Three times in a row:** STOP. Three-strike rule. Ask the user — there's something Phase 1 missed.

### Step 5: Stage & commit (one task = one commit)

Stage only the files the task named (plus any obvious one-liners noted in Step 3). Use Conventional Commits:

```
<type>(<scope>): <imperative summary ≤72 chars>

Refs FR-001.1
```

- `<type>` is usually `feat` or `fix` for plan tasks, occasionally `refactor` or `test`.
- `<scope>` matches the module touched: `auth`, `db`, `ui/login`, etc.
- The body MUST reference the sub-ID.
- Breaking changes get `!` after the type/scope.

In the **same commit**, edit `docs/plans/<FR-N>.md` to mark this task `[x]`. The plan and code MUST land together.

If a pre-commit hook fails, fix the cause. Never `--no-verify` without explicit user approval.

### Step 6: Verify the commit

After the commit:

```bash
git log -1 --stat   # confirm files + plan checkbox both committed
git status          # confirm clean working tree
```

The plan file should show the checkbox flipped in the diff. If it doesn't, you made the commit before editing the plan — STOP and amend (the user will approve since this is a one-line fix).

### Step 7: Hand back to user

```markdown
## ✅ FR-001.1 complete

**Commit:** `<hash>` — `feat(db): add lastLoginAt to User schema`
**Tests:** all green
**Typecheck:** clean
**Plan:** `[x] FR-001.1` marked

### Remaining tasks
- [ ] FR-001.2 — Login handler reads/writes lastLoginAt
- [ ] FR-001.3 — Inline error component
- [ ] FR-001.4 — LoginForm wires inline error

### Next?
- Continue → FR-001.2
- Pause / review / abort
```

**Wait for the user's call before starting the next task.** Do not auto-chain.

If all tasks are done, route to Phase 3 (finalize).

## Anti-patterns (forbidden)

- ❌ Marking `[x]` without a passing test.
- ❌ Two `[x]` in one commit.
- ❌ Auto-starting the next task. Always return control to the user between tasks.
- ❌ Bypassing pre-commit hooks (`--no-verify`) without explicit approval.
- ❌ Touching files outside the task's list (beyond trivial one-liners).
- ❌ Silently fixing unrelated bugs you noticed mid-task. Note them, surface to user; don't blend them in.
- ❌ Looping > 3 times on the same failure. Three-strike rule applies.
