# Bad vs AI-Ready Requirement Examples

Use these as templates when rewriting fuzzy requirements. The pattern is consistent: replace adjectives with thresholds, add a measurement method, and reference the test or eval that proves it.

| # | ❌ Bad | ✅ AI-ready |
|---|---|---|
| 1 | "The system should load quickly." | **FR-12 (MUST):** The dashboard p95 time-to-interactive MUST be ≤1.5s on a throttled 4G connection (1.6 Mbps, 150ms RTT) for payloads ≤200KB. Measured by Lighthouse CI on every PR. |
| 2 | "Users can reset their password easily." | **FR-7 (MUST):** Given user clicks "Forgot Password" and enters email, When email matches an active account, Then system sends a single-use reset token (TTL 15 min) within 2 min; When email doesn't match, Then system returns the same generic success message (do not reveal account existence). |
| 3 | "Improve search to be more relevant." | **FR-3 (MUST):** Full-text search over `products.title` and `products.description` using Postgres `tsvector`. Top 20 results ordered by `ts_rank_cd` DESC, then `updated_at` DESC. MUST handle empty query (return `[]`), >256 chars (truncate), unicode/emoji (NFKC-normalize). Eval: ≥85% nDCG@10 against `/evals/search_v1.jsonl`. |
| 4 | "The chatbot should be helpful and accurate." | **FR-9 (MUST):** Correctness ≥92% on `/evals/support_qa.jsonl` (300 items) scored by GPT-4o-as-judge with rubric v2. Mandatory guardrails: 100% pass on `evals/pii_leak.jsonl`; refusal pattern `I can only help with <scope>` for off-topic. Soft: 40–180-word response, friendly-professional tone. |
| 5 | "Add admin permissions." | **FR-15 (MUST):** Role `org_admin` with RBAC matrix: [User/invite → allowed if same org_id]; [User/delete → allowed if target.role ≠ owner]; [Billing/view → denied]. Out of scope: custom roles, per-resource ACLs. Every admin action emits event `admin.user.*` with `{actor_id, target_id, org_id, timestamp}`. |

## Rewrite recipe

1. **Find the fuzzy adjective.** Fast, easy, intuitive, robust, modern, seamless, helpful, accurate, secure, reliable.
2. **Pick the unit.** Time, percentage, count, byte size, rank, error rate.
3. **Set a baseline and target with a date.** "p95 ≤ 1.5s by 2026-Q2" beats "fast".
4. **Name the measurement tool.** Lighthouse CI, k6, pgbench, eval set + judge, custom Prom metric.
5. **Add the failure path.** What does the system do when it can't meet the threshold? (Degrade, queue, error code, retry.)
6. **Wire to a test.** Cite the test file and name, or the eval dataset path.
