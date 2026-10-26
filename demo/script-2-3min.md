# Demo video script: short version (about 2.5 minutes)

Use this when a job post asks for a quick video, or as the first link in an application. The longer cut is [script-4-5min.md](script-4-5min.md).

Before recording, open these tabs:
- the repo README
- W1 execution #2
- W2 executions #7 and #8
- W3 execution #34
- W4 demo execution #13 and the `w4_errors` table
- W5 executions #27 and #29, plus the `w5_clients` table

| Time | Screen | Say |
|---|---|---|
| 0:00 | Repo README | "I'm Keon, a developer in Quezon City. These are five n8n workflows I built around what automation roles ask for: safe webhooks, AI with guardrails, data pipelines, and monitoring." |
| 0:15 | **W1** canvas, then execution #2 | "A contact form hits this webhook. I build an idempotency key from each request, so when the same request comes in twice, the second one is skipped. Claude never runs and no reply goes out. When Claude does draft a reply, it's validated as JSON and a person has to approve it before anything is sent." |
| 0:45 | **W2** executions #7, then #8 | "This pipeline checks the USGS earthquake feed every day. It hashes each record and compares them all in one read, not one lookup per record. Run it twice and you get zero changes and zero alerts. Change one record and you get exactly one update and one alert." |
| 1:15 | **W3** execution #34 | "This is an AI agent with tools and hard limits. I forced it into an endless loop, and it stopped at turn five and logged why. It also has a token budget and a timeout, and it can only draft emails, never send them on its own." |
| 1:40 | **W4** execution #13, then `w4_errors` | "Every workflow reports to one error handler. Here a job hits a dead server: it retries three times with growing waits, then fails, and the handler logs the workflow, the node, the error and a link to the run." |
| 2:00 | **W5** execution #27, the `w5_clients` table, then #29 | "Client settings live in a table. An unknown client gets a 404 and an error log. I added one row, sent the same request, and got that client's digest. No workflow changes." |
| 2:20 | README | "Claude and Gmail are mocked in these demos because no live keys are attached. Everything else ran for real. The repo link is below." |
| 2:30 | end | |

## Tips

- Keep each workflow's canvas groups collapsed. You only need to show the execution, not the internals.
- Say the numbers ("zero alerts", "turn five", "one row"). They're what people remember.
- Check each README's test table first, in case an execution number changed.
