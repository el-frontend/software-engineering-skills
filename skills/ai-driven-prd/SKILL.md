---
name: ai-driven-prd
description: Use when the user wants to author, refine, or audit a Product Requirements Document for AI coding agents. Walks through an 8-phase pipeline (Socratic discovery → PRD draft → acceptance criteria → adversarial review → task decomposition → AI-readiness gate → test generation → handoff). Triggers on "write a PRD", "spec this feature", "draft requirements", "prepare X for Claude/Cursor/Copilot/Windsurf/Aider to build", "audit my PRD", "is this PRD AI-ready", "score this spec".
---

# AI-Driven PRD

Reusable framework for transforming raw stakeholder requests into PRDs that AI coding agents (Claude Code, Cursor, Copilot, Windsurf, Aider) can execute with minimal ambiguity.

## Core principle

The spec — not the code — is the durable artifact. The reader is "a junior developer who never leaves the project": an LLM with short memory, dated training data, and a tendency to fill gaps with the average of the internet. Therefore the PRD must be **explicit, atomic, machine-verifiable, and self-contained**. Omission is read as permission; vague adjectives become hallucinations.

## When to use

- New feature request → start at **Phase 1**.
- User has a brief / notes / transcript already → start at **Phase 2**.
- Existing PRD needs hardening → **Phase 3, 4, or 6**.
- User asks to "audit" / "score" / "review" a PRD for AI readiness → **Phase 6**.

Skip for: ad-hoc bug fixes, exploratory questions, single-file refactors, anything under ~50 LoC of work.

## The 8 phases

Each phase has an INPUT, an OUTPUT artifact, and a **HUMAN GATE** before advancing.

| # | Phase | Input | Output | Prompt file |
|---|---|---|---|---|
| 1 | Socratic discovery | Raw request | `brief.md` | [prompts/01-discovery.md](prompts/01-discovery.md) |
| 2 | Raw → PRD | `brief.md` | `docs/prd/prd-<slug>.md` | [prompts/02-raw-to-prd.md](prompts/02-raw-to-prd.md) |
| 3 | Acceptance criteria | PRD with FRs | Gherkin/EARS blocks | [prompts/03-acceptance.md](prompts/03-acceptance.md) |
| 4 | Adversarial pass | PRD + ACs | failure-modes table | [prompts/04-adversarial.md](prompts/04-adversarial.md) |
| 5 | Task decomposition | Validated PRD | `tasks/tasks-prd-<slug>.md` | [prompts/05-tasks.md](prompts/05-tasks.md) |
| 6 | AI-readiness review | PRD + tasks | score ≥13/15 or fixes | [prompts/06-readiness.md](prompts/06-readiness.md) |
| 7 | Test generation | ACs + edge cases | failing tests committed | [prompts/07-tests.md](prompts/07-tests.md) |
| 8 | Execution loop | PRD + tasks + tests | implemented PR | [prompts/08-execution.md](prompts/08-execution.md) |

## Operating protocol

1. **Identify the entry phase** from what the user already has.
2. **Read the matching prompt file** in `prompts/` and run it on the user's content. Treat the prompt body as a system instruction — follow its rules (tone, output format, anti-patterns) literally.
3. **Stop at every phase boundary.** Show the artifact and ask the user: *approved / refine `<section>` / restart phase*. Do not chain phases without explicit approval.
4. **Phase 2 drafting**: copy [templates/master-prd.md](templates/master-prd.md) as the scaffold and fill it. Do not rearrange section order — the template's ordering exploits LLM positional attention bias.
5. **Phase 6 gate**: score the PRD against [references/checklist.md](references/checklist.md). If <13/15, return the prioritized fixes and block the handoff.
6. **Default artifact paths** (declared in `templates/master-prd.md` §12): `docs/prd/`, `tasks/`, `tests/`, `evals/`, `progress.json`.

## Hard rules (non-negotiable)

- **No fuzzy adjectives.** *Fast, easy, intuitive, robust, modern, seamless* trigger an immediate rewrite to a quantified threshold.
- **Number everything.** Every functional requirement, user story, and acceptance criterion gets an ID; cross-reference FR-N ↔ AC-N ↔ test name.
- **DO NOT list ≥4 items.** Concrete forbidden actions, not vibes.
- **Each implementation phase ends runnable + tested.** No plumbing-only sequences.
- **Ambiguity → ASK.** Never invent. If a section can't be filled, mark it `⚠️ NEEDS INPUT:` with the specific question.
- **Segment for parsing.** Keep headings, code fences, and Anthropic-style XML tags (`<context>`, `<requirements>`, `<do_not>`, `<acceptance_criteria>`). LLMs follow structure better than prose.
- **RFC 2119 keywords** (MUST / MUST NOT / SHOULD / MAY) on every normative statement.

## What NOT to do (common failure modes)

See [references/anti-patterns.md](references/anti-patterns.md) for the full list. Top three to watch:
- Over-specification (300 requirements that exceed context budget).
- Sub-specification (no pinned stack → agent picks Express in your Fastify project).
- Implicit scope (no DO NOT list → agent "helps" by refactoring unrelated files).

## References

- Master PRD template: [templates/master-prd.md](templates/master-prd.md)
- Phase prompts: [prompts/01-discovery.md](prompts/01-discovery.md) … [prompts/08-execution.md](prompts/08-execution.md)
- 15-point AI-readiness checklist: [references/checklist.md](references/checklist.md)
- Bad vs AI-ready requirement examples: [references/examples.md](references/examples.md)
- Anti-patterns and remedies: [references/anti-patterns.md](references/anti-patterns.md)
- Per-tool notes (Claude Code, Cursor, Copilot, Windsurf, Aider) and framework matrix: [references/tooling-notes.md](references/tooling-notes.md)
