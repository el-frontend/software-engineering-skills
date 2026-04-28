# Phase 3 — Finalize

All tasks in the plan are `[x]`. Now you produce the PR description and surface anything the team should learn from this iteration. **You do not open the PR yourself unless the user explicitly tells you to.**

## Inputs

- Completed plan at `docs/plans/<FR-N>.md` (all `[x]`).
- The branch with all task commits.
- The PRD and original ACs.

## What to do

### Step 1: Verify completion

Score against [`references/checklist.md`](../references/checklist.md). All 8 items MUST pass:

1. Every plan task is `[x]`.
2. Every AC referenced in the plan has a passing test.
3. Working tree is clean.
4. Test, typecheck, and lint commands all pass on `HEAD`.
5. Each task has its own commit, conventional format, with `Refs FR-N.M` in the body.
6. No commits used `--no-verify` without explicit approval.
7. No files outside the plan's scope were modified (excluding trivial one-liners noted in commit bodies).
8. `docs/plans/<FR-N>.md` `## Done when` block: every box is `[x]` except the PR-opened one (that's the next step).

If any item fails, do NOT proceed. Report the gap and either return to Phase 2 (if a task is incomplete) or fix in place.

### Step 2: Generate the PR description

Use this template:

```markdown
## Summary

Implements FR-001 — Login flow.

- Adds `lastLoginAt` tracking on the User model
- Server-side login handler updates `lastLoginAt` and returns 401 with retry-after on rate limit
- Client form shows inline 401 errors and respects rate-limit message

## Acceptance criteria

- ✅ AC-001.1 — covered by `tests/auth/login.spec.ts:happy path`
- ✅ AC-001.2 — covered by `tests/components/LoginForm.spec.tsx:401 inline error`
- ✅ AC-001.4 — covered by `tests/db/user.spec.ts:lastLoginAt updated on login`
- ✅ AC-001.5 — covered by `tests/auth/login.spec.ts:429 rate limit`

## Plan

See `docs/plans/FR-001.md` — every task `[x]`.

## Test plan

- [x] Run `<TEST_CMD>` — all green
- [x] Run `<TYPECHECK_CMD>` — clean
- [ ] Manual smoke: log in with valid creds → check `lastLoginAt` updates
- [ ] Manual smoke: 6 wrong attempts in 15 min → rate-limit message shows

## Out of scope

- OAuth / social login (deferred to FR-005)
- "Remember me" cookie (deferred — not in FR-001 ACs)

## Notes for reviewer

- Reused `createSession()` from `src/lib/auth/session.ts` — no new session helper.
- New component `<InlineError>` lives in `src/components/InlineError.tsx`; consider promoting to design system if reused elsewhere.

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

Fill the placeholders from real data. The test-plan checkboxes for `<TEST_CMD>` / `<TYPECHECK_CMD>` are already passing — mark them `[x]`. Manual-smoke items stay `[ ]` for the user to verify.

### Step 3: Surface CLAUDE.md feedback (if any)

During the task loop, did you hit any *recurring* issue — something the user had to correct twice or more? Examples:

- Agent kept importing from the wrong path.
- Agent kept inventing a non-existent helper.
- Agent kept missing a project-specific rule.

If yes, propose a one-line addition to `CLAUDE.md`:

```markdown
## Suggested CLAUDE.md update

> "Use `@/lib/...` aliases for internal imports — never relative `../../`."

Reason: corrected 3 times during FR-001 task loop.
```

The user decides whether to accept. **Do not edit `CLAUDE.md` automatically.**

If nothing recurred, skip this section.

### Step 4: Hand back

```markdown
## 🚀 FR-001 ready for PR

- All 4 tasks `[x]`
- All 4 ACs covered by passing tests
- 4 conventional commits on `feat/FR-001-login`

### PR description (ready to paste)

<the markdown from Step 2>

### Suggested CLAUDE.md update (optional)

<from Step 3, if any>

### Next steps
- Open the PR yourself, or tell me to: *"open the PR"*
- After merge, close any tracking issue or remove the plan from `docs/plans/` if it was throw-away
```

### Step 5: If the user says "open the PR"

Then — and only then — run:

```bash
gh pr create --title "feat: implement FR-001 login flow" --body "<the description>"
```

Match the PR title to the dominant commit type. For multi-type PRs (`feat` + `fix`), prefer `feat`.

## Anti-patterns (forbidden)

- ❌ Auto-opening the PR. The user pulls the trigger.
- ❌ Editing `CLAUDE.md` without the user's approval.
- ❌ Marking the manual-smoke items as `[x]` — those are for the user to verify.
- ❌ Squashing the commits before the PR. Conventional commits per task = traceability; if reviewers want squash-merge, that's the merge strategy, not your job.
- ❌ Hiding scope creep in a "miscellaneous" bullet. If you touched something outside the plan, name it explicitly.
