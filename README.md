# Pitchgate

> The verified BD network for crypto/web3.

BD reps and protocols find each other through structured, verified profiles and mutual-consent connection requests — no cold DMs, no spam, no noise.

Live: [pitchgate.xyz](https://pitchgate.xyz)
Waitlist: [tally.so/r/q4Oky8](https://tally.so/r/q4Oky8)
Twitter: [@pitchgatexyz](https://x.com/pitchgatexyz)

---

## What it does

- **Person profiles** — individual BD reps get vouched by peers and accumulate a Signal reputation score
- **Project profiles** — protocols list what they offer and what they're looking for
- **Connection requests** — person sends a request to a project, project members accept or decline, contact details unlock on match
- **Trust system** — tiered vouching (Tier 0 → 1 → 2 → 3) prevents spam and ensures every profile is real
- **Signal score** — additive reputation metric: vouches + endorsements + confirmed matches

---

## Stack

| Layer | Tech |
|---|---|
| Frontend | Next.js (App Router), plain JavaScript |
| Backend | Next.js API routes |
| Database | PostgreSQL on Railway |
| Auth | Clerk |
| Email | Resend |
| Hosting | Vercel (frontend) + Railway (DB) |
| AI (Phase 2) | Anthropic API, claude-sonnet-4-6 |

---

## Local setup

### Prerequisites
- Node.js 18+
- A Railway account with a Postgres database
- A Clerk account
- A Resend account

### 1. Clone the repo

```bash
git clone https://github.com/YOUR_USERNAME/pitchgate
cd pitchgate
npm install
```

### 2. Set up environment variables

Copy the example file and fill in your values:

```bash
cp .env.example .env.local
```

```env
# Railway Postgres
DATABASE_URL=

# Clerk
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=

# Resend
RESEND_API_KEY=

# Anthropic (Phase 2 only — leave blank for MVP)
ANTHROPIC_API_KEY=
```

### 3. Set up the database

Run the schema SQL against your Railway Postgres instance.
Schema file: `lib/schema.sql`

You can run it via the Railway dashboard query editor or
with psql:

```bash
psql $DATABASE_URL -f lib/schema.sql
```

### 4. Run locally

```bash
npm run dev
```

App runs at `http://localhost:3000`

---

## Database schema

See `lib/schema.sql` for the full schema.

Key tables:
- `profiles` — individual BD reps (person accounts)
- `projects` — protocol/product profiles
- `project_members` — links people to projects
- `vouches` — trust graph
- `endorsements` — "I know this person" signals
- `requests` — connection requests (person → project)
- `matches` — confirmed connections

---

## Trust tiers

| Tier | Who | How to get there |
|---|---|---|
| 0 | Unverified | Just signed up |
| 1 | Vouched | 1 Tier 2 vouch OR 3 Tier 1 vouches |
| 2 | Trusted anchor | Granted by admin only |
| 3 | Admin | Bojan only, one account |

Tier 0 can browse but cannot send requests, vouch, or endorse.

---

## Signal score

Additive reputation score on person profiles. Never decays.

| Event | Points |
|---|---|
| Tier 1 vouch received | +1 |
| Tier 2 vouch received | +3 |
| Endorsement received | +1 |
| Confirmed match | +5 |

---

## Roadmap

**Phase 1 — MVP (current)**
Verified person profiles, project profiles, connection requests,
vouch system, Signal score, match reveal

**Phase 2 — Intent board**
Post BD intents in natural language, AI parsing via Claude API,
match intents against project profiles

**Phase 3 — Smart resolution**
Semantic matching, confidence scoring, ranked suggestions

---

## Project structure

```
app/              Next.js App Router pages and API routes
components/       Reusable UI components
lib/              DB pool, auth helpers, email, signal logic
styles/           Global CSS
AGENTS.md         Instructions for Claude Code — read before coding
SKILLS.md         Reusable patterns and code snippets
```

---

## Environment variables reference

| Variable | Required | Notes |
|---|---|---|
| `DATABASE_URL` | Yes | Railway Postgres connection string |
| `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` | Yes | Clerk frontend key |
| `CLERK_SECRET_KEY` | Yes | Clerk backend key — never expose |
| `RESEND_API_KEY` | Yes | Transactional email |
| `ANTHROPIC_API_KEY` | Phase 2 | Not needed for MVP |

---

## Contributing

This is a private project. Not open for external contributions at this stage.

---

## License

Private. All rights reserved.
