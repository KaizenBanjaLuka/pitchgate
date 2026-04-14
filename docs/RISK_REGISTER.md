# Risk Register & Risk Management — Pitchgate

> Version 1.0 · Maintained by PM · Reviewed at each sprint retrospective and phase gate

| Field | Detail |
|---|---|
| **Project** | Pitchgate — Verified BD Network for Crypto/Web3 |
| **PM / Owner** | Bojan Pilipović |
| **Version** | 1.0 |
| **Last reviewed** | Q4 2024 |

---

## Part 1 — Risk Management Framework

### 1.1 What is risk management

Risk management is the practice of identifying things that could go wrong before they do, assessing how likely and how damaging they are, and deciding in advance how to respond. The goal is not to eliminate risk — that is impossible. The goal is to make risk visible and deliberate, so the team is never blindsided.

A risk is a potential future event. An issue is a risk that has already happened. The risk register tracks risks. Issues go on the issue log.

---

### 1.2 Risk identification

Risks are identified from four sources:

- **Historical** — what went wrong on similar projects before
- **Technical** — what could break in the architecture, dependencies, or integrations
- **External** — vendors, market conditions, regulatory changes, third-party platforms
- **Human** — team capacity, knowledge gaps, stakeholder misalignment

In practice, risks surface during sprint planning (new work reveals new unknowns), retrospectives (what nearly went wrong), and whenever a new dependency or integration is introduced.

---

### 1.3 Risk assessment — probability × impact matrix

Every risk is scored on two dimensions:

| | Low impact | Medium impact | High impact |
|---|---|---|---|
| **High likelihood** | Monitor | Mitigate | Mitigate / Avoid |
| **Medium likelihood** | Accept | Monitor | Mitigate |
| **Low likelihood** | Accept | Accept | Mitigate |

**Probability / likelihood scale:**
- Low — unlikely to occur in this project lifecycle
- Medium — could occur, has happened on similar projects
- High — likely to occur without active intervention

**Impact scale:**
- Low — minor inconvenience, resolved within a sprint
- Medium — affects schedule or quality, requires PM attention
- High — threatens delivery, budget, security, or the core product

---

### 1.4 Risk response strategies (PMBOK)

| Strategy | When to use | Example |
|---|---|---|
| **Avoid** | Eliminate the risk entirely by changing the plan | Don't build a feature that introduces the risk |
| **Mitigate** | Reduce probability or impact to acceptable level | Add input validation to reduce SQL injection risk |
| **Transfer** | Shift the risk to a third party | Use a managed auth service (Clerk) instead of building auth in-house |
| **Accept** | Acknowledge the risk and do nothing proactively | Accept vendor SLA risk because no in-house alternative exists |

Active acceptance means you know the risk exists and have a contingency plan. Passive acceptance means you've noted it and will deal with it if it materialises.

---

### 1.5 Residual risk

After a response strategy is applied, some risk remains. This is residual risk. It should be documented and monitored. If residual risk is still High impact, a secondary response should be considered.

---

### 1.6 Risk review cadence

| Context | When |
|---|---|
| Phase-gated delivery | At each phase gate — full register review |
| Sprint-based delivery | Embedded in ceremonies — see table below |

**In a sprint-based model:**

| Ceremony | Risk activity |
|---|---|
| Sprint planning | Scan new work items for unknowns and dependencies |
| Daily standup | Blockers surfaced in real time — unresolved 48h blocker = active risk |
| Sprint review | Stakeholder feedback may reveal scope or expectation risks |
| Sprint retrospective | Formal look-back — what materialised, what is new, register updated |
| Phase gate / release | Full register review before any major release or transition |

---

### 1.7 Risk ownership

Every risk has a named owner. The owner is responsible for monitoring the risk and executing the response strategy. On a solo project, the PM owns all risks. On a team, ownership is assigned to whoever has the most context and control over that risk domain.

A risk without an owner is not managed — it is just documented.

---

## Part 2 — Pitchgate Risk Register

### Risk scoring key

| Score | Likelihood | Impact |
|---|---|---|
| 🔴 High | Likely without intervention | Threatens delivery, security, or core product |
| 🟡 Medium | Could occur | Affects schedule or quality |
| 🟢 Low | Unlikely | Minor, resolvable within a sprint |

---

### Risk register

| ID | Risk | Category | Likelihood | Impact | Response strategy | Response detail | Residual risk | Owner | Status |
|---|---|---|---|---|---|---|---|---|---|
| R-01 | Cold start — insufficient vouching chains to onboard new users organically | Product / Market | 🔴 High | 🔴 High | Mitigate | Admin (Tier 3) manually seeds 5 Tier 2 anchors from existing Web3 / HoudiniSwap network before waitlist activation. Tier 2 anchors commit to vouching first cohort. | 🟡 Medium — organic growth still unproven beyond seeded cohort | PM | Active |
| R-02 | Contact data exposure — telegram and email returned in API response before match confirmed | Security | 🟢 Low | 🔴 High | Mitigate | Contact fields excluded at DB query level, not filtered at UI level. Security rule documented in AGENTS.md. Reviewed before every push touching API routes or contact fields. | 🟢 Low — enforced at query layer with no UI bypass | PM | Active |
| R-03 | Vouching system abuse — colluding users vouch fake or low-quality accounts to Tier 1 | Trust / Integrity | 🟡 Medium | 🔴 High | Mitigate | Monthly vouch caps enforced (Tier 1: 5/month, Tier 2: 20/month). One vouch per pair via UNIQUE constraint. Tier 2 anchors personally known to admin. Admin (Tier 3) can revoke Tier 2 status. | 🟡 Medium — collusion between Tier 1 users remains possible at scale | PM | Active |
| R-04 | Scope creep — Phase 2 features built before Phase 1 is validated | Delivery | 🔴 High | 🟡 Medium | Avoid | Phase gates defined in AGENTS.md. All Phase 2+ items labelled and locked in backlog. Explicit go/no-go required before any Phase 2 work begins. No exceptions. | 🟢 Low — gates enforced through documented process | PM | Active |
| R-05 | AI-assisted development introduces insecure code — SQL injection, auth bypass, or data leakage | Technical / Security | 🟡 Medium | 🔴 High | Mitigate | Parameterized queries enforced across all DB interactions. Auth check at top of every protected route. Security conventions in AGENTS.md reviewed each development session. All output reviewed by PM before commit. | 🟡 Medium — review is manual; no automated security scanning in place | PM | Active |
| R-06 | Third-party platform outage — Clerk, Railway, Vercel, or Resend unavailable | External / Infrastructure | 🟡 Medium | 🔴 High | Transfer / Accept | All services on managed platforms with published SLAs. No viable in-house alternative. Risk transferred to vendor. Accepted as a platform dependency. | 🟡 Medium — no redundancy or failover in place for MVP | Vendor | Accepted |
| R-07 | Low activation — waitlist signups do not convert to active verified profiles | Product / GTM | 🔴 High | 🟡 Medium | Mitigate | Personal outreach to first cohort from existing HoudiniSwap and UNIZEN BD network. Onboarding kept to minimum viable steps. Vouching path clearly communicated on Tier 0 pending screen. | 🟡 Medium — conversion depends on quality of first-cohort outreach | PM | Active |
| R-08 | Resend deliverability failure — match or vouch email not delivered | Technical | 🟢 Low | 🟡 Medium | Mitigate | Email is fire-and-forget — never blocks the API response. Contact reveal and match status accessible in-app regardless of email delivery. Errors logged silently via .catch(). | 🟢 Low — in-app fallback covers core user journey | PM | Active |
| R-09 | Key person dependency — all product, delivery, and technical knowledge held by one person | Operational | 🔴 High | 🔴 High | Mitigate | Architecture, conventions, and decisions documented in AGENTS.md, README, and this PMP. GitHub history provides full decision trail. Phase 2 onwards: consider onboarding a co-founder or technical partner. | 🟡 Medium — documentation reduces but does not eliminate single-point-of-failure risk | PM | Active |
| R-10 | Regulatory or platform risk — Web3 BD coordination tools attracting compliance scrutiny | External / Legal | 🟢 Low | 🟡 Medium | Monitor | No financial transactions, token transfers, or investment advice on platform. Pure coordination and reputation layer. Monitor regulatory developments in key markets (US, EU). | 🟢 Low — current product scope does not trigger known regulatory frameworks | PM | Monitoring |

---

## Part 3 — Risk Response Playbook

For the two highest-priority risks, a response playbook is documented in advance.

### R-01 — Cold start (High / High)

**Trigger:** fewer than 3 organic Tier 1 upgrades in the first 30 days post-launch without admin seeding.

**Response:**
1. PM personally reaches out to 10 known Web3 BD contacts for Tier 2 anchor invitations
2. Tier 2 anchors are briefed on vouching mechanics and asked to vouch 3–5 known contacts each
3. If still blocked after 60 days: consider relaxing Tier 1 requirements temporarily (e.g. 2 Tier 1 vouches instead of 3) with a documented sunset clause
4. Document outcome and update product hypothesis if cold start cannot be solved organically

### R-02 / R-05 — Security (Low→High / Medium→High)

**Trigger:** any suspected or confirmed data exposure of contact fields, or discovery of an unparameterized query.

**Response:**
1. All deployments halted immediately — P0
2. Affected API routes taken offline or patched within 2 hours
3. Scope of exposure assessed — which users, which fields, which time window
4. Affected users notified directly if contact data was exposed
5. Root cause documented in GitHub issue
6. AGENTS.md updated with additional rule to prevent recurrence
7. Deployment resumes only after PM sign-off on fix

---

*This register is a living document. Risks are added, updated, and closed as the project evolves. Last reviewed: Q4 2024.*
