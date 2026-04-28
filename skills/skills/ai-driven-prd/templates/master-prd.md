# Master PRD Template

Copy this entire file to `docs/prd/prd-<slug>.md` and fill it in. The order of sections matters — critical constraints are repeated near the start and end of the document, exploiting the positional-attention bias of LLMs. Do not reorder.

```markdown
---
id: PRD-<NNN>
title: <Feature Name>
version: 1.0.0
status: draft | reviewed | approved | in-progress | done
owner: <pm-handle>
target_agents: [claude-code, cursor, copilot]
created: YYYY-MM-DD
updated: YYYY-MM-DD
related_prds: []
---

# <Feature Name> — PRD

<context>
  Repo: <url> · Branch: feature/<slug>
  Primary stack: <language> <version> / <framework> <version>
  This PRD is authoritative. If it conflicts with tribal knowledge,
  the PRD wins. If ambiguity is found, STOP and ask — do not invent.
</context>

## 1. TL;DR
Five lines max: what, why, for whom, success metric, deadline.

## 2. Problem & Context
Current state, quantified pain (tickets/week, $/month, users affected),
links to user research, traces, or incident postmortems. What breaks
if we do nothing in 6 months.

## 3. Goals
<goals>
- G1: <measurable outcome with baseline → target by date>
- G2: ...
</goals>

## 4. Non-Goals / Out of Scope
<non_goals>
- Explicitly NOT doing: <thing>, because <reason>.
- Deferred to future PRD: <thing>.
</non_goals>

## 5. Personas & User Stories
### Personas
- **<Role>**: context, JTBD, current workaround.

### User Stories
- **US-1**: As a <role>, I want <capability>, so that <outcome>.
- **US-2**: ...

## 6. Functional Requirements
<requirements>
- **FR-1** (MUST): <single-sentence declarative behavior>.
  - Acceptance: see AC-1.
- **FR-2** (SHOULD): ...
- **FR-3** (MUST NOT): The system MUST NOT <forbidden behavior>.
</requirements>

## 7. Non-Functional Requirements
<nfr>
- **Performance**: p95 latency ≤ <Xms> under <load> measured by <tool>.
- **Security**: authN via <mechanism>; authZ matrix in §11; PII fields: [...].
- **Accessibility**: WCAG 2.2 AA; keyboard-only flows required.
- **Observability**: emit events [...]; metrics [...]; alert on [...].
- **Availability**: <SLO>. Rollback plan in §17.
- **i18n**: supported locales [...]; UTF-8 throughout; NFKC normalization on input.
</nfr>

## 8. Tech Stack & Constraints
<constraints>
- Language: <pinned version>
- Framework: <pinned version>
- Packages allowed: [...]. Packages forbidden: [...].
- Runtime targets: Node 20, Postgres 16, Redis 7.
- MUST reuse: @src/shared/db.ts, @src/lib/errors.ts.
- MUST follow style of: @src/features/users/service.ts.
</constraints>

## 9. Data Model
```sql
CREATE TABLE <name> (
  id UUID PRIMARY KEY,
  ...
);
-- Migration: migrations/<timestamp>_<name>.sql
-- PII fields: [email, phone] — tier-2
```

## 10. API Contracts
```yaml
# OpenAPI excerpt
POST /api/v1/<resource>:
  request:
    body: { email: string, password: string (≥12 chars) }
  responses:
    201: { token: string, user: User }
    400: { code: "VALIDATION_ERROR", message: string, fields: [...] }
    429: { code: "RATE_LIMITED", retryAfter: number }
```
Example request/response pairs MUST be included for each endpoint.

## 11. Authorization Matrix
| Entity | Action | Role | Allowed | Condition |
|---|---|---|---|---|
| User | invite | org_admin | yes | same org_id |
| User | delete | org_admin | yes | target.role ≠ owner |
| Billing | view | org_admin | no | — |

## 12. File Structure
```
src/features/<feature>/        ← new
├── handler.ts
├── service.ts
├── schema.ts
└── __tests__/
    └── service.spec.ts
tests/integration/<feature>.spec.ts   ← new
src/shared/db.ts                      ← DO NOT MODIFY
src/middleware/auth.ts                ← DO NOT MODIFY
```

## 13. Reference Code (anti-hallucination anchors)
<examples>
- Code style: follow @src/features/users/service.ts
- Test style: follow @tests/features/users.spec.ts
- Error envelope: use @src/shared/errors.ts (ApiError class)
- Logging: use @src/shared/logger.ts (never console.log)
</examples>

## 14. Acceptance Criteria
<acceptance_criteria>
### AC-1 (covers FR-1, US-1)
```gherkin
Given a user with role=admin and an active session
When they POST /api/v1/projects with body {name: "Foo"}
Then response status is 201
  And response body contains {id: <uuid>, name: "Foo", created_at: <iso8601>}
  And a row exists in projects where name = "Foo"
  And event "project.created" is emitted with {actor_id, project_id}
```
Covered by: `tests/integration/projects.spec.ts::"admin creates project"`

### AC-2 (edge case: duplicate name)
```gherkin
Given a project named "Foo" already exists in the same org
When the user POSTs /api/v1/projects with body {name: "Foo"}
Then response status is 409
  And response body is {code: "DUPLICATE", message: "..."}
  And no new row is created
```
</acceptance_criteria>

## 15. Edge Cases & Failure Modes
| Category | Trigger | Expected behavior | Test |
|---|---|---|---|
| Empty input | name = "" | 400 VALIDATION_ERROR | AC-3 |
| Concurrency | double-submit within 100ms | idempotency key → 1 row | AC-4 |
| Unicode | name contains emoji/RTL | accepted, NFKC-normalized | AC-5 |
| Rate limit | 5 failures / 10 min | 429 + 15-min lockout | AC-6 |
| Dep down | DB unreachable | 503 with retry-after | AC-7 |

## 16. Testing Strategy
- **Unit**: `npm test -- features/<feature>` — ≥85% line coverage for new code.
- **Integration**: `npm run test:int` — all ACs covered.
- **E2E**: `npm run e2e -- <feature>` — happy path + 2 critical edge cases.
- **Perf (if NFR)**: `npm run bench:<feature>` — must run in CI.
- **TDD required**: write failing tests first, commit, then implement.

## 17. Implementation Phases
Each phase ends in a runnable, tested, reviewable state. Agent MUST
stop after each phase for human approval.

- **Phase 0**: `git checkout -b feature/<slug>`
- **Phase 1** (schema): migration + model + unit tests. Verifiable via `npm run db:migrate`.
- **Phase 2** (service): business logic + unit tests. Verifiable via service tests.
- **Phase 3** (API): handler + validation + integration tests. Verifiable via curl/Postman.
- **Phase 4** (wiring): routes, middleware, feature flag. Verifiable via E2E.
- **Phase 5** (observability): metrics, logs, dashboard, alert. Verifiable in staging.

## 18. DO NOT
<do_not>
- DO NOT modify: src/shared/db.ts, src/middleware/auth.ts, any migration
  file older than today.
- DO NOT add dependencies without listing them here and pausing for approval.
- DO NOT refactor files outside src/features/<feature>/.
- DO NOT mark a task complete while any test fails or lint errors exist.
- DO NOT commit secrets; use env vars documented in §20.
- DO NOT invent API endpoints; check §10 or ASK.
</do_not>

## 19. Dependencies, Secrets, Feature Flags
- External: <service> v<N> (docs: <url>).
- Secrets: `STRIPE_KEY`, `JWT_SECRET` (document in .env.example only).
- Feature flag: `feature.<slug>.enabled` (default off). Rollout: 1% → 10% → 100%.
- Rollback: toggle flag off; no schema rollback needed (migrations are additive).

## 20. Success Metrics
- **Product**: <metric baseline → target by date>, guardrail: <counter-metric>.
- **AI output quality**: PR size ≤ 400 LoC, review cycles ≤ 2, 0 regressions in
  existing tests, lint clean on first commit.

## 21. Glossary
- **<DomainTerm>**: definition the agent will not know from training.

## 22. References
- Design doc: <url>
- ADR: docs/architecture/adr-NNN.md
- Related PRDs: [...]
- External docs (pinned versions): [...]

## 23. Assumptions Made by AI
Before any code, the agent MUST output:
```
ASSUMPTIONS I'M MAKING:
1. <...>
2. <...>
```
And wait for confirmation if any assumption is non-trivial.

<final_reminders>
- Start with Plan Mode. Do not edit files before plan is approved.
- Quote the exact section of this PRD you are implementing in each commit.
- One sub-task at a time; mark [x] in tasks.md; STOP for approval.
- If blocked or ambiguous: ASK — do not invent.
- Repeat: the PRD is authoritative. The DO NOT list is absolute.
</final_reminders>
```
