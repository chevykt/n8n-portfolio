# W1 plan: Webhook intake, duplicate protection, AI triage, approval gate

Status: draft, waiting for Keon's OK. Nothing built yet.

## Nodes (in order)

1. **Webhook: Contact Form** (POST `/contact-intake`). Uses Header Auth with the shared secret `X-Intake-Secret`. n8n rejects requests without the secret (403) before any node runs.
2. **Code: Normalize + Idempotency Key**: trims and lowercases the fields. `key = event_id` if present, otherwise `sha256(email|subject|message)`.
3. **Data Table: Find Key** (`w1_seen_events`) → **IF: Already Seen?**
   - Yes → **Log: duplicate_skipped** → respond 200 `{status:"duplicate"}`. No LLM call, no email.
   - No → **Data Table: Insert Key**, then continue.
4. **Respond 202 `{status:"accepted"}`**. The caller gets its answer right away and the rest runs async. This matters because the approval wait can take hours.
5. **Anthropic: Classify + Draft** (Claude Haiku, the cheapest model). `max_tokens` 600, temperature 0. The prompt asks for JSON only:
   `{intent: sales|support|billing|spam|other, urgency: low|medium|high, summary: str<=200, reply_draft: str<=1200}`
6. **Code: Validate Schema**: parses the JSON and checks enums, types and lengths. Also captures `usage.input_tokens/output_tokens`.
   - Invalid → **retry once** with a stricter "JSON only" prompt → validate again → still invalid → **Manual Review** path: email to the inbox, status `manual_review`. Nothing is sent.
7. **Gmail: Send and Wait for Approval**: n8n's built-in Approve / Reject buttons, emailed to the portfolio Gmail. It shows the intent, the urgency and the draft. Times out after 24h, which logs `approval_timeout`.
8. **IF: Approved?**
   - Approve → **Gmail: Send Reply**. The recipient is always the portfolio Gmail (plus-alias such as `you+customer@`), never a real third party.
   - Reject → nothing is sent.
9. **Log Run** (`w1_runs` data table) at each exit: `run_id, idem_key, status, intent, urgency, approver_decision, tokens_in, tokens_out, received_at, decided_at, execution_id`.

## Data stores

n8n **Data Tables**, built into n8n: no Google OAuth, and they live inside the trial. Limit: they have no unique constraint, so the dedup is find-then-insert. Two identical webhooks in the same millisecond could both pass. That is documented as a known limit; the fix at scale is Postgres `INSERT ... ON CONFLICT DO NOTHING`.

## Failure cases handled

Missing or wrong secret, duplicate event, malformed LLM output (retry, then manual review), LLM API error (W4 error handler, attached later), approval timeout, rejection.

## Tests (real executions, evidence saved to docs/w1-results.md)

| # | Test | Expected |
|---|---|---|
| T1 | Valid payload | 202, approval email arrives, approve → one reply |
| T2 | Same payload again | 200 duplicate, no LLM call, no email, logged |
| T3 | No secret header | 403, nothing logged, no execution past the webhook |
| T4 | Malformed LLM output (pinned bad JSON via test mode) | retry once → manual_review, nothing sent |
| T5 | Reject the draft | no reply sent, `approver_decision=rejected` logged |

## Cost per run (estimate, confirmed after T1)

About 400 input + about 300 output tokens on Haiku: a fraction of a cent. A retry doubles it at most.

## Needs from Keon before build

- Credentials created in the n8n UI (I can't create credentials and must never see secrets):
  `Anthropic – portfolio`, `Gmail – portfolio` (OAuth), `Header Auth – W1 intake secret`.
- The trial limits from **Settings → Usage and plan** (no tool exposes them).
