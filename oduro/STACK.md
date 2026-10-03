# Oduro stack

The agency site is a separate project from Scourge. Build it in its own repo.

MCP is how Cursor builds and checks these accounts. The live site does not send mail or book meetings through MCP. It calls the same vendors' APIs from the app and from Make.

## Who owns the accounts

Yes. Every tool in this stack can live in the client's own account. Ownership comes from where the project is created, not from a handover export at the end.

The client opens the account, puts the billing card on it, and invites Oduro as a member. The domain, the phone number, the git repo, and the model API key are in their name too. When the retainer ends, they remove the invite and the system keeps running on their subscription.

The Cursor MCP connections already set up for Scourge point at Oduro's own Vercel and Supabase. Client work uses a connection to the client's account. Building through the Scourge connection would put their system in Oduro's account.

Oduro's own site is the dogfood case. Oduro Enterprises owns those accounts, because Oduro is the client.

| Tool | How the client keeps it |
| --- | --- |
| Next.js on Vercel | Repo in their GitHub organisation. Project on a Vercel team they own. They invite Oduro onto that team. |
| Supabase | Project inside an organisation they own. They invite Oduro. |
| Make | Their Make organisation. They invite Oduro. The model API key used in scenarios is theirs. |
| Zoho Mail | Their Zoho org. Inbox for human mail on their domain. They invite Oduro if needed. |
| Zoho ZeptoMail | Same Zoho org. Transactional sends (instant + nurture) via Make. Do not send automated mail through Zoho Mail — Zoho's usage policy forbids it. |
| Booking provider | Their account on the chosen booking tool, connected to their calendar. Provider not locked yet (Cal.com was a candidate). |
| Notion | Their workspace. The assistant only reads pages in that workspace. |
| Vapi | Their Vapi organisation. The phone number is bought in their name, or brought from a telephony account they already own. |
| Stripe | Their Stripe account. They invite Oduro with a role they can revoke. Client payments never run through an Oduro Stripe account. |
| HubSpot | Their portal. Oduro is a user they can remove. |

A workflow file, a copied database, or a shared login is not ownership. The running system has to be born in their account. If a build starts in an Oduro account by mistake, Vercel can transfer the project onto their team, and Supabase can move with an invite to their organisation. Start in their account so that transfer is unnecessary.

## Dogfood flow

The site runs the system Oduro sells.

1. Someone chooses Enquire and fills the funnel form.
2. The answers pick an instant-reply route.
3. An AI-written email goes out at once, in their words, with one link to book a meeting on the Oduro calendar.
4. Two days after the form, if that email address has no booking, one nurture email goes out.
5. A booking at any point before those two days ends the sequence. No second sales email.

### Form

Collect the answers that change the email. Ask for one primary route (pillar) so the first email has a single subject.

- Name
- Work email
- Company
- Primary route, one choice (pillar):
  - Growth systems (websites, funnels, landing pages)
  - Automation (workflows, dashboards, follow-up)
  - AI systems (chat, voice, knowledge assistants)
  - Custom software (internal tools, web apps, mobile apps)
  - Not sure
- Source attribution (set when they arrive from a solution page; keep even if they change primary route):
  - Source pillar
  - Source solution
- What should the work improve, in their words (short text)
- How many enquiries they handle in a typical week

When the Enquire CTA is hit from a solution page, pre-select primary route from that pillar and store source pillar + source solution for tailored follow-up.

Store every submission in Supabase before any email sends. A second submit from the same email updates the record and does not send a second instant email.

### Instant email

Make receives the new enquiry, reads the route (and source attribution), and asks a model to write the email from the form answers only. The model may not invent results, client names, or prices. Prefer the client's own model API key in Make, not Make AI credits alone.

Every route shares the same booking link: one event type on the chosen booking provider, connected to the calendar already in use. The event lands there when they book. The route changes the email, not the calendar.

Send through **Zoho ZeptoMail** (not Zoho Mail), from a named address at oduro.co.uk, with a **Reply-To** that lands in the Zoho Mail inbox a human reads. Named From address is still to be confirmed.

Zoho Mail stays the inbox only. Automated / transactional sends go through ZeptoMail so they stay inside Zoho's usage policy and do not risk blocking the human mailbox.

### Nurture

Make waits two days, then asks the booking provider whether that attendee email has an upcoming booking.

- Booking exists: stop.
- No booking: send one nurture email through ZeptoMail. This email is a fixed note, not a second AI draft, so it cannot drift. It repeats the same booking link and includes a way to opt out.

A cancelled booking counts as no booking, so the nurture still sends.

Booking provider is not locked. Behaviour is locked: book link in the email, and a reliable “has upcoming booking for this email?” check before nurture. If the provider cannot answer that yet, defer automated suppression until it can.

## Tools

One tool per job. Prefer vendors with an official MCP so Cursor can set up and inspect accounts.

| Job | Tool | Official MCP | Used for |
| --- | --- | --- | --- |
| Websites, funnels, landing pages, internal tools, web apps, chat UI | Next.js on Vercel | `https://mcp.vercel.com` (already connected) | The agency site and later client sites and apps |
| Enquiry records, dashboard data | Supabase | `https://mcp.supabase.com` (already connected) | Form submissions, route, attribution, email state, the numbers behind client dashboards |
| Workflow, wait, and routing | Make | `https://mcp.make.com` ([docs](https://developers.make.com/mcp-server)) | Instant-email scenario, two-day wait, booking check, nurture send |
| Human inbox | Zoho Mail | — | Receive replies; day-to-day mail for oduro.co.uk |
| Transactional email | Zoho ZeptoMail | — (Make has [ZeptoMail modules](https://apps.make.com/zoho-zeptomail)) | Instant email and nurture email via Make. Not Resend. |
| Booking | TBD | — | Meeting link + “has booking?” check for nurture. Cal.com remains a candidate, not a decision. |
| Knowledge the assistant may quote | Notion | `https://mcp.notion.com/mcp` | Approved pages for knowledge assistants and the site chat |
| Voice agents | Vapi | `https://mcp.vapi.ai/mcp` | Phone and voice agents |
| Payments on SaaS builds | Stripe | `https://mcp.stripe.com` | Checkout and subscriptions when a build needs them |
| A client's existing CRM | HubSpot | `https://mcp.hubspot.com` | Only when that client already runs HubSpot |

Funnels and landing pages are pages and the form in the Next.js site. They do not need a second form product.

Dashboards and reporting are pages in the same Next.js app, reading Supabase. PostHog stays available for Oduro's own site analytics. The PostHog MCP is already in Cursor and still needs a sign-in. It is not the client reporting tool.

Conversational AI and knowledge assistants are a chat on the site. Answers come from the Notion pages approved for that purpose. If the pages do not contain the answer, the assistant says so and offers the booking link. Site chat is not in agency-site v1.

The AI that personalises the first email runs inside the Make scenario. It does not need its own vendor MCP beyond the model provider.

## Offering map

| Offering | Where it lives |
| --- | --- |
| Websites | Next.js on Vercel |
| Funnels and landing pages | Next.js on Vercel, form stored in Supabase |
| Workflow automation | Make |
| Dashboards and reporting | Next.js reading Supabase |
| Follow-up and nurture | Make, ZeptoMail, booking provider |
| Conversational AI and chatbots | Next.js chat, grounded on Notion |
| Voice agents | Vapi |
| Knowledge assistants | Notion as the source, Next.js chat as the interface |
| Internal tools | Next.js and Supabase |
| Web apps | Next.js, Supabase, Stripe when needed |
| Mobile apps | Client-owned app stores / accounts; stack chosen per engagement |

## Accounts to open before build

- Vercel project for the Oduro site (Oduro Enterprises)
- Supabase project for enquiries
- Make organisation for Oduro, with MCP connected in Cursor
- Zoho Mail already on oduro.co.uk (inbox) — keep as human mail
- Zoho ZeptoMail enabled for oduro.co.uk (SPF/DKIM for ZeptoMail alongside Zoho Mail)
- Booking provider + event type (once chosen), connected to the calendar in use
- Model API key in the Make org for instant-email drafts
- Notion workspace only when site chat / knowledge assistants are dogfooded
- Vapi and Stripe when those offerings are actually dogfooded, not before the enquiry flow works
