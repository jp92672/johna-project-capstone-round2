# Project | Capstone | Round 2 | GDPR Documentation
### John Adams

**Client:** Chleo Hair Studio, an independent premium hair salon in Prenzlauer Berg, Berlin
**Controller:** Chleo Hair Studio. **Processor (on the controller's instruction):** the consultant, operating the n8n instance and connected tools.

**Open item:** a Data Processing Agreement between the salon (controller) and the consultant (processor) would need to be executed before any real client data is processed. Not yet in place, this is a theoretical engagement with no real client.

## Data flow map

1. A client contacts the salon via WhatsApp, website chat, or the direct-booking form, providing name, phone, email, and optionally free-text notes.
2. Twilio (WhatsApp transport) or the website webhook passes the message into n8n.
3. n8n's AI Agent sends the relevant conversation context to OpenAI to interpret the request and draft a reply.
4. n8n reads and writes client and appointment records in Airtable (`Clients`, `Appointments`), and creates/updates/deletes events in Google Calendar.
5. For Use Case 3, n8n reads client visit/purchase history from Airtable on a schedule, drafts a message via OpenAI, and sends it through Resend.
6. LangSmith receives a trace of the conversation (EU region endpoint) for monitoring.
7. On any workflow failure, n8n emails the owner via Resend and writes a row to the Airtable `Error Log` table (workflow name, error message, execution link, no client personal data in this specific log).

## Processing activities register

| Activity | Purpose | Legal basis | Retention | Recipients |
|---|---|---|---|---|
| Booking, reschedule, cancel (Use Case 1) | Deliver the appointment service the client requested | Contract (Art. 6(1)(b)), necessary to perform the requested booking | 24 months after the client's last visit, no statutory minimum applies (GDPR storage limitation default); financial/invoicing records, if any, are subject to the German 10-year statutory retention under §257 HGB / §147 AO regardless of this default | n8n (processor), OpenAI, Airtable, Google Calendar, Twilio |
| Client notes (client-visible) | Service personalization (e.g. stated preferences) | Contract (Art. 6(1)(b)) | Same as above | Airtable |
| Stylist notes (staff-only) | Internal service quality, never shown to the client or the AI | Legitimate interest (Art. 6(1)(f)), routine service-quality record-keeping | Same as above | Airtable only, structurally excluded from the AI's context |
| Retention and reorder outreach (Use Case 3) | Marketing to existing clients | Consent (Art. 6(1)(a)), explicit per-client opt-in required before any send | Same as above; opt-out or consent withdrawal stops processing for this purpose immediately | Airtable, OpenAI, Resend |
| Appointment reminders (24h/12h) | Reduce no-shows, communicate the cancellation policy | Contract (Art. 6(1)(b)), a service communication tied to a booking already made | Same as above | Airtable, OpenAI, Resend |
| Conversation tracing | Debugging, quality monitoring, transparency | Legitimate interest (Art. 6(1)(f)), operating the system safely and correctly | Governed by LangSmith's own retention settings, not separately configured | LangSmith (EU region) |
| Failure/error logging | Detect and resolve system failures | Legitimate interest (Art. 6(1)(f)) | Governed by Airtable's default retention; no automatic purge configured | Airtable, the owner via email |

## Short DPIA: the conversational booking assistant (Use Case 1)

This is the highest-risk processing activity in the system: a live conversational agent handling identifiable personal data (name, phone, email, appointment history) with a third-party LLM in the loop, and a documented history of a real defect.

- **Special category data (Art. 9).** No special-category data is collected through the booking flow itself. Stylist notes could plausibly reference health-adjacent details (e.g. allergies, scalp conditions), so they are kept staff-only in Airtable and structurally excluded from the AI's context, they are never sent to OpenAI or surfaced to the client, which keeps this data out of the automated processing path entirely.
- **DPIA trigger (EDPB nine criteria).** Two criteria apply, which is enough to require a DPIA: systematic monitoring (conversation tracing on every interaction) and, arguably, evaluation/profiling (Use Case 3 selects clients for outreach based on visit/purchase history). This profiling does not produce a legal or similarly significant effect on the client (it results in a marketing message, not a decision about them), so it does not trigger Art. 22 safeguards, but it is still a form of automated processing worth naming here.
- **Nature of processing.** Real-time conversational handling of contact details and appointment data, sent to an external LLM for interpretation, written to a CRM and a calendar.
- **Necessity and proportionality.** The data collected (name, phone, email, service, time) is the minimum needed to book and manage an appointment. No data beyond this is collected through the booking flow itself.
- **Risk identified.** During pre-launch testing (no real clients involved), a defect allowed the assistant to disclose booking details (name, service, time) in response to a general question like "who's booked Friday?" This was a real, confirmed defect caught before go-live, not a theoretical one, and since no real client data was exposed to an unauthorized party, it does not trigger Art. 33/34 breach notification.
- **How it was addressed.** Two independent layers: the calendar API call itself was changed to strip identifying fields (name, attendees, description) at the data source, so they never reach the AI's context regardless of prompt wording; and an explicit prompt rule was added forbidding disclosure of any other client's information under any framing. Live re-tested against the exact exploit phrasing that originally surfaced the issue, and it now correctly refuses.
- **Residual risk.** Rated Likelihood 2, Impact 5 in `roi_risk_assessment.md`'s risk matrix, a future prompt or config change could reintroduce a similar leak if the same defense-in-depth pattern isn't followed. Mitigation is the two-layer design itself, not a one-time fix.
- **Outcome.** The identified high risk has a documented mitigation already built and verified, not merely planned. No further mitigation is deemed necessary before pilot, subject to the two remaining Article 50 items noted in `eu_ai_act_compliance.md` (WhatsApp disclosure live-test, real Terms/Privacy Policy content) being closed first.

## Data subject rights support

Current support is manual and owner-mediated, there is no self-service portal:

- **Access and rectification.** The owner can look up and edit a client's record directly in Airtable.
- **Erasure.** The owner can delete a client's record directly in Airtable. No automated cascading deletion across Google Calendar or LangSmith trace history exists yet, this is a real gap: erasure from Airtable does not currently remove associated calendar events or historical conversation traces.
- **Restriction and objection.** A client can ask to be excluded from retention outreach; this maps directly onto the existing opt-in fields (`Product Reminders`, `Booking Reminder Channel(s)`), setting them to none/blank stops future sends. There is no equivalent one-click mechanism for objecting to the core booking-data processing itself, since that data is necessary to deliver the service the client requested.
- **Portability.** Not currently supported as a self-service export. A client's data could be manually exported from Airtable by the owner on request.

**Open item:** the 24-month default retention period (see processing activities register) is not yet enforced. GDPR does not require automated deletion, a manual owner-run review (e.g. quarterly, deleting records past the 24-month mark) is sufficient, but this process needs to actually be set up and followed, not just defined on paper.

## Third-party and cross-border transfers

Every processor in this system is a US-headquartered company: OpenAI, Twilio, Resend, Google (Calendar), and Airtable. Personal data of EU-based clients moves to each of these outside the EU by default, which requires a valid transfer mechanism under GDPR Chapter V (typically Standard Contractual Clauses via each vendor's own Data Processing Addendum).

**What's confirmed:** LangSmith's monitoring endpoint was deliberately configured to the EU region (`eu.api.smith.langchain.com`), a real, verified design choice that keeps conversation trace data in the EU rather than defaulting to the US endpoint.

**What's not yet confirmed:** whether OpenAI, Twilio, Resend, Google, and Airtable's specific account-level configurations for this deployment have an executed DPA with Standard Contractual Clauses in place. This is a real, open compliance item, each vendor's DPA needs to be reviewed and, where available, an EU-region processing option selected, rather than assumed compliant by default.
