# Conventions Pinned by This Skill

Two conventions are seeded into every project this skill bootstraps. Keep them consistent across projects so the rest of the framework's skills (`generate-dev-plan`, `execute-plan`) can rely on them.

## 1. Conventional Commits

Spec: <https://www.conventionalcommits.org/en/v1.0.0/>

Format:

```
<type>(<scope>): <short summary>

[optional body]

[optional footer(s)]
```

Common types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `build`, `ci`, `perf`, `style`.

Examples:

```
feat(auth): add OAuth callback handler
fix(api): handle 429 retries on /search
docs(readme): document the four mandatory rules
chore(deps): bump zod to 3.23
```

Rules of thumb:

- One logical change per commit.
- Summary ≤ 72 chars, imperative mood ("add", not "added" or "adds").
- Use the scope to point at the module (`auth`, `api`, `ui/login`) — keep it short.
- Breaking changes: append `!` after the type/scope (`feat(api)!: rename /v1/users to /v2/users`) and explain in the footer.

## 2. Plan Checkbox Format

Plans live in `docs/plans/<REQUIREMENT_ID>.md`. Tasks use markdown checkboxes:

```markdown
# FR-001 — Login flow

- [ ] FR-001.1 — Build LoginForm component (state + validation)
- [ ] FR-001.2 — Wire form to POST /api/auth/login
- [x] FR-001.3 — Handle 401 with inline error message
- [ ] FR-001.4 — Add Playwright e2e test for happy path
```

Rules:

- Every task gets a sub-ID under the requirement (`FR-001.1`, `FR-001.2`, …).
- Tasks MUST be independently completable. If a task depends on another, list that dependency in the line.
- Mark `[ ]` → `[x]` as work completes — same commit that ships the work.
- Each task SHOULD end "runnable + tested." Plumbing-only tasks are an anti-pattern.

These two conventions are referenced in the generated `CLAUDE.md` so future agent sessions follow them automatically.
