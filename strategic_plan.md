# Project | Capstone | Round 2 | Strategic Deployment and Commercialisation Plan
### John Adams

**Client:** Chleo Hair Studio, an independent premium hair salon in Prenzlauer Berg, Berlin
**Sector:** Hair and beauty services (Germany) · **Company size:** Micro business

## Phases: POC to MVP to Pilot to Full Deployment

**Phase 1, POC.** Round 1: an n8n workflow plus a LangSmith monitoring sample proving the retention concept (Use Case 3) could run and be observed. A concept demonstration, not a system anyone used.

**Phase 2, MVP.** Round 2: the concept built into a working product against WhatsApp (Twilio), Google Calendar, and Airtable, with live LangSmith tracing. Covers Use Case 1 (booking and inquiry) and Use Case 3 (retention and re-engagement, including the rebooking and reorder-reminder emails, sent via Resend). Exercised through structured test conversations, not yet live client traffic.

**Phase 3, Pilot.** The MVP in beta: Chleo's real clients use it, at a small sample size for a fixed review window rather than an open-ended rollout. As a single micro-business, this is Chleo's real booking channel during the window, with the consultant staying closely involved to catch issues fast.

Existing channels (phone, WhatsApp DMs to the owners, email, Instagram DMs) stay open in parallel throughout the pilot. Nothing is cut off before full deployment: a client who prefers to keep messaging an owner directly can still do so, the new system runs alongside as an added option, not a forced switch.

**Phase 4, Full deployment.** The system becomes Chleo's default booking and retention channel with minimal day-to-day consultant involvement, backed by ongoing maintenance and care under one of the two support tiers below (a monthly retainer or on-demand hourly), rather than left to run unmonitored after handover.

**Cutover, pilot to full deployment.** Old channels are not switched off at once. Once the greenlight criteria are met, the WhatsApp business number and website chat become the salon's advertised primary channel (bio links, booking page, any signage), while phone and DMs stay reachable as a fallback rather than being closed. This avoids a hard cutover risk for a single-location business with no backup if the new channel has a bad day right after launch.

## Timeline and Milestones

| Milestone | Duration | Depends on |
|---|---|---|
| POC | 1.5 days | — |
| MVP build | 1 week | Round 1 POC |
| Pilot | 2 weeks | MVP build complete, owner sign-off |
| Full launch | 1 week | Pilot review |
| Handover and training | 0.5 days | Full launch |
| Ongoing maintenance | Continuous from full deployment onward | Support tier selected (see Commercialisation model) |

**Total, kickoff to full launch: roughly 4.5 weeks** (1.5 + 5 + 10 + 5 + 0.5 working days). POC and MVP durations reflect actual build time; the pilot's 2 week span is a planning estimate, since pace there depends on owner availability and real client volume.

## Go-to-market (buyers, channel, pricing, differentiator)

**Buyer.** Chleo Hair Studio: an independent, appointment-only salon with 4 chairs and no dedicated admin staff.

**Channel.** Direct engagement, this plan.

**Pricing.** This is the full one-time project fee: building the system and putting it into production for Chleo. Priced hourly at €120/hour (the same rate as the on-demand support tier):

| Phase | Duration | Hours | Cost |
|---|---|---:|---:|
| Phase 1, POC | 1.5 days | 12 | €1,440 |
| Phase 2, MVP build | 1 week | 40 | €4,800 |
| Phase 3, Pilot support | 2 weeks | 10 | €1,200 |
| Phase 4, Full launch | 1 week | 14 | €1,680 |
| Handover and training | 0.5 days | 4 | €480 |
| **One-time project fee** | **~4.5 weeks** | **80** | **€9,600** |

Plus €50-140/month client-paid tooling (Round 1 estimate, unchanged). Ongoing support after handover, Chleo picks one. Only the dev retainer covers new feature work, the other two are upkeep only:

| Support tier | Scope | Rate |
|---|---|---|
| Dev retainer | Maintenance plus new feature work, from a dedicated consultant | €120/hour, 8 hours/month included as a baseline block, adjustable per agreement |
| Maintenance retainer | Upkeep only: tool updates, bug fixes. No new features. | 10-15% of monthly tool spend, €150/month minimum |
| On-demand | Upkeep only, reactive, no monthly commitment, called in when something breaks | €120/hour |

**Differentiator.** Built and tested against Chleo's actual real calendar, CRM, and WhatsApp number, not a curated demo, so what Chleo sees in the pitch is what they get on day one. It also matches a stated client preference: 80 percent of salon and spa guests want to book by mobile phone (Zenoti, cited in `Round 1 artifacts/research/sector_research.md`), which this system meets over WhatsApp and web chat.

## Stakeholder communication plan

- **Owners.** Weekly check-in during pilot, monthly once in full deployment, reviewing the KPIs below each time.
- **Clients.** Told plainly, the first time they use a new channel, that they are talking to an AI assistant. Ties to the transparency obligations covered in `compliance/eu_ai_act_compliance.md`. During the pilot, told this is a new option alongside existing ones, not a replacement. At the cutover to full deployment, told once (a message on the old channels, plus in-salon signage) that WhatsApp/web chat is now the fastest way to book, without implying phone or DM stop working.
- **Chair renters.** Informed the system exists, not operationally involved. They are not affected: the system only treats the two owner-operators as bookable stylists.

## KPIs per phase

**POC (met).** The retention concept runs end to end in a controlled test and produces a traceable LangSmith run.

**MVP (met).** Bookings complete in structured test conversations across both channels, tool calls (calendar, CRM) succeed reliably, no open critical privacy issue. The one critical issue found, the assistant disclosing other clients' bookings to an unauthenticated chatter, was fixed and reverified live.

**Pilot.** Best-guess starting targets, set from established sector benchmarks rather than invented figures, to be refined against Chleo's own numbers after a full business immersion:

| KPI | Sector average | Sector top performer | Starting target |
|---|---:|---:|---|
| Booking resolution rate (online) | 30% | 59% | 30%, the sector average, as a floor to beat |
| Chair utilization | 67% | 84% | 67%, the sector average, as a floor to beat |
| Retention outreach response rate | No benchmark found | No benchmark found | Not set. No established industry figure was found for this specific metric; needs either your input on a comparable benchmark or the pilot's own first cycle as the baseline |

Source for the sector figures: `Round 1 artifacts/research/sector_research.md` (Zenoti operational benchmark data).

**Greenlight to full deployment.** All of the following:
- No open critical security or privacy issue.
- Owner-notification and basic error handling built and tested against at least one real failure.
- Resolution rate and chair utilization trending upward against the pilot baseline.
- Owners confirm, in a check-in, that admin burden has measurably dropped.

**If greenlight criteria aren't met.** Two different situations call for two different responses:
- **Metrics just haven't hit target yet** (resolution rate or utilization still below the pilot baseline). No separate fix cycle needed, the pilot is already producing the data to close the gap. Simply extend the pilot window and re-check against the greenlight criteria at the next owner check-in.
- **A critical issue is found** (security, privacy, or a failure that puts a real client booking at risk). Pause the affected use case immediately rather than waiting for the next scheduled check-in, fix it, then resume the pilot clock.

**Full deployment.** The starting targets above, now read against Chleo's own pilot-period numbers instead of the sector benchmark, trending up toward the sector's top-performer range, with zero unresolved critical incidents over a quarter.

## Commercialisation model

A services model, not a SaaS product. Chleo owns and pays for its own tool accounts (n8n, Twilio, an LLM API, Airtable), which keeps them in control of their own data and avoids a lock-in dependency on the consultant. Revenue is the one-time project fee plus an optional ongoing support retainer, not a recurring license fee.
