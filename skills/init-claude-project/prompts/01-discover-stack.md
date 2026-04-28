# Phase 1 — Discover Stack

You are inspecting a target repository to produce a **stack summary** the user will approve before any `CLAUDE.md` is written. Do not write any files in this phase. Do not invent.

## Inputs

- The current working directory of the target repo.
- The user's intent: bootstrap this repo for Claude Code.

## What to do

### Step 1: Read the manifest files that exist

Check for these in order. For each one that exists, read it and note what it tells you. **Read; do not guess.**

- `package.json` — Node/JS/TS. Note: package manager (npm/pnpm/yarn/bun — infer from lockfile), framework (Next.js, Vite, Remix, Express, NestJS…), test runner (`scripts.test`), lint/format (`scripts.lint`, `eslint`/`biome`/`prettier` in deps).
- `pnpm-lock.yaml` / `yarn.lock` / `bun.lockb` / `package-lock.json` — confirms package manager.
- `tsconfig.json` — TypeScript? Strict mode?
- `pyproject.toml` / `requirements.txt` / `Pipfile` — Python. Note: package manager (uv/poetry/pip), test runner (pytest/unittest), linter (ruff/flake8/black).
- `Cargo.toml` — Rust.
- `go.mod` — Go.
- `Gemfile` — Ruby.
- `composer.json` — PHP.
- `Makefile` / `justfile` / `Taskfile.yml` — task runner. Read all targets.
- `.github/workflows/*.yml` — CI commands (often the cleanest source of "how to test/build this").
- `README.md` — sometimes documents the intended commands.

### Step 2: Detect existing AI-tooling state

- Does `CLAUDE.md` already exist? If yes, read it. Do NOT plan to overwrite — flag for the user.
- Does `.claude/` exist? Note any installed skills/plugins.
- Does `docs/` already exist? Note its current shape.

### Step 3: Produce the stack summary

Output exactly this structure as a markdown block. Mark anything you couldn't verify as `⚠️ NEEDS INPUT:` rather than guessing.

```markdown
## Stack summary

- **Language(s):** <e.g. TypeScript 5.4>
- **Runtime:** <e.g. Node 20, Bun 1.1, Python 3.12>
- **Package manager:** <pnpm | npm | yarn | bun | uv | poetry | …>
- **Framework(s):** <Next.js 15 (App Router), Prisma, …>
- **Test runner:** <vitest | jest | pytest | …>
- **Lint/format:** <eslint + prettier | biome | ruff | …>
- **CI:** <GitHub Actions | none detected>

## Commands (verified from manifest)

- Install: `<cmd>`
- Dev: `<cmd>`
- Build: `<cmd>`
- Test: `<cmd>`
- Lint: `<cmd>`
- Typecheck: `<cmd>`

## Existing AI-tooling state

- `CLAUDE.md`: <exists / does not exist>. <one-line summary if exists>
- `.claude/`: <contents or "absent">
- `docs/`: <contents or "absent">

## Open questions

- ⚠️ NEEDS INPUT: <specific question>
- ⚠️ NEEDS INPUT: <specific question>
```

### Step 4: Hand to user

Present the summary and ask exactly one of:

> *Approved → proceed to Phase 2 / Refine `<section>` / Restart phase 1*

**Do not proceed to Phase 2 without explicit approval.**

## Anti-patterns (forbidden)

- ❌ Inventing a `test` command because the project "looks like a Node project". Only list commands you read from a manifest.
- ❌ Listing every dependency in `package.json` — only list framework-level ones that change how Claude works.
- ❌ Skipping the existing-CLAUDE.md check.
- ❌ Producing more than ~25 lines. Stack summary should be skimmable.
