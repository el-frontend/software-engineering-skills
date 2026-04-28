# Generate-Dev-Plan Anti-Patterns

Common failure modes in dev-plan generation, with remedies. If you see one of these in your draft, fix before handoff.

## 1. Plumbing-only tasks

**Symptom.** Tasks like:
```
- [ ] FR-001.1 — Add User type
- [ ] FR-001.2 — Add empty login handler
- [ ] FR-001.3 — Add tests
- [ ] FR-001.4 — Add validation
```

**Why it's bad.** Each of these leaves the codebase non-functional. The agent can mark `.1` complete with green types but a broken app, then `.2` with a stub handler that doesn't satisfy any AC.

**Remedy.** Merge into vertical slices: `FR-001.1 — Add User.lastLoginAt + login handler that updates it + passing test`. End every task runnable + tested.

---

## 2. Phantom tasks

**Symptom.** Tasks that "create" files/functions that already exist in the codebase. Agent works for an hour, then realizes the work duplicates existing code.

**Why it's bad.** Wastes a session and produces conflicts.

**Remedy.** Phase 2's prior-art check exists exactly for this. If you skipped it, go back. If you found prior art, the plan must say `reuse <fn> from <path>:<line>` — not "create <fn>".

---

## 3. Monolithic tasks

**Symptom.** `[ ] FR-001 — Implement the login feature`.

**Why it's bad.** No granularity, no checkpoints, no parallelism. The agent will run for hours and you can't tell what's done.

**Remedy.** If you can't decompose, you didn't investigate. Return to Phase 2. A task that takes >2 focused hours of human-equivalent effort is too big.

---

## 4. Bundled requirements

**Symptom.** One file `docs/plans/auth-features.md` covers FR-001, FR-002, FR-003.

**Why it's bad.** The framework's downstream tooling (`execute-plan`, conventional-commit hooks, AC traceability) assumes one plan file = one requirement. Bundling breaks the trace.

**Remedy.** One file per FR-N. If FRs share work, extract the shared work into a separate plan (e.g. `FR-001-002-shared.md`) and reference from both.

---

## 5. Vague file references

**Symptom.** `update the auth module`, `fix the user service`, `tweak the form`.

**Why it's bad.** The agent has to guess which files. It will guess wrong.

**Remedy.** Concrete paths only: `src/auth/login.ts`, `src/components/LoginForm.tsx`. If the file doesn't exist yet but will, write `(new) src/components/LoginForm.tsx`.

---

## 6. AC-orphan tasks

**Symptom.** A task with no `AC:` reference.

**Why it's bad.** It probably isn't doing user-visible work. It's plumbing, refactoring, or scope creep.

**Remedy.** Either fold into a task that does have an AC, or move to a separate PR for tech-debt cleanup. Plans only contain AC-satisfying work.

---

## 7. Invented decisions

**Symptom.** A task makes a choice (e.g. retry strategy, error format, cache TTL) that the PRD didn't specify and the user never confirmed.

**Why it's bad.** The framework's premise is that the spec is the source of truth. Inventing decisions in the plan moves authority from the user to the agent.

**Remedy.** Phase 2's job. If a decision shows up in the plan, the user must have signed off in Phase 2. If they didn't, the task isn't ready — back to Phase 2.

---

## 8. Stale dependency

**Symptom.** Plan says `FR-001.2 — assumes FR-000 done`, but FR-000 is open or partially done.

**Why it's bad.** The plan can't be executed without blocking on prerequisites.

**Remedy.** Phase 1's dependency check should catch this. If FR-000 isn't done, the user decides: implement FR-000 first, or scope the plan to only the parts that don't depend on it.

---

## 9. "Future-proofing" tasks

**Symptom.** Tasks like `[ ] add hooks for future i18n support`, `[ ] make the API extensible`.

**Why it's bad.** Speculative complexity. No AC, no test, no done-ness signal.

**Remedy.** Delete. If the user genuinely wants extensibility, it should be in the PRD as an explicit FR or NFR with a quantified threshold. Otherwise it's YAGNI.

---

## 10. Test-as-afterthought

**Symptom.** All tasks first, then `[ ] FR-001.N — write tests`.

**Why it's bad.** Tests added at the end don't shape the design. They become rubber-stamping.

**Remedy.** Each task includes its test. If the test is hard to write, that's signal the task is wrong-shaped. Red-green-refactor inside each task.
