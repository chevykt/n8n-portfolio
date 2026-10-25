# Paste-ready summary for applications

## One paragraph

I built a portfolio of five n8n workflows that cover what automation roles usually ask for. They include a webhook intake with idempotency and a human approval gate before any AI-drafted reply is sent. They also include a daily change-detection pipeline that compares stored hashes in one read instead of checking records one by one. There's a Claude tool-using agent with hard iteration, token and time limits, and a reliability layer: a global error handler, a reusable retry sub-workflow with exponential backoff, and an hourly health check. Finally, a per-client workflow where adding a client is a new config row, not new code. The data pipelines and error handling were run live against the public USGS earthquake API. The AI and email steps were tested with mocked responses, and I document exactly what is and isn't proven. Repo: <link>

## Bullets

- **Webhook intake (n8n + Claude):** shared-secret auth, idempotency key so duplicate requests do nothing, Claude output validated against a schema with one retry, email approval before any reply.
- **Change-detection pipeline:** daily USGS feed, per-record hashing, one windowed comparison instead of per-record lookups, upsert + duplicate-proof history, one batched alert and a daily report. Verified live: a rerun produces zero alerts.
- **AI agent with guardrails:** explicit Claude tool-use loop with a 5-turn cap, 6,000-token budget and 60 s timeout; live API and database tools; drafts only, and a human approves any send.
- **Reliability:** global error workflow, reusable HTTP retry sub-workflow (2s/4s/8s backoff), and an hourly health check. A real production failure was caught, logged and linked.
- **Multi-tenant config:** one workflow serves every client from a config table. A new client went live by adding one row.
- **Docs:** setup under 30 minutes per workflow, test evidence, and an explicit "not proven" list.
