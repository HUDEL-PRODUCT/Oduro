# Architecture Research

**Domain:** Agency marketing site + enquire → book → nurture dogfood automation
**Researched:** 3 October 2026
**Confidence:** HIGH

## Standard Architecture

### System Overview

Greenfield agency sites that dogfood an acquisition loop typically split into a **presentation surface** (Next.js), a **system of record** (Supabase), and an **orchestration plane** (Make) that owns waits, AI drafting, transactional mail, and booking checks. The site never sends mail or books meetings directly; Make talks to ZeptoMail and the booking provider.

```
┌──────────────────────────────────────────────────────────────────────────┐
│  PRESENTATION — Next.js on Vercel (oduro.co.uk)                          │
│                                                                          │
│  Home(+Proof) · Approach · Pillars · Solutions · Enquire · Legal/Cookies │
│       │                                                                  │
│       ▼  Server Action / Route Handler (validate → upsert)               │
│  Supabase service-role client     PostHog (consent-gated)                │
└───────────────────────────┬──────────────────────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────────────────────┐
│  SYSTEM OF RECORD — Supabase Postgres                                    │
│  enquiries: identity, attribution, route, email state, opt-out           │
│  UNIQUE(email) · INSERT → webhook · UPDATE on resubmit (no re-mail)      │
└───────────────────────────┬──────────────────────────────────────────────┘
                            │ Database Webhook (INSERT only)
                            ▼
┌──────────────────────────────────────────────────────────────────────────┐
│  ORCHESTRATION — Make.com (Oduro org)                                    │
│                                                                          │
│  Webhook → AI draft (model API) → ZeptoMail instant (+ booking link)     │
│         → Sleep 2 days → booking check → nurture OR stop                 │
│         → write status back to Supabase                                  │
│                                                                          │
│  ZeptoMail (sends) │ Booking provider (link + check) │ Zoho Mail (inbox) │
└──────────────────────────────────────────────────────────────────────────┘

Human replies → Zoho Mail inbox (Reply-To). Automated sends never use Zoho Mail SMTP.
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| Next.js site | Brand IA, catalogue, Enquire UI, legal, cookie gate, attribution query/state into form | App Router, Server Components for content, client form for interactivity |
| Enquire API boundary | Validate fields, normalize email, upsert enquiry, return success without waiting on Make | Server Action or `app/api/enquire/route.ts` with service-role Supabase client |
| Supabase `enquiries` | System of record: form answers, `primary_route`, `source_pillar`, `source_solution`, send/opt-out timestamps | Postgres table + RLS (deny anon write; server-only insert) + unique email |
| Supabase → Make bridge | Notify orchestration only when a **new** enquiry row is created | Database Webhook on `INSERT` → Make Custom Webhook (or Make Supabase Watch Events) |
| Make scenario | AI instant email → wait 2 days → booking check → one nurture or stop; write status back | One primary scenario; Sleep + Router modules |
| Zoho ZeptoMail | Transactional instant + nurture sends; named From; Reply-To inbox | Make Zoho ZeptoMail / Zoho CPaaS modules (Send Email / Template) |
| Zoho Mail | Human inbox for replies only | DNS MX; not used for automation |
| Booking provider | Shared event-type link in emails; “upcoming booking for this email?” before nurture | Vendor TBD; Cal.com candidate (`GET /v2/bookings?status=upcoming&attendeeEmail=`) |
| PostHog | First-party product analytics on the site | Client SDK behind cookie consent |

## Site Information Architecture

Catalogue depth is a product requirement: visitor sees the full range before enquire. Routes map 1:1 to that IA.

| Route | Purpose | Enquire attribution |
|-------|---------|---------------------|
| `/` | Connected-business outcome; brand hero; light proof teaser; CTA to pillars / enquire | None (or `source_pillar` null) |
| `/approach` | Audit → Design → Build → Test → Launch → Improve | None |
| `/pillars` | Four pillars + interactive solution cards | Optional pillar hover/click → card links to solution |
| `/solutions/[pillar]/[slug]` | Problem → includes → works well with → outcomes → long Enquire CTA | Sets `source_pillar` + `source_solution`; pre-selects `primary_route` |
| `/enquire` | Single form for whole site | Reads query/state: `?pillar=&solution=`; keeps attribution even if user changes primary route |
| `/privacy`, `/terms` | Legal v1 | — |
| Cookie consent | Gate analytics; disclose enquiry/email processing | — |
| Proof | Testimonials on home and/or short `/proof` if needed; keep light in v1 | — |
| Internal tools demo | Embed on Custom software → Internal tools solution page only | Attribution from that solution page |

**Pillar → solution slugs (catalogue):**

```
growth-systems/     websites | funnels-landing-pages
automation/         workflow-automation | follow-up-nurture | dashboards-reporting
custom-software/    internal-tools | web-apps | mobile-apps
ai-systems/         knowledge-assistants | conversational-ai-chatbots | voice-agents
```

**Primary route enum (form):** `growth_systems` | `automation` | `ai_systems` | `custom_software` | `not_sure` — independent of attribution fields.

## Recommended Project Structure

```
apps/web/  (or repo root if single-package)
├── app/
│   ├── (marketing)/
│   │   ├── page.tsx                 # Home: outcome + proof teaser + CTAs
│   │   ├── approach/page.tsx
│   │   ├── pillars/page.tsx
│   │   ├── solutions/[pillar]/[slug]/page.tsx
│   │   ├── enquire/page.tsx         # Form + attribution from searchParams
│   │   ├── privacy/page.tsx
│   │   └── terms/page.tsx
│   ├── api/
│   │   └── enquire/route.ts         # Optional if not using Server Action
│   ├── layout.tsx                   # Nav, footer, cookie banner shell
│   └── globals.css
├── components/
│   ├── enquire/
│   │   ├── EnquireForm.tsx          # Fields + client UX
│   │   └── AttributionFields.tsx    # Hidden source_pillar / source_solution
│   ├── catalogue/
│   │   ├── PillarGrid.tsx
│   │   ├── SolutionCard.tsx
│   │   └── SolutionPageSections.tsx
│   ├── marketing/                   # Hero, approach steps, proof quotes
│   └── legal/CookieConsent.tsx
├── content/
│   └── solutions/                   # Typed MD/TS content per solution (not CMS in v1)
├── lib/
│   ├── supabase/
│   │   ├── server.ts                # Service-role client for enquire write
│   │   └── types.ts                 # Generated or hand types for enquiries
│   ├── enquire/
│   │   ├── schema.ts                # Zod validation
│   │   ├── submit.ts                # Upsert + isNew flag
│   │   └── attribution.ts           # Parse pillar/solution from URL
│   └── analytics/posthog.ts
├── supabase/
│   └── migrations/
│       └── 001_enquiries.sql
└── make/                            # Scenario docs / export JSON (optional)
    └── enquire-nurture.md
```

### Structure Rationale

- **`(marketing)` route group:** Shared chrome without polluting URLs; keeps legal and enquire in the same navigation model.
- **`content/solutions`:** Catalogue copy colocated and versioned; avoids a CMS for v1 while supporting deep solution pages.
- **`lib/enquire`:** Validation and upsert logic stay out of UI components so Make contracts and DB columns have one owner.
- **`supabase/migrations`:** Schema is the contract for Make fields; ship before wiring webhooks.
- **No mail SDK in Next.js:** Enforces the boundary — store first, orchestrate elsewhere.

## Architectural Patterns

### Pattern 1: Store-first, orchestrate-second

**What:** The web app’s only mutation is writing/updating Supabase. Email, AI, wait, and booking checks live in Make.
**When to use:** Always for this dogfood loop (and client copies of the same system).
**Trade-offs:** Visitor success response can return before email sends (correct); debugging spans two systems; Make must update status columns for observability.

**Example:**
```typescript
// lib/enquire/submit.ts
export async function submitEnquiry(input: EnquireInput) {
  const email = input.email.trim().toLowerCase()
  const supabase = createServiceClient()

  const { data: existing } = await supabase
    .from('enquiries')
    .select('id, instant_email_sent_at')
    .eq('email', email)
    .maybeSingle()

  const row = {
    email,
    name: input.name,
    company: input.company,
    primary_route: input.primary_route,
    source_pillar: input.source_pillar,       // may be null
    source_solution: input.source_solution,   // may be null
    improve_text: input.improve_text,
    weekly_enquiry_volume: input.weekly_enquiry_volume,
    updated_at: new Date().toISOString(),
  }

  if (existing) {
    // Resubmit: refresh answers/attribution; do NOT clear instant_email_sent_at
    await supabase.from('enquiries').update(row).eq('id', existing.id)
    return { ok: true, isNew: false }
  }

  await supabase.from('enquiries').insert({
    ...row,
    instant_email_status: 'pending',
  })
  // INSERT webhook → Make; UPDATE path never re-triggers instant mail
  return { ok: true, isNew: true }
}
```

### Pattern 2: Attribution sticky, route editable

**What:** `source_pillar` / `source_solution` are set when the visitor lands on Enquire from a solution CTA (query params or session). `primary_route` is pre-selected from the pillar but remains user-editable. Attribution fields are hidden (or read-only) and must not be cleared when the user changes primary route.
**When to use:** Every catalogue CTA that deep-links to `/enquire`.
**Trade-offs:** Slightly more form state; enables tailored AI copy without fragmenting into multiple forms.

**Example:**
```typescript
// /enquire?pillar=automation&solution=follow-up-nurture
const attribution = {
  source_pillar: searchParams.pillar ?? null,
  source_solution: searchParams.solution ?? null,
  defaultPrimaryRoute: pillarToRoute(searchParams.pillar) ?? 'not_sure',
}
```

### Pattern 3: Idempotent instant email via INSERT-only webhook

**What:** Make is triggered only on Supabase `INSERT`. Same-email resubmits are `UPDATE`s, so they never re-enter the instant-email path. Make still gates on `instant_email_sent_at IS NULL` before send, then writes `instant_email_sent_at` + status after ZeptoMail succeeds.
**When to use:** Required by product rule: one instant email per email address.
**Trade-offs:** If INSERT webhook fails, need Make/Supabase retry or a scheduled “pending” sweeper (phase-2 hardening). Do not fire Make from the Next.js request.

### Pattern 4: Shared booking link, provider-agnostic check

**What:** Instant and nurture emails use the **same** event-type URL. Nurture suppression calls the provider’s “upcoming booking for attendee email?” API. Cancelled = no booking → nurture still sends.
**When to use:** After booking provider is chosen; behaviour is locked now.
**Trade-offs:** Provider choice is deferred; if API cannot answer reliably, defer automated nurture suppression (per STACK.md) rather than guessing.

## Data Flow

### Request Flow (visitor → booked conversation)

```
Visitor browses Home / Pillars / Solution
    ↓ CTA Enquire (?pillar=&solution=)
EnquireForm (primary_route pre-selected; attribution sticky)
    ↓ submit
Server Action / Route Handler
    → Zod validate
    → normalize email
    → Supabase upsert
         ├─ INSERT (new)  → Database Webhook → Make
         └─ UPDATE (same email) → stop (no second instant)
    ↓
HTTP 200 + thank-you UI (do not await Make)

Make (INSERT path only)
    → read record (route + attribution + improve_text + volume)
    → model API: draft instant email (no invented results/prices/names)
    → ZeptoMail: send (From named@oduro.co.uk, Reply-To Zoho Mail inbox)
         body includes shared booking link
    → update enquiry: instant_email_sent_at, instant_email_status='sent'
    → Sleep 2 days
    → Booking provider: upcoming for this email?
         ├─ yes → stop; mark nurture_skipped_reason='booked'
         └─ no  → ZeptoMail fixed nurture + opt-out link
                   → nurture_sent_at

Human reply → Zoho Mail inbox (outside Make)
Opt-out click → small Next/API or Make webhook → enquiries.opted_out_at
```

### State Management

```
Enquiry lifecycle (Supabase columns)
  pending → instant_sent → [wait] → booked | nurture_sent | opted_out

UI state (client)
  form fields + attribution (from URL) — ephemeral until submit

Make scenario state
  execution / sleep — not the source of truth; write back to Supabase
```

### Key Data Flows

1. **Attribution capture:** Solution page CTA → query params → EnquireForm hidden fields → persisted columns used by AI draft (and optionally nurture personalization later).
2. **Idempotent enquire:** Unique email + INSERT webhook only + never clear `instant_email_sent_at` on resubmit.
3. **Nurture suppression:** Make Sleep → booking API by attendee email → branch; cancelled bookings count as absent.
4. **Opt-out:** Link in nurture → set `opted_out_at`; any future automation must check this flag (v1 has only one nurture, but store it for dogfood honesty).

### Suggested `enquiries` fields (contract)

| Column | Notes |
|--------|--------|
| `id` | uuid PK |
| `email` | unique, lowercased |
| `name`, `company` | |
| `primary_route` | pillar enum + `not_sure` |
| `source_pillar`, `source_solution` | nullable attribution; keep on resubmit |
| `improve_text` | free text |
| `weekly_enquiry_volume` | structured or short text |
| `instant_email_status` | `pending` \| `sent` \| `failed` |
| `instant_email_sent_at` | null until first successful send |
| `nurture_sent_at`, `nurture_skipped_reason` | `booked` \| `opted_out` \| null |
| `opted_out_at` | |
| `created_at`, `updated_at` | |

## Suggested Build Order (dependencies)

Build along the data-flow spine; do not wire ZeptoMail before the store exists.

| Order | Component | Depends on | Delivers |
|-------|-----------|------------|----------|
| 1 | Accounts + DNS | Domain ownership | Vercel, Supabase, Make, Zoho Mail, ZeptoMail SPF/DKIM |
| 2 | Supabase schema + RLS | Supabase project | `enquiries` contract; unique email |
| 3 | Next.js shell + IA routes | Vercel project | Home, approach, pillars, solution stubs, legal shells |
| 4 | Catalogue content + solution pages | IA routes | Attribution-capable CTAs; internal-tools embed |
| 5 | Enquire form + server upsert | Schema + content CTAs | Store-first submissions; resubmit update behaviour |
| 6 | Supabase INSERT webhook → Make | Working upsert | Orchestration entry |
| 7 | Make instant path (AI + ZeptoMail + booking link) | Webhook + ZeptoMail agent + model key | Tailored instant email |
| 8 | Booking provider event type | Calendar owner decision | Shared link in email |
| 9 | Make nurture path (Sleep + booking check + fixed email + opt-out) | Instant path + booking API | Full dogfood loop |
| 10 | Cookie consent + PostHog + legal copy polish | Live enquire/email | Compliant analytics and policies |

**Phase ordering rationale for roadmap:** Site IA and store before Make; instant email before nurture wait; booking provider before automated suppression; legal/cookies before or with enquire go-live (required for email collection).

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| 0–1k enquiries | Single Next.js app, one Supabase project, one Make scenario — correct |
| 1k–100k | Add pending-email sweeper; ZeptoMail dedicated Agent; index `email`, `instant_email_status`; monitor webhook failures in `net` schema |
| 100k+ | Unlikely for agency site; if reused as client template, isolate Make orgs per client (already the ownership model) |

### Scaling Priorities

1. **First bottleneck:** Make Sleep + webhook reliability (missed INSERT → no email). Mitigation: status columns + periodic “pending > N minutes” rescue scenario.
2. **Second bottleneck:** Booking API rate limits / filter correctness on nurture day. Mitigation: provider smoke test before enabling auto-nurture.

## Anti-Patterns

### Anti-Pattern 1: Send email from the Next.js request

**What people do:** Call ZeptoMail (or SMTP) inside the Server Action, then write to Supabase.
**Why it's wrong:** Couples visitor latency to ESP; breaks “store before any email”; hard for clients to own/edit the flow; duplicates what Make is sold for.
**Do this instead:** Upsert → webhook → Make → ZeptoMail.

### Anti-Pattern 2: Trigger Make on every upsert

**What people do:** Webhook on INSERT and UPDATE, or call Make HTTP from the app on every submit.
**Why it's wrong:** Resubmits send a second instant email; violates product rule.
**Do this instead:** INSERT-only webhook + never reset `instant_email_sent_at`.

### Anti-Pattern 3: Zoho Mail for transactional sends

**What people do:** SMTP/API through Zoho Mail mailbox for enquire/nurture.
**Why it's wrong:** Against Zoho Mail usage policy; risks human inbox reputation.
**Do this instead:** ZeptoMail (Make modules) for automation; Zoho Mail as Reply-To / inbox only.

### Anti-Pattern 4: Multiple enquire forms per pillar

**What people do:** Separate forms or routes that fragment attribution.
**Why it's wrong:** Harder to maintain; weaker routing; catalogue CTAs diverge.
**Do this instead:** One `/enquire` with sticky attribution + editable primary route.

### Anti-Pattern 5: AI invents proof in instant email

**What people do:** Loose prompt that allows case studies, prices, or results.
**Why it's wrong:** Compliance and brand risk; not grounded in form answers.
**Do this instead:** Prompt constrained to form fields + shared booking link; fixed nurture (no second AI draft).

### Anti-Pattern 6: Booking-provider lock-in in app code

**What people do:** Hardcode Cal.com SDK in Next.js for the loop.
**Why it's wrong:** Provider is intentionally open; site should only deep-link the shared URL.
**Do this instead:** Booking URL as config/env; Make owns the “has booking?” check adapter.

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| Supabase | Server-side service role write; Database Webhooks → Make | Webhooks are async via `pg_net`; do not block inserts ([docs](https://supabase.com/docs/guides/database/webhooks)) |
| Make | Custom Webhook or Supabase Watch Events trigger | Prefer dashboard webhook URL pasted into Supabase ([Make Supabase app](https://apps.make.com/supabase)) |
| Zoho ZeptoMail | Make Send Email / Template Email | Official Make integration; Agent + From must match ([ZeptoMail Make help](https://www.zoho.com/zeptomail/help/make-integration.html)); logs in Processed emails |
| Zoho Mail | DNS + human mailbox | Reply-To only for automation |
| Booking provider | Public booking URL + API check in Make | Behaviour locked; Cal.com: `attendeeEmail` + `status=upcoming` ([API](https://cal.com/docs/api-reference/v2/bookings/get-all-bookings)) |
| Model API | HTTP module inside Make | Client-owned key; not Make AI credits alone |
| PostHog | Browser SDK | Consent-gated; not the client reporting product |
| Vercel | Host Next.js | Oduro Enterprises team/project |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| Marketing UI ↔ Enquire submit | FormData → Server Action | No direct Supabase anon inserts for enquiries |
| Enquire submit ↔ Supabase | Service-role upsert | RLS: no public write |
| Supabase ↔ Make | HTTP webhook on INSERT | Payload: `type`, `table`, `record` |
| Make ↔ ZeptoMail | Make app module | Instant AI body; nurture fixed template/body |
| Make ↔ Booking | HTTP / native module | Check before nurture only |
| Make ↔ Supabase | HTTP update row | Status write-back for ops visibility |
| Site ↔ Booking | Outbound link only | No booking SDK required in v1 |

## Component Boundary Summary (for roadmap)

```
[Visitor]
   → Next.js (IA + form + legal)
   → Supabase (canonical enquiry + attribution + email state)
   → Make (AI instant → wait → booking check → nurture)
   → ZeptoMail (sends) + Booking provider (link + check)
   → Zoho Mail (human replies)
```

**Trust boundary:** Anything that sends mail or reads calendars stays in Make with secrets in the Make org — not in the browser, not in Scourge MCP orgs.

## Sources

- Project decisions: `.planning/PROJECT.md`, `oduro/STACK.md`, `oduro/BUSINESS.md` (3 Oct 2026)
- Supabase Database Webhooks: https://supabase.com/docs/guides/database/webhooks (HIGH — official; payload INSERT/UPDATE/DELETE)
- Make ↔ Supabase webhook setup: https://apps.make.com/supabase (HIGH — official Make app docs)
- Next.js Server Actions / forms: https://nextjs.org/docs/app/guides/forms (HIGH — Context7 `/websites/nextjs`)
- Zoho ZeptoMail Make integration: https://www.zoho.com/zeptomail/help/make-integration.html (HIGH — official; page updated Sep 2026; may surface as Zoho CPaaS branding)
- ZeptoMail send API (boundary check): https://www.zoho.com/zeptomail/help/api/email-sending.html (HIGH)
- Cal.com bookings filter (candidate provider): https://cal.com/docs/api-reference/v2/bookings/get-all-bookings (MEDIUM — candidate only; behaviour locked, vendor open)

---
*Architecture research for: Oduro agency site + enquire dogfood automation*
*Researched: 3 October 2026*
