# Project Management Plan — Pitchgate

> Version 1.0 · Living document — updated at each phase gate

| Field | Detail |
|---|---|
| **Project** | Pitchgate — Verified BD Network for Crypto/Web3 |
| **PM / PO** | Bojan Pilipović |
| **Plan version** | 1.0 |
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

### 2.1 Scheduling approach

Pitchgate is delivered in a phase-gated model rather than fixed sprints. Each phase has a defined entry condition (what must be true before it starts) and exit condition (what must be true before the next phase begins).

Work within each phase is managed as a continuous flow using the GitHub Kanban board. Issues are pulled from backlog as capacity allows, with no fixed sprint cadence.

### 2.2 Phase structure

| Phase | Description | Entry condition | Exit condition |
|---|---|---|---|
| **Phase 0** | Architecture and foundations | Project charter approved | Schema finalized, AGENTS.md complete, repo initialized |
| **Phase 1** | MVP — core product | Phase 0 complete | All MVP features live, 100 verified users onboarded |
| **Phase 2** | Intent board + AI layer | Phase 1 exit validated | Intent board live, AI matching producing suggestions |
| **Phase 3** | Semantic resolution engine | Phase 2 validated | Confidence scoring, ranked match suggestions live |

### 2.3 Phase 1 milestone schedule

| Milestone | Description | Status |
|---|---|---|
| M1 | Architecture finalized — schema, entity model, AGENTS.md | ✅ Done |
| M2 | Auth and onboarding live — Clerk, person profile creation | ✅ Done |
| M3 | Trust system live — Tier logic, vouch caps, endorsements, Signal score | ✅ Done |
| M4 | Connection flow live — requests, inbox, accept/decline, contact reveal, email | ✅ Done |
| M5 | MVP launch — pitchgate.xyz live, waitlist open, Twitter live | ✅ Done |
| M6 | 100 verified users onboarded — Phase 1 exit criterion met | 🔄 In progress |

### 2.4 Schedule risk

The primary schedule risk is the cold start problem: new users cannot reach Tier 1 without existing Tier 1/2 vouchers in their network. This creates a dependency on manual seeding by the admin (Tier 3) and early Tier 2 anchors. Mitigation is covered in the Risk Register.

---

## 3. Resource Management

### 3.1 Team structure

Pitchgate is a solo-led product. All product, delivery, and architectural decisions are made by the PM/PO. Development is executed via AI-assisted development (Claude Code), with the PM/PO responsible for directing, reviewing, and deploying all output.

| Role | Person | Responsibilities |
|---|---|---|
| Project Sponsor | Bojan Pilipović | Strategic direction, go/no-go decisions, phase gate approval |
| PM / PO | Bojan Pilipović | Scope definition, backlog management, delivery oversight |
| Lead Developer (AI-assisted) | Claude Code (directed by PM) | Feature implementation, API routes, database queries, UI |
| Infrastructure | Vercel / Railway / Clerk / Resend | Managed services — no internal ops resource required |

### 3.2 Infrastructure and tooling

| Layer | Tool | Purpose |
|---|---|---|
| Frontend hosting | Vercel | Automatic deploys from main branch |
| Database | PostgreSQL on Railway | Managed Postgres — no DBA required |
| Auth | Clerk | Session management, user identity |
| Email | Resend | Transactional email |
| Version control | GitHub | Source of truth, Kanban board, issue tracking |
| AI development | Claude Code | AI-assisted feature development |
| Waitlist | Tally | Pre-launch waitlist capture |

### 3.3 Environment variables and secrets

All secrets are managed via environment variables. No secrets are committed to the repository. The following are required for Phase 1:

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
| R-07 | Low user activation — waitlist signups do not convert to active profiles | High | Medium | **Mitigate** — Personal outreach to first cohort from existing HoudiniSwap and Web3 BD network. Onboarding flow kept minimal (no friction on profile creation). | PM | Active |
| R-08 | Resend email deliverability issues cause failed match notifications | Low | Medium | **Mitigate** — Email is fire-and-forget (never blocks API response). Errors logged silently. Contact reveal is accessible in-app regardless of email delivery. | PM | Active |

### 4.3 Risk review cadence

Risks are reviewed at each phase gate and whenever a new high-impact issue is raised. Risk register is updated in this document at each review.

---

## 5. Communications Management

### 5.1 Stakeholder communication approach

Given the solo-led structure, formal internal communication overhead is minimal. Communication is focused on two audiences: **external users/community** and **the PM's own delivery record**.

### 5.2 Communications matrix

| Audience | Channel | Frequency | Content | Owner |
|---|---|---|---|---|
| Waitlist / early users | Tally (waitlist), Twitter @pitchgatexyz | As needed / milestone-based | Launch updates, feature announcements, onboarding invites | PM |
| Tier 2 anchors (trust gatekeepers) | Direct message (Telegram / Twitter) | Weekly during seeding phase | Onboarding status, vouching guidance, feedback requests | PM |
| Active users (Tier 1+) | In-app email via Resend | Event-triggered | Connection request received, request accepted, vouch received | Automated |
| Delivery record (self) | GitHub issues + Kanban board | Continuous | Feature status, decisions, blockers, retrospective notes | PM |
| Public / market | Twitter @pitchgatexyz, Medium | Monthly / milestone | Product updates, BD ecosystem commentary | PM |

### 5.3 Escalation path

As a solo-led project, escalation is self-managed. Any blocker that cannot be resolved within 48 hours is documented as a GitHub issue with the label `blocker` and triaged at the next working session. Critical security issues are treated as P0 — all other work stops until resolved.

---

## 6. Quality Management

### 6.1 Quality approach

Quality is enforced through documented conventions (AGENTS.md), consistent code review (PM reviews all AI-generated output before commit), and a set of non-negotiable rules applied to every feature.

### 6.2 Non-negotiable quality rules

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

### 6.3 Definition of done

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

### 7.1 Change request process

| Step | Action |
|---|---|
| 1 | Change identified — raised as GitHub issue with label `change-request` |
| 2 | PM assesses: scope impact, schedule impact, architectural risk |
| 3 | Decision documented on the issue: approved / rejected / deferred with rationale |
| 4 | If approved: AGENTS.md updated, work added to backlog |
| 5 | If deferred: labelled with correct phase, added to backlog, not actioned |

### 7.2 Change authority

| Change type | Authority |
|---|---|
| Minor — UI, copy, non-breaking API change | PM self-approved |
| Significant — new feature, schema change, new dependency | PM self-approved with AGENTS.md update |
| Phase gate — moving from one phase to another | PM gate review required — explicit go/no-go decision documented |
| Security-critical — any change touching auth, contact fields, or tier logic | PM review + security checklist before deploy |

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
- [ ] Cold start problem demonstrably solved (organic vouching chains active without admin seeding)
- [ ] User feedback collected from minimum 10 active users

**Gate decision outputs:**
- Go: Phase 2 kickoff issue created, AGENTS.md Phase 2 section activated
- No-go: Blocker documented, revisit date set, Phase 1 work continues

### 8.3 Phase 2 → Phase 3 gate

To be defined at Phase 2 kickoff. Minimum criteria will include: intent board live, minimum viable AI matching producing suggestions, and user adoption of intent posting validated.

---

*This document is the authoritative project management plan for Pitchgate Phase 1. It is updated at each phase gate and whenever a significant change is approved.*
