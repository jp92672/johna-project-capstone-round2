# Project | Capstone | Round 2 | MVP Documentation
### John Adams

**Client:** Chleo Hair Studio, an independent premium hair salon in Prenzlauer Berg, Berlin

## Repo structure

This folder (`mvp/`) holds the MVP itself. See the repo root [README.md](../README.md) for how it fits into the rest of the submission.

```
mvp/
├── mvp_documentation.md   this file
├── website/               home.html, book.html, manage.html, assets/
├── n8n workflows/         exported JSON for all four workflows (see below)
└── outputs/               airtable_schema.md, plus point-in-time CSV exports of Clients, Appointments, Error Log
```

Root-level items referenced elsewhere in this doc: `../Round 1 artifacts/` (unchanged, kept per submission requirements), `../requirements.txt` (Python deps for the Round 1 LangSmith notebook only, the MVP itself is n8n + static HTML), `../.env.example` (env vars used by the Round 1 notebook and the n8n host config).

## What runs, and where

The MVP is n8n workflows plus a static website, not a Python or JS application. There is no local server to start beyond opening the HTML files or pointing them at the live n8n webhooks.

**Use Case 1, Booking and Inquiry Assistant** — n8n workflow `Capstone R2 - Use Case 1 Booking Assistant` (`wmG4kUCinU0rG269`), two entry points feeding one shared agent:
- Twilio Trigger (WhatsApp/SMS)
- Webhook `chleo-booking-chat` (website chat, used by `website/book.html`)

Also `Capstone R2 - Direct Booking API` (`DvDUoIikgHO0ySnk`), a separate, fully deterministic workflow with no LLM involved:
- `GET /webhook/chleo-availability` returns real open slots computed from Google Calendar and business hours
- `POST /webhook/chleo-direct-book` takes an exact service/time/contact payload and books it directly

**Use Case 3, Retention and Re-Engagement** — n8n workflow `Capstone R2 - Use Case 3 Retention & Re-Engagement` (`IZOvgzAIouXSfEh1`), on a 3x/day Europe/Berlin schedule. Reads Airtable, drafts and sends rebooking nudges, reorder reminders, and 24h/12h appointment reminders.

**Shared error handling** — `Capstone R2 - Error Alert` (`n2qpIdNehXFzbFTg`), wired as the `errorWorkflow` setting on all three production workflows (`Use Case 1 Booking Assistant`, `Direct Booking API`, and `Use Case 3 Retention & Re-Engagement`).

**Data layer** — Airtable base `Chleo Hair Studio CRM` (`appE1hAG2NEl8YdkX`): `Clients`, `Appointments`, `Error Log` tables. Schema in `outputs/airtable_schema.md`.

**Calendar** — a real Google Calendar (Google Cloud project `n8n-ironhack`), standing in for the salon's booking calendar.

## Core AI capability

A conversational agent (n8n AI Agent node, GPT-5-mini) with tool-calling access to the calendar and the CRM, handling booking, rescheduling, cancelling, and availability questions over WhatsApp and website chat in natural language, not a fixed menu of options. A second AI capability drafts the retention emails (rebooking nudges, reorder reminders) in Use Case 3, computed elapsed-time figures come from a deterministic expression, not the model, but the message copy itself is model-generated per client.

Both capabilities are demonstrably real: live-tested end to end multiple times, with real Google Calendar events created, real Airtable records written, real WhatsApp messages exchanged via Twilio, and real emails delivered via Resend. See `working_files/session_notes_2026-09-02.md` and `working_files/session_notes_2026-09-03.md` for the specific test executions and what each one confirmed.

## Basic error handling

- **Retries.** Every node calling out to Airtable, Google Calendar, Twilio, Resend, or OpenAI across all three production workflows retries automatically (3 tries, 2 second wait) before failing, so a transient timeout or rate limit self-heals instead of breaking the run.
- **Detection and notification.** Any unhandled failure in any of the three production workflows triggers the shared Error Alert workflow (`n2qpIdNehXFzbFTg`), which writes a row to Airtable's `Error Log` table first, then sends the owner an email generated from that row — Airtable is the source of truth, the email just reflects it.
- **Client-trace reporting.** On the client-facing points most likely to fail mid-conversation — `Send Reply (Twilio)` and `Respond to Website` in the Booking Assistant, `Create Direct Booking Event` in the Direct Booking API, and `Send Rebooking Email`/`Send Reorder Email` in Retention & Re-Engagement — the node is set to continue past an error instead of crashing, captures the client's actual phone/email/message or booking request at that exact moment, and forwards it into the Error Alert workflow's second entry point (`Report Failure (Rich)`, an Execute Workflow Trigger). The original passive Error Trigger (`On Any Workflow Failure`) still covers every other node in all three workflows as a fallback net — it just reports without client context when it fires.
- **Two-audience alert email.** The email (and the `Error Log` row behind it) is split into two clearly labeled sections rather than one dump of raw error text: **FOR THE OWNER** — who the client was (`Client Contact`), what they wanted (`Client Message`), a plain-language explanation of what went wrong (`Owner Summary`, e.g. "we couldn't confirm this client's booking — they think they have an appointment but it was never created"), an explicit call to contact the client directly, and a direct link to the Airtable record. **FOR THE DEV** — the workflow/node name, the raw error message, the n8n execution ID and link, and a `Relevant Tool` hint pointing at whichever external system is actually worth checking for that failure point (Google Calendar/Cloud Console for booking creation, Twilio Console + LangSmith for the WhatsApp/website reply path, Resend dashboard + Airtable for the retention emails). Proven live across multiple induced failures: see `outputs/airtable_export_Error Log.csv`.
- **What this does not cover.** No silent-fail audit of the remaining nodes not wired for rich reporting, no fallback LLM provider if OpenAI itself is down, no alert throttling (a real outage sends one email per failed execution, not a summary), and no external uptime monitoring (n8n being down entirely can't be caught from inside n8n). This tier was explicitly deferred: the MVP is not yet taking real client traffic, so it was judged not worth building ahead of need.

## Known limitations

- **No reverse sync from Google Calendar to Airtable.** Deleting or editing an event directly in the calendar does not update the matching Airtable record.
- **No lock against simultaneous double-booking** of the same slot on the direct-booking form.
- **WhatsApp send for retention reminders is built but not wired.** Cooldown, cap, and opt-in logic all support a WhatsApp channel; only the email send path is implemented.
- **`manage.html` is not linked from site navigation.** A scope decision, not a bug: the WhatsApp and website chat agents already handle reschedule and cancel requests directly, so a separate self-service page was judged unnecessary for now. The page itself still works if reached by a direct URL.
- **Retention emails (Use Case 3) are hard-routed to the developer's inbox, not real client addresses.** An `Override Email For Test` node rewrites every send target to `dev-test@example.com` before the rebooking-nudge and reorder-reminder emails go out (the 24h/12h appointment reminders are unaffected). This is deliberate, not forgotten: the client records are synthetic dummy data with fake `@example.com` addresses, so sending to the real stored email would just bounce. Removing this override and pointing sends at real client emails is a "go live" step, out of scope for this MVP pass.

## Credentials and setup

Airtable, Google Calendar, Twilio, Resend, and n8n's own OpenAI model connections are configured as n8n credentials through the n8n UI or API, they are not environment variables in this repo. `.env.example` lists what actually is repo-level config: the Round 1 LangSmith notebook's keys, and the handful of env vars n8n's own host (`~/.config/n8n/docker-compose.yml`) needs to run with tracing and a stable public webhook URL.

To stand this up fresh: an n8n instance (self-hosted or cloud), a Google Cloud project with the Calendar API enabled, a Twilio account with WhatsApp sandbox access, a Resend account with a verified sending domain, an Airtable base matching `outputs/airtable_schema.md`, and an OpenAI API key.
