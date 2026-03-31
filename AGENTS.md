# AGENTS.md — Pitchgate
Read this fully before writing any code.

## Product
Pitchgate (pitchgate.xyz) — verified BD network for crypto/web3.
BD reps and protocols connect through structured profiles,
connection requests, and (later) AI-resolved BD intents.
No cold DMs. No spam. Verified, rate-limited, mutual-consent connections.

## Owner
Bojan Pilipović. No traditional dev background. PM/PO role.
Explain technical decisions in plain language.
Make recommendations — don't present 5 options when 1 is right.
Flag anything that needs manual setup (env vars, dashboard config, etc.)

## Stack
- Frontend: Next.js (App Router), plain JavaScript (no TypeScript)
- Backend: Next.js API routes
- Database: PostgreSQL on Railway
- Auth: Clerk (email sign-up, session management)
- Email: Resend (transactional — request sent, match confirmed)
- AI (Phase 2): Anthropic API, claude-sonnet-4-6 (intent parsing only)
- Hosting: Vercel (frontend), Railway (Postgres)

## Design system
Background: #080A0F
Surface: #0E1118
Surface alt: #141820
Border: rgba(255,255,255,0.07)
Border bright: rgba(255,255,255,0.15)
Accent yellow: #E8FF47 (primary CTA, highlights)
Accent teal: #47FFCA (secondary, labels)
Text primary: #F0F2F7
Text muted: #6B7280
Text muted2: #9CA3AF
Font display: Syne (headings, weights 700/800)
Font body/mono: DM Mono (body, inputs, code)
No Tailwind. No CSS frameworks. Custom CSS only.
No shadows. No gradients except subtle radial glows.
Grid background pattern on hero/section headers.
Mobile responsive required on every component — never optional.

## Folder structure
/
├── app/
│   ├── layout.js
│   ├── page.js                  # landing / marketing page
│   ├── dashboard/
│   │   └── page.js              # authenticated home
│   ├── profiles/
│   │   ├── page.js              # directory / browse
│   │   └── [id]/
│   │       └── page.js          # single profile view
│   ├── requests/
│   │   └── page.js              # inbox — sent + received requests
│   ├── onboarding/
│   │   └── page.js              # profile creation after sign-up
│   └── api/
│       ├── profiles/
│       │   ├── route.js         # GET all, POST create
│       │   └── [id]/
│       │       └── route.js     # GET one, PATCH update
│       ├── requests/
│       │   ├── route.js         # GET inbox, POST new request
│       │   └── [id]/
│       │       └── route.js     # PATCH accept/decline
│       ├── matches/
│       │   └── route.js         # GET confirmed matches
│       ├── vouches/
│       │   └── route.js         # POST vouch for a user
│       └── endorsements/
│           └── route.js         # POST "I know this person"
├── components/
│   ├── ProfileCard.js
│   ├── DirectoryFilters.js
│   ├── RequestButton.js
│   ├── RequestInbox.js
│   ├── MatchReveal.js
│   ├── VouchButton.js
│   └── EndorseButton.js
├── lib/
│   ├── db.js                    # Postgres connection pool
│   ├── auth.js                  # Clerk helpers
│   ├── email.js                 # Resend helpers
│   └── signal.js                # Signal score calculation
├── styles/
│   └── globals.css
├── AGENTS.md
├── README.md
├── SKILLS.md
└── .gitignore

## Trust system — read carefully, this is core logic

### Tiers
Tier 0 — signed up, no vouches. Can browse everything but
         cannot send requests, vouch, or endorse. Sees a
         pending screen explaining how to get vouched.
Tier 1 — vouched. Can send/receive requests, vouch others,
         endorse others. Requires EITHER:
         - 1 vouch from a Tier 2 user, OR
         - 3 vouches from Tier 1 users
         Monthly vouch cap: 5 vouches.
Tier 2 — trusted anchor. Only Bojan (Tier 3) can grant this.
         Their vouches carry triple weight toward Signal score.
         Can verify project associations for same-project colleagues.
         Monthly vouch cap: 20 vouches.
Tier 3 — admin (Bojan only). One account, ever.
         Can grant/revoke Tier 2. Can flip project_verified.
         No vouch cap.

Tier upgrade check runs in the same transaction as every
new vouch insert. Never leave tier stale.
Tier is stored on profiles.tier — never computed on the fly.

### Project association
project_name is self-reported (e.g. "HoudiniSwap").
project_verified is set by Tier 3 (admin) OR by a Tier 2
user who shares the same project_name — they confirm
colleagues actually work there.
Multiple profiles can share the same project_name.
project_verified = true shows ✓ next to project name.
project_verified = false shows project name with no checkmark.
Never allow users to set their own project_verified via API.

### Vouch rules
One vouch per voucher/vouched pair — UNIQUE constraint.
Cannot vouch for yourself.
Tier 0 users cannot vouch.
Monthly cap enforced in POST /api/vouches — count vouches
in current calendar month for this voucher.
Return 429 with human-readable message if cap exceeded.

### Endorsements ("I know this person")
Lighter than a vouch — does not count toward tier upgrade.
Any Tier 1+ user can endorse any other user once.
Adds +1 to Signal score and increments knows_count.
One endorsement per endorser/endorsed pair — UNIQUE constraint.
Cannot endorse yourself.

## Signal score — reputation system

Signal is purely additive. It never decays. Never decreases.

Recalculate and update signal_score on profiles whenever:
- A new vouch is received
- A new endorsement is received
- A new match is confirmed (both sides accepted a request)

Signal score formula:
  +1 per Tier 1 vouch received
  +3 per Tier 2 vouch received
  +1 per endorsement ("I know this person") received
  +5 per confirmed match (automatic on request accepted)

signal_score, knows_count, and matches_count all live on
the profiles table and update atomically with the event
that triggered them — never as a separate async operation.

Display on profile card:
  ◈ Signal 47
  ★ 12 people know this person
  ✓ 3 confirmed matches

## Database schema

profiles:
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  user_id TEXT UNIQUE NOT NULL           -- Clerk user ID
  project_name TEXT NOT NULL
  project_verified BOOLEAN DEFAULT false
  category TEXT NOT NULL                 -- DEX, lending, onramp, wallet, L2...
  chains TEXT[]                          -- ['EVM', 'Solana', 'Cosmos']
  offering TEXT NOT NULL
  seeking TEXT NOT NULL
  deal_types TEXT[]                      -- ['rev_share', 'integration', 'co_marketing']
  telegram TEXT                          -- hidden until match confirmed
  contact_email TEXT                     -- hidden until match confirmed
  tier INTEGER DEFAULT 0                 -- 0 / 1 / 2 / 3
  signal_score INTEGER DEFAULT 0
  knows_count INTEGER DEFAULT 0
  matches_count INTEGER DEFAULT 0
  created_at TIMESTAMPTZ DEFAULT now()

vouches:
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  voucher_id UUID REFERENCES profiles(id)
  vouched_id UUID REFERENCES profiles(id)
  created_at TIMESTAMPTZ DEFAULT now()
  UNIQUE(voucher_id, vouched_id)

endorsements:
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  endorser_id UUID REFERENCES profiles(id)
  endorsed_id UUID REFERENCES profiles(id)
  created_at TIMESTAMPTZ DEFAULT now()
  UNIQUE(endorser_id, endorsed_id)

requests:
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  sender_id UUID REFERENCES profiles(id)
  receiver_id UUID REFERENCES profiles(id)
  message TEXT
  status TEXT DEFAULT 'pending'          -- pending / accepted / declined
  created_at TIMESTAMPTZ DEFAULT now()

matches:
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  profile_a UUID REFERENCES profiles(id)
  profile_b UUID REFERENCES profiles(id)
  created_at TIMESTAMPTZ DEFAULT now()

## Database conventions
Use UUID primary keys (gen_random_uuid()) on all tables.
Use TIMESTAMPTZ DEFAULT now() for all timestamps.
Never return contact fields (telegram, contact_email) unless
a confirmed match exists — enforce at query level, not UI.
Use connection pool via lib/db.js — never instantiate pg
directly in route files.
Always parameterize queries — never interpolate user input.
Signal score and counts update in the same transaction as
the triggering event — never as a separate async operation.

## Core business logic rules
Rate limit: one connection request per sender/receiver pair
per 90 days — enforce in POST /api/requests before inserting.
Return 429 with human-readable message if blocked.
Match creation: create matches row only when request status
set to 'accepted' — do this in PATCH /api/requests/[id]
atomically. Increment matches_count on both profiles and
update signal_score for both in the same transaction.
Contact reveal: telegram and contact_email only in profile
API response if caller has confirmed match with that profile.
Check matches table before building response.
Tier upgrade: check and update tier in same transaction as
new vouch insert. Never leave tier stale.
project_verified: only Tier 3 OR a Tier 2 with same
project_name can set to true — validate in API before update.

## Auth conventions
Use Clerk for all authentication.
Get current user in API routes via Clerk's auth() helper.
Always check auth at top of every API route —
return 401 if no session found.
User's Clerk ID stored as user_id TEXT on profiles table.
One profile per user_id — UNIQUE constraint.

## Email conventions
Use Resend for all transactional email.
Send email in these moments only (MVP):
  1. Connection request received — notify receiver
  2. Request accepted — notify both sides, include contact info
  3. Vouch received — notify vouched user
Email logic lives in lib/email.js — never inline in routes.
Never block API response waiting for email — fire and forget,
use .catch to log errors silently.

## Git hygiene
Never commit secrets — all keys go in .env.local.
.gitignore must cover: .env, .env.local, .env*.local,
node_modules, .DS_Store, .next, out
Always verify .gitignore before first push.
Never use git add . — always add files explicitly by name.
Environment variables:
  DATABASE_URL                           -- Railway Postgres
  NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY
  CLERK_SECRET_KEY
  RESEND_API_KEY
  ANTHROPIC_API_KEY                      -- Phase 2 only

## Code quality
Plain JavaScript — no TypeScript, ever.
Custom CSS only — no Tailwind, no CSS frameworks.
Handle errors gracefully — never crash the UI.
Always show meaningful empty states — never a blank section.
Comments explain why, not just what.
Functions: small, single-purpose.
Use env vars for all configurable values.
No inline styles — all styling in globals.css or
component-level style blocks.

## Security
Never expose secret keys in frontend or client components.
CLERK_SECRET_KEY, RESEND_API_KEY, ANTHROPIC_API_KEY,
DATABASE_URL live only in .env.local / Railway env vars.
Frontend only knows NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY.
Contact fields must never appear in API response unless
match confirmed — most critical security rule in product.
Tier 2 and project_verified can never be self-assigned —
always validate caller tier before allowing these updates.
Remind Bojan before any push if something risks exposing
secrets or contact data prematurely.

## Phase gates — do NOT build yet
- AI intent parsing (Phase 2)
- Intent board or intent posting (Phase 2)
- Semantic matching or embeddings (Phase 3)
- Token or on-chain features
- Public API or webhooks
- Admin dashboard (use DB directly for now)
- Signal leaderboard (post-MVP)
If asked to build these, flag as post-MVP and confirm
before proceeding.

## After each significant feature
Summarize what was built and what is logically next.
Flag any open issues or things needing manual setup.
Suggest the next GitHub issue to work on.
