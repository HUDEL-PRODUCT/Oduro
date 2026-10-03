# Requirements: Oduro Agency Site

**Defined:** 2026-10-03
**Core Value:** A stranger can enquire and receive a tailored path to a booked conversation (instant email + booking link + one nurture if needed), while the site sells interconnection and the full catalogue.

## v1 Requirements

### Site & Brand

- [ ] **SITE-01**: Visitor sees an outcome-led homepage for a broad buyer stating the connected-business positioning with a clear Enquire CTA
- [ ] **SITE-02**: Visitor can read Our Approach (Audit → Design → Build → Test → Launch → Improve)
- [ ] **SITE-03**: Visitor can navigate via primary nav: Solutions, Our Approach, Guides, About, Enquire
- [ ] **SITE-04**: Site uses an Agentryx-inspired colour system without cloning Agentryx copy or layout
- [ ] **SITE-05**: Visitor can reach Privacy, Terms, and Cookies from the footer (or equivalent persistent links)
- [ ] **SITE-06**: UI screens and design system are produced in Google Stitch (Stitch MCP) and used as the visual source of truth for implementation
- [ ] **SITE-07**: Homepage ends with a testimonials carousel using real quotes
- [ ] **SITE-08**: Visitor can open an About page covering offerings overview, what Oduro builds, how we work, and our standards

### Catalogue

- [ ] **CAT-01**: Visitor can open a pillars overview page showing four pillars with interactive solution cards
- [ ] **CAT-02**: Visitor can open Growth solution pages for Websites and Funnels & landing pages
- [ ] **CAT-03**: Visitor can open Automation solution pages for Workflow automation, Follow-up & nurture, and Dashboards & reporting
- [ ] **CAT-04**: Visitor can open Custom software solution pages for Internal tools, Web apps, and Mobile apps
- [ ] **CAT-05**: Visitor can open AI systems solution pages for Knowledge assistants, Conversational AI & chatbots, and Voice agents
- [ ] **CAT-06**: Each solution page presents problem, what it includes, what it works well with, outcomes, embedded proof relevant to that solution, and a long Enquire CTA
- [ ] **CAT-07**: Visitor can interact with an embedded app demo on the Internal tools solution page

### Guides

- [ ] **GUIDE-01**: Visitor can open a Guides index page showing guide cards
- [ ] **GUIDE-02**: Visitor can click a card to open the full guide page
- [ ] **GUIDE-03**: Site ships with 10 published guides (original Oduro content)
- [ ] **GUIDE-04**: Each guide is tagged to exactly one pillar: Growth, Automation, AI, or Software (internal tools / custom software)
- [ ] **GUIDE-05**: Visitor can filter or clearly see guides by pillar tag on the Guides index

### Enquire

- [ ] **ENQ-01**: Visitor can submit one shared Enquire form used across the site
- [ ] **ENQ-02**: Primary route (pillar) is pre-selected when the visitor arrives from a solution or pillar page
- [ ] **ENQ-03**: System stores source pillar and source solution attribution even if the visitor changes primary route
- [ ] **ENQ-04**: Form collects name, work email, company, primary route, what should improve, and weekly enquiry volume
- [ ] **ENQ-05**: System stores the enquiry in Supabase before any email is sent
- [ ] **ENQ-06**: A second submit from the same email updates the record and does not send a second instant email
- [ ] **ENQ-07**: After submit, visitor sees a clear confirmation / next-step state

### Dogfood Loop

- [ ] **LOOP-01**: New enquiry triggers a Make scenario (site does not send mail directly)
- [ ] **LOOP-02**: Make drafts an AI instant email from form answers, route, and attribution only (no invented results, client names, or prices)
- [ ] **LOOP-03**: Instant email sends via Zoho ZeptoMail from oduro.co.uk with Reply-To to the Zoho Mail inbox
- [ ] **LOOP-04**: Every instant email includes the same shared booking link (one event type for all routes)
- [ ] **LOOP-05**: If no upcoming booking exists after two days, system sends one fixed nurture email (scheduled wait, not Make Sleep)
- [ ] **LOOP-06**: If an upcoming booking exists for that email, nurture is suppressed
- [ ] **LOOP-07**: Nurture email includes a way to opt out

### Legal & Analytics

- [ ] **LEG-01**: Visitor can read a privacy policy page
- [ ] **LEG-02**: Visitor can read a terms page
- [ ] **LEG-03**: Visitor can manage cookie consent; non-essential tracking is gated
- [ ] **AN-01**: PostHog analytics runs on oduro.co.uk behind consent

## v2 Requirements

Deferred until the enquire→book loop and v1 blog are validated.

### Content & depth

- **CONT-02**: Industries hub
- **CONT-03**: Full case-study CMS beyond solution-page proof embeds
- **CONT-04**: Ongoing guides cadence beyond the initial 10 posts

### Later dogfood

- **CHAT-01**: Site chat / Notion-grounded knowledge assistant
- **VOICE-01**: Voice agent dogfood on the marketing site
- **PAY-01**: Stripe dogfood on the agency site

## Out of Scope

| Feature | Reason |
|---------|--------|
| Separate “Proof” nav item | Proof lives on solution pages; testimonials carousel on homepage |
| Resend or non-Zoho ESP | Zoho Mail inbox + ZeptoMail transactional locked |
| Zoho Mail mailbox/SMTP for automated sends | Against Zoho usage policy; risks blocking inbox |
| Multi-email nurture drips (3+) | One fixed nurture; avoid spam/brand fatigue |
| Multiple Enquire forms | One form + attribution |
| On-site calendar as only CTA | Booking link lives in instant email; qualify first |
| Pricing configurator | Custom systems; scope on call |
| Agentryx copy clone / successor narrative | Parallel brand; design reference only |
| Client project builds in this repo | Separate repos; client-owned accounts |
| Locking booking vendor before choice | Behaviour locked; Cal.com candidate only |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| SITE-01 | Phase 5 | Pending |
| SITE-02 | Phase 5 | Pending |
| SITE-03 | Phase 3 | Pending |
| SITE-04 | Phase 3 | Pending |
| SITE-05 | Phase 4 | Pending |
| SITE-06 | Phase 2 | Pending |
| SITE-07 | Phase 5 | Pending |
| SITE-08 | Phase 5 | Pending |
| CAT-01 | Phase 6 | Pending |
| CAT-02 | Phase 6 | Pending |
| CAT-03 | Phase 6 | Pending |
| CAT-04 | Phase 6 | Pending |
| CAT-05 | Phase 6 | Pending |
| CAT-06 | Phase 6 | Pending |
| CAT-07 | Phase 6 | Pending |
| GUIDE-01 | Phase 7 | Pending |
| GUIDE-02 | Phase 7 | Pending |
| GUIDE-03 | Phase 7 | Pending |
| GUIDE-04 | Phase 7 | Pending |
| GUIDE-05 | Phase 7 | Pending |
| ENQ-01 | Phase 8 | Pending |
| ENQ-02 | Phase 8 | Pending |
| ENQ-03 | Phase 8 | Pending |
| ENQ-04 | Phase 8 | Pending |
| ENQ-05 | Phase 8 | Pending |
| ENQ-06 | Phase 8 | Pending |
| ENQ-07 | Phase 8 | Pending |
| LOOP-01 | Phase 9 | Pending |
| LOOP-02 | Phase 9 | Pending |
| LOOP-03 | Phase 9 | Pending |
| LOOP-04 | Phase 9 | Pending |
| LOOP-05 | Phase 10 | Pending |
| LOOP-06 | Phase 10 | Pending |
| LOOP-07 | Phase 10 | Pending |
| LEG-01 | Phase 4 | Pending |
| LEG-02 | Phase 4 | Pending |
| LEG-03 | Phase 4 | Pending |
| AN-01 | Phase 11 | Pending |

**Coverage:**
- v1 requirements: 38 total
- Mapped to phases: 38
- Unmapped: 0
- Phase 1 (Account Bootstrap & Deliverability): foundation only — no REQ IDs

---
*Requirements defined: 2026-10-03*
*Last updated: 2026-10-03 — roadmap traceability mapped*
