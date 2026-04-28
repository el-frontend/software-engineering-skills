# Phase 2 — Investigate

You have an approved requirement bundle from Phase 1. Now you investigate the codebase and resolve every open question **before** any plan is written. The output of this phase is **investigation notes**, not tasks.

## Inputs

- Approved requirement bundle from Phase 1.
- The target repo.
- Any `docs/architecture.md` / `CLAUDE.md` imports.

## What to do

### Step 1: Map the affected code

For each piece of the requirement, locate the code that will be touched:

- **Entry points** — routes, handlers, CLI commands, UI screens.
- **Domain models** — types, schemas, database tables, value objects.
- **Cross-cutting concerns** — auth middleware, logging, feature flags, i18n, telemetry.
- **Tests** — existing test files that cover (or fail to cover) the affected areas.
- **Designs** — `docs/designs/<FR-N>-*.html` if they exist. Open them.

For each: record `<path/to/file.ts>:<line-or-range>` so the plan's tasks can name them.

### Step 2: Check for prior art

Is anything already implemented? **Phantom tasks** (planning work that already exists) is a top failure mode. Specifically check:

- The exact endpoint/route/component named in the requirement — does it exist?
- Helper functions that would be reused — are they already there? Where?
- Adjacent features that solved a similar problem — what pattern did they use?
- Migration files / schema changes already merged that this requirement assumed didn't exist yet.

If you find prior art, the plan MUST reference it (`reuse <fn> from <file>:<line>`) instead of inventing duplicates.

### Step 3: Identify the dependency graph

For the work itself, map the dependencies between the unit tasks you'll later create:

- What MUST exist before anything else? (e.g. database schema before query code)
- What can run in parallel?
- Where are the integration points (front ↔ back, service ↔ service)?

You'll use this in Phase 3 to order tasks. A dependency you missed here is a stuck task there.

### Step 4: Resolve open questions

Take every `⚠️ MISSING:` from Phase 1 and every ambiguity uncovered in steps 1–3, and ask the user **specific, decision-shaped questions**:

- ❌ "How should errors be handled?" → too open
- ✅ "On 401 from `/api/auth/login`, should the form show an inline message or redirect to `/forgot-password`? AC-001.2 doesn't say."

Batch the questions. Get answers before continuing.

### Step 5: Output investigation notes

Output exactly this structure:

```markdown
## Investigation — FR-001

### Affected files
- `src/routes/auth.ts:42-78` — current login handler
- `src/components/LoginForm.tsx` — does NOT exist; will be created
- `prisma/schema.prisma:User` — has `passwordHash`, no `lastLoginAt` (AC-001.4 needs it)
- `src/lib/auth/session.ts:12` — reuse `createSession()`
- `tests/auth/login.spec.ts:1-40` — covers happy path; missing 401 case

### Prior art / reuse
- `createSession()` in `src/lib/auth/session.ts:12` — reuse, do not duplicate
- `<FormField>` pattern from `src/components/SignupForm.tsx` — copy structure

### Dependency graph
- Schema migration (lastLoginAt) → server-side handler → client form → e2e test
- Inline error component is independent — can run in parallel with handler work

### Resolved questions
- Q: Inline error vs redirect on 401? → **A:** Inline (user confirmed in Phase 2)
- Q: Rate-limit threshold? → **A:** 5 attempts / 15 min (user, refers to NFR-3)

### Confirmation
✅ All open questions resolved. Ready for Phase 3.
```

### Step 6: Hand to user

> *Approved → proceed to Phase 3 / Refine investigation / Restart phase 2*

**Phase 3 MUST NOT begin if any open question is still unresolved.**

## Anti-patterns (forbidden)

- ❌ Skipping prior-art check. Phantom tasks waste sessions and clutter PRs.
- ❌ Writing tasks here. This phase produces *notes*, not a plan.
- ❌ Asking vague questions ("how should we handle X?"). Every question must propose options or be decision-shaped.
- ❌ Resolving ambiguity yourself ("I'll assume Y") instead of asking. Inventing decisions is a top-3 framework failure mode.
- ❌ Inventing files / functions / endpoints that don't exist in the repo. Always read before referencing.
