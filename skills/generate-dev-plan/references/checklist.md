# Generate-Dev-Plan Quality Checklist

Score before handing the plan back to the user. **All 7 items MUST pass.** Any ❌ blocks handoff and sends you back to Phase 2 or 3.

| # | Item | Pass criterion |
|---|------|---------------|
| 1 | One requirement per file | Plan covers exactly one FR-N. No bundling. |
| 2 | Every task has a sub-ID | `FR-N.M` on every line. Sequential or `a/b` for parallel. |
| 3 | Every task names files | Concrete paths listed. No vague "update the X module". |
| 4 | Every task references an AC | `AC: AC-N.M` present. If absent, the task is probably plumbing — fold it. |
| 5 | Every task references a test | Either adds a new test (named) or modifies an existing test file (named). |
| 6 | No plumbing-only tasks | Each task ends runnable + tested. No standalone "scaffold types" / "add empty handler". |
| 7 | No `⚠️ MISSING:` markers | Every Phase 1/2 ambiguity resolved. If any remain, the plan is not ready. |

## Quick scoring rubric

For each task line, ask:
- Could a different engineer pick this up and finish it without asking me anything? → ✅
- Does completing this task leave the build/tests green? → ✅
- Does this task make user-visible progress against an AC? → ✅

If you answered "no" to any of the three, fix the task or fold it into another.

## On failure

Report which item failed and the specific fix. Examples:

- ❌ Item 4 (FR-001.3 has no AC reference) → "Either fold into FR-001.2 or identify the AC it satisfies. If no AC matches, the task isn't user-visible — re-evaluate why it's here."
- ❌ Item 6 (FR-001.1 = `add types`, FR-001.2 = `wire handler`) → "Merge into one task: `FR-001.1 — add types AND wire handler, with a passing test`."
- ❌ Item 7 (`⚠️ MISSING: rate-limit threshold` still in the plan) → "Return to Phase 2 and resolve with the user."
