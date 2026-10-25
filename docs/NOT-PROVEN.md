# What this project does NOT prove

Read this before an interview. Everything below is true of the project as built on 2026-10-25.

## Not run at all

- **No live Claude API call.** W1 and W3 were tested with pinned (fake) Claude responses. The prompts, model id (`claude-haiku-5-5`), request format and response parsing were never checked against a real key. A real model may format output differently from the fixtures.
- **No email was sent or received.** Every Gmail node was pinned or disabled. The Approve / Reject buttons of n8n's "send and wait" were never clicked by a person. The approval logic was tested by faking the approval result.
- **W1's 403 on a missing secret** is n8n's built-in Header Auth. It was not exercised.
- **W3's 60-second timeout** branch was never reached in a test.
- **A real multi-turn agent conversation** (tool call, then answer) was not run. Pinned data returns the same reply every turn, so W3's tests cover the loop, the limits and each exit separately.
- **Scheduled runs.** W2 (daily) and the W4 health check (hourly) were run by hand. Neither schedule has fired: W2 isn't published, and the health check can't be published without a Gmail credential.

## Proven, with limits

- **W2's "scale" story** is a design argument. The real data was 182 records per run, not 400,000. The approach (one windowed read, in-memory diff, writes only for changes) is sound, but it was not load-tested.
- **Duplicate protection** uses n8n Data Tables, which have no unique constraints. Sequential duplicates are blocked (tested); simultaneous duplicates within milliseconds could slip through. The fix is a database unique key.
- **Webhooks for W4-demo and W5 have no auth.** They are published and only return public data, but in production they should use Header Auth like W1.

## Proven for real

- W2: live fetch, hashing, diff, upsert, history, failure path and report against the real USGS feed (5 runs).
- W4: a real production failure travelled retry sub-workflow → job failure → global error handler → log row. The retry backoff timings were measured (9.2 s for 3 attempts).
- W5: live production webhook runs for 4 sample clients; a new client was added as one row and worked with zero workflow edits; unknown clients fail into the W4 error log.
- W1 and W3 logic: validation, retry, routing, idempotency, iteration and token limits, and logging all ran for real around mocked AI and email calls.

## Out of scope (don't claim)

OpenClaw, Hermes, Zoho CRM, Shopify, ManyChat, GoHighLevel, Slack, Postgres.
