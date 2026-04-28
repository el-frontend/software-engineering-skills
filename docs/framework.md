# AI Software Development Framework (V2)

This framework optimizes the software development lifecycle using autonomous agents and advanced reasoning models, focusing on **context persistence** and **continuous validation through test-driven development**.

> Translated from the original Spanish source. The original PDF is the source of truth; this document is the AI-readable working copy.

## Prerequisites

This document targets AI-driven development, specifically using **Claude Code** — either in the terminal or in the Claude desktop app.

### Requirements

- A **Claude account** is required. Other tools (Open Code, GitHub Copilot, Antigravity) may work, but this guide is tested and tuned for Claude Code.
- The user MUST be comfortable with **Skills**. Reference: [Anthropic Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview).

---

## Phase Detail and Technical Argument

### 1. Functional Requirements Extraction & Optimization for Language Models

Modern development based on agile processes like SCRUM has solid foundations that translate well into AI-guided development — but every team member must adapt to the new normal.

One artifact that SCRUM produces is the **Functional Requirements Document**. These are usually based on prebuilt templates that are extremely **verbose** and written for humans (clients, stakeholders, other engineers). Language models (GPT, Claude, etc.) drown in irrelevant content and burn unnecessary tokens against the context window.

That's why **AI-optimized requirement documents** exist. The goal is to be clear and concise enough to avoid token waste and pointless formality.

The traditional **Product Requirements Document (PRD)** has been adapted into the **AI-driven PRD** (sometimes called *Prompt Requirement Document*) — a PRD optimized for AI generation/implementation.

#### Guidelines for a good AI-driven PRD

- The document MUST state the **version** and the **application name** being worked on.
- It MUST include a project summary, **architecture overview**, **tech stacks**, project context, and notable data points.
- It MUST define **Actors (Roles)** that participate.
- Functional requirements MUST be grouped by module (or logical unit), each with a **unique requirement ID**. Requirements MUST be written in plain language, describing functionality, flow, and validations.
- Non-functional requirements MUST be defined when they exist.
- The document MUST include AI-facing instructions on technical decisions, business rules, and architecture details.

**Output:** A detailed requirements document that serves as the **source of truth** for the agent.

> This phase is implemented by [`skills/ai-driven-prd/`](../skills/ai-driven-prd/SKILL.md).

---

### 2. Configuration: Agent Skills, CLAUDE.md, AGENTS.md

The goal of this phase is to optimize **how the model sees your project** — the rules to follow, how to follow them, and what to do regarding technical and architectural decisions.

#### Documentation

##### CLAUDE.md

The primary documentation for a Claude-driven project MUST be `CLAUDE.md`. It contains a summary of the most important project decisions: architecture, rules, technical specs, and tech stack.

`CLAUDE.md` is a special file that Claude reads at the start of every conversation. It includes Bash commands, code style, and workflow rules. This gives Claude **persistent context** that cannot be inferred from the code alone.

The `/init` command analyzes the codebase to detect build systems, test frameworks, and code patterns — giving you a solid base to refine.

There is no required format for `CLAUDE.md`, but **keep it short and readable**. It loads every session, so include only things that apply broadly. For domain knowledge or workflows that are only sometimes relevant, use **skills** instead — Claude loads them on demand without inflating every conversation.

**Keep it concise.** For every line, ask: *"Would removing this cause Claude to make a mistake?"* If no, delete it. Bloated `CLAUDE.md` files cause Claude to ignore your real instructions.

`CLAUDE.md` files can import additional files using the `@path/to/import` syntax. This composition keeps `CLAUDE.md` concise while linking out to deeper documentation.

Unless you use advanced tooling (e.g. **Superpowers** installed) that implements TDD-driven processes, it's critical to add a rule in `CLAUDE.md` requiring the model to verify functionality and ensure tests pass after every change.

In our `CLAUDE.md` we add instructions for:

- Where the application **designs** live and how to read them.
- Where **development plans** live and how to mark plan tasks complete.
- Use **conventional commits**.

##### `docs/` Folder

At the project root we keep a `docs/` folder containing all relevant documentation: development plans, designs, and general project documentation.

#### SKILLS

Skills are folders containing instructions, scripts, and resources that Claude **discovers and loads dynamically** when relevant to a task. Think of them as specialized training manuals that grant Claude expertise in specific domains.

To install skills, use Claude plugins or a CLI like [skills.sh](https://skills.sh/).

The idea: install only the skills your project needs. Add more as new requirements surface.

#### Token-Optimization Tools

Many tools in this space are early and evolve fast. Two that have worked in practice:

- **Caveman** — [github.com/juliusbrussee/caveman](https://github.com/juliusbrussee/caveman) — makes the AI speak like a caveman, cutting token usage.
- **Rtk** — [github.com/rtk-ai/rtk](https://github.com/rtk-ai/rtk) — proxy for token reduction.

#### Development-Process Optimization Tools

Combined with skills, these enforce vetted engineering practices:

- **Superpowers** — [github.com/obra/superpowers](https://github.com/obra/superpowers)
- **GetShitDone (GSD)** — [github.com/gsd-build/get-shit-done](https://github.com/gsd-build/get-shit-done)
- **Karpathy Skills** — [github.com/forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills/issues)

> This phase will be implemented by `skills/init-claude-project/` (in progress).

---

## Development Process

### 3. Plan Creation

Plans are a **fundamental** part of this development process. Before writing any code, the recommended path is to generate a development plan that lets the AI investigate what to do and — through interaction with the operator (you) — make decisions captured in the plan. Claude provides a **plan mode** for exactly this.

Once you've identified what to work on, generate the plan with an instruction like:

> *"Generate a development plan to implement functionality REF-RF-0001; the requirement lives in `docs/prd-v0.1`. Each task MUST be independent and MUST include a checkbox to be marked as it's implemented."*

Each plan produces tasks that can be marked complete with `[x]` (markdown checkbox).

> This phase will be implemented by `skills/generate-dev-plan/` (planned).

---

### 4. Declarative Design with Stitch / Claude Design (or other design tools)

Instead of designing pixels manually, use [Stitch](https://stitch.withgoogle.com/) to generate UI via "Vibe Design" (describe the intent), or use the new Claude Design tool.

Both let you create system designs and export the generated screens as HTML. The workflow: iterate on screen generation or modification according to the requirement you're implementing.

After producing the screens, export to HTML alongside a screenshot and place them under `docs/designs/` with a name tied to the feature or requirement ID.

> Note: If screens already exist, skip this step and just copy designs into `docs/designs/`.

> This phase will be implemented by `skills/declarative-design/` (planned).

---

### 5. AI-Guided Development

Thanks to the configuration, requirements docs, designs, and plans built in earlier phases, the development process is ready.

To start, ask Claude to **execute the plan**. The chat will switch to "Ask Permissions" mode by default — switch to **"Accept Edits"** for a smoother flow. Once implementation finishes, Claude will run tests, compile, and verify there are no type errors.

After execution, you have code ready for review. **Review carefully** — examine the generated code, check the modified files, and analyze implications for other features. If you find issues, communicate them precisely to Claude. If the same problem recurs, add a note in `CLAUDE.md` to prevent it.

When you're satisfied, ask Claude to make the commit (or do it yourself) and finalize by opening a Pull Request.

> This phase will be implemented by `skills/execute-plan/` (planned).

---

## Workflow Diagram

[Workflow diagram (Google Drive)](https://drive.google.com/file/d/1UCDGJH0c_LQaoXL8kAPLhagN3dXKs_bx/view?usp=sharing)

## References

- [Claude Code best practices](https://code.claude.com/docs/es/best-practices)
- [Claude Code Skills explained](https://claude.com/blog/skills-explained)
- [Anthropic Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
