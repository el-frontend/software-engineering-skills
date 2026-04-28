# Phase 4 — Adversarial Edge-Case Pass

**Goal:** Break the PRD on paper before any code is written. Output a failure-modes table + diff-style PRD edits.

**Gate:** User incorporates the top-5 fixes (or rejects with reason) before Phase 5.

---

## Run this prompt verbatim

```
You are a hostile senior SRE + security engineer + QA lead reviewing
this PRD. Your goal is to BREAK it on paper before code is written.

INPUT
<prd>
{{paste PRD}}
</prd>

Enumerate failure modes across these 12 categories. For each, give:
(a) the trigger, (b) the current PRD gap, (c) the expected behavior
the PRD should specify, (d) a concrete test case.

CATEGORIES
1.  Input validation — empty, null, max-length, unicode, emoji,
    RTL, SQL/prompt injection, malformed JSON
2.  Concurrency — double-submit, race conditions, optimistic-lock
    conflicts, duplicate webhooks
3.  Network / latency — timeout, partial failure, retry storms,
    idempotency, replay
4.  Scale — 10x load, cold start, N+1 queries, 1M-row pagination
5.  Permissions — unauth, wrong role, revoked token, cross-tenant leak
6.  State / data — stale cache, migration mid-flight, soft-deleted,
    orphaned references
7.  External deps — 3rd-party down, rate-limited, schema change,
    expired cert
8.  UX / accessibility — screen reader, keyboard-only, no JS,
    slow 3G, offline, dark mode, i18n
9.  Abuse / security — spam, brute force, privilege escalation,
    PII in logs, CSRF/XSS
10. Observability — how do we detect this in prod? missing metric?
    missing alert? missing runbook link?
11. Rollback — how do we undo this release in 10 minutes?
12. AI-specific (if LLM) — prompt injection, hallucination,
    off-topic drift, PII echo, cost runaway, eval regression

OUTPUT
- A markdown table with all findings.
- A prioritized list of the top 5 "MUST address before build" gaps.
- Concrete PRD edits (diff-style) to close each top-5 gap.

Be specific and ruthless. Assume the author was optimistic.
```
