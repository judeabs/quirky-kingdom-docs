Technologies:
Typesense - search tool
Medusa - CMS package

Main features:
AI Toy Finder - AI-assisted
Storefront

D2C = direct to customer
Wants to sell trending, STEM-learning toys for gifting - playful premium.

Target markets:
India
Future expansion for UAE and other GCC countries (multi-currency, multi-region)

Must haves:
Widely-adopted open-source tools for production / enterprise grade e-commerce tooling
Scalable, flexible, easy to extend later
Headless / API-first is preferred for independent evolution between the storefront and the AI features

Out of scope:
No Shopify or other subscription-based SaaS commerce platforms


Questions.
Which AI provider should we use?
 - Either anthropic or openai

How should the users log in? Via social media or regular username and password?
 - 

Is there an admin role?
 - 

Jev for quicker searching rather than a general LLM?
 - Yes. Jev could convert a shopper's request into a predictable search contract faster.

 {
    occasion: "return_gift",
    quantity: 20,
    budget: {
        amount: 300,
        currency: "INR",
        basis: "per_item"
    }
 }

 1. Jev - interpret choices (occasion, budget, category, etc.)
 2. Our code - validate and normalize numbers
 3. TypeSense - retrieve matching products
 4. Medusa - Verify current prices and stock