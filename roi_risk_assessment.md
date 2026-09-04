# Project | Capstone | Round 2 | ROI and Risk Assessment
### John Adams

**Client:** Chleo Hair Studio, an independent premium hair salon in Prenzlauer Berg, Berlin

## Upfront costs

**€9,600**, the one-time project fee from `strategic_plan.md` (80 hours at €120/hour: POC, MVP build, pilot support, full launch, handover).

## Ongoing costs

| Item | Monthly | Source |
|---|---:|---|
| Client-paid tooling (n8n, Twilio, LLM API, Airtable) | €95 (midpoint of €50-140) | `Round 1 artifacts/cost_estimation/cost_analysis.md` |
| Maintenance retainer (10-15% of tool spend, €150 minimum) | €150 | `strategic_plan.md` support tiers. At €95/month tooling, the percentage falls under the €150 floor, so the floor applies |
| **Total ongoing** | **€245/month, €2,940/year** | |

This assumes the maintenance retainer tier, upkeep only, no new feature work. The dev retainer tier (€120/hour, 8 hours/month baseline, ~€960/month) is available but not assumed here, since it's a heavier commitment than a 4-chair salon's stated budget constraints support by default.

## Quantified business value

Two value drivers are estimated from sourced sector benchmarks. A third, retention outreach, is real and built but deliberately left unquantified, since no comparable industry benchmark was found for it (see `use_case_definition.md`, `strategic_plan.md`).

**1. Owner time recovered.** Assumes 3 hours/week combined across the two owners, freed from manually triaging calls/WhatsApp/email/DM bookings, redirected to service hours. This hours figure is my own estimate, not sourced, and should be checked against the owners' actual time once the pilot runs. Valued at €102/hour (average of the salon's cut prices, `Round 1 artifacts/research/sector_research.md`), 48 working weeks/year: **3 × €102 × 48 = €14,688/year**.

**2. Chair utilization improvement.** Each point of chair utilization is worth about €7,442/year at this salon's pricing (`Round 1 artifacts/research/sector_research.md`, `Round 1 artifacts/dashboard/02_salon_utilization_opportunity.csv`). The Round 1 pitch deck's full gap between average (67%) and top-performer (84%) utilization is €126,513/year, 17 points. Capturing that whole gap is not a realistic planning assumption: the deck's own SOM funnel (slide 3) shows the salon would need 1,700-2,300 active clients to run at 82% utilization at all, and closing the full 17-point gap in year one would mean converting several new clients every single working day for a year, unrealistic for a 4-chair, appointment-only, largely referral-driven salon. A realistic capture is a small slice of that gap: **0.5 percentage points, €3,721/year**, from better online resolution of inquiries that previously went unanswered or mishandled, not a wholesale utilization transformation.

**Total estimated Year 1 value: €18,409/year** (€14,688 owner time + €3,721 utilization). Both figures are estimates pending real pilot data, not measured outcomes. Retention email revenue is real upside not included in this total.

## ROI, 12 and 36 months

ROI = (Net Benefit / Total Cost) × 100

| | 12 months | 36 months |
|---|---:|---:|
| Total cost (upfront + ongoing) | €9,600 + €2,940 = €12,540 | €9,600 + €8,820 = €18,420 |
| Value (flat €18,409/year, no growth modeled) | €18,409 | €55,227 |
| Net benefit | €5,869 | €36,807 |
| **ROI** | **47%** | **200%** |

Year 1 is deliberately modest: the project fee dominates year-1 cost, so the salon is still largely paying for the investment rather than profiting from it. By 36 months, value is 3x total cost, a 200% ROI. The 36-month figure holds value flat year over year as a conservative simplification, no compounding modeled from retention or reputation effects, which would only push this higher.

## Break-even

Value only starts accruing at full deployment (Phase 4), not from project kickoff. After the ~4.5-week build-to-full-launch period (`strategic_plan.md`), monthly net benefit is about €18,409/12 − €245 ≈ €1,289/month. Recovering the €9,600 upfront fee against that takes about 7.4 months. **Break-even is roughly 8-9 months from project kickoff**, accounting for the pilot period before value starts accruing. This matches the intended story: the salon pays for the system first, and clearly benefits from month 9 onward.

## Assumptions table

| Assumption | Why |
|---|---|
| €120/hour consultant rate | Set with the client, matches `strategic_plan.md` pricing |
| 3 hours/week owner time saved | Estimate, not sourced. Needs pilot validation |
| 0.5 percentage points of utilization capture | A deliberately small slice of the Round 1 deck's 17-point average-to-top-performer gap, scaled down from the full €126,513/year figure since capturing that whole gap would require an unrealistic pace of new-client acquisition |
| €102/hour service rate | Average of the salon's own cut prices, `Round 1 artifacts/research/sector_research.md` |
| Retention outreach value not quantified | No sector benchmark found for response rate on this kind of nudge; real, built, and live, just not counted in this ROI figure |
| Maintenance retainer, not dev retainer, assumed for ongoing cost | Lighter commitment matching a 4-chair salon's stated budget constraints |
| Value held flat across 36 months | Conservative simplification, no compounding modeled |

## Risk matrix

| # | Risk | Category | Likelihood (1-5) | Impact (1-5) | Mitigation |
|---|---|---|---:|---:|---|
| 1 | A future prompt or config change reintroduces a data leak of one client's identity to another chatter | Regulatory | 2 | 5 | Data-layer field masking on the calendar API plus a prompt rule are both in place (defense in depth); the exact class of bug already found and fixed once, live-tested against the original exploit phrasing |
| 2 | EU AI Act transparency obligations are missed or misclassified | Regulatory | 2 | 3 | Formal classification and reasoning documented in `compliance/eu_ai_act_compliance.md`, reviewed before pilot |
| 3 | A chair renter's independent-contractor status is challenged if scheduling looks too centralized (Scheinselbstständigkeit) | Regulatory | 1 | 4 | The system only ever books the two owner-operators as stylists; chair renters are not scheduled or managed through it at all |
| 4 | The n8n host goes down with no external monitoring to catch it | Technical | 3 | 4 | Retry-on-fail and an owner-notification workflow are built; external uptime monitoring is still needed before real client traffic, flagged in `mvp/mvp_documentation.md` |
| 5 | OpenAI outage with no fallback provider | Technical | 2 | 3 | Retry-on-fail covers transient issues; a fallback provider is explicitly deferred as a pre-launch item, not silently missing |
| 6 | A shared household phone/email causes two people's records or bookings to collide | Technical | 2 | 3 | Name+Email compound key on the CRM upsert, plus a prompt rule that trusts the client's stated name over a lookup match; live-tested with two people sharing one phone number |
| 7 | A client isn't told they're talking to an AI, or the AI is used to impersonate salon staff to extract information | Ethical | 2 | 3 | A CRITICAL prompt rule already blocks staff-impersonation attempts, live-tested; explicit AI-disclosure to clients is part of the stakeholder communication plan in `strategic_plan.md` |
| 8 | Owner over-relies on the assistant without reviewing edge cases it handles badly | Operational | 3 | 3 | Weekly owner check-ins during the pilot, the Error Log and owner-alert email on failures, and two-factor verification before any existing appointment is disclosed or changed |
| 9 | Manual calendar changes (e.g. an owner deleting an event directly in Google Calendar) go stale in Airtable | Operational | 3 | 2 | Accepted MVP limitation, documented, no reverse sync built. Revisit if this becomes a frequent real workflow |
| 10 | The system is up but a step inside a workflow fails silently (a booking doesn't save, a reminder doesn't send) and nobody notices | Technical | 3 | 4 | Retry-on-fail and an owner-alert cover hard failures; nodes set to "continue on fail" have not yet been audited node by node for silent failures, flagged as an open item in `mvp/mvp_documentation.md` |
