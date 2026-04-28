# 15-Point AI-Readiness Checklist

This is the operational counterpart of `prompts/06-readiness.md`. Use it as a hard gate before handing the PRD to a coding agent.

**Pass condition:** ≥13/15 green (✅). 2–3 iteration passes are normal.

| # | Criterion | Acceptable evidence |
|---|---|---|
| 1 | Unambiguous language | No fuzzy adjective without a quantified threshold (no "fast", "easy", "intuitive", "robust" left bare) |
| 2 | Verifiable acceptance criteria | Given/When/Then or EARS block on every FR |
| 3 | Numbered atomic requirements | FR-1, FR-2 …; one behavior per sentence |
| 4 | Self-contained | Every external reference linked with `@path` or URL — no tribal knowledge |
| 5 | Concrete examples | ≥1 input → expected-output example per non-trivial FR |
| 6 | Explicit Non-Goals | Dedicated section with ≥3 items |
| 7 | Quantified success metrics | Baseline + target + date + guardrail metric |
| 8 | Machine-parseable structure | Consistent headings, code fences, tables, XML tags |
| 9 | Dependency-ordered phases | Each implementation phase ends in a runnable + tested state |
| 10 | Edge cases enumerated | Failure-modes table covering 12 categories (validation, concurrency, latency, scale, permissions, state, deps, UX, abuse, observability, rollback, AI-specific) |
| 11 | NFRs specified | Performance, security, accessibility, observability — with numbers |
| 12 | DO NOT list populated | ≥4 concrete forbidden actions |
| 13 | Context references | Paths to existing files, ADRs, external docs |
| 14 | Probabilistic criteria (if applicable) | Eval set + thresholds + guardrails for any LLM/ML feature |
| 15 | Handoff artifacts ready | `tasks.md`, tests, rollback plan, feature flag, runbook |

## How to score

- **✅ pass** — evidence is clearly present and complete.
- **⚠️ partial** — partial coverage; rewrite required.
- **❌ fail** — missing entirely; block the handoff.

## Common reasons items fail

| Item | Frequent failure |
|---|---|
| 1 | "Loads quickly" without a number; "easy to use" without a target task time |
| 5 | Abstract requirements with no input/output sample |
| 6 | Non-Goals section absent or vacuous ("nothing else"); reviewers should be able to point at three things explicitly excluded |
| 10 | Only happy-path edge cases listed; no concurrency, abuse, or observability gaps |
| 12 | DO NOT list contains vibes ("don't break things") instead of file paths or commands |
| 14 | LLM features specified with prose only — no eval dataset, no threshold |
