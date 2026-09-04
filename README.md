# Project | Capstone | README
### John Adams

**Client:** Chleo Hair Studio, an independent premium hair salon in Prenzlauer Berg, Berlin
**Sector:** Hair & beauty services (Germany) · **Company size:** Micro business (2 owner-operators, 2 independent chair renters, 0 employees)

## Overview

Full two-round AI consulting capstone for Chleo Hair Studio. Round 1 is the pitch package (research, dashboard, POC, monitoring, cost/timeline). Round 2 is the full consulting package plus a working MVP: a booking-and-inquiry AI agent (WhatsApp + website chat) and a retention/re-engagement automation, both running against real Google Calendar, Airtable, Twilio, and Resend accounts, with EU AI Act and GDPR documentation, an ROI/risk assessment, and a strategic deployment plan.

**Decision after Round 1:** KEEP — same sector, company size, and client scenario carried into Round 2. Use Case 2 (no-show recovery) was descoped; Use Cases 1 and 3 became the Round 2 MVP. Full reasoning in [`Round 1 artifacts/feedback/round1_decision.md`](Round%201%20artifacts/feedback/round1_decision.md) and the evolution note in [`use_case_definition.md`](use_case_definition.md).

## Repo structure

```
README.md                    this file
use_case_definition.md       problem, stakeholders, success criteria, out-of-scope, Round 1 → 2 evolution
roi_risk_assessment.md       costs, value, 12/36-month ROI, assumptions, risk matrix (10 risks)
strategic_plan.md            POC → pilot → full deployment, GTM, KPIs, commercialisation model
presentation.pdf             final Round 2 presentation deck (exported from presentation/)
poc/                         no-code/low-code POC (Round 1 workflow, carried forward per submission requirements)
compliance/                  EU AI Act + GDPR documentation
mvp/                         the working MVP: n8n workflows, website, data exports, mvp_documentation.md
presentation/                slide deck source (pitch-deck-slideshow.html) + qa_prep.md
Round 1 artifacts/           Round 1 pitch package, unchanged, kept per submission requirements
requirements.txt             Python deps for the Round 1 LangSmith notebook only (the MVP itself is n8n + static HTML)
.env.example                 env vars used by the Round 1 notebook and the n8n host config
```

## How to view each deliverable

- **Use case definition:** [`use_case_definition.md`](use_case_definition.md).
- **POC:** [`poc/poc_documentation.md`](poc/poc_documentation.md) — tools, steps, AI capability shown, limits vs. production, how to reproduce. Workflow export is `Round 1 artifacts/n8n/workflow.json` (this POC carries forward unchanged from Round 1; the Round 2 MVP below is the built-out version).
- **Working MVP:** [`mvp/mvp_documentation.md`](mvp/mvp_documentation.md) — what runs, where, the core AI capability, error handling, known limitations, and setup. Supporting files: `mvp/website/` (the client-facing site), `mvp/n8n workflows/` (exported JSON for all four production workflows), `mvp/outputs/` (schema + point-in-time data exports and screenshots used as demo evidence).
- **ROI and risk:** [`roi_risk_assessment.md`](roi_risk_assessment.md).
- **EU AI Act compliance:** [`compliance/eu_ai_act_compliance.md`](compliance/eu_ai_act_compliance.md).
- **GDPR:** [`compliance/gdpr_documentation.md`](compliance/gdpr_documentation.md).
- **Strategic plan:** [`strategic_plan.md`](strategic_plan.md).
- **Presentation:** open `presentation.pdf`, or `presentation/pitch-deck-slideshow.html` in any browser for the interactive version (arrow keys or click to navigate). Q&A prep notes in `presentation/qa_prep.md`.
- **Round 1 package:** [`Round 1 artifacts/README.md`](Round%201%20artifacts/README.md).

## Setup

```bash
pip install -r requirements.txt
cp .env.example .env
```

Fill in `.env` with a real `OPENAI_API_KEY` and `LANGSMITH_API_KEY` before running the Round 1 LangSmith notebook. Never commit `.env`, it's excluded via `.gitignore`. The MVP itself has no local server to start — see the Credentials and setup section of `mvp/mvp_documentation.md` for what it takes to stand it up fresh (n8n instance, Google Cloud project, Twilio, Resend, Airtable, OpenAI).

## A note on redaction

This repo is public. Real personal inboxes and a personal phone number used only for development/testing (not the client's) have been redacted from workflow exports, data exports, and screenshots throughout `mvp/` — placeholders like `dev-test@example.com` stand in for them. All client data throughout is synthetic or the consultant's own test data; no real Chleo Hair Studio client exists, per the project brief's scope constraint.
