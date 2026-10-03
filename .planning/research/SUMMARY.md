# Project Research Summary

**Project:** Oduro Agency Site (oduro.co.uk)
**Domain:** B2B agency marketing site + enquire → book → nurture dogfood automation
**Researched:** 2026-10-03
**Confidence:** HIGH

## Executive Summary

Oduro’s v1 product is a **public agency marketing site that dogfoods the first system it sells**: a stranger enquires → gets an AI-tailored instant email with a shared booking link → receives one fixed nurture after ~2 days only if they have not booked. The brand promise is a **connected business** (growth / automation / software / AI as one system); the enquire loop is proof, not the slogan. Experts build this as three planes: **Next.js presentation**, **Supabase system of record**, **Make orchestration** for AI, waits, ZeptoMail, and booking checks — the site never sends mail or books meetings itself.

**Recommended approach:** Pin Next.js **16.3.8** App Router on Vercel; Zod-validated Server Action upserts into Supabase (unique work email; INSERT-only webhook to Make); Make drafts the instant email from form fields only and sends via **ZeptoMail** (Reply-To → Zoho Mail inbox); schedule nurture via **due-at + scheduled scenario** (never multi-day Make Sleep); gate nurture on a live “upcoming booking for this email?” API (prefer Cal.com). Ship the full deep catalogue (pillars → 11 solution pages) with sticky attribution into one Enquire form; keep proof light (real testimonials + Internal tools demo embed). Legal/cookies ship with enquire go-live.

**Key risks:** (1) Zoho Mail used for automation → inbox suspension — ZeptoMail only; (2) SPF overwrite when Mail + ZeptoMail share oduro.co.uk — merge DNS before first send; (3) Make Sleep for the 2-day wait — split scenarios; (4) booking vendor assumed without API proof — gate nurture automation; (5) Agentryx copy clone — colour/IA depth only; (6) wrong-org provisioning via Scourge MCP — Oduro Enterprises only. Mitigate by encoding store-first + INSERT-only idempotency in schema/scenario, dual-path DNS verification as a go-live blocker, and a written booking API proof before nurture ships.

## Key Findings

### Recommended Stack

Details: [STACK.md](./STACK.md)

Locked vendors with verified npm/docs versions. Site writes DB only; Make owns mail, AI, wait, and booking. Prefer Cal.com for Make-native List Bookings by `attendeeEmail`; Calendly equally capable if OAuth is acceptable. No Resend, no site chat, no mail from Next.js in v1.

**Core technologies:**
- **Next.js 16.3.8 + React 19.3** — marketing site + Server Actions — Active LTS security-patched App Router default
- **Supabase** (`@supabase/supabase-js@2.117.2`, `@supabase/ssr@0.12.7`) — enquiry store of record before any email — RLS + service-role server writes
- **Make.com** — AI draft → ZeptoMail → wait → booking check → nurture — client-operable; native ZeptoMail/Supabase/Cal.com apps
- **Zoho ZeptoMail + Zoho Mail** — transactional sends vs human inbox — policy-safe split; Reply-To bridges them
- **PostHog** (`posthog-js` + `instrumentation-client`) — own-site analytics — consent-gated
- **Cal.com (preferred) / Calendly** — shared book link + upcoming-by-email check — behaviour locked, vendor TBD
- **zod 4.x** (+ optional RHF) — enquire validation — server re-validate always
- **Tailwind 4 + motion** — marketing UI — utility-first; 2–3 intentional motions

### Expected Features

Details: [FEATURES.md](./FEATURES.md)

MVP validates Core Value: enquire → tailored path → booked conversation, while selling interconnection via a full catalogue. Defer blog, industries, chat, Voice dogfood, and case-study CMS.

**Must have (table stakes):**
- Outcome-led homepage + Approach (Audit→Improve) — what you do / for whom / how
- Shallow nav: Solutions / Approach / Proof / Enquire — offer, proof, talk
- Pillars overview + 11 solution detail pages with cross-links — catalogue completeness
- Persistent Enquire CTA + one qualify-enough form — route, attribution, volume, improve note
- Post-submit confirmation — next step clear (email / book)
- Light social proof (real testimonials) — no invented cases
- Legal: privacy, terms, cookie consent — UK/GDPR for form + email
- Mobile-responsive / fast + PostHog — 2026 baseline + funnel visibility

**Should have (competitive):**
- Deep catalogue IA with “works well with” — interconnection before enquire
- Source-aware Enquire (sticky attribution, editable primary route) — tailored follow-up
- Instant AI email (form fields only) + shared booking link — Core Value path
- Booking-aware single nurture (2-day + opt-out) — stop if booked; fixed copy
- Same-email idempotent submit — no second instant email
- Dogfood as meta-proof + Internal tools demo embed — sell what you run

**Defer (v2+):**
- Site chat / Notion assistant, Voice (Vapi) dogfood, Stripe on agency site
- Full blog, industries hub, case-study CMS, long nurture drips
- On-site calendar as primary CTA, pricing configurator, live chat widgets
- Resend / non-Zoho ESP; Agentryx “successor” narrative

### Architecture Approach

Details: [ARCHITECTURE.md](./ARCHITECTURE.md)

Three-plane architecture: presentation (Next.js) → system of record (Supabase `enquiries`) → orchestration (Make → ZeptoMail / booking / status write-back). Patterns: **store-first**, **attribution sticky / route editable**, **INSERT-only webhook for idempotent instant email**, **provider-agnostic booking check in Make**. Catalogue in repo content modules (no CMS v1). Opt-out and email state live on the enquiry row.

**Major components:**
1. **Next.js site** — IA, catalogue, Enquire UI, legal, cookie gate, attribution into form
2. **Enquire API boundary** — Zod validate → normalize email → service-role upsert; thank-you without awaiting Make
3. **Supabase `enquiries`** — unique email; attribution; send/nurture/opt-out timestamps; INSERT webhook only
4. **Make scenarios** — instant path (AI + ZeptoMail) separate from nurture path (due-at schedule + booking check)
5. **ZeptoMail / Zoho Mail / booking provider** — transactional sends; human inbox; shared link + upcoming-by-email API
6. **PostHog** — consent-gated first-party analytics

### Critical Pitfalls

Details: [PITFALLS.md](./PITFALLS.md)

1. **Zoho Mail for automation** — Use ZeptoMail modules only; Zoho Mail = inbox + Reply-To; go-live blocker
2. **SPF/DKIM collision on oduro.co.uk** — Merge SPF includes; keep both DKIMs; verify ZeptoMail before traffic
3. **Multi-day Make Sleep** — Split instant vs scheduled due-at nurture; Sleep is minutes-scale only
4. **Unproven booking “has upcoming?” API** — Vendor gate with live test; cancelled = send nurture; defer suppression if API missing
5. **Duplicate instant emails** — Unique email upsert + INSERT-only trigger + never clear `instant_email_sent_at`
6. **Agentryx clone / overclaim** — Colour + IA depth only; AI prompt forbids invented proof; real testimonials only
7. **Wrong-account ownership** — Bootstrap Oduro Enterprises first; never provision via Scourge MCP
8. **Cookies / opt-out afterthought** — Gate PostHog; nurture must have working opt-out; privacy names the real vendors

## Implications for Roadmap

Based on research, suggested phase structure (dependency spine: accounts → store → site IA → catalogue → enquire → instant email → booking/nurture → analytics polish):

### Phase 1: Account Bootstrap & Deliverability
**Rationale:** Wrong-org and broken DNS are highest-cost recoveries; nothing else should send until ZeptoMail is verified.
**Delivers:** Oduro Enterprises projects (Vercel, Supabase, Make, Zoho Mail, ZeptoMail); merged SPF/DKIM; dual send-path auth tests; booking account scaffold (vendor may still be TBD).
**Addresses:** Constraint — client-owned stack; email policy split
**Avoids:** Wrong-account ownership; Zoho Mail misuse; SPF overwrite

### Phase 2: Site Shell, IA Routes & Legal
**Rationale:** Legal/cookies must exist before public enquire; shell unlocks catalogue and form work in parallel later.
**Delivers:** Next.js App Router shell on Vercel; routes for home, approach, pillars, solutions stubs, enquire, privacy, terms; cookie consent gating non-essential scripts; nav/footer chrome.
**Addresses:** Table stakes — nav, legal, responsive shell; PostHog wiring gated
**Avoids:** Cookie consent afterthought; placeholder policies at go-live

### Phase 3: Catalogue Content & Attribution CTAs
**Rationale:** Deep catalogue is a P1 product requirement and feeds Enquire attribution; content originality is a brand go-live gate.
**Delivers:** Four pillars + 11 solution pages (problem → includes → works well with → outcomes); cross-links; Enquire CTAs with `?pillar=&solution=`; Internal tools demo embed; homepage outcome + Approach + light testimonials (original copy).
**Addresses:** Table stakes + differentiator catalogue IA; light proof
**Avoids:** Agentryx clone; catalogue overclaim / invented proof

### Phase 4: Enquire Store & Idempotent Submit
**Rationale:** Store-first is the architectural spine; Make must not run until upsert + unique email + INSERT-only contract exists.
**Delivers:** Supabase `enquiries` migration + RLS; Zod schema; Server Action upsert; thank-you UX; attribution sticky / route editable; double-submit tests.
**Addresses:** Enquire form + attribution; same-email idempotency
**Avoids:** Duplicate instant emails; anon service-role leaks; email-from-Next anti-pattern

### Phase 5: Instant Email Orchestration (Make + ZeptoMail + AI)
**Rationale:** Core Value path starts here; nurture depends on instant path writing durable due/status fields.
**Delivers:** Supabase INSERT → Make; constrained AI draft (form fields only); ZeptoMail Send Email with booking link + Reply-To inbox; status write-back (`instant_email_sent_at`); incomplete-execution storage; own model API key in Make.
**Addresses:** Instant AI-tailored email; dogfood credibility
**Avoids:** Zoho Mail outbound; AI hallucination; triggering Make on UPDATE

### Phase 6: Booking Provider Gate & Nurture Loop
**Rationale:** Behaviour locked but vendor open; nurture automation must not ship without API proof; wait must not use Sleep.
**Delivers:** Chosen provider + shared event type; documented “upcoming by email” proof; scheduled nurture scenario (due-at query); booking check (cancelled = nurture); one fixed ZeptoMail template + opt-out → Supabase suppression.
**Addresses:** Booking-aware single nurture + opt-out
**Avoids:** Fake booking check; multi-day Sleep; nurture without opt-out; credit-burning pollers

### Phase 7: Analytics Hardening & Launch Verification
**Rationale:** Funnel visibility and “looks done but isn’t” checklist after the loop works end-to-end.
**Delivers:** Consent-gated PostHog events on enquire funnel; privacy copy naming Make/ZeptoMail/booking; go-live checklist (DNS, ZeptoMail path, idempotency, book/cancel/nurture matrix, copy freeze).
**Addresses:** Analytics; launch compliance polish
**Avoids:** Shipping with unchecked deliverability or duplicate-send gaps

### Phase Ordering Rationale

- Accounts and DNS before any real ZeptoMail traffic (policy + deliverability blockers).
- Legal/cookies with or before public enquire (PECR / GDPR).
- Catalogue before or tightly with Enquire so attribution CTAs exist.
- Supabase upsert before Make (store-first; INSERT-only idempotency).
- Instant email before nurture; booking API proof before automated suppression.
- Content originality runs through Phases 2–3 but freezes before launch marketing.
- Grouping follows architecture boundaries: presentation → store → orchestration → booking adapter.

### Research Flags

Phases likely needing deeper research during planning:
- **Phase 1 (DNS/ZeptoMail):** Confirm live ZeptoMail console record set (SPF merge vs CNAME-only UI changes).
- **Phase 5 (Make AI + ZeptoMail):** Prompt library + ZeptoMail Mail Agent From identity; sample-output review.
- **Phase 6 (Booking + nurture):** Final vendor API parity if not Cal.com; Make plan Sleep/execution caps on Oduro org; PECR classification of nurture copy (err toward opt-out).

Phases with standard patterns (skip research-phase):
- **Phase 2:** Next.js App Router marketing shell + cookie consent — well documented.
- **Phase 3:** Static/MDX catalogue pages — content work, not novel architecture.
- **Phase 4:** Supabase upsert + Zod Server Actions — established patterns in ARCHITECTURE.md.
- **Phase 7:** PostHog `instrumentation-client` — official Next docs path.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | Locked vendors; npm versions + official Make/ZeptoMail/Cal.com/PostHog/Next docs verified; booking vendor MEDIUM until chosen |
| Features | HIGH | PROJECT.md scope locked; Agentryx IA verified; B2B table stakes corroborated by 2026 guides |
| Architecture | HIGH | Store-first / INSERT-webhook patterns match official Supabase + Make docs; build order explicit |
| Pitfalls | HIGH | Zoho policy, SPF/DKIM, ICO PECR from official sources; Make multi-day wait MEDIUM (plan-dependent) |

**Overall confidence:** HIGH

### Gaps to Address

- **Booking vendor finalization:** Prefer Cal.com; prove upcoming-by-email + cancel matrix before Phase 6 automation.
- **Make org plan limits:** Confirm execution-time / Sleep maxima and credit budget on Oduro Make org at Phase 5–6 planning.
- **Named From + calendar owner:** Still TBD in PROJECT.md — needed for ZeptoMail Agent and booking event ownership.
- **Homepage line wording / niche:** Intentionally open — do not block catalogue or loop.
- **Nurture PECR classification:** Treat as follow-up marketing with opt-out unless counsel says otherwise.
- **ZeptoMail DNS UI drift:** Use console values at implementation, not remembered record shapes.

## Sources

### Primary (HIGH confidence)
- Next.js Sept 2026 security / Active LTS 16.3.8 — https://nextjs.org/blog/september-2026-security-release
- Next.js App Router / forms — https://nextjs.org/docs/app/guides/forms
- Supabase Database Webhooks — https://supabase.com/docs/guides/database/webhooks
- Make Supabase / ZeptoMail / Cal.com apps — https://apps.make.com/supabase , https://apps.make.com/zoho-zeptomail , https://apps.make.com/cal-com
- Zoho Mail Usage Policy — https://www.zoho.com/mail/help/usage-policy.html
- ZeptoMail SPF/DKIM — https://www.zoho.com/cpaas/articles/authentication-domains.html
- ZeptoMail send API — https://www.zoho.com/zeptomail/help/api/email-sending.html
- Cal.com bookings API — https://cal.com/docs/api-reference/v2/bookings/get-all-bookings
- PostHog Next.js — https://posthog.com/docs/libraries/next-js
- ICO PECR electronic mail + cookies guidance
- Project locks — `.planning/PROJECT.md`, `oduro/STACK.md`, `oduro/BUSINESS.md`

### Secondary (MEDIUM confidence)
- Agentryx site map / solution IA (design reference only)
- B2B website strategy guides 2026 (nav jobs, services, proof)
- Make community / help on Sleep limits, incomplete executions, credit polling
- Calendly scheduled_events invitee_email filter (parity with Cal.com check)

### Tertiary (LOW confidence)
- Exact Make Free/Core execution caps on Oduro org — verify in-product
- Whether nurture copy is strictly “marketing” vs transactional under PECR for this wording — counsel/err toward opt-out

---
*Research completed: 2026-10-03*
*Ready for roadmap: yes*
