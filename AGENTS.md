# AGENTS.md — Pitchgate
Read this fully before writing any code.

## Product
Pitchgate (pitchgate.xyz) — verified BD network for crypto/web3.
BD reps and protocols connect through structured profiles,
connection requests, and (later) AI-resolved BD intents.
No cold DMs. No spam. Verified, rate-limited, mutual-consent connections.
Free for the first 100 verified users.

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

## Two distinct entity types
Pitchgate has two separate profile types. Never conflate them.

PERSON — an individual BD rep or partnerships professional.
  - Gets vouched, accumulates Signal score
  - Belongs to one or more projects as a member
  - Sends and receives connection requests
  - Identified by their Clerk user_id

PROJECT — a protocol, product, or company.
  - Has its own category, chains, offering, seeking fields
  - Created and managed by its BD rep members
  - What BD intents match against in Phase 2
  - Has its own verified status (admin-only flip)
  - Receives connection requests into a shared inbox
  - Multiple people can be members of one project

Think of it like GitHub: person = user account, project = org.
Aaron (person) is a member of HoudiniSwap (project).
When someone sends a request to HoudiniSwap, all its members see it.

## Folder structure
/
├── app/
│   ├── layout.js
│   ├── page.js                        # landing / marketing
│   ├── dashboard/
│   │   └── page.js                    # authenticated home
│   ├── onboarding/
│   │   └── page.js                    # person profile creation
│   ├── people/
│   │   ├── page.js                    # browse BD reps
│   │   └── [id]/
│   │       └── page.js                # single person profile
│   ├── projects/
│   │   ├── page.js                    # browse projects / directory
│   │   ├── new/
│   │   │   └── page.js                # create a new project
│   │   └── [id]/
│   │       └── page.js                # single project profile
│   ├── requests/
│   │   └── page.js                    # inbox — sent + received
│   └── api/
│       ├── profiles/
│       │   ├── route.js               # GET all people, POST create person
│       │   └── [id]/
│       │       └── route.js           # GET one, PATCH update person
│       ├── projects/
│       │   ├── route.js               # GET all projects, POST create
│       │   └── [id]/
│       │       ├── route.js           # GET one, PATCH update
│       │       └── members/
│       │           └── route.js       # GET members, POST join request
│       ├── requests/
│       │   ├── route.js               # GET inbox, POST new request
│       │   └── [id]/
│       │       └── route.js           # PATCH accept/decline
│       ├── matches/
│       │   └── route.js               # GET confirmed matches
│       ├── vouches/
│       │   └── route.js               # POST vouch for a person
│       └── endorsements/
│           └── route.js               # POST "I know this person"
├── components/
│   ├── PersonCard.js
│   ├── ProjectCard.js
│   ├── DirectoryFilters.js
│   ├── RequestButton.js
│   ├── RequestInbox.js
│   ├── MatchReveal.js
│   ├── VouchButton.js
│   └── EndorseButton.js
├── lib/
│   ├── db.js                          # Postgres connection pool
│   ├── auth.js                        # Clerk helpers
│   ├── email.js                       # Resend helpers
│   └── signal.js                      # Signal score calculation
├── styles/
│   └── globals.css
├── AGENTS.md
├── README.md
├── SKILLS.md
└── .gitignore

## Database schema

### profiles (people)
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  user_id TEXT UNIQUE NOT NULL           -- Clerk user ID
  name TEXT NOT NULL
  role TEXT NOT NULL                     -- e.g. "BD Lead", "Partnerships"
  bio TEXT
  telegram TEXT                          -- hidden until match confirmed
  contact_email TEXT                     -- hidden until match confirmed
  twitter TEXT                           -- public, optional
  tier INTEGER DEFAULT 0                 -- 0 / 1 / 2 / 3
  signal_score INTEGER DEFAULT 0
  knows_count INTEGER DEFAULT 0
  matches_count INTEGER DEFAULT 0
  created_at TIMESTAMPTZ DEFAULT now()

### projects
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  name TEXT UNIQUE NOT NULL              -- e.g. "HoudiniSwap"
  category TEXT NOT NULL                 -- DEX, lending, onramp, wallet, L2...
  chains TEXT[]                          -- ['EVM', 'Solana', 'Cosmos']
  offering TEXT NOT NULL                 -- what they bring to a partnership
  seeking TEXT NOT NULL                  -- what they're looking for
  deal_types TEXT[]                      -- ['rev_share', 'integration', 'co_marketing']
  website TEXT
  twitter TEXT
  verified BOOLEAN DEFAULT false         -- admin-only flip
  created_at TIMESTAMPTZ DEFAULT now()

### project_members
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  project_id UUID REFERENCES projects(id)
  profile_id UUID REFERENCES profiles(id)
  role TEXT DEFAULT 'member'             -- 'admin' / 'member'
  created_at TIMESTAMPTZ DEFAULT now()
  UNIQUE(project_id, profile_id)

### vouches
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  voucher_id UUID REFERENCES profiles(id)
  vouched_id UUID REFERENCES profiles(id)
  created_at TIMESTAMPTZ DEFAULT now()
  UNIQUE(voucher_id, vouched_id)

### endorsements
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  endorser_id UUID REFERENCES profiles(id)
  endorsed_id UUID REFERENCES profiles(id)
  created_at TIMESTAMPTZ DEFAULT now()
  UNIQUE(endorser_id, endorsed_id)

### requests
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  sender_profile_id UUID REFERENCES profiles(id)
  receiver_project_id UUID REFERENCES projects(id)  -- requests go to projects
  message TEXT
  status TEXT DEFAULT 'pending'          -- pending / accepted / declined
  created_at TIMESTAMPTZ DEFAULT now()

### matches
  id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  profile_id UUID REFERENCES profiles(id)
  project_id UUID REFERENCES projects(id)
  created_at TIMESTAMPTZ DEFAULT now()

## Trust system — read carefully, this is core logic

### Person tiers
Tier 0 — signed up, no vouches. Can browse everything.
         Cannot send requests, vouch, or endorse.
         Sees a pending screen explaining how to get vouched.
Tier 1 — vouched. Full access. Requires EITHER:
         - 1 vouch from a Tier 2 person, OR
         - 3 vouches from Tier 1 people
         Monthly vouch cap: 5 vouches outgoing.
Tier 2 — trusted anchor. Granted only by Tier 3 (Bojan).
         Can verify project membership for same-project colleagues.
         Monthly vouch cap: 20 vouches outgoing.
Tier 3 — admin (Bojan only). One account, ever.
         Can grant/revoke Tier 2. Can flip projects.verified.
         No vouch cap.

Tier upgrade check runs in the same transaction as every
new vouch insert. Tier stored on profiles — never computed
on the fly.

### Project verification
Any Tier 1+ person can create a project.
New projects have verified = false and are visible but marked.
Only Tier 3 (admin) can set verified = true on a project.
Verified projects get a ✓ badge in the directory.
Unverified projects are visible, clearly labeled as unverified.

### Project membership
First person to create a project is automatically role = 'admin'.
Other people can request to join a project.
Project admin (or Tier 3) approves join requests.
A Tier 2 person can verify that a colleague actually works
at their shared project — sets a verified flag on that
project_members row (not on the project itself).

### Vouch rules
One vouch per voucher/vouched pair — UNIQUE constraint.
Cannot vouch for yourself.
Tier 0 cannot vouch.
Monthly cap enforced in POST /api/vouches.
Return 429 with human-readable message if cap exceeded.

### Endorsements ("I know this person")
Does not count toward tier upgrade.
Any Tier 1+ person can endorse any other person once.
Adds +1 to signal_score and increments knows_count.
UNIQUE(endorser_id, endorsed_id). Cannot endorse yourself.

## Signal score — reputation (persons only)

Signal is purely additive. Never decays. Never decreases.

Recalculate on:
  - New vouch received
  - New endorsement received
  - New match confirmed (automatic when request accepted)

Formula:
  +1 per Tier 1 vouch received
  +3 per Tier 2 vouch received
  +1 per endorsement received
  +5 per confirmed match

signal_score, knows_count, matches_count update atomically
with the event that triggered them — never async.

Display on person profile card:
  ◈ Signal 47
  ★ 12 people know this person
  ✓ 3 confirmed matches

Projects do not have Signal scores in MVP.
Project trust is shown via: verified badge + member count
+ combined Signal of their members (display only, not stored).

## Request and match flow

Requests go FROM a person TO a project.
All project members (role = admin or member) see incoming
requests in a shared project inbox.
Any project member can accept or decline on behalf of project.
On accept:
  - Create a matches row (profile_id + project_id)
  - Increment matches_count on the person's profile
  - Recalculate signal_score for the person (+5)
  - Unlock contact fields: show person's telegram/email to
    project members, show project contact to the person
  - Send email to both sides via Resend
Rate limit: one request per person/project pair per 90 days.

## Contact reveal rules
Person's telegram and contact_email: only visible to members
of a project they have a confirmed match with.
Never return these fields in any API response unless match
confirmed — enforce at query level, not UI level.
This is the most critical security rule in the codebase.

## Database conventions
UUID primary keys everywhere (gen_random_uuid()).
TIMESTAMPTZ DEFAULT now() for all timestamps.
Connection pool via lib/db.js — never instantiate pg directly
in route files.
Always parameterize queries — never interpolate user input.
Signal and counts update atomically with triggering event.

## Auth conventions
Use Clerk for all authentication.
Get current user in API routes via Clerk's auth() helper.
Always check auth at top of every protected API route.
Return 401 if no session. Return 403 if insufficient tier.
user_id on profiles = Clerk user ID (TEXT, UNIQUE).
One person profile per user_id — UNIQUE constraint.

## Email conventions
Use Resend for all transactional email.
Send email only at these moments (MVP):
  1. Connection request received — notify project members
  2. Request accepted — notify both sides with contact info
  3. Vouch received — notify vouched person
Lives in lib/email.js — never inline in route files.
Fire and forget — never block API response on email.
Use .catch to log email errors silently.

## Git hygiene
Never commit secrets — all keys in .env.local only.
.gitignore must cover: .env, .env.local, .env*.local,
node_modules, .DS_Store, .next, out
Never use git add . — always add files explicitly by name.
Verify .gitignore before first push.

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
Meaningful empty states everywhere — never a blank section.
Comments explain why, not what.
Functions: small, single-purpose.
All configurable values via env vars.
No inline styles — globals.css or component style blocks.

## Security
Secret keys never in frontend or client components.
Contact fields never in API response unless match confirmed.
Tier 2 and project verified status can never be self-assigned.
Validate caller tier server-side before any privileged update.
Remind Bojan before any push that risks exposing secrets
or contact data prematurely.

## Phase gates — do NOT build yet
- AI intent parsing (Phase 2)
- Intent board / intent posting (Phase 2)
- Semantic matching or embeddings (Phase 3)
- Token or on-chain features
- Public API or webhooks
- Admin dashboard (use DB directly for now)
- Signal leaderboard (post-MVP)
- Monetization / subscription tier (post 100 users)
Flag these as post-MVP and confirm before proceeding.

## After each significant feature
Summarize what was built and what is logically next.
Flag anything needing manual setup.
Suggest the next GitHub issue to work on.
