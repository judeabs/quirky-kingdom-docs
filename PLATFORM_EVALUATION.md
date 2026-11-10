# Platform evaluation — Quirky Kingdom

**Recommendation:** Medusa v2 is the leading candidate, pending a checkout proof of concept. This is a project-fit recommendation, not a measured performance ranking.

Scope baseline: [TICKET.md](./TICKET.md). The UI prototype is optional context per the user.

## Compare the same requirements

| Criterion | Medusa v2 | Saleor | Vendure |
|---|---|---|---|
| Core and API | TypeScript, REST | Python core, GraphQL | TypeScript, GraphQL |
| Custom extensions | Modules and workflows | Technology-independent apps/webhooks; TypeScript SDK available | Plugins, custom fields and pricing strategies |
| Paid wrapping / engraving | Official personalised-product recipe calculates server-side unit price | Requires an app/checkout pricing design; not proven here | Official configurable-product example covers engraving and wrapping surcharges |
| Bulk quantities | Stock validation and commercial pricing rules need design | Same scenario must be validated | Quantity pricing can use custom pricing strategy; stock rules still need validation |
| India payments | Verify v2 provider or build adapter | Verify payment app or build adapter | Verify payment handler or build adapter |
| Regional pricing | Regions and price configuration | Channels and currency configuration | Channels and currency configuration |
| Licence | MIT core | BSD-3 core | GPLv3 core; consult current plugin exception and commercial terms |
| Admin usability | Gift instructions need validation | App/dashboard workflow needs validation | Custom-field/admin workflow needs validation |
| Hosting and performance | Not benchmarked | Not benchmarked | Not benchmarked |
| Maturity/adoption | Collect version-specific releases and production references | Same evidence required | Same evidence required |

All three require storefront development, hosting, India integrations and gifting rules. No claim of a ready-made Razorpay/Shiprocket integration is made here. Regional features do not automatically deliver country-specific tax, legal entities, Arabic content or RTL.

## Why Medusa leads

The expected team works in TypeScript, and Medusa's REST APIs and workflows fit the proposed integration approach. Its MIT core meets the no-commerce-subscription constraint. These are reasonable preferences, subject to the team's actual experience and a bounded checkout experiment.

**Vendure is a serious alternative:** it also uses TypeScript and documents exactly the gifting pricing scenario we need. GraphQL is a team preference, not a capability deficiency.

**Saleor remains viable:** TypeScript apps can implement integrations without modifying its Python core. Operating the Python server is a separate concern from the language used for extensions.

WooCommerce and Bagisto are optional baselines in the ticket; they are not scored here. The earlier numeric scores, idle-memory claim and B2B-based elimination logic were removed because no comparable experiment supported them.

## Proof of concept — not yet passed

Pin a release and compatible Node/Postgres versions. Complete a cart with two differently personalised copies of one variant, server-calculated charges, stock checks and order/admin persistence. Check price tampering and partial refund behaviour. Use a test provider and label it as such; gateway onboarding remains a separate dependency.

## Evidence

Checked 2026-11-05 using Context7 and primary documentation. Moving-branch examples must be checked against the selected release.

- [Medusa personalised products](https://github.com/medusajs/medusa/blob/develop/www/apps/resources/app/recipes/personalized-products/example/page.mdx): server-calculated custom price.
- [Saleor apps](https://docs.saleor.io/developer/extending/apps/overview): language-independent extensions and TypeScript app template.
- [Vendure configurable products](https://github.com/vendurehq/vendure/blob/master/docs/docs/guides/how-to/configurable-products/index.mdx): custom fields and engraving pricing.
- [Vendure licensing FAQ](https://github.com/vendurehq/vendure/blob/master/license/license-faq.md): assess current exceptions before deriving obligations.
- [Medusa licence](https://github.com/medusajs/medusa/blob/develop/LICENSE), [Saleor licence](https://github.com/saleor/saleor/blob/main/LICENSE).
