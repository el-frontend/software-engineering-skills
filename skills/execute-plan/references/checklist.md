# Execute-Plan Handoff Checklist

Score before opening (or proposing) a PR. **All 8 items MUST pass.** Any ❌ blocks handoff.

| # | Item | Pass criterion | Verification |
|---|------|----------------|--------------|
| 1 | Plan complete | Every task in `docs/plans/<FR-N>.md` is `[x]` | `grep "\\[ \\]" docs/plans/<FR-N>.md` returns empty |
| 2 | ACs covered by tests | Every AC referenced in the plan has a passing test | Cross-check the plan's task lines vs test runner output |
| 3 | Working tree clean | No uncommitted changes | `git status` |
| 4 | Test + typecheck + lint pass on HEAD | All three commands from CLAUDE.md exit 0 | Run them once before declaring done |
| 5 | One commit per task | `git log` shows N commits for N tasks; each subject is conventional | `git log --oneline <base>..HEAD` |
| 6 | No `--no-verify` commits | No bypassed hooks (unless user explicitly approved) | `git log --format=%B <base>..HEAD` — no skip markers |
| 7 | Scope contained | No files outside the plan's named files were modified (excluding obvious one-liners noted in commit bodies) | `git diff --name-only <base>..HEAD` vs union of plan-task file lists |
| 8 | "Done when" satisfied | Every box in the plan's `## Done when` block is `[x]` except the PR-opened one | Visual scan of the plan |

## Quick scoring rubric

For each commit, ask:
- Subject is conventional + ≤72 chars + imperative? → ✅
- Body references `FR-N.M`? → ✅
- Diff includes both code and the matching `[x]` flip in the plan? → ✅
- Test covering the new behavior is in the diff? → ✅

If any "no", that commit is wrong. Fix before declaring done — usually with `git commit --amend` (only if not pushed) or a follow-up commit.

## On failure

- ❌ Item 1 (one task still `[ ]`) → return to Phase 2 for that task. Do NOT manually flip the checkbox.
- ❌ Item 5 (two tasks in one commit) → if not pushed, split with `git reset HEAD~1` then re-commit per task. If pushed, surface to user — they decide whether to accept the bundled commit or rebase.
- ❌ Item 7 (touched a file outside scope) → either revert the out-of-scope change or update the plan to include that file (plan was wrong) and re-commit. The user decides.
