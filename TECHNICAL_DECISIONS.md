# Technical decisions

## Medusa v2 — Commerce backend

**Recommendation:** Use self-hosted Medusa v2, pending a small checkout proof of concept.

### Pros

- **Open source:** MIT-licensed core; no monthly commerce platform fee.
- **TypeScript:** Fits the proposed Next.js stack and Node/TS team.
- **Extensible:** Custom modules and workflows suit gifting features.
- **API-first:** Storefront and AI Finder can evolve independently.
- **Regional support:** Foundation for INR at launch and GCC currencies later.

### Cons

- Gift Studio, AI Finder, OTP, and shipping need custom work.
- India payment integrations must be checked for v2 compatibility.
- We manage hosting, updates, backups, and monitoring.
- GCC localisation, taxes, and fulfilment still require implementation.

### Advantages over alternatives

Medusa is recommended for this project because it fits our expected team skills and custom gifting needs.

| Alternative | Why choose Medusa here? |
|---|---|
| **Saleor** | Both support TypeScript extensions. Medusa also has a TypeScript core; Saleor's server uses Python. Our preference depends on which stack the team can operate. |
| **Vendure** | Both use TypeScript and support custom features. Medusa's REST APIs may be more familiar if the team has not used GraphQL, which Vendure uses. This is a team preference, not a clear capability advantage. |
| **Building from scratch** | Medusa supplies the shopping basics, so we can focus on Gift Studio and AI Finder. |

Comparison sources: [Saleor](https://github.com/saleor/saleor), [Vendure](https://docs.vendure.io/current/framework/developer-guide/overview).

### Fit with the ticket

Meets the open-source and headless requirements. Provides core catalogue, cart, inventory, and order capabilities. Custom gifting and India/GCC integrations are feasible directions to validate, rather than ready-made features.

**Before confirming:** Test paid gift options, India payments/refunds, COD, and INR/AED configuration.

Details and sources: [Platform evaluation](./PLATFORM_EVALUATION.md) · [Medusa docs](https://docs.medusajs.com/learn) · [Licence](https://github.com/medusajs/medusa/blob/develop/LICENSE)

---

## Typesense — Product search

**Recommendation:** Use self-hosted **Typesense** for the launch catalogue. It is the proposed best fit for this store, pending a small search test.

### Pros

- **Useful shopping filters:** Narrow toys by age, price, occasion, theme and STEM skill.
- **Handles spelling mistakes:** Helps shoppers find products even when they mistype.
- **AI expansion path:** Supports keyword and semantic search together, so AI Finder can reuse the same search engine.
- **Independent of checkout:** Search helps discovery; Medusa remains responsible for current prices, stock and orders.

### Cons

- Adds a service to host, secure and keep updated with catalogue changes.
- Search indexes need RAM; embeddings and local AI models increase resource requirements.
- Good recommendations still depend on accurate product attributes and ranking tests.

### Advantages over alternatives

| Engine | Strength | Fit for Quirky Kingdom |
|---|---|---|
| **Typesense** | Shopping filters, typo tolerance and hybrid search | Recommended starting point for the proposed catalogue and Finder. |
| **Meilisearch** | Also offers typo tolerance, filters and hybrid search | Strong alternative; Typesense is not proven faster or more accurate. Compare real toy queries if results disappoint or the team prefers Meilisearch. |
| **OpenSearch** | Flexible queries, aggregations and hybrid search pipelines | Consider when complex search requirements or existing team expertise justify more configuration and operational work. No automatic migration is needed as the store grows. |

**Fit with the ticket:** Covers catalogue filters and provides a path to semantic AI discovery. Start with keyword search and filters; add semantic matching when evaluation shows a benefit.

**Before confirming:** Test misspelled names, age/price filters, regional prices, sufficient stock for bulk gifts, relevance and update delays. Measure RAM and latency on representative catalogue data.

Sources checked with Context7: [Typesense search](https://typesense.org/docs/30.2/api/search.html), [Typesense resource requirements](https://typesense.org/docs/guide/system-requirements.html), [Meilisearch documentation](https://github.com/meilisearch/documentation), [OpenSearch hybrid search](https://github.com/opensearch-project/documentation-website/blob/main/_vector-search/ai-search/hybrid-search/aggregations.md).
