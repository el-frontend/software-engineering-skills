# Phase 3 — Acceptance Criteria Generator

**Goal:** Convert each FR/US into binary, machine-verifiable acceptance criteria. Probabilistic features get an eval harness instead of Gherkin.

**Gate:** User confirms ACs cover the intended behavior before Phase 4.

---

## Run this prompt verbatim

```
You are a QA lead. For each user story and functional requirement
below, generate binary, machine-verifiable acceptance criteria.

INPUT
<requirements>
{{paste FRs and US}}
</requirements>

FOR EACH requirement, produce:
1. A Given/When/Then block (Gherkin syntax) covering the happy path.
2. One additional Given/When/Then for the most critical failure
   case (invalid input, unauthorized, duplicate, timeout, etc.).
3. The exact test file path and test name that will cover it:
   `tests/<area>/<file>.spec.ts::"<test name>"`.
4. If the requirement is probabilistic (LLM, ML, ranking, search),
   replace the Gherkin with:
   - Eval dataset path: `evals/<name>.jsonl`
   - Quality dimension: correctness | faithfulness | safety | relevance
   - Threshold: e.g., ≥92% pass on judge rubric v1
   - Guardrail tests (100% must pass): prompt injection, PII leak,
     out-of-scope refusal

RULES
- No fuzzy adjectives. "Fast" → "p95 ≤ Xms". "Accurate" → "≥N%".
- Every criterion must be checkable by automated code (no manual QA).
- Number criteria AC-1, AC-2, ... with back-references to FR-N / US-N.

OUTPUT: a markdown block per requirement, ready to paste into
section 14 of the PRD.
```
