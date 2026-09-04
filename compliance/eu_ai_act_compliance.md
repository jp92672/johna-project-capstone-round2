# Project | Capstone | Round 2 | EU AI Act Compliance Documentation
### John Adams

**Client:** Chleo Hair Studio, an independent premium hair salon in Prenzlauer Berg, Berlin
**Systems covered:** Use Case 1 (Booking and Inquiry Assistant, WhatsApp and website chat) and Use Case 3 (Retention and Re-Engagement email drafting).

## Risk classification and reasoning

**Classification: Limited risk**, under the transparency obligations in Article 50 of the EU AI Act (Regulation (EU) 2024/1689). Not prohibited, not high-risk, not purely minimal-risk.

Step by step:

1. **Not a prohibited practice.** The system does not do subliminal manipulation, social scoring, biometric categorization, or any of the practices banned under Title II. Ruled out.
2. **Not high-risk under Annex III.** High-risk categories cover things like biometric identification, employment decisions, credit scoring, access to essential public services, law enforcement, and education access. A salon booking assistant and a retention-email drafting tool fit none of these. It does not make decisions about a person's access to a service they're entitled to, book vs. no-book is availability-driven, not an eligibility judgment, and it doesn't touch credit, insurance, employment, or any other Annex III category. Ruled out.
3. **Meets the Article 50 transparency trigger.** Article 50(1) requires that a natural person be informed they are interacting with an AI system, unless it's obvious from context, when the system is "intended to interact directly with natural persons." Use Case 1 is exactly this: a WhatsApp and website chat assistant holding a natural-language conversation with a client. This applies regardless of how capable or risky the underlying task is, the trigger is the direct conversational interaction itself.
4. **Use Case 3 is a narrower case.** The retention and reminder emails are AI-drafted but one-way and transactional/relationship-based (an existing client relationship, not a stranger being contacted), not a live conversational interaction, and not synthetic content "informing the public on matters of public interest" (the other Article 50 trigger, aimed at deepfakes and generated news-like content). Article 50's strict text points more clearly at Use Case 1. Applying the same disclosure practice to Use Case 3 anyway is treated here as good practice, not read as a separate hard legal requirement, since the content is short, personal, and from a real known sender.
5. **Result: Limited risk.** The obligation is disclosure, not the heavier Title III regime (risk management system, conformity assessment, CE marking, notified body review) that only applies to high-risk systems.

## Role map

| Role | Entity | Key obligations that flow from this role |
|---|---|---|
| Provider | The consultant (built and configured the system) | Design the system to support the deployer's Article 50 disclosure duty; document the system honestly (this file, `mvp/mvp_documentation.md`); no CE marking, registration, or conformity-body role required at Limited risk |
| Deployer | Chleo Hair Studio (uses the system in a professional context, in front of its clients) | Actually show the AI disclosure to clients at first contact (Article 50(1)); ensure staff have basic AI literacy (Article 4) about what the system does and its limits |
| Vendor | OpenAI (LLM), Twilio (WhatsApp), Google (Calendar), Airtable (CRM), Resend (email) | Each carries its own obligations as a tool/model provider (OpenAI's GPAI duties under Title VIII in particular) — separate from, and upstream of, Chleo's deployer duties |

Role split confirmed here is a first-pass mapping for this exercise, not a legal determination; a lawyer would confirm it before contract language is drawn up.

## Mandatory requirements summary

Because this is Limited risk, not High-risk:

- **Required:** inform the natural person that they are interacting with an AI system, clearly and distinguishably, at the latest at the time of first interaction (Article 50(1)). This applies to Use Case 1.
- **Required, general obligation across all risk tiers:** AI literacy (Article 4), anyone deploying or operating the system on the client's side should understand at a basic level what it does and doesn't do, and its limitations.
- **Not required at this tier:** a formal risk management system, conformity assessment procedure, CE marking, registration in the EU database, or notified-body review. Those are Title III, High-risk-only obligations.
- **Separate, upstream obligation:** the underlying general-purpose AI model (OpenAI's model) carries its own GPAI provider obligations under Title VIII. Those are OpenAI's responsibility as the model provider, not Chleo's or the consultant's as the deployer.

## Conformity assessment summary

**Legal basis for self-assessment.** No formal conformity assessment procedure, notified-body review, or CE marking is legally required at Limited risk (those are Title III, High-risk-only). What follows is a self-assessed statement against the one obligation that does apply: Article 50(1) disclosure.

**Timing note.** Article 50's transparency obligations became legally applicable on 2 August 2026. That date has already passed. This is not a future deadline to plan around — it is a live obligation. The two open items below (WhatsApp verification, real Terms/Privacy content) must close before any real client is exposed to the system; they cannot sit on a general "nice to have soon" backlog.

**Gap register**

| # | Obligation | Current state | Required state | Severity | Escalation needed? |
|---|---|---|---|---|---|
| 1 | Article 50(1) disclosure, website chat (Use Case 1) | **Met.** Always-visible line below the chat input: "AI-powered; AI can make mistakes. By using AI Mode you agree to our Terms and Privacy Policy." (`mvp/website/book.html:244`) | — | — | No |
| 2 | Terms/Privacy consent, direct booking form (not an Article 50 item — see note) | **Met.** Required checkbox gates the submit button (`mvp/website/book.html:366-368`) | — | — | No |
| 3 | Article 50(1) disclosure, WhatsApp/SMS | Built as a CRITICAL prompt rule (agent must open its first reply in a thread with an AI-disclosure line before continuing), but **not live-tested** against a real fresh WhatsApp number. Prompt-driven, model has to correctly judge "this is the first message" — unlike the website's deterministic checkbox, this can silently fail | Confirmed by an actual live message from a fresh number, then treated as verified | Significant (the obligation may not actually be firing in the one channel this is hardest to visually audit) | No — this is a test-and-confirm task, not a design question |
| 4 | Terms/Privacy Policy links referenced by the disclosure and the consent checkbox | Fake placeholder URLs, no real Terms of Service or Privacy Policy content exists yet | Real, published Terms and Privacy Policy pages the disclosure and checkbox can honestly point to | Significant (a disclosure that links to nothing hollows out the consent it's asking for) | Possibly — real Terms/Privacy content should get a legal look before publishing, not just a documentation pass |
| 5 | Disclosure on Use Case 3 outreach (good practice, not a hard Art. 50 requirement per the reasoning above) | **Met.** Both the rebooking and reorder email footers now include "This message was drafted with AI assistance." next to the existing GDPR unsubscribe line (n8n workflow `IZOvgzAIouXSfEh1`, `Send Rebooking Email` / `Send Reorder Email` nodes) | — | — | No |

Note: the CRITICAL prompt rule blocking staff-impersonation and unauthenticated appointment lookups (built and live-tested, see `mvp/mvp_documentation.md`) is a separate control — it supports good-faith use of the system but does not itself satisfy the disclosure obligation. Refusing to leak data and disclosing you're an AI are two different duties.

Note on gap #2: the direct booking form posts to `Capstone R2 - Direct Booking API`, a fully deterministic workflow with no LLM involved (see `mvp/mvp_documentation.md`). No AI system interacts with the client on that path, so Article 50 does not legally apply there at all, the same reasoning already applied to Use Case 3 in the classification above. The checkbox is listed here because it's real and relevant to document, not because it closes an Article 50 gap.

**Remediation plan**
- WhatsApp: send one real message from a fresh, never-before-seen number and confirm the disclosure line actually opens the reply. Until this is done, treat the channel's Article 50 compliance as unverified, not confirmed.
- Terms/Privacy Policy: write real content and swap out the placeholder links before any real client sees the disclosure or the checkbox.

**Overall recommendation: Proceed with conditions.** Classification is correct and no heavier compliance regime applies. Website chat, the direct booking form, and the Use Case 3 email footers already satisfy Article 50(1) / good-practice disclosure and are verified in code. Before real client traffic starts: (1) confirm the WhatsApp disclosure actually fires on a live test, and (2) replace the placeholder Terms/Privacy links with real content. Both are scoped, known tasks, not open legal questions.

**What this assessment is not.** This is not a legal opinion, not a formal conformity assessment (none is required at this tier), and not a certification. It is a first-pass consulting assessment; role mapping and classification above should be confirmed by a lawyer before any binding compliance claim is made to Chleo or to regulators.

## Technical documentation outline

Full Annex IV technical documentation is a High-risk-system requirement and does not legally apply here. The outline below is a proportionate, lighter version kept for internal governance and in case classification is ever revisited.

1. **System overview** — purpose, the two use cases, intended users (Chleo's clients and the two owner-operators)
2. **Architecture** — n8n workflows, entry points (Twilio, website webhook, direct-booking API, retention schedule), data stores (Airtable, Google Calendar), the LLM in use, referenced from `mvp/mvp_documentation.md`
3. **Data used** — client name, phone, email, appointment and purchase history; see `compliance/gdpr_documentation.md` for the full data flow map and legal basis
4. **Capabilities and limitations** — what the assistant can and cannot do, referenced from `mvp/mvp_documentation.md`'s Known limitations section
5. **Testing and validation record** — the specific live tests run and what each confirmed, referenced from `working_files/session_notes_2026-09-02.md` and `working_files/session_notes_2026-09-03.md`
6. **Human oversight measures** — owner check-ins during pilot, the Error Log and failure-alert email, two-factor verification before disclosing or changing an existing appointment
7. **Monitoring** — LangSmith tracing (EU region), the Airtable Error Log table
8. **Known open risks** — cross-referenced from `roi_risk_assessment.md`'s risk matrix
