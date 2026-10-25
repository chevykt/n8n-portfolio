# W1 test results

Workflow: `W1 - Contact Intake: Dedupe + AI Triage + Approval` (25 nodes).

## Stage 1: logic tests with pinned external calls (2026-10-25)

Run with n8n test mode: webhook, Claude HTTP calls and Gmail nodes return pinned (fake) data. Everything else ran for real, including the Code validator, the IF routing and both data tables.

| Test | Exec | Input | Result | Pass |
|---|---|---|---|---|
| T4 malformed LLM output | 1 | Claude returns prose; retry returns `intent: "refund"` | `not_json` → retry → `bad_intent` → manual review path; approval email node never ran; logged `manual_review`, tokens 670 in / 52 out (both calls summed) | yes |
| T2 duplicate event | 2 | same `event_id` as exec 1 | `Already Seen?` true → 200 duplicate; Claude node not executed; logged `duplicate_skipped`, 0 tokens | yes |
| Invalid payload | 3 | bad email, empty message | 400 response; no data-table read/write, no Claude call | yes |
| T5 rejected draft (logic) | 4 | valid JSON wrapped in ```json fences; approval pinned `approved:false` | fences stripped, validated; `Send Approved Reply` not executed; logged `not_sent` / `rejected`, 330 / 70 tokens | yes |
| T1 approved draft (logic) | 5 | valid JSON; approval pinned `approved:true` | `Approved?` true → reply node → logged `sent` / `approved`, 342 / 88 tokens | yes |

Executions 1-5 are visible in the workflow's **Executions** tab; each one highlights the path it took on the canvas.

## Not run: live tests (real Claude, real Gmail)

Deliberately out of scope: the workflow is a portfolio piece and no live keys are attached. Credential slots are left empty. What a live run would add:

- the real Claude response shape and model id (`claude-haiku-5-5`) against a real key
- the real Gmail approval email and button callback
- T3 (missing `X-Intake-Secret` → 403), which is enforced by n8n's webhook Header Auth and was not exercised

## Known limits

- Dedupe is find-then-insert on an n8n Data Table, which has no unique constraint. Two identical requests within milliseconds could both pass. At scale: Postgres `INSERT ... ON CONFLICT DO NOTHING`.
- Invalid-payload (400) requests are not logged to `w1_runs`; n8n's execution list still records them.
- Logic tests used pinned data; the real Claude response format and the Gmail approval buttons are verified only in stage 2.
