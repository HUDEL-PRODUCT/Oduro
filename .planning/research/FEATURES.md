# Feature Research

**Domain:** B2B agency marketing website — connected-business systems (growth / automation / software / AI) with deep service catalogue and enquire→book→nurture dogfood
**Researched:** 2026-10-03
**Confidence:** HIGH (project scope locked in PROJECT.md; competitor IA verified on Agentryx; industry table-stakes corroborated by 2026 B2B site guides)

## Feature Landscape

### Table Stakes (Users Expect These)

Features visitors assume exist on a serious B2B agency site. Missing these = bounce or distrust — not differentiation.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Outcome-led homepage | Buyer needs “what do you do / for whom?” in one screen | MEDIUM | Connected-business positioning; original copy; Agentryx colour feel only. Approach section: Audit → Design → Build → Test → Launch → Improve. |
| Primary nav: Solutions / Approach / Proof / Enquire | Three jobs: what you offer, can you prove it, how do I talk | LOW | Keep nav shallow. No industries/blog mega-menus in v1. |
| Pillars overview page | Catalogue agencies always expose “what we sell” | MEDIUM | Four pillars with interactive solution cards linking to detail pages. |
| Solution detail pages (full catalogue) | Buyers evaluate specific offerings before enquiring | HIGH | Per solution: problem → includes → works well with → outcomes → long Enquire CTA. 11 solutions across Growth / Automation / Custom software / AI. |
| Persistent Enquire CTA | Contact always one click away | LOW | Header + end-of-page bands. One shared Enquire experience (not per-service forms). |
| Qualify-enough Enquire form | Serious B2B enquiries need route + context | MEDIUM | Fields: name, work email, company, primary route (pillar), attribution (source pillar/solution), what should improve, weekly enquiry volume. Pre-select pillar from solution page. |
| Light social proof | Buyers forward proof to colleagues | LOW | Real testimonials ready to use. Logos optional if rights exist; do not invent case studies. |
| Legal: privacy, terms, cookie consent | Required for form + email under UK/GDPR norms | MEDIUM | Cookie banner + policy pages before any analytics/email. |
| Mobile-responsive, fast site | Table stakes for any 2026 marketing site | LOW–MEDIUM | Next.js / Vercel baseline; watch Core Web Vitals on catalogue pages. |
| Clear post-submit confirmation | “Did it work?” after form submit | LOW | Thank-you state with next step (check email / book). Avoid silent refresh. |
| Analytics on own site | Operator needs funnel visibility | LOW | PostHog for Oduro’s own analytics (not client dashboards). |

### Differentiators (Competitive Advantage)

Features that set this site apart from brochure agencies — aligned to Core Value: enquire → tailored path → booked conversation, while selling interconnection.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| Deep catalogue IA (pillars → cards → solution pages) with cross-links | Visitor sees full range and how pieces connect before enquire | HIGH | “Works well with” is the interconnection signal. Agentryx-depth without industries hub. |
| Source-aware Enquire (pillar pre-select + stored attribution) | Follow-up speaks to the page they came from without fragmenting CTAs | MEDIUM | Primary route editable; source pillar/solution always retained for Make/AI email. |
| Instant AI-tailored email (route + form answers only) | Feels like a human reply, not a generic autoresponder | HIGH | Make + model; no invented results/prices/names. Booking link in every send. ZeptoMail only. |
| Shared booking link in email (behaviour-locked) | High-intent next step without forcing calendar on-site | MEDIUM | Provider open (Cal.com candidate). Same event type for all routes. |
| Booking-aware single nurture (2-day wait + opt-out) | Reminds without nagging; stops if booked | HIGH | Check “upcoming booking for this email?” before send. Fixed copy (not second AI draft). One nurture max. |
| Same-email idempotent submit | No double instant email on resubmit | MEDIUM | Supabase: upsert by email; update record; suppress second instant send. |
| Dogfood as proof: site runs the sold system | Credibility for “connected business” — meta-proof beats brochure claims | HIGH | Enquire→email→book→nurture is the first installed system. |
| Internal tools page embeds live app demo | Tangible product proof without full case-study CMS | MEDIUM | One embed on Internal tools only; keep light elsewhere. |
| Client-owned stack story (implicit in copy/process) | Differentiates from agencies that trap work in agency accounts | LOW | Approach + solution copy; not a feature page. |

### Anti-Features (Commonly Requested, Often Problematic)

Explicitly do **not** build in v1 — even though peers (e.g. Agentryx) ship them.

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| Site chat / Notion-grounded knowledge assistant | “AI agency should have chat” | Scope, grounding, hallucination risk; later dogfood | Enquire + tailored email; defer chat to post–enquiry-loop |
| Voice (Vapi) dogfood on marketing site | Showcase AI voice offering | Distracts from enquire loop; ops cost | Catalogue page for Voice agents only; dogfood after loop works |
| Stripe / payments on agency site | Monetize or demo SaaS | Wrong job for v1 acquisition site | Keep Stripe for client builds later |
| Full blog / content hub | SEO and thought leadership | Content ops; delays catalogue + loop | Defer; optional 0–1 posts only if needed for legal/launch |
| Industries hub (multi-vertical pages) | Niche SEO and persona fit | Niche intentionally unlocked; breadth dilutes | Broad homepage; niche later |
| Full case-study CMS at Agentryx breadth | Proof depth | No volume of own cases yet; build cost | Light testimonials + one demo embed |
| Multiple Enquire forms / per-solution forms | “Better qualification” | Fragments routing and Make scenarios | One form; attribution + primary route |
| Long multi-email nurture sequences (5–7 emails) | Marketing automation fashion | Policy risk, brand fatigue, booking-check complexity | One fixed nurture + opt-out |
| On-site calendar embed as only CTA | Removes friction | Skips qualification fields needed for tailored email | Book link inside instant email (primary); optional later embed |
| Live chat widget (Intercom/etc.) | Instant response expectation | Staffing; conflicts with automated email path | Instant email is the response |
| Pricing / package configurator | Transparency | Custom systems don’t package cleanly; wrong quotes | Scope on call after enquire |
| Partner / careers / AI scorecard lead magnets | Growth toys peers have | Not Core Value; content debt | Out of scope until enquiry loop validated |
| Resend or non-Zoho ESP | Familiar DX | Stack locked: Zoho Mail inbox + ZeptoMail transactional | ZeptoMail via Make only |
| Cloning Agentryx copy or “successor” narrative | Fast content | Brand/legal risk; parallel brand | Colour + IA depth inspiration only |

## Feature Dependencies

```
Legal (privacy / terms / cookies)
    └──requires──> Enquire form live
                       └──requires──> Supabase enquiry store (idempotent upsert)
                                          └──requires──> Make: AI instant email via ZeptoMail
                                                             └──requires──> Booking link (provider + event type)
                                                                                └──requires──> Booking-aware nurture (wait + check + opt-out)

Homepage outcome + Approach
    └──enhances──> Catalogue trust

Pillars overview
    └──requires──> Solution detail pages
                       └──enhances──> Source attribution on Enquire
                                          └──enhances──> Tailored instant email

Internal tools demo embed
    └──enhances──> Custom software / Internal tools solution page only

Testimonials
    └──enhances──> Homepage + selected solution pages

PostHog analytics
    └──enhances──> Funnel measurement (does not block enquire loop)

Site chat / Voice / Blog / Industries
    └──conflicts──> v1 focus (enquiry loop + catalogue)
```

### Dependency Notes

- **Enquire requires legal:** Form + automated email need privacy notice, terms acceptance path, and cookie consent before non-essential trackers.
- **Instant email requires Supabase-first write:** Persist before any send; same-email resubmit must not re-trigger instant email.
- **Nurture requires booking provider that can answer “has upcoming booking for this email?”:** If provider cannot, defer automated suppression until it can (behaviour locked in STACK.md).
- **Attribution enhances email quality:** Solution-page CTAs must pass source pillar/solution into the form; Make/AI use route + attribution + free text.
- **Catalogue does not require blog/industries:** Deep solution pages are independent of content hubs.
- **Demo embed is optional for homepage:** Only Internal tools page needs it for v1 proof budget.

## MVP Definition

### Launch With (v1)

Minimum to validate Core Value: stranger enquires → tailored path → booked conversation; site sells interconnection + full catalogue.

- [ ] Outcome-led homepage + Approach section — positioning and process clarity
- [ ] Pillars overview + all solution detail pages (11) with cross-links and Enquire CTAs — catalogue completeness
- [ ] One Enquire form with pillar pre-select + source attribution + required fields — qualification + routing
- [ ] Supabase persistence + same-email upsert / no second instant email — reliability
- [ ] Make orchestration: AI instant email (ZeptoMail) + shared booking link — Core Value path
- [ ] 2-day booking check + one fixed nurture + opt-out — closed loop without spam
- [ ] Light proof: testimonials + Internal tools demo embed — enough trust without case-study CMS
- [ ] Legal: privacy, terms, cookie consent — compliance for enquire + email
- [ ] Deploy on oduro.co.uk (Next.js / Vercel) + PostHog — live + measurable

### Add After Validation (v1.x)

- [ ] On-site booking embed (secondary to email link) — when provider locked and UX demands it
- [ ] Thin FAQ on homepage or Enquire — if sales calls repeat the same objections
- [ ] About / who we are short page — if trust gap appears without team faces
- [ ] Second demo or short case write-ups — when real delivery stories exist
- [ ] Niche-flavoured homepage variant — when first niche is chosen

### Future Consideration (v2+)

- [ ] Site chat grounded on Notion — after enquiry loop proven
- [ ] Voice agent dogfood — after chat/loop stable
- [ ] Blog / topic clusters for SEO — after catalogue + conversion stable
- [ ] Industries hub — after niche strategy locked
- [ ] Full case-study system — when proof inventory justifies CMS
- [ ] Partners / careers / lead magnets (scorecards) — growth experiments only

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| Homepage + Approach | HIGH | MEDIUM | P1 |
| Pillars + solution pages | HIGH | HIGH | P1 |
| Enquire form + attribution | HIGH | MEDIUM | P1 |
| Supabase store + idempotent submit | HIGH | MEDIUM | P1 |
| Instant AI email + ZeptoMail | HIGH | HIGH | P1 |
| Booking link + nurture suppression | HIGH | HIGH | P1 |
| Privacy / terms / cookies | HIGH | MEDIUM | P1 |
| Testimonials | MEDIUM | LOW | P1 |
| Internal tools demo embed | MEDIUM | MEDIUM | P1 |
| PostHog | MEDIUM | LOW | P1 |
| Thank-you / confirmation UX | MEDIUM | LOW | P1 |
| On-site booking embed | MEDIUM | LOW | P2 |
| About page | MEDIUM | LOW | P2 |
| FAQ | MEDIUM | LOW | P2 |
| Blog / industries / case CMS | MEDIUM | HIGH | P3 |
| Site chat / Voice dogfood | MEDIUM | HIGH | P3 |
| Pricing configurator | LOW | HIGH | P3 (avoid) |
| Live chat widget | LOW | MEDIUM | P3 (avoid) |

**Priority key:**
- P1: Must have for launch
- P2: Should have after enquire loop is live
- P3: Nice to have / future — or deliberate avoid

## Competitor Feature Analysis

| Feature | Agentryx (design ref) | Typical B2B agency site (2026 guides) | Oduro v1 approach |
|---------|----------------------|----------------------------------------|-------------------|
| Homepage outcome | Connected acquisition/ops positioning | Clear offer + CTA | Connected business; original copy |
| Deep solutions catalogue | Yes — many `/solutions/*` pages | Service pages expected | Yes — pillars → cards → solution pages |
| Industries hub | Yes — multi-vertical | Recommended for SEO | **Defer** — niche unlocked |
| Blog | Yes | Often listed as must-have | **Defer** — not Core Value |
| Case studies CMS | Yes | Strongly expected | **Light only** — testimonials + one demo |
| Approach / process | Yes | Process section common | Yes — six-step project flow |
| Contact / enquire | Form + email | Form or book-a-call | One Enquire + email booking path |
| Instant tailored email | Not public product claim | Generic autoresponder common | **Differentiator** — AI via Make |
| Booking-aware nurture | Not visible as dogfood | Long drips common | **Differentiator** — one nurture, stop if booked |
| Site chat | Not required for peers | Optional | **Out of scope v1** |
| Legal pages | Privacy + terms | Required | Privacy + terms + cookies |
| Demo embed | Product-ish proof elsewhere | Rare on agency sites | Internal tools page only |

## Sources

- Project scope: `/Users/joeloduro/ODUROX/.planning/PROJECT.md`, `oduro/BUSINESS.md`, `oduro/STACK.md` (2026-10-03)
- Competitor IA: [Agentryx](https://www.agentryx.io) site map (solutions, industries, blog, case studies, testimonials, approach, contact, privacy/terms); sample solution page [Internal Tools](https://www.agentryx.io/solutions/internal-tools); [Contact](https://www.agentryx.io/contact) — MEDIUM–HIGH confidence
- Industry table stakes: [B2B Web Design 2026](https://www.2m-webstudio.com/blog/b2b-web-design) (nav jobs: offer / proof / talk); [Creative agency website guide](https://foliopage.com/magazine/agency-website) (home/services/work/about/contact); [B2B Website Strategy 2026](https://www.apexure.com/blog/b2b-website-strategy/) — MEDIUM confidence
- Enquire→nurture patterns: booking-exit nurture best practice ([GHL nurture sequence guidance](https://hlgrowthpartner.com/post/gohighlevel-email-automation-lead-nurture-sequence)) — MEDIUM confidence; Oduro deliberately shorter (one nurture) than typical 4–7 email drips

---
*Feature research for: Oduro agency marketing site (enquire→book dogfood)*
*Researched: 2026-10-03*
