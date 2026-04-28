# Phase 6 — AI-Readiness Review

**Goal:** Score the PRD against the 15-point checklist. Hard gate: ≥13/15 to proceed to implementation.

**Gate:** Iterate (typically 2–3 passes) until score ≥13/15.

**Reference:** the full checklist with evidence rules is in [`../references/checklist.md`](../references/checklist.md).

---

## Run this prompt verbatim

```
You are a VP of Product + Principal Engineer reviewing this PRD
for AI-readiness. Score it against the 15-point checklist below.
For each item: ✅ pass, ⚠️ partial, ❌ fail — with a one-line
justification and, for ⚠️/❌, a specific rewrite.

INPUT
<prd>
{{paste PRD}}
</prd>

CHECKLIST
1.  Unambiguous language (no fuzzy adjectives without thresholds).
2.  Testable acceptance criteria (Given/When/Then or EARS).
3.  Numbered, atomic requirements (FR-1, FR-2, ...).
4.  Self-contained (no tribal knowledge; all external refs linked).
5.  Concrete examples per non-trivial requirement.
6.  Explicit Non-Goals / Out-of-Scope.
7.  Quantified success metrics (baseline + target + date + guardrail).
8.  Machine-parseable structure (consistent headings, tables, fences).
9.  Dependency-ordered phases, each ending runnable + tested.
10. Edge cases enumerated (empty, error, concurrency, permission,
    scale, i18n, offline).
11. NFRs specified (perf, security, a11y, observability).
12. DO NOT list populated with ≥4 concrete forbidden actions.
13. References & context links (to code files, docs, ADRs).
14. AI-specific criteria if probabilistic (eval set + thresholds +
    guardrails).
15. Handoff artifacts ready (tasks.md, test plan, rollback, flag).

OUTPUT
- Score table (15 rows, ✅/⚠️/❌, justification, rewrite if needed).
- Total: X/15. Gate: ≥13/15 to proceed to implementation.
- Top 5 issues in priority order with proposed diffs.
- 3 open questions for the PM.
```
