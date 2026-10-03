# Oduro Agency Site

## What This Is

The public website for **Oduro** (Oduro Enterprises) at **oduro.co.uk**. It sells a connected business — growth systems, automation, custom software, and AI designed to work as one — and runs the first system Oduro installs for itself: enquire → tailored instant email → book a conversation → one nurture if they have not booked.

Oduro designs, builds, and improves the infrastructure behind customer acquisition and delivery by connecting the tools, teams, and information a business depends on. This repo is the agency site only (dogfood), not Scourge and not client delivery repos.

## Core Value

A stranger can enquire from the site and receive a tailored path to a booked conversation (instant email + booking link + one nurture if needed), while the site clearly sells interconnection and the full catalogue.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Outcome-led homepage for a **broad** buyer (niche not locked): connected-business positioning; original copy (direction from business notes; do not paste Agentryx verbatim)
- [ ] Colour sensibility inspired by [Agentryx](https://www.agentryx.io) as a **design reference only** (parallel brand — not a successor story on the site)
- [ ] Primary nav: Solutions / Our Approach / Guides / About / Enquire
- [ ] About page: offerings overview, what we build, how we work, standards
- [ ] Our Approach: Audit → Design → Build → Test → Launch → Improve
- [ ] Homepage testimonials carousel (real quotes) at the bottom
- [ ] Solution pages embed proof relevant to that solution (no separate Proof nav)
- [ ] Guides index with cards + 10 published guides; each guide tagged to one pillar (Growth, Automation, AI, Software)
- [ ] Google Stitch for UI designs / design system before Next.js implementation
- [ ] Pillars overview page with interactive solution cards
- [ ] Solution detail pages for each offering: problem → includes → works well with → outcomes → proof → long Enquire CTA
- [ ] Catalogue pillars/solutions:
  - Growth systems — Websites; Funnels & landing pages
  - Automation — Workflow automation; Follow-up & nurture; Dashboards & reporting
  - Custom software — Internal tools; Web apps; Mobile apps
  - AI systems — Knowledge assistants; Conversational AI & chatbots; Voice agents
- [ ] Internal tools solution page embeds the existing app demo
- [ ] One Enquire experience: primary route = pillar (pre-selected from source page); store source pillar + source solution for tailored follow-up
- [ ] Form fields: name, work email, company, primary route, attribution, what should improve, weekly enquiry volume
- [ ] Submissions stored in Supabase before any email; same-email resubmit updates record and does not send a second instant email
- [ ] Make.com orchestration: AI instant email → shared booking link → 2-day wait → booking check → one fixed nurture + opt-out (or stop if booked)
- [ ] Email: **Zoho Mail** inbox; **Zoho ZeptoMail** for automated sends via Make (not Zoho Mail mailbox API)
- [ ] Legal v1: privacy policy, terms, cookie consent
- [ ] Site on Next.js / Vercel; enquiries in Supabase; domain oduro.co.uk

### Out of Scope

- Site chat / Notion-grounded knowledge assistant on the Oduro site — later dogfood, not this v1
- Voice (Vapi) and Stripe dogfood on the agency site — after enquiry loop works
- Industries hub / full case-study CMS — v1 has solution-page proof + homepage carousel + 10 pillar-tagged guides only
- Locking first niche, social handles, or final homepage line wording — open intentionally
- Locking booking provider (Cal.com candidate only) — behaviour locked, vendor open
- Building client projects inside this repo — separate repos; client-owned accounts
- Resend or other third-party ESP for v1 transactional mail — Zoho ZeptoMail locked
- Using Zoho Mail mailbox/SMTP for automated enquire/nurture — against Zoho usage policy; ZeptoMail only

## Context

Source notes live in `oduro/BUSINESS.md` and `oduro/STACK.md` (updated during questioning). Decisions captured 1–3 October 2026.

**Positioning evolution.** Earlier notes framed the sold outcome as “enquiries → booked conversations.” That is now the **first dogfood system**, not the brand promise. The brand outcome is **a connected business**.

**Agentryx.** Parallel brand. Use for colour feel and information-depth inspiration only. Do not clone copy or present Oduro as its public successor.

**Account ownership.** Every tool lives in accounts the client owns and pays for. For this site, Oduro Enterprises is the client. Cursor MCP for Scourge must not be used to create Oduro assets in the wrong org.

**Email research (Oct 2026).** Zoho Mail explicitly disallows automated and transactional sending; limits are reputation-based (roughly 50–500 external/hour). Keep Zoho Mail for human inbox; send system mail via ZeptoMail through Make.

**Git.** Repo init may be blocked on this machine until the Xcode license is accepted (`sudo xcodebuild -license`).

## Constraints

- **Domain**: oduro.co.uk — owned; site and public URLs use it
- **Stack**: Next.js on Vercel, Supabase, Make, Zoho Mail (inbox), Zoho ZeptoMail (transactional), PostHog for own analytics
- **Automation platform**: Make.com locked (client-operable; official MCP at `https://mcp.make.com`)
- **Booking**: provider TBD; behaviour locked (book link in email; “has upcoming booking for this email?” before nurture)
- **Sender / booker**: named From and calendar owner TBD
- **Buyer**: broad for v1; niche later
- **Separate from Scourge**: own repo and Oduro-owned vendor accounts
- **UI design**: Google Stitch (via Cursor Stitch MCP) for screen generation and design system before / alongside GSD UI phases — Agentryx colour feel as reference input to Stitch, not a Figma-only handoff

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| UI designs in Google Stitch | Visual exploration + design system via Stitch MCP before implementing Next.js UI | — Pending |
| Agency site only (dogfood) | Prove the system on Oduro before client breadth | — Pending |
| Outcome = connected business; enquiry loop = first system | Brand is interconnection; dogfood proves acquisition connectivity | — Pending |
| Broad buyer for v1 | Niche open; homepage leads with outcome | — Pending |
| Deep catalogue IA (pillars → cards → solution pages) | Visitor sees full range before enquire | — Pending |
| Enquire: pillar pre-select + source pillar/solution attribution | Tailor follow-up without fragmenting the form | — Pending |
| Make.com for orchestration | Easiest for clients to own; MCP available | — Pending |
| Zoho Mail inbox + ZeptoMail transactional | One vendor family; policy-safe automation; no Resend | — Pending |
| Agentryx = design reference only | Parallel brand; colour + depth inspiration | — Pending |
| Legal (privacy, terms, cookies) in v1 | Required for enquire + email | — Pending |
| Light proof + internal-tools demo embed | Real testimonials; demo on Internal tools page | — Pending |
| Booking provider open | Not sold on Cal.com; lock behaviour not vendor | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 3 October 2026 after initialization*
