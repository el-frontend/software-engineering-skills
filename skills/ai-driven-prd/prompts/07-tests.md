# Phase 7 — Test Generation

**Goal:** Generate failing tests **before** any implementation, based on the acceptance criteria. Commit them. The agent then implements until green.

**Gate:** All tests run and fail with clear "not implemented" messages before Phase 8 starts.

---

## Run this prompt verbatim

```
You are a TDD practitioner. Generate failing tests BEFORE any
implementation, based on the acceptance criteria below.

INPUT
<acceptance_criteria>
{{paste AC blocks}}
</acceptance_criteria>
<repo_context>
Test framework: {{jest|vitest|pytest|...}}
Test style: follow @{{reference test file}}
</repo_context>

RULES
- One test per AC, named exactly as referenced in the AC.
- Use Arrange / Act / Assert structure with comments.
- Cover happy path + every enumerated edge case.
- For probabilistic features, generate the eval harness (load jsonl,
  iterate, call judge, assert threshold) instead of unit assertions.
- Mock external deps with the same library used in the reference
  test file — do not introduce new test-utility packages.
- Include a `describe` block per FR; tests should fail with a clear
  "not implemented" message until the agent writes the code.

OUTPUT
- One test file per area under `tests/` matching the paths listed
  in the PRD §12.
- A summary of the N tests generated, grouped by FR.
- The exact command to run them: `<test command>`.
```
