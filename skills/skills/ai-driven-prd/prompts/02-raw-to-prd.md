# Phase 2 — Raw → Structured PRD

**Goal:** Transform the approved `brief.md` into a populated `docs/prd/prd-<slug>.md` using the master template.

**Gate:** User reviews the PRD draft and approves before Phase 3.

**Before running:** copy `templates/master-prd.md` to `docs/prd/prd-<slug>.md` as the scaffold.

---

## Run this prompt verbatim

```
You are a senior PM. Transform the brief below into a PRD following
the master template exactly. Write for a junior developer: explicit,
unambiguous, no jargon without a glossary entry.

INPUTS
<brief>
{{paste brief.md here}}
</brief>
<repo_context>
Stack: {{stack}}. Conventions: see @AGENTS.md.
Existing patterns to mimic: {{@file1, @file2}}.
</repo_context>

RULES
- Use RFC 2119 keywords (MUST, MUST NOT, SHOULD, MAY) in every
  normative statement.
- Number every functional requirement (FR-1, FR-2, ...) and every
  user story (US-1, US-2, ...).
- Replace every fuzzy adjective with a measurable threshold.
- Include at least one concrete example (input → expected output)
  per non-trivial FR.
- Explicitly enumerate Non-Goals. If you are unsure whether X is
  in scope, list it under "Open Questions" — do not assume.
- Reference existing code with @path/to/file.ts where applicable.
- Populate §18 DO NOT with at least 4 concrete forbidden actions.
- If any section cannot be filled from the brief, mark it
  "⚠️ NEEDS INPUT:" and list the 1–3 questions needed to complete it.

OUTPUT
Save as docs/prd/prd-<slug>.md using the master PRD template
verbatim. Do NOT start implementing. After writing, list the top
3 ambiguities that still remain and ask for clarification.
```
