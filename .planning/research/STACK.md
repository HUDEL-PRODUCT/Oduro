# Stack Research

**Domain:** B2B agency marketing site + enquire-to-booking dogfood automation  
**Researched:** 2026-10-03  
**Confidence:** HIGH (locked vendors + npm/official docs verified; booking vendor recommendation MEDIUM pending product choice)

Locked decisions respected: Next.js on Vercel, Supabase, Make.com, Zoho Mail (inbox) + Zoho ZeptoMail (transactional), PostHog, no Resend, no site chat in v1, booking provider behaviour locked / vendor open.

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why Recommended | Confidence |
|------------|---------|---------|-----------------|------------|
| **Next.js** | **16.3.8** (Active LTS) | Marketing site + enquire Server Actions | App Router is default; Turbopack default bundler; 16.3.8 is the Sept 2026 security-patched Active LTS. Use App Router only. | HIGH |
| **React** / **react-dom** | **19.3.0** | UI runtime | Peer range for Next 16; RSC + Server Actions are the standard mutation path for forms. | HIGH |
| **TypeScript** | **5.x** (≥5.1; current **7.0.2** on npm OK if tooling accepts it — pin what `create-next-app` scaffolds if conflict) | Type safety | Standard for Next/Supabase typed clients. | HIGH |
| **Vercel** | Current platform | Hosting, previews, env, domain | Locked; pairs with Next; MCP at `https://mcp.vercel.com`. | HIGH |
| **Supabase** (Postgres) | Cloud project + JS **@supabase/supabase-js@2.117.2**, **@supabase/ssr@0.12.7** | Enquiry store of record before any email | Locked; RLS + service-role server writes; Make has native Supabase modules (`Watch Events`, Search/Update rows). | HIGH |
| **Make.com** | Current cloud org | Orchestration: AI email → wait → booking check → nurture | Locked; client-operable; official MCP `https://mcp.make.com`; native ZeptoMail + Supabase + Cal.com apps. | HIGH |
| **Zoho Mail** | Org on oduro.co.uk | Human inbox / Reply-To destination | Locked; inbox only — not for automated sends (Zoho usage policy). | HIGH |
| **Zoho ZeptoMail** | Mail Agent on oduro.co.uk | Instant + nurture transactional sends | Locked; Make modules **Send an Email** / **Send a Template Email**; API supports `from`, `reply_to`, HTML body. | HIGH |
| **PostHog** | **posthog-js@1.435.8**, **posthog-node@5.55.0**, optional **@posthog/react@1.11.3** | Product analytics for Oduro site | Locked; official Next pattern uses `instrumentation-client.ts` + `defaults: '2026-05-30'`. | HIGH |
| **Booking provider** | **TBD — prefer Cal.com** (or Calendly) | Shared book link + “has upcoming booking for email?” | Behaviour locked. Both expose email-filtered upcoming/active events; Cal.com has first-class Make **List Bookings** + `attendeeEmail`. | MEDIUM (vendor), HIGH (API capability for Cal.com & Calendly) |

### Supporting Libraries

| Library | Version | Purpose | When to Use | Confidence |
|---------|---------|---------|-------------|------------|
| **zod** | **4.6.5** | Shared request/response schemas for enquire form | Always — validate in Server Action before Supabase write. | HIGH |
| **react-hook-form** + **@hookform/resolvers** | **7.89.0** / **5.9.1** | Client UX (errors, dirty state) | Use when enquire form needs rich client feedback; still re-validate with Zod on the server. Skip if form stays simple enough for native `FormData` + Server Action only. | HIGH |
| **tailwindcss** | **4.3.3** | Styling | Default in current `create-next-app`; keep utility-first for marketing pages. | HIGH |
| **motion** (Framer Motion) | **14.0.0** | Intentional page/hero motion | Marketing polish (2–3 motions); do not over-animate. | HIGH |
| **lucide-react** | **0.544+** (current npm line OK) | Icons | Solution cards / UI affordances without emoji. | MEDIUM |
| **clsx** + **tailwind-merge** | **2.1.1** / **3.7.0** | Class composition | When building reusable UI primitives. | HIGH |
| **vanilla-cookieconsent** | **3.1.0** | Cookie consent (legal v1) | Gate non-essential PostHog cookies until consent; lighter than Cookiebot for a small site. | MEDIUM |
| **sharp** | **0.34+** (npm **0.35.5**) | `next/image` optimization | Default Next image pipeline on Vercel. | HIGH |
| **OpenAI or Anthropic API** (key in Make) | Latest stable provider SDK in Make HTTP/AI modules | Draft instant email from form fields only | Prefer client-owned model API key in Make — not Make AI credits alone. | HIGH |

### Development Tools

| Tool | Purpose | Notes |
|------|---------|-------|
| **Node.js** | Runtime for Next | Next 16 requires **≥ 20.9**. |
| **eslint** + **eslint-config-next** | Lint | Match Next version (**16.3.8**). |
| **prettier** | Format | Optional but recommended for greenfield consistency. |
| **Supabase CLI / MCP** | Schema, migrations, advisors | Use Oduro-owned org only (not Scourge connection). |
| **Make MCP** | Inspect/build scenarios | `https://mcp.make.com`. |
| **PostHog wizard** (optional) | Faster analytics bootstrap | `npx @posthog/wizard` — or manual `instrumentation-client.ts`. |
| **Vercel CLI / MCP** | Deploy + env | Domain oduro.co.uk on Oduro Enterprises team. |

## Integration Architecture (Make + ZeptoMail + Supabase)

Prescriptive wiring for the dogfood loop. Site never sends mail or books meetings through MCP — APIs from Next/Make only.

```
Browser → Next.js Server Action (Zod)
       → Supabase upsert enquiries (source of truth)
       → Make (Watch Events / webhook on INSERT or gated UPDATE)
            → Filter: skip if instant already sent
            → AI draft (model API key in Make)
            → ZeptoMail “Send an Email” (instant, Reply-To → Zoho Mail)
            → Sleep 2 days
            → Booking API: upcoming for attendee email?
                 → Yes → stop; write status in Supabase
                 → No  → ZeptoMail template nurture + opt-out → update Supabase
```

### Supabase role

- **Store first, email second.** Persist enquiry before Make runs.
- **Same email resubmit:** upsert by work email; do **not** trigger a second instant send (gate with `instant_email_sent_at` / equivalent).
- Prefer **Server Action + service role** (server-only env) for insert/upsert so the anon key never gets broad write scope; or a tight RLS `INSERT`/`UPDATE` policy if using publishable key.
- Columns to plan for: identity fields, `primary_route`, `source_pillar`, `source_solution`, free-text improve note, weekly enquiry volume, send/nurture/opt-out timestamps, booking-check result.
- Make modules: **Watch Events** (instant webhook — configure matching Supabase Database Webhook), **Search Rows**, **Update a Row** / **Upsert a Record**.

### Make role

1. Trigger on new eligible enquiry (INSERT, or UPDATE only when first-send flag flips false→true carefully — simplest: INSERT-only + explicit “resend” ops later).
2. Filter duplicates / already-sent.
3. Call model with form fields only; system prompt: no invented results, clients, or prices.
4. **ZeptoMail → Send an Email** for AI HTML/text body; set **From** (named @oduro.co.uk, verified Mail Agent) and **Reply-To** (Zoho Mail inbox).
5. **Sleep** ~2 days.
6. Booking check (see below).
7. If unbooked: **ZeptoMail → Send a Template Email** for fixed nurture (not a second AI draft) + opt-out link; update Supabase.

### ZeptoMail role

- Transactional only; SPF/DKIM for ZeptoMail alongside Zoho Mail on oduro.co.uk.
- Instant: Make **Send an Email** (dynamic AI body).
- Nurture: Make **Send a Template Email** (fixed copy).
- API confirms `reply_to` array — map Reply-To to the human Zoho Mail address.
- Do **not** send automated mail via Zoho Mail mailbox/SMTP.

### Booking check contract

Before nurture, answer: **does this attendee email have an upcoming (non-cancelled) booking?**

| Provider | How to check | Make support | Verdict for Oduro |
|----------|--------------|--------------|-------------------|
| **Cal.com** (preferred candidate) | `GET /v2/bookings?attendeeEmail=&status=upcoming` + header `cal-api-version: 2026-05-01` | Native modules: Watch Booking*, **List Bookings**, API call | Best fit: email filter + Make app. Cancelled ≠ upcoming → nurture still sends (matches business rule). |
| **Calendly** | `GET /scheduled_events?invitee_email=&status=active&min_start_time=now` (+ user or org URI) | Make Calendly app + HTTP | Equally capable for the check; slightly more OAuth/URI ceremony. |
| **Other** | Must expose “list upcoming by invitee email” | Prefer native Make app or stable REST | If missing → defer automated nurture suppression until it can. |

Shared booking link: one event type for all routes; route changes email copy, not calendar.

## Installation

```bash
# Scaffold (App Router, TypeScript, Tailwind, ESLint, Turbopack defaults)
npx create-next-app@latest . --yes

# Pin security-patched Next line
npm install next@16.3.8 react@19.3.0 react-dom@19.3.0

# Data + validation
npm install @supabase/supabase-js@2.117.2 @supabase/ssr@0.12.7 zod@4.6.5

# Forms (optional client layer)
npm install react-hook-form@7.89.0 @hookform/resolvers@5.9.1

# Analytics
npm install posthog-js@1.435.8 posthog-node@5.55.0 @posthog/react@1.11.3

# UI helpers
npm install motion@14.0.0 lucide-react clsx@2.1.1 tailwind-merge@3.7.0 vanilla-cookieconsent@3.1.0

# Dev
npm install -D typescript eslint eslint-config-next@16.3.8 prettier
```

### Env (illustrative)

```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=   # or anon key per project settings
SUPABASE_SERVICE_ROLE_KEY=             # server-only — enquire upsert

# PostHog
NEXT_PUBLIC_POSTHOG_PROJECT_TOKEN=
NEXT_PUBLIC_POSTHOG_HOST=https://eu.i.posthog.com   # or us — match project region

# Public booking link (no secret)
NEXT_PUBLIC_BOOKING_URL=

# Secrets live in Make / Vercel server env — not in the browser:
# MAKE_*, ZEITOMAIL via Make connection, CALCOM_API_KEY or CALENDLY_TOKEN in Make
```

## Alternatives Considered

| Recommended | Alternative | When to Use Alternative |
|-------------|-------------|-------------------------|
| Next.js 16.3.8 App Router | Next 15.5.x Maintenance LTS | Only if a dependency cannot yet run on 16 — otherwise stay on Active LTS. |
| Server Actions + Zod | Route Handler `POST /api/enquire` | Prefer if you need non-form clients or explicit REST for Make polling; Actions are enough for the site form. |
| Make.com | n8n / Zapier / custom workers | Locked to Make for client ownership + MCP. Do not switch in v1. |
| ZeptoMail | Resend / Postmark / SendGrid | Locked out for v1 (Resend explicitly out of scope). |
| Cal.com | Calendly | Choose Calendly if the calendar owner already lives there and OAuth is acceptable; both support email-based upcoming checks. |
| Cal.com / Calendly | HubSpot Meetings / SavvyCal | Only if API can answer upcoming-by-email reliably in Make; verify before locking. |
| posthog-js via `instrumentation-client` | `@posthog/next` pre-release | Stick to documented stable path unless team adopts the new package deliberately. |
| vanilla-cookieconsent | Cookiebot / OneTrust | Use managed CMP if legal counsel requires certified consent logs beyond DIY. |
| Tailwind utilities | CSS Modules / shadcn full kit | shadcn/ui is fine later for forms/buttons; don’t import a full dashboard kit for a marketing site. |

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| **Resend** (or other third-party ESP) for v1 | Explicitly out of scope; Zoho family locked | ZeptoMail via Make |
| **Zoho Mail SMTP / mailbox API** for enquire/nurture | Against Zoho usage policy; risks inbox reputation | ZeptoMail only for automated sends |
| **Site chat / Notion-grounded assistant** | Deferred dogfood | Enquire form + email loop |
| **Pages Router** | App Router is the 2025–2026 standard; splits patterns | App Router + Server Actions |
| **`@supabase/auth-helpers-nextjs`** | Deprecated in favour of `@supabase/ssr` | `@supabase/ssr` |
| **Client-side Supabase writes with service role** | Secret leak | Server Action / Route Handler only |
| **Make AI credits as sole model** | Weaker ownership/control for clients | Client-owned OpenAI/Anthropic key in Make |
| **Second form SaaS** (Typeform, Tally) | Extra vendor; breaks single-source enquire attribution | Next.js form → Supabase |
| **Sending mail from Next.js directly in v1** | Orchestration (wait, booking check, nurture) belongs in Make | Next writes DB; Make sends |
| **Heavy CMS (Contentful, Sanity) in v1** | Catalogue is finite; slows dogfood | MDX/TS content modules or static data in repo |
| **HubSpot as required CRM for Oduro site v1** | Not locked; adds portal complexity | Supabase + Make; HubSpot only when a client already runs it |

## Stack Patterns by Variant

**If booking provider = Cal.com:**
- Make: after Sleep → **List Bookings** with `attendeeEmail` = enquiry email, `status` = `upcoming`.
- Non-empty → stop nurture; empty → ZeptoMail template.
- Optionally also **Watch Booking Created** to mark `booked_at` early in Supabase (nice-to-have, not required for the 2-day gate).

**If booking provider = Calendly:**
- Make: HTTP/`Calendly` module → list scheduled events with `invitee_email`, `status=active`, `min_start_time` ≈ now.
- Same stop/send branch as above.

**If booking provider cannot query by email yet:**
- Ship book link in instant email.
- Defer automated nurture suppression (per PROJECT.md) — do not fake the check.

**If enquire volume stays tiny:**
- Skip reverse proxy for PostHog initially; add Vercel rewrite proxy when adblock loss matters.
- Keep PostHog EU vs US host aligned with project region (UK agency → often EU).

## Version Compatibility

| Package A | Compatible With | Notes |
|-----------|-----------------|-------|
| `next@16.3.8` | `react@^19`, `react-dom@^19`, Node ≥ 20.9 | Pin 16.3.8 for Sept 2026 security fixes. |
| `@supabase/ssr@0.12.7` | `@supabase/supabase-js@^2.114` | Peer satisfied by 2.117.2. |
| `eslint-config-next@16.3.8` | `next@16.3.8` | Keep versions aligned. |
| `posthog-js@1.435.x` | Next App Router `instrumentation-client` | Use `defaults: '2026-05-30'` per current PostHog Next docs. |
| `zod@4.x` | Server Actions / RHF resolvers 5.x | Prefer Zod on server even if client uses RHF. |
| Cal.com API v2 | Make Cal.com **List Bookings** | Requires current `cal-api-version` header on raw HTTP; Make module abstracts this. |

## Sources

- Next.js releases / security: https://nextjs.org/blog/september-2026-security-release — Active LTS **16.3.8** (HIGH)
- Next.js install / App Router defaults: https://nextjs.org/docs/app/getting-started/installation (HIGH)
- Context7 `/websites/nextjs` — Server Actions + Zod form validation (HIGH)
- npm registry 2026-10-03 — package versions listed above (HIGH)
- Supabase SSR: `@supabase/ssr` + Next App Router client patterns (HIGH)
- Make ZeptoMail app: https://apps.make.com/zoho-zeptomail — Send an Email / Send a Template Email (HIGH)
- ZeptoMail API: https://www.zoho.com/zeptomail/help/api/email-sending.html — `from`, `reply_to`, htmlbody (HIGH)
- Make Supabase app: https://apps.make.com/supabase — Watch Events + row modules (HIGH)
- Make Cal.com: https://apps.make.com/cal-com — List Bookings (HIGH)
- Cal.com bookings API: https://cal.com/docs/api-reference/v2/bookings/get-all-bookings — `attendeeEmail`, `status=upcoming` (HIGH)
- Calendly scheduled events: `invitee_email` + `status=active` (HIGH)
- PostHog Next.js: https://posthog.com/docs/libraries/next-js — `instrumentation-client`, posthog-node flush settings (HIGH)
- Project locks: `.planning/PROJECT.md`, `oduro/STACK.md`, `oduro/BUSINESS.md` (HIGH)

---
*Stack research for: Oduro agency site (enquire → ZeptoMail → book → nurture)*  
*Researched: 2026-10-03*
