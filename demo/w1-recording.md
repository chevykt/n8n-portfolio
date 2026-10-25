# W1 screen recording (about 60-90s)

Open the workflow in n8n, then its **Executions** tab. Each execution highlights the path it took.

| Time | Show | Say (roughly) |
|---|---|---|
| 0:00 | Canvas, groups collapsed | "A contact form hits this webhook. It's protected by a shared-secret header." |
| 0:10 | Expand **1. Intake + validation** | "Fields are normalized and an idempotency key is built from the event ID, or a hash of the content." |
| 0:20 | Execution **#2** | "Same event sent twice: the second run is found in the data table, returns 200, and never calls the AI." |
| 0:35 | Execution **#5** | "New event: Claude Haiku returns structured JSON, a Code node validates it, and a human gets an Approve / Reject email. Only after approval is the reply sent." |
| 0:50 | Execution **#1** | "If the model returns bad output, it retries once with a stricter prompt. If it fails again, it goes to manual review and nothing is sent." |
| 1:05 | Execution **#4** | "A rejected draft sends nothing." |
| 1:15 | Data table **w1_runs** | "Every run is logged with its status, the approver's decision and the tokens used." |

## What to say about keys

"In this demo the Claude and Gmail calls are mocked with n8n's test data, because no live keys are attached. Everything else ran for real: the validation, the duplicate check against the data table, the routing and the logging. Plugging in an Anthropic key and a Gmail account is a credential setting, not a code change."

## Don't claim

- That it was run against the live Claude API or sent real emails.
- That the 403 secret check was tested. n8n enforces it, but it wasn't exercised.
