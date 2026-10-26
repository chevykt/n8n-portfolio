# Demo video script: long version (about 4.5 minutes)

The 2-3 minute cut is [script-2-3min.md](script-2-3min.md).

Before recording: open the n8n workflows list, the Data tables tab, and these executions in separate tabs:
- W1: #2, #5
- W2: #7, #8, #9
- W3: #34, #40
- W4 demo: #13, plus the w4_errors table
- W5: #27, #29

The execution numbers are the ones in each workflow's README.

| Time | Screen | Say |
|---|---|---|
| 0:00 | Repo README | "I'm Keon, a developer in Quezon City. This is a set of five n8n workflows I built to show the things automation roles ask for: safe webhooks, AI with guardrails, pipelines and monitoring." |
| 0:20 | **W1** canvas | "A contact form hits this webhook. It needs a shared secret. I build an idempotency key from the event ID, so the same request twice is processed once." |
| 0:35 | W1 execution #2 | "Here's the second copy of a request: found in the data table, answered 200, and Claude never ran." |
| 0:50 | W1 execution #5 | "A new request: Claude classifies it and drafts a reply as JSON, a Code node validates it, and a person gets an Approve / Reject email. Nothing is sent until they approve. Bad AI output gets one retry, then goes to manual review." |
| 1:20 | **W2** canvas, then w2_reports table | "This one watches the USGS earthquake feed every day. Each record is hashed, and the stored hashes are loaded in one read, not one lookup per record. Only changes are written." |
| 1:40 | W2 executions #7 and #8 | "Run it twice in a row: zero changes, zero alerts. Then I fed it a revised magnitude for one quake: one change, the row updated in place, one history entry, one batched alert." |
| 2:00 | W2 execution #9 | "If the API is down, it retries three times and records the failure in the report. It never fails silently." |
| 2:20 | **W3** canvas | "An AI agent with tools: live quake data, a client lookup, and email drafts. I built the loop explicitly so every limit is code I can show you: five turns, a 6,000-token budget, a 60-second timeout." |
| 2:40 | W3 execution #34 + w3_runs | "I forced it to loop forever. It stopped at turn five and logged why, with tokens per turn." |
| 2:55 | W3 execution #40 | "The agent can only draft email. Sending needs a human approval, and here a rejection means nothing went out." |
| 3:10 | **W4** demo execution #13, then w4_errors | "Every workflow points to one error handler. Here a job calls my retry sub-workflow against a dead host: three tries with growing waits, then it fails, and the handler logs the workflow, the node, the error and a link straight to the run." |
| 3:35 | **W5** canvas + w5_clients table | "Client behaviour lives in a table: region, threshold, tone, on/off. Same workflow for everyone." |
| 3:50 | W5 executions #27 → add row → #29 | "This client didn't exist, so it got a 404 and an error log. I added one row, sent the same request, and got a Japan digest in their tone. No workflow edits." |
| 4:10 | NOT-PROVEN.md | "One thing I solved: n8n's AI Agent node hides its token usage, so I built the loop myself to enforce a real budget. And to be upfront: Claude and Gmail are mocked in the AI tests, because no live keys are attached. Everything else ran for real. The repo link is in the description." |
| 4:30 | end | |

## Tips

- Record the canvas with groups collapsed, then expand one group per point.
- Zoom the browser to 110%, so node names are readable.
- Execution numbers may shift if you rerun things. Check each README's table before recording.
