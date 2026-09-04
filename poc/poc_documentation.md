# Project | Capstone | Round 2 | POC Documentation
### John Adams

**Use case:** Use Case 3, Retention and Re-Engagement Automation.
**Status:** This is the Round 1 proof of concept, carried forward unchanged into Round 2 as the required no-code/low-code POC. It sits earlier in the plan than the working MVP: see Phase 1 in `strategic_plan.md`. The full Round 2 MVP (both use cases, real WhatsApp, real calendar, real CRM) is documented separately in `mvp/mvp_documentation.md`.

## Workflow export

`Round 1 artifacts/n8n/workflow.json`. The original write-up is in `Round 1 artifacts/n8n/workflow_documentation.md`.

## Demo recording

Demoed live at the Round 2 presentation, consistent with Round 1. No standalone recording file is included in this repo.

## Tools

n8n (self-hosted), an OpenAI model node (GPT-4o-mini) for message drafting, Resend for email delivery, and an n8n Data Table as the client data source.

## Steps

1. **Get Retention Clients** reads every row from a Data Table (`salon_retention_clients`): name, email, last visit date, last purchase date, last purchase category.
2. Two independent checks run per client: **Needs Rebooking?** (true if last visit was more than 8 weeks ago) and **Needs Reorder?** (true if last purchase was more than 30 days ago). A client can trip neither, one, or both.
3. For each tripped check, an AI Agent node drafts a short, on-brand message: a "we've missed you" nudge for rebooking, a product-specific reminder for reorder.
4. An Override Email For Test node redirects every send to one real test inbox, a demo-only safety measure, not production behavior.
5. Resend sends the drafted message as a real email.

## AI capability shown

Real AI-drafted natural-language messages generated from real client data and real conditional logic, not hardcoded templates or a hypothetical description. Tested live against 5 sample clients; results and a sample message are in `Round 1 artifacts/n8n/workflow_documentation.md`. A second live run confirmed actual email delivery via Resend, with real message IDs returned and visible in the test inbox.

## Limits vs. production

- **Test inbox, not real clients.** The Override Email For Test node sends every message to one inbox instead of each client's own address. Production removes this node.
- **Test sender, not a verified domain.** Uses Resend's zero-setup `onboarding@resend.dev` sender. Production needs a domain verified under Chleo's own name (the Round 2 MVP already does this, see `mvp/mvp_documentation.md`).
- **Data source is a manually-entered Data Table, not the salon's real booking system.** The Round 2 MVP replaces this with a real Airtable CRM populated from live bookings.
- **No human approval step.** Messages send automatically here to demonstrate the logic. A production rollout would likely add owner review for at least the first weeks of use.
- **No cooldown, cap, or opt-in logic.** A client tripping the same threshold repeatedly would get the same message every run. The Round 2 MVP adds cooldown tracking, a lifetime send cap, and client opt-in gating before any message goes out, see `mvp/mvp_documentation.md`.

## How to reproduce

1. Import `Round 1 artifacts/n8n/workflow.json` into an n8n instance (self-hosted or cloud).
2. Create a Data Table named `salon_retention_clients` with columns: `name`, `email`, `last_visit_date`, `last_purchase_date`, `last_purchase_category`. Add sample rows.
3. Connect an OpenAI credential and a Resend credential (HTTP Header Auth).
4. Set the Override Email For Test node's value to a test inbox, or remove it to send to each client's real address.
5. Run the Start (manual trigger) node.

## How this relates to the Round 2 MVP

This POC proved the retention concept could run and produce real, working output. It did not prove the concept could run against real client traffic, avoid repeat-spamming a client, or respect a client's own opt-in preference, none of that logic existed yet. The Round 2 MVP rebuilds this use case against a real Airtable CRM with cooldown, lifetime-cap, and opt-in gating, and adds a second, related capability (24h/12h appointment reminders with a real cancellation policy) that did not exist in this POC at all. Full detail in `mvp/mvp_documentation.md`.
