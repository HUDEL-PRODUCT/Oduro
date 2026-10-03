# Roadmap: Oduro Agency Site

## Overview

Ship oduro.co.uk as a connected-business agency site that dogfoods enquire → tailored instant email → book → one nurture if needed. Work runs from Oduro Enterprises accounts and deliverability, through Stitch design and Next.js IA (shell, legal, homepage/About, catalogue, guides), into a store-first Enquire path, then Make/ZeptoMail orchestration and booking-aware nurture, finishing with consent-gated analytics and launch verification.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Account Bootstrap & Deliverability** - Oduro Enterprises vendors live; oduro.co.uk Mail + ZeptoMail DNS verified
- [ ] **Phase 2: Stitch Design System & Key Screens** - Google Stitch design system and key screens as visual source of truth
- [ ] **Phase 3: Site Shell, Nav & Brand Chrome** - Next.js App Router shell with primary nav and Agentryx-inspired colour system
- [ ] **Phase 4: Legal Pages & Cookie Consent** - Privacy, terms, cookie consent, and persistent legal links
- [ ] **Phase 5: Homepage, Approach & About** - Outcome-led home, Our Approach, About, and testimonials carousel
- [ ] **Phase 6: Catalogue & Proof Embeds** - Pillars overview, 11 solution pages with proof, Internal tools demo
- [ ] **Phase 7: Guides Library** - Guides index, 10 pillar-tagged guides, filter/visibility by pillar
- [ ] **Phase 8: Enquire Store & Form** - One Enquire form, sticky attribution, Supabase upsert, confirmation
- [ ] **Phase 9: Instant Email Orchestration** - Make INSERT path → AI draft → ZeptoMail + shared booking link
- [ ] **Phase 10: Booking Gate & Nurture Loop** - Due-at nurture, upcoming-booking check, opt-out
- [ ] **Phase 11: Analytics & Launch Verification** - Consent-gated PostHog and go-live checklist

## Phase Details

### Phase 1: Account Bootstrap & Deliverability
**Goal**: Oduro Enterprises owns every vendor project for the site, and oduro.co.uk can authenticate both human Mail and ZeptoMail transactional sends
**Depends on**: Nothing (first phase)
**Requirements**: (foundation — no v1 REQ IDs; unblocks LOOP email path)
**Success Criteria** (what must be TRUE):
  1. Operator can open Vercel, Supabase, Make, Zoho Mail, and ZeptoMail projects under Oduro Enterprises (not Scourge / personal)
  2. Operator can send a test from Zoho Mail and a ZeptoMail transactional test with passing SPF/DKIM (merged SPF; both DKIMs present)
  3. ZeptoMail domain shows Verified in console before any live enquire traffic
  4. Booking provider account scaffold exists (vendor may still be TBD; calendar owner decision recorded when chosen)
**Plans**: TBD

### Phase 2: Stitch Design System & Key Screens
**Goal**: UI screens and a design system exist in Google Stitch as the visual source of truth before (and for) Next.js implementation
**Depends on**: Phase 1
**Requirements**: SITE-06
**Success Criteria** (what must be TRUE):
  1. Operator can open a Stitch project with a documented design system (tokens, type, components) for Oduro
  2. Operator can view Stitch screens covering at least home, pillars/solutions chrome, enquire, and a content page pattern
  3. Design direction uses Agentryx only as colour/depth reference — no Agentryx copy or layout clone in Stitch frames
**Plans**: TBD
**UI hint**: yes

### Phase 3: Site Shell, Nav & Brand Chrome
**Goal**: Visitors land on a Next.js site shell on Vercel with working primary navigation and the brand colour system applied
**Depends on**: Phase 2
**Requirements**: SITE-03, SITE-04
**Success Criteria** (what must be TRUE):
  1. Visitor can use primary nav links: Solutions, Our Approach, Guides, About, Enquire
  2. Visitor sees an Agentryx-inspired colour system on live routes without Agentryx copy or layout cloning
  3. Visitor can load stub or real routes for home, approach, pillars, solutions, guides, about, enquire, privacy, and terms under shared chrome
**Plans**: TBD
**UI hint**: yes

### Phase 4: Legal Pages & Cookie Consent
**Goal**: Visitors can read privacy and terms and control cookie consent so non-essential tracking stays gated
**Depends on**: Phase 3
**Requirements**: SITE-05, LEG-01, LEG-02, LEG-03
**Success Criteria** (what must be TRUE):
  1. Visitor can open a privacy policy page that describes enquire storage, Make, ZeptoMail, and booking processing
  2. Visitor can open a terms page
  3. Visitor can manage cookie consent; non-essential scripts do not load before consent
  4. Visitor can reach Privacy, Terms, and Cookies from the footer (or equivalent persistent links)
**Plans**: TBD
**UI hint**: yes

### Phase 5: Homepage, Approach & About
**Goal**: Visitors understand the connected-business positioning, how Oduro works, who Oduro is, and see real social proof
**Depends on**: Phase 3
**Requirements**: SITE-01, SITE-02, SITE-07, SITE-08
**Success Criteria** (what must be TRUE):
  1. Visitor sees an outcome-led homepage for a broad buyer with connected-business positioning and a clear Enquire CTA
  2. Visitor can read Our Approach as Audit → Design → Build → Test → Launch → Improve
  3. Visitor can open About covering offerings overview, what Oduro builds, how we work, and standards
  4. Visitor sees a homepage testimonials carousel using real quotes at the bottom of the page
**Plans**: TBD
**UI hint**: yes

### Phase 6: Catalogue & Proof Embeds
**Goal**: Visitors can browse the full catalogue (pillars → solutions) with proof and a working Internal tools demo embed
**Depends on**: Phase 3, Phase 5 (homepage CTAs into catalogue)
**Requirements**: CAT-01, CAT-02, CAT-03, CAT-04, CAT-05, CAT-06, CAT-07
**Success Criteria** (what must be TRUE):
  1. Visitor can open a pillars overview with four pillars and interactive solution cards
  2. Visitor can open all Growth, Automation, Custom software, and AI solution pages (11 total)
  3. Each solution page presents problem, includes, works well with, outcomes, relevant proof, and a long Enquire CTA
  4. Visitor can interact with an embedded app demo on the Internal tools solution page
  5. Enquire CTAs from catalogue pages carry pillar/solution context into `/enquire`
**Plans**: TBD
**UI hint**: yes

### Phase 7: Guides Library
**Goal**: Visitors can discover and read 10 original, pillar-tagged guides from a filterable index
**Depends on**: Phase 3
**Requirements**: GUIDE-01, GUIDE-02, GUIDE-03, GUIDE-04, GUIDE-05
**Success Criteria** (what must be TRUE):
  1. Visitor can open a Guides index showing guide cards
  2. Visitor can click a card to open the full guide page
  3. Site ships with 10 published guides of original Oduro content
  4. Each guide is tagged to exactly one pillar (Growth, Automation, AI, or Software)
  5. Visitor can filter or clearly see guides by pillar tag on the Guides index
**Plans**: TBD
**UI hint**: yes

### Phase 8: Enquire Store & Form
**Goal**: A stranger can submit one Enquire form; answers land in Supabase first with sticky attribution and clear next steps
**Depends on**: Phase 1 (Supabase project), Phase 4 (legal/consent before public form), Phase 6 (attribution CTAs)
**Requirements**: ENQ-01, ENQ-02, ENQ-03, ENQ-04, ENQ-05, ENQ-06, ENQ-07
**Success Criteria** (what must be TRUE):
  1. Visitor can submit one shared Enquire form used across the site
  2. Primary route (pillar) is pre-selected when arriving from a solution or pillar page; visitor can still change it
  3. System stores source pillar and source solution even if the visitor changes primary route
  4. Form collects name, work email, company, primary route, what should improve, and weekly enquiry volume
  5. Enquiry is stored in Supabase before any email; same-email resubmit updates the record and does not create a second “new” send path; visitor sees a clear confirmation / next-step state
**Plans**: TBD
**UI hint**: yes

### Phase 9: Instant Email Orchestration
**Goal**: A new enquiry triggers Make to send one AI-tailored ZeptoMail instant email with the shared booking link
**Depends on**: Phase 1 (ZeptoMail verified), Phase 8
**Requirements**: LOOP-01, LOOP-02, LOOP-03, LOOP-04
**Success Criteria** (what must be TRUE):
  1. New enquiry INSERT triggers a Make scenario (site does not send mail directly)
  2. Instant email is drafted from form answers, route, and attribution only (no invented results, client names, or prices)
  3. Instant email sends via Zoho ZeptoMail from oduro.co.uk with Reply-To to the Zoho Mail inbox
  4. Every instant email includes the same shared booking link (one event type for all routes)
  5. Same-email resubmit does not send a second instant email (INSERT-only / status gate)
**Plans**: TBD

### Phase 10: Booking Gate & Nurture Loop
**Goal**: Unbooked enquirers get exactly one fixed nurture after two days, with live booking suppression and opt-out
**Depends on**: Phase 9
**Requirements**: LOOP-05, LOOP-06, LOOP-07
**Success Criteria** (what must be TRUE):
  1. If no upcoming booking exists after two days, system sends one fixed nurture email (scheduled due-at path — not multi-day Make Sleep)
  2. If an upcoming booking exists for that email, nurture is suppressed (cancelled booking counts as no booking)
  3. Nurture email includes a working way to opt out that is honoured for further marketing sends
  4. Operator has a written API proof that the chosen booking provider can answer “upcoming booking for this email?”
**Plans**: TBD

### Phase 11: Analytics & Launch Verification
**Goal**: Consent-gated PostHog runs on oduro.co.uk and the dogfood loop passes a go-live checklist
**Depends on**: Phase 4, Phase 8, Phase 9, Phase 10
**Requirements**: AN-01
**Success Criteria** (what must be TRUE):
  1. PostHog analytics runs on oduro.co.uk only after cookie consent for non-essential tracking
  2. Operator can verify enquire funnel events (or documented equivalent) without pre-consent capture
  3. Go-live checklist passes: ZeptoMail-only automation, merged DNS, idempotent enquire, book/cancel/nurture matrix, opt-out, original copy freeze
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10 → 11

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Account Bootstrap & Deliverability | 0/TBD | Not started | - |
| 2. Stitch Design System & Key Screens | 0/TBD | Not started | - |
| 3. Site Shell, Nav & Brand Chrome | 0/TBD | Not started | - |
| 4. Legal Pages & Cookie Consent | 0/TBD | Not started | - |
| 5. Homepage, Approach & About | 0/TBD | Not started | - |
| 6. Catalogue & Proof Embeds | 0/TBD | Not started | - |
| 7. Guides Library | 0/TBD | Not started | - |
| 8. Enquire Store & Form | 0/TBD | Not started | - |
| 9. Instant Email Orchestration | 0/TBD | Not started | - |
| 10. Booking Gate & Nurture Loop | 0/TBD | Not started | - |
| 11. Analytics & Launch Verification | 0/TBD | Not started | - |

## Coverage Summary

| Phase | Requirement IDs | Count |
|-------|-----------------|-------|
| 1 | (foundation) | 0 |
| 2 | SITE-06 | 1 |
| 3 | SITE-03, SITE-04 | 2 |
| 4 | SITE-05, LEG-01, LEG-02, LEG-03 | 4 |
| 5 | SITE-01, SITE-02, SITE-07, SITE-08 | 4 |
| 6 | CAT-01 … CAT-07 | 7 |
| 7 | GUIDE-01 … GUIDE-05 | 5 |
| 8 | ENQ-01 … ENQ-07 | 7 |
| 9 | LOOP-01 … LOOP-04 | 4 |
| 10 | LOOP-05 … LOOP-07 | 3 |
| 11 | AN-01 | 1 |
| **Total** | | **38/38** |

---
*Roadmap created: 2026-10-03*
*Granularity: fine (11 phases)*
