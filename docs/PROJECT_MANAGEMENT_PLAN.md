# Project Management Plan — Pitchgate

> Version 2.0 · Living document — updated at each phase gate

| Field | Detail |
|---|---|
| **Project** | Pitchgate — Verified BD Network for Crypto/Web3 |
| **PM / PO** | Bojan Pilipović |
| **Plan version** | 2.0 |
| **Last updated** | Q4 2024 |
| **Status** | Phase 1 — Active |

---

## Table of Contents

1. [Scope Management](#1-scope-management)
2. [Schedule Management](#2-schedule-management)
3. [Resource Management](#3-resource-management)
4. [Risk Management](#4-risk-management)
5. [Communications Management](#5-communications-management)
6. [Quality Management](#6-quality-management)
7. [Change Management](#7-change-management)
8. [Phase Gate Process](#8-phase-gate-process)

---

## 1. Scope Management

### 1.1 Scope definition approach

Scope is defined and maintained through two primary documents:

- **AGENTS.md** — the authoritative source of truth for what is and is not built. Every feature, API route, database table, and phase gate is documented here. No code is written without a corresponding entry or explicit alignment with AGENTS.md.
- **GitHub Kanban board** — tracks all work items as issues across four lanes: Backlog, In Progress, In Review, Done.

Before any new feature begins, it is assessed against three criteria:
1. Is it in Phase 1 scope?
2. Does it serve a validated user need?
3. Does it introduce architectural risk or technical debt?

If the answer to (1) is no, the item is logged in the backlog with a phase label and not touched until a phase gate review.

### 1.2 In scope — Phase 1 (MVP)

- Person profile creation and management
- Project profile creation and management
- Tiered vouching system (Tier 0–3) with monthly caps
- Signal reputation score (additive: vouches + endorsements + confirmed matches)
- Connection request flow (person → project)
- Shared project inbox for incoming requests
- Contact reveal on confirmed match (telegram, email)
- Transactional email via Resend (request received, accepted, vouch received)
- Authentication and session management via Clerk
- PostgreSQL data layer on Railway
- Public landing page and Tally waitlist

### 1.3 Out of scope — post Phase 1

The following are explicitly deferred and documented as phase gates in AGENTS.md. No work begins on these without a formal phase gate decision:

| Item | Target phase |
|---|---|
| AI intent parsing via Anthropic API | Phase 2 |
| Intent board — posting and browsing BD intents | Phase 2 |
| Semantic matching / embeddings | Phase 3 |
| On-chain or token features | Post Phase 3 |
| Public API or webhooks | Post-MVP |
| Admin dashboard UI | Post-MVP |
| Signal leaderboard | Post Phase 1 |
| Monetization / subscription tiers | Post 100 users |
| Mobile native app | Not scoped |

### 1.4 Scope change process

All scope changes follow this process:

1. Change is raised as a GitHub issue with the label `scope-change`
2. PM evaluates: impact on schedule, architecture, and phase gate integrity
3. If Phase 1 scope: accepted or rejected with written rationale on the issue
4. If post-Phase 1 scope: added to backlog with correct phase label, not actioned
5. AGENTS.md updated to reflect any accepted changes before work begins

---

## 2. Schedule Management

### 2.1 Scheduling approach — current (phase-gated flow)

Pitchgate is currently delivered in a phase-gated model rather than fixed sprints. Each phase has a defined entry condition (what must be true before it starts) and exit condition (what must be true before the next phase begins).

Work within each phase is managed as a continuous flow using the GitHub Kanban board. Issues are pulled from backlog as capacity allows, with no fixed sprint cadence. This is the appropriate model for a solo AI-assisted build — sprint ceremony overhead does not pay off without a team.

### 2.2 Scheduling approach — sprint-based (team / outsourcing context)

In a team or outsourced delivery context, the scheduling model shifts to fixed sprints inside each phase. The phase gates remain as strategic checkpoints, but delivery rhythm becomes sprint-based.

**Sprint structure:**

| Ceremony | Timing | Purpose |
|---|---|---|
| Sprint planning | Sprint start | Pull from backlog, commit to sprint goal, agree acceptance criteria |
| Daily standup | Daily (15 min) | Blockers, progress, dependencies — async written update in outsourcing context |
| Sprint review | Sprint end | Demo working software to stakeholders, collect feedback, formal acceptance |
| Sprint retrospective | Sprint end | Risk review, process health, one improvement to carry forward |

**Sprint length:** 2 weeks recommended for a product in active development. 1 week if the team is new or the domain is uncertain.

**Sprint goal:** every sprint has one sentence describing the value delivered — not a task list. The goal anchors scope decisions during the sprint.

**Velocity-based forecasting:** after 3–4 sprints, average velocity (story points completed per sprint) becomes the forecasting tool. Remaining backlog points ÷ velocity = sprints to completion.

**Backlog structure:**

```
Epic → User story → Tasks
```

Example for Pitchgate:
- Epic: *Trust system*
- Story: *As a Tier 1 user, I can vouch for another user so they can reach Tier 1*
- Tasks: API route, vouch cap logic, UI button, email notification, DoD checklist

### 2.3 Phase structure

| Phase | Description | Entry condition | Exit condition |
|---|---|---|---|
| **Phase 0** | Architecture and foundations | Project charter approved | Schema finalized, AGENTS.md complete, repo initialized |
| **Phase 1** | MVP — core product | Phase 0 complete | All MVP features live, 100 verified users onboarded |
| **Phase 2** | Intent board + AI layer | Phase 1 exit validated | Intent board live, AI matching producing suggestions |
| **Phase 3** | Semantic resolution engine | Phase 2 validated | Confidence scoring, ranked match suggestions live |

### 2.4 Phase 1 milestone schedule

| Milestone | Description | Status |
|---|---|---|
| M1 | Architecture finalized — schema, entity model, AGENTS.md | ✅ Done |
| M2 | Auth and onboarding live — Clerk, person profile creation | ✅ Done |
| M3 | Trust system live — Tier logic, vouch caps, endorsements, Signal score | ✅ Done |
| M4 | Connection flow live — requests, inbox, accept/decline, contact reveal, email | ✅ Done |
| M5 | MVP launch — pitchgate.xyz live, waitlist open, Twitter live | ✅ Done |
| M6 | 100 verified users onboarded — Phase 1 exit criterion met | 🔄 In progress |

### 2.5 Schedule risk

The primary schedule risk is the cold start problem: new users cannot reach Tier 1 without existing Tier 1/2 vouchers in their network. This creates a dependency on manual seeding by the admin (Tier 3) and early Tier 2 anchors. Mitigation is covered in the Risk Register.

---

## 3. Resource Management

### 3.1 Team structure — current

Pitchgate is a solo-led product. All product, delivery, and architectural decisions are made by the PM/PO. Development is executed via AI-assisted development (Claude Code), with the PM/PO responsible for directing, reviewing, and deploying all output.

| Role | Person | Responsibilities |
|---|---|---|
| Project Sponsor | Bojan Pilipović | Strategic direction, go/no-go decisions, phase gate approval |
| PM / PO | Bojan Pilipović | Scope definition, backlog management, delivery oversight |
| Lead Developer (AI-assisted) | Claude Code (directed by PM) | Feature implementation, API routes, database queries, UI |
| Infrastructure | Vercel / Railway / Clerk / Resend | Managed services — no internal ops resource required |

### 3.2 Team structure — outsourced / sprint context

In an outsourced or team delivery context, the resource model expands as follows:

| Role | Responsibility |
|---|---|
| PM / PO | Backlog ownership, sprint planning, stakeholder comms, vendor management |
| Outsourced team lead | Day-to-day delivery coordination, first escalation point for blockers |
| Outsourced developers | Feature implementation against agreed acceptance criteria |
| QA (outsourced or shared) | Acceptance testing against DoD before sprint review |
| Stakeholders | Sprint review attendance, feedback, formal acceptance |

**Cross-timezone discipline:** identify the daily overlap window between time zones. All real-time decisions, sprint ceremonies, and escalations requiring live input are scheduled within this window. Everything outside the overlap is async with defined response SLAs — 4 hours for blockers, 24 hours for standard queries.

### 3.3 Infrastructure and tooling

| Layer | Tool | Purpose |
|---|---|---|
| Frontend hosting | Vercel | Automatic deploys from main branch |
| Database | PostgreSQL on Railway | Managed Postgres — no DBA required |
| Auth | Clerk | Session management, user identity |
| Email | Resend | Transactional email |
| Version control | GitHub | Source of truth, Kanban board, issue tracking |
| AI development | Claude Code | AI-assisted feature development |
| Waitlist | Tally | Pre-launch waitlist capture |

### 3.4 Environment variables and secrets

All secrets are managed via environment variables. No secrets are committed to the repository.

| Variable | Service | Required |
|---|---|---|
| `DATABASE_URL` | Railway Postgres | Yes |
| `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` | Clerk | Yes |
| `CLERK_SECRET_KEY` | Clerk | Yes |
| `RESEND_API_KEY` | Resend | Yes |
| `ANTHROPIC_API_KEY` | Anthropic | Phase 2 only |

---

## 4. Risk Management

### 4.1 Risk management approach

Risks are identified at project initiation and reviewed at each phase gate. Each risk is assessed on two dimensions:

- **Likelihood**: Low / Medium / High
- **Impact**: Low / Medium / High

Response strategies follow standard PMBOK categories: avoid, mitigate, transfer, accept.

### 4.2 Risk register

| ID | Risk | Likelihood | Impact | Response strategy | Owner | Status |
|---|---|---|---|---|---|---|
| R-01 | Cold start — insufficient vouching chains to onboard new users organically | High | High | **Mitigate** — Admin manually seeds Tier 2 anchors from existing personal Web3 network before public launch. Target: 5 Tier 2 anchors seeded before waitlist activation. | PM | Active |
| R-02 | Contact data exposure via API misconfiguration | Low | High | **Mitigate** — Contact fields excluded at query level, not UI level. Security review before every push that touches API routes or contact fields. Documented as critical rule in AGENTS.md. | PM | Active |
| R-03 | Third-party service outage (Clerk, Railway, Vercel, Resend) | Medium | High | **Accept / Transfer** — All services on managed platforms with published SLAs. No in-house mitigation possible. Accepted as platform dependency risk. | Vendor | Accepted |
| R-04 | Scope creep into Phase 2 before Phase 1 is validated | High | Medium | **Avoid** — Phase gates explicitly defined in AGENTS.md. Any Phase 2 feature request requires explicit phase gate decision before work begins. No exceptions. | PM | Active |
| R-05 | Vouching system abuse — Tier 1 users colluding to vouch fake accounts | Medium | High | **Mitigate** — Monthly vouch caps (5 outgoing for Tier 1, 20 for Tier 2). One vouch per pair enforced by UNIQUE constraint. Tier 2 anchors personally known to admin. | PM | Active |
| R-06 | AI-assisted development produces insecure code (SQL injection, auth bypass) | Medium | High | **Mitigate** — Parameterized queries enforced in all DB interactions. Auth check at top of every protected API route. Security conventions documented in AGENTS.md and reviewed per session. | PM | Active |
| R-07 | Low user activation — waitlist signups do not convert to active profiles | High | Medium | **Mitigate** — Personal outreach to first cohort from existing HoudiniSwap and Web3 BD network. Onboarding flow kept minimal. | PM | Active |
| R-08 | Resend email deliverability issues cause failed match notifications | Low | Medium | **Mitigate** — Email is fire-and-forget, never blocks API response. Contact reveal accessible in-app regardless of email delivery. | PM | Active |

### 4.3 Risk review cadence

**In the current phase-gated model:** risks are reviewed formally at each phase gate and whenever a new high-impact issue is raised.

**In a sprint-based model:** risk management is embedded into existing ceremonies — not treated as a separate meeting.

| Ceremony | Risk activity |
|---|---|
| Sprint planning | Quick scan of sprint items for new technical unknowns or dependencies before committing |
| Daily standup | Blockers surfaced in real time — a blocker unresolved for 48h is an active risk to the sprint goal |
| Sprint review | Stakeholder feedback may surface scope or expectation risks not previously visible |
| Sprint retrospective | Formal risk look-back — what materialized, what is new, what gets added to the register |
| Phase gate / release | Full risk register review before any major release or phase transition |

The rule: risks are detected continuously in standups, reviewed lightly in planning, and formally assessed once per sprint in the retrospective.

---

## 5. Communications Management

### 5.1 Stakeholder communication approach

In an agile model, most communication becomes **pull, not push.** Status is made permanently visible through the board, the sprint goal, and the backlog — so stakeholders can check in when they need to. Formal communication happens at defined moments (sprint review, planning) rather than through continuous status reports.

In an outsourcing / cross-timezone context, all communication is **written by default.** Verbal agreements do not exist. Decisions, change requests, and escalations are documented in the agreed channel before they are acted on.

### 5.2 Communications matrix

| Audience | Channel | Frequency | Content | Owner |
|---|---|---|---|---|
| Core team (internal) | Standup | Daily | Blockers, progress, dependencies | Team |
| Core team (internal) | Sprint planning | Every sprint start | Commitment, sprint goal, capacity | PM |
| Core team (internal) | Retrospective | Every sprint end | Process health, risk review | PM |
| Stakeholders / client | Sprint review / demo | Every sprint end | Working software, formal acceptance, feedback | PM |
| Stakeholders / client | Slack / async channel | As needed | Decisions, blockers needing input | PM |
| Stakeholders / client | Roadmap / backlog | Always visible | What's planned, what's coming | PM |
| Outsourced team | Daily written standup | Daily (async) | Progress, blockers, questions | Team lead |
| Outsourced team | Sprint planning call | Every sprint start | Scope, priorities, acceptance criteria | PM |
| Outsourced team | PR / code review | Continuous | Quality, standards, feedback | PM / lead dev |
| Executive / sponsor | Sprint review summary | Every 2–4 sprints | Velocity, risks, milestone progress | PM |
| Waitlist / early users | Twitter, Tally | Milestone-based | Launch updates, onboarding invites | PM |
| Active users (Tier 1+) | Transactional email via Resend | Event-triggered | Request received, accepted, vouch received | Automated |

### 5.3 Escalation path

**Track 1 — delivery escalation (internal agile)**

> Blocker identified in standup → PM attempts to resolve within 24h → if unresolved, escalated to sponsor or relevant stakeholder → if it threatens the sprint goal, sprint scope is negotiated explicitly — never silently absorbed

The key principle: you never let a blocker silently kill a sprint goal. Surface it early, make a conscious decision — remove the item, adjust the goal, or clear the blocker. Silence is not a resolution.

**Track 2 — vendor / outsourcing escalation**

> Issue identified (quality, missed delivery, miscommunication) → raised with outsourced team lead first — never skip this step → if unresolved within one sprint → escalated to vendor account manager or engagement lead → if contractual or relationship-level → escalated to sponsor / procurement

All escalations in an outsourcing context are documented in writing before and after resolution. This is not bureaucracy — it is protection for both sides.

**Distinguishing performance vs. process issues:**

A vendor delivering late once is a **performance issue** — address it at team lead level. A vendor consistently missing acceptance criteria is a **process issue** — your definition of done or handoff process is broken, and the PM owns that before blaming the vendor.

**Cross-timezone rule:** anything requiring a real-time decision waits for the overlap window. No async approvals on sprint scope changes, contract scope, or security-critical issues.

---

## 6. Quality Management

### 6.1 Quality approach

In agile, quality shifts left — it is built into every step of the sprint, not tested at the end. In an outsourcing / cross-timezone context, quality problems are almost always upstream: vague acceptance criteria, unclear Definition of Done, or assumptions made in the gap between time zones. Fix the brief before blaming the team.

### 6.2 Four quality layers — agile / outsourcing context

**Definition of Done** — the primary quality gate. A shared, written DoD agreed with the outsourced team before sprint 1. Every story must meet it before it is called done. No exceptions negotiated mid-sprint. Output that does not meet DoD goes back — it is not accepted and "fixed later."

**Acceptance criteria per story** — every item has written AC before it enters the sprint. Not during. Not after. This is the main protection against timezone ambiguity — the team should not need to ask what done looks like at 2am your time.

**Async code review via PRs** — no code merges without a PR review. Written, tracked, resolved before merge. This is the continuous quality touchpoint that works across time zones, replacing in-person peer review.

**Sprint review as formal acceptance** — the sprint review is a gate, not a demo. Stakeholders formally accept or reject what was built. Anything not accepted returns to the backlog as a new story with clear remediation criteria.

### 6.3 Non-negotiable quality rules — Pitchgate specific

**Security**
- Contact fields (telegram, contact_email) are never returned in any API response unless a confirmed match exists — enforced at query level
- All DB queries use parameterized inputs — no string interpolation with user input
- Auth check at the top of every protected API route — 401 if no session, 403 if insufficient tier
- Tier 2 status and project verification can never be self-assigned

**Data integrity**
- Signal score and counts update atomically with the triggering event — never async
- Tier upgrade check runs in the same transaction as every new vouch insert
- All UUID primary keys generated server-side (gen_random_uuid())

**Code quality**
- Plain JavaScript only — no TypeScript
- Custom CSS only — no Tailwind or CSS frameworks
- Meaningful empty states on every UI component — never a blank section
- Functions are small and single-purpose
- All configurable values via environment variables — no hardcoded config

**Deployment**
- No secrets committed to the repository — all in .env.local
- Never use `git add .` — always add files explicitly by name
- .gitignore verified before every first push to a new environment

### 6.4 Definition of done — Pitchgate

A feature is considered done when:

- [ ] API route(s) implemented and returning correct responses
- [ ] UI component renders correctly on desktop and mobile
- [ ] Auth and tier checks in place on all protected routes
- [ ] Empty states handled — no blank sections
- [ ] Transactional emails triggered where applicable
- [ ] AGENTS.md updated if the feature introduces new patterns
- [ ] GitHub issue closed and moved to Done on Kanban board

---

## 7. Change Management

### 7.1 Change management approach

In agile, the backlog is the change mechanism. Small changes flow through it naturally. Big changes — anything affecting a sprint in flight, a release date, contract scope, or budget — require a formal decision before work starts.

In an outsourcing context, ungoverned changes create billing disputes, scope arguments, and blown timelines. The vendor should never start work on something outside agreed sprint scope without written confirmation from the PM. This protects both sides.

### 7.2 What flows through the backlog vs. what requires formal process

| Change type | Process |
|---|---|
| New story, minor scope addition, reprioritization | Added to backlog, estimated, pulled into future sprint through normal planning |
| Change to a sprint currently in flight | Formal decision required — document impact, get PM sign-off, update sprint goal if accepted |
| Change affecting release date or contract scope | Full change request — written, impact-assessed, signed off before work starts |
| Security-critical change | P0 — all other work stops, PM review + security checklist before deploy |

### 7.3 The PM's role in change management

The PM is the buffer between stakeholder enthusiasm and team capacity. Stakeholders will always want to add things mid-sprint. The response is never "no" — it is: **"We can add this. What comes out?"** Making the trade-off visible is the job.

### 7.4 Change authority

| Change type | Authority |
|---|---|
| Minor — UI, copy, non-breaking API change | PM self-approved |
| Significant — new feature, schema change, new dependency | PM self-approved with AGENTS.md update |
| Phase gate — moving from one phase to the next | PM gate review — explicit go/no-go documented |
| Security-critical — auth, contact fields, tier logic | PM review + security checklist before deploy |
| Contract / budget scope (outsourcing) | PM + sponsor sign-off — written confirmation required |

### 7.5 Cross-timezone change discipline

Change requests raised outside the overlap window receive a written acknowledgment but no approval until the next live sync. No async approvals on anything affecting sprint scope, contract scope, or security. Too much room for misinterpretation.

---

## 8. Phase Gate Process

### 8.1 Phase gate definition

A phase gate is a formal go/no-go decision point between phases. No Phase 2 (or later) work begins until the exit conditions for the current phase are met and the gate decision is documented.

### 8.2 Phase 1 → Phase 2 gate

**Exit conditions — all must be met:**

- [ ] All Phase 1 MVP features live and stable in production
- [ ] 100 verified Tier 1+ users onboarded
- [ ] Minimum 50 accepted connection requests with contact reveal confirmed
- [ ] No open P0 or P1 security issues
- [ ] Cold start problem demonstrably solved — organic vouching chains active without admin seeding
- [ ] User feedback collected from minimum 10 active users

**Gate decision outputs:**
- Go: Phase 2 kickoff issue created, AGENTS.md Phase 2 section activated
- No-go: Blocker documented, revisit date set, Phase 1 work continues

### 8.3 Phase 2 → Phase 3 gate

To be defined at Phase 2 kickoff. Minimum criteria will include: intent board live, minimum viable AI matching producing suggestions, and user adoption of intent posting validated.

---

*This document is the authoritative project management plan for Pitchgate. It is updated at each phase gate and whenever a significant change is approved. Version history is tracked via Git.*
