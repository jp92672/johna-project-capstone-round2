# Project | Capstone | Round 2 | Use Case Definition
### John Adams

**Client:** Chleo Hair Studio, an independent premium hair salon in Prenzlauer Berg, Berlin
**Sector:** Hair and beauty services (Germany) · **Company size:** Micro business

## Business problem statement

Chleo Hair Studio runs on a single booking tool, but most bookings do not go through it. Clients call, message on WhatsApp, email, or DM the owners directly, and each of those channels lands on one of two working owners instead of a system. The owners manually check availability, explain when the salon is full, and arrange alternate dates by hand, on top of doing the hair services themselves.

A second, related problem sits on the client-retention side. The salon has a loyal client base and an upscale retail line, but nothing prompts a client to rebook or reorder. Repeat visits and repeat product purchases happen only through habit and word of mouth, not through any structured outreach.

Both problems share one root cause: a two-person, no-employee business has no spare admin capacity, so any fix has to run itself.

## Company profile (industry, size, current state)

- **Industry:** Hair and beauty services, high-end independent segment.
- **Size:** Micro business. Two owner-operators, two independent chair renters, zero employees. Four chairs total, open Tuesday to Saturday.
- **Pricing:** Cuts €90 to €115, color services €145 to €275.
- **Current state:** Bookings are fragmented across a scheduling tool plus phone, WhatsApp, email, and Instagram DMs, with no unified system connecting bookings, payments, and retail. No-shows and late cancellations happen weekly to biweekly, offset only by a 50% late-cancellation/no-show fee that does not recover the lost chair time. Client and purchase history exist only in the owners' heads and informal notes, not in any structured record. Retail revenue, despite carrying premium brands (Head On, Wella, Davines), is light and inconsistent relative to the salon's client loyalty and reputation.

## Proposed AI solution and system type

**Use Case 1, Booking and Inquiry Assistant.** A conversational AI agent (LLM with tool-calling access to a calendar and a client database) that answers booking, availability, reschedule, and cancellation requests over WhatsApp and website chat, and a separate deterministic booking flow on the website for clients who prefer a direct click-through form. System type: conversational agent with function-calling / tool-use architecture, backed by a workflow automation backbone (n8n) rather than a custom-coded application.

**Use Case 3, Retention and Re-Engagement Automation.** A scheduled workflow that reads client visit and purchase history from a CRM, applies threshold rules (for example, time since last visit, time since last product purchase) to identify clients due for outreach, and has an AI component draft the outreach message before it is sent by email. System type: rule-based trigger logic paired with AI-generated natural-language content, not a conversational agent.

Both use cases share one CRM (Airtable) as their data layer, so a booking recorded by Use Case 1 becomes visit history Use Case 3 can act on later.

## Key stakeholders and interests

- **Salon owners (2 owner-operators).** Want the manual booking triage and retention outreach removed from their own time without adding headcount, new fixed costs, or operational complexity they cannot maintain themselves.
- **Chair renters (2).** Renters are not to be affected by any scheduling system the salon adopts. Their interest is that the system does not centralize scheduling control over their own client relationships in a way that could affect their independent-contractor status (false employment status).
- **Clients.** Want a fast answer to "can I book," "can I reschedule," or "can I cancel" without waiting on the owners, and want any automated outreach to feel personal rather than generic marketing. Also have a direct interest in their own personal data being handled correctly and not disclosed to anyone else.
- **Regulatory and compliance interest.** The system handles personal data (names, contact details, appointment history) for identifiable individuals in the EU, which brings GDPR and, depending on final classification, EU AI Act obligations into scope. Full treatment of this is deferred to `compliance/eu_ai_act_compliance.md` and `compliance/gdpr_documentation.md`.

## Success criteria

1. **Booking assistant resolution rate.** The share of inbound booking, reschedule, and cancellation requests (WhatsApp and website chat combined) that the assistant resolves without an owner needing to step in. **Target: 30 percent**, set at the sector's average online booking rate (top-performer rate is 59 percent), to be revised during a pilot period once the salon's own baseline is known (Zenoti operational benchmark data, cited in `Round 1 artifacts/research/sector_research.md`).
2. **Chair utilization.** Staff/chair utilization percentage, tracked from appointment records once the MVP has live usage. Sector benchmark is 67 percent average versus 84 percent for top performers, worth roughly €7,442 per utilization point per year at this salon's pricing (`Round 1 artifacts/research/sector_research.md`).
3. **Retention outreach response rate.** The share of "haven't seen you in a while" or reorder-reminder messages (Use Case 3) that result in a rebooking or repeat purchase within a 30 day window after send. **Target: 5 percent**, set above the low end of the standard 2-5 percent win-back email conversion benchmark (retail win-back campaigns average 3.8 percent) since these messages come from a known, personal salon relationship rather than a cold retail list — to be revised once real send data exists. Sources: [Eightx, "Win-back and reactivation rate benchmarks for DTC"](https://eightx.co/blog/average-win-back-reactivation-rate-benchmarks); [Email Calculator, "Email Winback Conversion Rate"](https://emailcalculator.com/glossary/email-winback-conversion-rate).

**Note:** Given some of the research data points are North American or Germany as a whole, metrics and KPIs will be adjusted to this client's specific performance baselines once a business emersion workshop has taken place and we can be more deterministic in our measures.

## Out-of-scope boundaries

- **Use Case 2, No-Show and Cancellation Recovery**, is formally descoped for Round 2. Recorded in `Round 1 artifacts/feedback/round1_decision.md` after it was flagged as too complex to build as a working MVP in the available timeframe.
- **Reverse sync from the calendar back to the CRM.** If an appointment is cancelled directly in the calendar rather than through the assistant, the CRM record does not update automatically. Accepted as an MVP limitation.
- **Simultaneous double-booking of the same slot.** No server-side lock exists between two clients viewing and confirming the same open slot at the same moment. Accepted as an MVP limitation given the salon's scale.
- **Payment and cancellation-fee handling.** The 50% late-cancellation/no-show fee process referenced in the business problem is not automated by either use case in this MVP.
- **Staff-side authentication.** There is no login system distinguishing an owner or stylist from a client in the assistant itself; the assistant is built to refuse staff-framed requests rather than to authenticate genuine staff members.
- **Full recommendation engine for retail.** Use Case 3's product-matching logic is simple rule-based pairing by service type, not a trained recommendation model.
- **Real client data.** Per the project brief's constraint, only synthetic or the consultant's own test data is used. No real Chleo Hair Studio client exists.

## Note on how this evolved from Round 1

The industry, company profile, and all three originally proposed use cases held up in the Round 1 pitch. The decision recorded in `Round 1 artifacts/feedback/round1_decision.md` was **KEEP**: no change to sector, company size, or client scenario. The only adjustment made after Round 1 was which use cases would become the working MVP. Use Case 2 was descoped per feedback; Use Cases 1 and 3 were confirmed as in scope for Round 2.

Two build-level changes are worth noting for continuity, though neither changes the use case definitions themselves:

- The Round 1 POC and its n8n Data Table were built around Use Case 3 (retention and re-engagement). For Round 2, the CRM layer was migrated to an Airtable base shared by both use cases, and Use Case 1 (booking) became the first use case built out to a working MVP, with WhatsApp (Twilio), website chat, calendar writes, and LangSmith monitoring.
- The originally proposed calendar backend (Acuity, standing in as Calendly for the build) was swapped to Google Calendar during the MVP build, after the connected Calendly account's booking-creation API required a paid plan the trial no longer covered. This is a tooling substitution, not a change to the use case itself: the assistant still checks and books against a calendar.
