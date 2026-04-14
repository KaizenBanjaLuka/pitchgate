# Project Charter — Pitchgate

> Version 1.0 · Project initiation document

| Field | Detail |
|---|---|
| **Project** | Pitchgate — Verified BD Network for Crypto/Web3 |
| **Owner / Sponsor** | Bojan Pilipović |
| **Initiated** | Q4 2024 |
| **Current status** | Phase 1 — Active (MVP live at pitchgate.xyz) |
| **Next gate** | Phase 2 — AI intent board (pending Phase 1 validation) |

---

## 1. Project Purpose and Business Case

The Web3 BD landscape has no trusted, structured channel for business development outreach. BD reps and protocols rely on cold Telegram DMs and informal Twitter introductions, resulting in high noise, low response rates, and no verifiable signal on who a counterparty actually is.

Pitchgate addresses this by creating a verified, mutual-consent BD network where every participant is vouched by peers, every connection request is intentional, and contact details are only revealed after both sides agree to connect. This reduces friction for legitimate partnerships and eliminates unsolicited outreach entirely.

---

## 2. Objectives and Success Criteria

| Objective | Success criterion | Phase |
|---|---|---|
| Establish a trusted BD network | 100 verified Tier 1+ users onboarded | Phase 1 |
| Validate mutual-consent connection model | 50+ accepted connection requests with contact reveal | Phase 1 |
| Demonstrate Signal score adoption | Avg Signal score >10 across active users | Phase 1 |
| Reduce BD outreach friction | Qualitative feedback from 10+ users citing time saved vs. cold DMs | Phase 1 |
| AI-assisted intent matching | Intent board live with >30 posted intents and >10 AI-suggested matches | Phase 2 |

---

## 3. Scope

### In scope — Phase 1 (MVP)

- Person profile creation and management
- Project profile creation and management
- Tiered vouching system (Tier 0–3)
- Signal reputation score
- Connection request flow (person → project)
- Contact reveal on confirmed match
- Transactional email notifications (Resend)
- Authentication via Clerk
- PostgreSQL data layer on Railway
- Public landing page and waitlist

### Out of scope — post Phase 1

- AI intent parsing (Phase 2)
- Intent board and intent posting (Phase 2)
- Semantic matching / embeddings (Phase 3)
- On-chain or token features
- Public API or webhooks
- Admin dashboard UI
- Signal leaderboard
- Monetization / subscription tiers
- Mobile native app

---

## 4. Constraints, Assumptions, and Dependencies

| Type | Detail |
|---|---|
| **Constraint** | Solo PM/PO-led build — no traditional engineering team. All development is AI-assisted (Claude Code). |
| **Constraint** | Free tier for first 100 verified users. No monetization revenue in Phase 1. |
| **Constraint** | Contact data (telegram, email) must never be exposed in API responses unless match is confirmed — enforced at query level, not UI level. |
| **Assumption** | Target users have existing Web3 networks and can seed initial vouching chains organically. |
| **Assumption** | Clerk, Railway, Vercel, and Resend free/starter tiers are sufficient to support the first 100 users without infrastructure cost. |
| **Dependency** | Clerk for all authentication — no auth is built in-house. |
| **Dependency** | Anthropic API (claude-sonnet-4-6) for Phase 2 AI layer. |

---

## 5. High-Level Milestones

| # | Milestone | Status |
|---|---|---|
| 1 | Architecture and schema finalized — entity model, trust tier logic, folder structure documented in AGENTS.md | ✅ Done |
| 2 | Core auth and onboarding live — Clerk auth, person profile creation, onboarding flow deployed to Vercel | ✅ Done |
| 3 | Trust system and vouching live — Tier 0–3 logic, vouch caps, endorsements, Signal score deployed | ✅ Done |
| 4 | Connection request and match flow live — person → project requests, shared inbox, accept/decline, contact reveal, email | ✅ Done |
| 5 | MVP launch and waitlist open — pitchgate.xyz live, Tally waitlist active, Twitter account launched | ✅ Done |
| 6 | 100 verified users onboarded — Phase 1 success criterion met. Gate review before Phase 2 begins. | 🔄 In progress |
| 7 | Phase 2 kickoff — intent board, Claude API integration for intent parsing, match suggestions | 📋 Planned |

---

## 6. High-Level Risks

| Risk | Likelihood | Impact | Initial response |
|---|---|---|---|
| Cold start — insufficient initial vouching chains to onboard new users organically | High | High | Admin (Tier 3) manually seeds Tier 2 anchors from known network |
| Contact data exposure via API misconfiguration | Low | High | Enforced at query level; never computed at UI level. Reviewed before each push. |
| Third-party service outage (Clerk, Railway, Vercel, Resend) | Medium | High | All services on managed platforms with SLA. No mitigation beyond vendor. |
| Scope creep into Phase 2 features before Phase 1 is validated | High | Medium | Phase gates defined in AGENTS.md. Explicit confirmation required before any Phase 2 work begins. |

---

## 7. Stakeholders

| Stakeholder | Role | Interest / influence |
|---|---|---|
| Bojan Pilipović | Project Sponsor + PM/PO | Full ownership — product vision, delivery, architecture decisions |
| Tier 2 anchor users | Trust gatekeepers | Control the vouching chain quality; critical to network integrity |
| BD reps (Tier 1 users) | Primary end users | Core network participants; validate connection request UX |
| Protocol BD teams (projects) | Secondary end users | Receive and manage inbound connection requests |
| Clerk / Railway / Vercel / Resend | Infrastructure vendors | Low influence, high dependency — platform risk |

---

## 8. Project Sponsor Authorization

| | |
|---|---|
| **Sponsor** | Bojan Pilipović |
| **Role** | Project Sponsor · PM/PO · Pitchgate |
| **Signature** | __________________ |
| **Date** | __________________ |

---

*This charter authorizes the project to proceed. Scope changes, phase gate decisions, and budget changes require sponsor sign-off.*
