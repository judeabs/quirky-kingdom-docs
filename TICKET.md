# [Quirky Kingdom] Tech stack selection, architecture & technical documentation plan

**Issue:** GENERALPRO-1  
**Source:** https://plane.abstractech.ae/abstractech/browse/GENERALPRO-1

---

**Type:** Research / Planning spike · **Estimate:** 2–3 days · **Priority:** High

**Design reference:** The Quirky Kingdom — Website Prototype.pdf (attached). The design is NOT final — use it for scope, not pixels.

Design Prototype: https://drive.google.com/file/d/1NcGvwYFDc0L6z8fzkxoixAejFCfuG9fw/view?usp=drive_link

## Context

The Quirky Kingdom is a D2C e-commerce store for trending toys and STEM learning toys, positioned around **gifting** ("playful premium"). Launch market is **India (INR)**, with expansion to **UAE and other GCC countries** later (multi-currency, multi-region, possibly Arabic/RTL).

Hard constraints:

- **No Shopify** or other monthly-fee SaaS commerce platforms.
- **Open-source, widely adopted, best-practice** e-commerce tooling — scalable, flexible, easy to extend later.
- Headless / API-first preferred so the storefront and AI features can evolve independently.

## Features in the prototype (scope to plan for)

- **AI Toy Finder** — natural-language search ("return gifts for 20 kids under ₹300", "educational toys for a 5 year old"); extracts age / occasion / quantity / budget, returns ranked matches with reasons and refine chips.
- Catalogue browsing: shop by age, occasion, theme (Harry Potter, K-pop, Minecraft…), STEM skill, category, trending, "viral right now" (Instagram reels / video).
- Product page: variants (colour), personalisation (+₹ add name), "why kids love it", safety & materials, "complete the gift" cross-sells, delivery-date check by pincode.
- **Gift Studio**: wrap choice (Signature / Boys / Girls / Unisex / Eco kraft), handwritten note (200 chars), hide price on invoice, pick a delivery date, ship to recipient's address.
- Cart & checkout: OTP phone login (+91), guest-friendly, delivery slots (earliest / pick date / express same-day in select cities), prepaid discount, **UPI, cards, net banking, wallets, COD**.
- WhatsApp: order updates, opt-in marketing, "talk to a gift expert" chat; video-call booking.
- Bulk / return-gift orders, order tracking, newsletter, mobile-first responsive UI.

## Deliverables

1. **Platform evaluation** — compare at least **Medusa.js v2, Saleor, Vendure** (optionally WooCommerce / Bagisto as baselines). Criteria: licence, community & maturity, extensibility (custom gift options, personalisation, bundles), multi-region/currency, admin UX, performance, hosting cost, team skill fit (Node/TS). Give a clear recommendation with reasoning.
2. **Recommended full stack** — storefront (e.g. Next.js), commerce backend, database (PostgreSQL), cache/queue (Redis), search (Meilisearch / Typesense / OpenSearch), CMS for editorial & landing content (e.g. Payload / Strapi, or built-in), media/CDN, auth/OTP, email/SMS/WhatsApp providers, analytics, hosting (AWS vs alternatives) with estimated monthly cost at launch and at scale.
3. **Payments plan** — shortlist and compare gateways for India (e.g. Razorpay, Cashfree, PhonePe PG, PayU) covering UPI, cards, net banking, wallets, COD handling, refunds, fees, settlement, and plugin availability for the chosen platform. Add a GCC-later section (e.g. Stripe UAE, Checkout.com, Telr, Tabby/Tamara BNPL). Recommend one primary + one fallback.
4. **AI Toy Finder design** — approach (LLM intent extraction → structured filters + vector/semantic search, e.g. pgvector or search-engine hybrid), product data model needed (age range, occasion, skills, themes, battery-free, screen-free…), model/provider options, cost per query, latency, fallback when AI fails.
5. **Integrations map** — shipping & pincode serviceability (e.g. Shiprocket / Delhivery), same-day/express partners, WhatsApp Business API provider (Meta Cloud API vs Gupshup / Interakt / WATI), OTP provider (e.g. MSG91), GST invoicing, Instagram feed/reels.
6. **Architecture diagram** — system context + component diagram (storefront, backend, admin, search, AI service, workers, third parties), data flow for checkout and for AI search.
7. **Data model draft** — products/variants, gift options (wrap, note, hide price, recipient, delivery date), personalisation, collections (age/occasion/theme/skill), orders, customers.
8. **Non-functionals** — security (PCI scope, OWASP), performance targets (Core Web Vitals, mobile-first), SEO, scalability, backups, observability, CI/CD, environments (dev / staging / prod).
9. **Delivery plan** — phased roadmap (MVP for India launch → enhancements → GCC expansion), rough effort per module, risks & open questions for Karan.

## Output format

- One technical document (Google Doc / Notion / Markdown in repo — share link in this ticket) + architecture diagrams.
- 30-minute walkthrough with Karan at the end.

## Suggested breakdown

- **Day 1:** Study prototype, platform evaluation, quick hands-on spin-up of the top candidate.
- **Day 2:** Full stack, payments, integrations, AI Toy Finder approach, architecture diagrams.
- **Day 3:** Data model, NFRs, phased roadmap & estimates, open questions, review walkthrough.

## Acceptance criteria

- Clear platform + payment gateway recommendation with comparison tables and reasoning.
- Every feature in the prototype is mapped to a component or integration.
- Architecture supports India launch and future GCC multi-region without a re-platform.
- All recommended tools are open source or pay-as-you-go (no monthly commerce platform licence).
- Open questions and assumptions listed explicitly.
