# The Quirky Kingdom — User journeys

How shoppers use the store, written for **QA, product, and anyone testing the site**. Each section is one path from start to finish. Steps use the **labels you see on screen** (bold).

**Scope:** Target journeys from the ticket; launch phases remain proposals in QUIRKY_KINGDOM_TECHNICAL_DOCUMENT.md §5. Technical blocks describe the proposed implementation, not verified application behaviour.

**Related:** [`QUIRKY_KINGDOM_TECHNICAL_DOCUMENT.md`](./QUIRKY_KINGDOM_TECHNICAL_DOCUMENT.md)

---

## 1. Find a gift with AI Toy Finder

**Goal:** Describe what you need in your own words and get a short list of matching toys.

**Who:** A parent or relative who knows the occasion but not the exact toy.

1. From the **home page**, tap **AI Toy Finder**.

   ```text
   The Next.js storefront opens the finder interface. The shopper can use it without signing in. In the proposed architecture, product data is already searchable in Typesense: a background worker updates that index when the catalogue changes in Medusa.
   ```

2. Type what you are looking for (for example: “return gifts for 20 kids under ₹300 each” or “educational toy for a 5 year old who loves space”).

   ```text
   When the shopper submits the query, the storefront sends it to the Finder API. The service calls an LLM to turn the sentence into structured fields such as age, occasion, quantity, budget per item, total budget, and theme. For the first example, it should extract quantity 20 and a maximum price of ₹300 per item. The service validates the response against a defined schema before using it to construct search filters; the LLM does not query the database or set product prices.
   ```

3. You should see a list of products, a short note on **why each fits**, and **refine chips** you can tap (age, budget, theme, and similar).

   ```text
   The finder queries Typesense with the validated filters, applying age, price, and availability constraints and ranking matching products. The requested quantity must also be checked against available stock; simply finding an in-stock product is insufficient for 20 gifts. Reasons are grounded in catalogue attributes, and refine chips represent filters the shopper can change. Semantic reranking is a proposed later enhancement. If the LLM fails or times out, the service falls back to basic age, budget, and quantity extraction and ordinary filtered browsing.
   ```

4. Tap a chip or change your words, then check the updated list.

   ```text
   Selecting a predefined chip updates the structured filters and runs another search; it does not need another LLM call. Submitting revised free text sends the query through intent extraction again. The storefront preserves the current selections and displays the latest response so an older, slower request cannot overwrite newer results.
   ```

5. Tap a product to open its page and read price, options, and delivery estimate.

   ```text
   Next.js opens the product page and fetches current product and variant details from Medusa. The search index helps discovery, but commerce data remains authoritative for pricing and availability. Once the shopper provides a pincode, the shipping integration checks serviceability and supplies an estimated delivery time. A search result alone does not guarantee stock, reserve an item, or promise a delivery date.
   ```

**Technical summary**

1. LLM - interpret age, occasion, quantity and budget
2. Our code - validate the search contract and clarify missing details
3. Typesense - retrieve and rank matching products
4. Medusa - verify current prices and available stock

**Technical summary with Jev (proposal)**

1. Jev - interpret occasion, budget basis and other choices from supplied options
2. Our code - extract candidate values, validate and normalise the search contract
3. Typesense - retrieve and rank matching products
4. Medusa - verify current prices and available stock


---

## 2. Browse the catalogue

**Goal:** Explore toys by age, occasion, theme, or what is popular right now.

**Who:** Someone who prefers to look around rather than search.

1. From the **home page**, tap **shop by** (age, occasion, theme, STEM skill, or category), or tap **Trending** / **Viral right now**.

   ```text
   Next.js opens a collection page using the chosen catalogue taxonomy. Medusa supplies product groupings, while Payload supplies curated editorial content and authorised reels.
   ```

2. You should see a **product listing** with photos and prices.

   ```text
   The storefront retrieves matching products from Typesense and loads images through the media CDN. Prices reflect the selected region; commerce validates the final price and availability before purchase.
   ```

3. Narrow the list with **filters** (age, occasion, theme, skill, price) and sort if you want.

   ```text
   Selections become validated search parameters retained in the page URL. Typesense applies filters together and sorts the results; an empty result should offer ways to broaden the selection.
   ```

4. Tap a product to open its **product page**.

   ```text
   The product page loads current product and variant details from Medusa. Opening it does not reserve inventory.
   ```

**Technical summary**

1. Next.js - display collections, filters and product pages
2. Payload - manage landing content and curated reels
3. Typesense - retrieve, filter and sort products
4. Medusa - provide catalogue details, variants and current prices

---

## 3. Add a product to the cart

**Goal:** Choose options on a product page and add the item to your cart.

**Who:** Anyone ready to buy one or more items.

1. From the **home page**, open any product (via **shop by**, search, **AI Toy Finder**, or a collection on home).

   ```text
   Next.js resolves the product link and loads its variants, description and regional prices. Search, collections and the Finder lead to the same product record.
   ```

2. On the **product page**, choose options such as **colour**.

   ```text
   The selected option combination identifies a variant with its own price and inventory. That variant ID is used when adding the item to the cart.
   ```

3. If offered, add **personalisation** (for example a name); the price should update on screen.

   ```text
   Personalisation is a proposed enhancement. The server validates eligibility and text limits, calculates the surcharge and returns the updated total; browser-supplied prices are never trusted.
   ```

4. Scroll to **why kids love it** and **safety & materials** if you want more detail; add any **complete the gift** suggestions you like.

   ```text
   These sections display maintained catalogue facts and curated product relationships. Suggested add-ons are separate selections with their own price and stock checks; safety claims must come from verified product content.
   ```

5. Enter your **pincode**; you should see an **estimated delivery** date or message.

   ```text
   The server checks the pincode through the shipping integration using the pickup location and relevant product details. The response is an estimate; final delivery options depend on the address, cart and operational cutoffs.
   ```

6. Tap **Add to cart**, then open the **cart**.

   ```text
   The server creates or updates the cart, validates the variant and quantity, and calculates the price. Personalisation stays attached to the correct configured line; adding to the cart alone does not guarantee stock.
   ```

**Technical summary**

1. Next.js - display product options and cart
2. Our code - validate personalisation and calculate surcharges when enabled
3. Shiprocket - check pincode serviceability and delivery estimates
4. Medusa - validate variants, quantities and prices before updating the cart

---

## 4. Send a gift (Gift Studio and checkout)

**Goal:** Buy a gift with wrap and a note, sent to someone else’s address.

**Who:** Someone buying for a birthday, festival, or surprise delivery.

1. From the **home page**, find a toy, open it, choose options, and tap **Add to cart**.

   ```text
   Medusa records the selected variant and quantity in a cart. The shopper can build the cart before signing in, using a protected session or scoped cart credential.
   ```

2. Open the **cart** and go to **Gift Studio** (or gift options for that item).

   ```text
   Gift Studio loads the selected line and its eligible options. A stable configuration ID keeps different names or notes on distinct cart lines.
   ```

3. Choose a **wrap** (Signature, Boys, Girls, Unisex, or Eco kraft).

   ```text
   The server validates the wrap option and recalculates any applicable charge. Whether wrapping and its price apply per unit or per package must be agreed before implementation.
   ```

4. Type a **handwritten note** (up to 200 characters). Turn on **hide price on invoice** if you do not want the recipient to see the price on the slip.

   ```text
   The server validates the note length and stores the note and hide-price preference. Packing instructions preserve the text for handwriting; the proposed output is a price-free recipient slip, while the billing-invoice interpretation still needs approval.
   ```

5. Pick a **delivery date**, enter the **recipient’s address**, and choose a **delivery slot** (next available, a date you pick, or **express same-day** if your area supports it).

   ```text
   Chosen dates, slots and same-day delivery are proposed enhancements. The server checks pincode, handling time, carrier coverage, cutoffs and capacity, then stores the requested date separately from the confirmed delivery promise.
   ```

6. **Sign in:** enter your **+91 mobile number**, enter the **code from SMS**, and add your name and email the first time. Or continue as **guest** if the site allows it.

   ```text
   The server requests and verifies an OTP through MSG91 with expiry, resend limits and attempt throttling before linking the cart to an authenticated customer. Guest checkout remains available; entering a phone number alone cannot reveal previous orders.
   ```

7. Choose how to pay: **UPI**, **card**, **net banking**, **wallet**, or **cash on delivery (COD)** if your pincode qualifies. Use any **prepaid discount** shown before you pay.

   ```text
   The server rechecks totals, stock, delivery and payment eligibility, applying prepaid discounts only to qualifying methods. Prepaid methods use hosted gateway checkout; COD follows separate courier-collection rules and remains unpaid at placement.
   ```

8. Turn on **WhatsApp order updates** if you want messages about the order (and marketing only if you opt in).

   ```text
   The backend records messaging preferences and separate marketing consent. Workers later send eligible WhatsApp templates; purchasing or requesting order updates does not automatically grant marketing permission.
   ```

9. Place the order. You should see a **confirmation** on screen and get **WhatsApp** (and email) with order details.

   ```text
   The backend verifies prepaid payment state or COD eligibility and completes the order with immutable gift and price snapshots. Workers send eligible confirmations when consent and contact details allow; notification failure must not create a duplicate order or undo a valid purchase.
   ```

**Technical summary**

1. Medusa / our gift module - store wrap, note and recipient details and calculate gift charges
2. MSG91 - verify phone login with SMS OTP
3. Shiprocket / our delivery rules - check shipping, COD and supported delivery options
4. Razorpay / Cashfree - process prepaid payments and report payment status
5. Medusa / background workers - confirm orders, preserve gift instructions and send eligible WhatsApp and SES email updates

---

## 5. Buy for yourself (simple checkout)

**Goal:** Order toys delivered to your own address, without gift wrap.

**Who:** Someone shopping for their own household.

1. From the **home page**, open a product, choose options, and **Add to cart**.

   ```text
   The server validates the variant and quantity and adds a priced line to the cart. Cart access remains tied to the shopper's session before sign-in.
   ```

2. Open the **cart**. Skip **Gift Studio** or leave default settings.

   ```text
   The cart retains an explicit no-wrap selection unless the shopper chooses otherwise. Removing a previously selected gift option triggers server-side repricing so optional charges are removed.
   ```

3. Enter or confirm **your delivery address** and **pincode**. If you are signed in, you may pick a **saved address**.

   ```text
   The server validates the address and checks pincode serviceability and shipping charges. Saved addresses require authenticated ownership; delivery options are rechecked when the address or cart changes.
   ```

4. **Sign in** with **+91** and the **SMS code**, or check out as **guest**.

   ```text
   OTP verification establishes identity before account data is exposed or the guest cart is linked. Guest checkout uses scoped access and does not attach historical orders just because the phone number matches.
   ```

5. Pay by UPI, card, net banking, wallet, or COD, then confirm the order.

   ```text
   The server recalculates the complete total and validates stock and payment rules. It verifies gateway state for prepaid orders; COD creates an outstanding collection obligation.
   ```

6. You should see **confirmation**. Later, use **My orders** (after sign-in) or the link in WhatsApp or email to track the parcel.

   ```text
   The confirmation page reads the completed order and its purchased price snapshots. Tracking requires authenticated ownership or an expiring, narrowly scoped guest link; notifications are sent asynchronously.
   ```

**Technical summary**

1. Next.js - collect cart, address and checkout details
2. MSG91 / our code - verify OTP or maintain guest checkout access
3. Medusa / Shiprocket - validate stock, totals and delivery eligibility
4. Razorpay / Cashfree / Medusa COD flow - handle prepaid payment or record courier collection

---

## 6. Track an order

**Goal:** See where your order is and get updates.

**Who:** Someone who already placed an order.

**Before you start:** You need the same **mobile number** you used when ordering, or a **tracking link** from WhatsApp or email.

1. From the **home page**, tap **Login** or **Account**.

   ```text
   The storefront opens the account interface and checks the current session. Protected order information is withheld until authentication succeeds.
   ```

2. Enter **+91 mobile** and the **SMS code**, then open **My orders**.  
   **Or:** open the **tracking link** from your message (you may land straight on the order status).

   ```text
   OTP verification establishes identity, and the backend returns only authorised orders. A guest tracking link uses a scoped, expiring token; invalid or expired links require renewed verification.
   ```

3. You should see steps such as confirmed, shipped, out for delivery, and delivered.

   ```text
   The backend combines order and fulfillment records with courier tracking events. Authenticated carrier updates are processed idempotently, and delayed events must not incorrectly move an order backwards.
   ```

4. **WhatsApp** (and sometimes SMS or email) sends updates when the status changes; you do not need to stay on the website.

   ```text
   Workers send eligible notifications when meaningful status transitions occur. Retries and deduplication prevent repeated messages; preferences and channel availability determine which notifications can be sent.
   ```

5. If you chose **COD**, pay the delivery person when the parcel arrives.

   ```text
   The courier collects the amount recorded for the COD order. Delivery, cash collection and remittance to the merchant are separate states reconciled against courier records.
   ```

**Technical summary**

1. MSG91 / our code - verify order ownership or validate a signed guest link
2. Medusa - retrieve authorised orders and fulfillment status
3. Shiprocket - provide tracking events and COD collection records
4. Background workers / Meta WhatsApp Cloud API / SES - reconcile updates and send eligible notifications

---

## 7. Return gifts or a large (bulk) order

**Goal:** Buy many of the same or mixed toys for a party or event, or ask for a quote when the order is very large.

**Who:** A host planning return gifts or a school or event organiser.

1. From the **home page**, use **AI Toy Finder** or **shop by** to find suitable toys.

   ```text
   Search applies the requested age, occasion and budget filters. Return-gift requests must distinguish a per-item budget from a total budget and account for the required quantity.
   ```

2. Open each product, set **quantity**, and **Add to cart** for all items you need.

   ```text
   The server validates quantities and sellable stock for every variant, then recalculates the cart. Quantity discounts apply only when configured and approved.
   ```

3. Optional: open **Gift Studio** on each line for wrap and a note per child.

   ```text
   Different names or notes use distinct gift configurations, normally separate lines per child; identical configurations may share a quantity. Per-unit versus per-package wrapping must be defined and preserved in packing instructions.
   ```

4. **If checkout is available:** go to checkout, **sign in** or use **guest**, pay, and confirm.  
   **If the site asks for a quote:** submit **bulk / return-gift inquiry** on the site or via **WhatsApp**; when the team confirms price and stock, come back to the **home page**, use the link they send, **sign in**, and pay.

   ```text
   Standard orders use normal checkout. Quote inquiries create a staff-reviewed request; an approved quote becomes a controlled draft order or checkout link with confirmed prices, quantities and expiry, followed by stock and payment revalidation.
   ```

5. Follow the order in **My orders** or via **WhatsApp** updates.

   ```text
   After checkout completes, normal order and fulfillment records drive tracking. An inquiry or unpaid quote must not appear as a confirmed, paid purchase.
   ```

**Technical summary**

1. Typesense - find toys matching the occasion and budget
2. Medusa / our gift module - validate quantities, prices and separate gift configurations
3. Medusa Admin / our quote workflow - review inquiries and create approved checkout links
4. Razorpay / Cashfree / Medusa COD flow - complete eligible orders for normal fulfillment and tracking

**Technical summary with Jev (proposal)**

1. Jev - interpret the bulk search request when using AI Toy Finder
2. Our code / Typesense - validate quantity and budget, then retrieve matching products
3. Medusa / our gift module - validate quantities, prices and separate gift configurations
4. Medusa Admin / our quote workflow - review inquiries and create approved checkout links
5. Razorpay / Cashfree / Medusa COD flow - complete eligible orders for normal fulfillment and tracking


---

## 8. Talk to a gift expert

**Goal:** Get human help choosing gifts, then buy on the website.

**Who:** Someone unsure what to buy or planning a bigger gift list.

1. From the **home page** (or from a product page), tap **Talk to a gift expert**.

   ```text
   The storefront opens the business's WhatsApp contact entry, optionally including a product reference or suggested message. Starting a chat does not grant access to the shopper's account.
   ```

2. You should move to **WhatsApp**. Share the child’s **age**, **occasion**, **budget**, and **how many** you need.

   ```text
   The conversation goes to staffed business support. Shared preferences guide human recommendations; they do not automatically update the cart, reserve stock or grant marketing consent.
   ```

3. The expert may send **product links**; open them to view items on the store.

   ```text
   Product links open normal storefront product pages backed by current commerce data. A recommendation in chat does not guarantee price or availability.
   ```

4. Add recommended items to your **cart**. You can **book a video call** if offered in the chat.

   ```text
   Recommended items use the normal cart validation flow. Video booking is a proposed enhancement: a booking module checks staff availability and records an appointment and meeting link separately from the purchase.
   ```

5. On the store, open the **cart**, **sign in** or continue as **guest**, add gift options if you want, and pay.

   ```text
   The shopper completes the same server-priced checkout used elsewhere. Staff-agreed discounts or quotes must exist as approved backend records, rather than being trusted from chat text.
   ```

**Technical summary**

1. Next.js / WhatsApp - connect the shopper with a gift expert
2. Medusa - load current products and validate recommended cart items
3. Our booking module (enhancement) - check staff availability and record video appointments
4. Medusa / payment adapters - apply approved quotes and complete checkout

---

## 9. Join the newsletter

**Goal:** Sign up for emails about new toys and offers without buying today.

**Who:** A visitor who wants updates later.

1. From the **home page**, scroll to the **footer** or use the **newsletter** popup on mobile.

   ```text
   The storefront displays the newsletter form independently of account registration. A popup should remain accessible and avoid blocking browsing or checkout.
   ```

2. Enter your **email** and tap subscribe.

   ```text
   The server validates the email, applies abuse limits and records consent and the subscription request. Repeated submissions must not create duplicate subscribers or commerce accounts.
   ```

3. You should see a short **thank you** or confirmation message.

   ```text
   The interface acknowledges a successfully stored request. If double opt-in is adopted, a worker sends a verification link and the subscription remains pending until confirmation; the message should explain that step.
   ```

4. When you receive a newsletter, use the links inside to return to the **home page**, **AI Toy Finder**, or a themed collection.

   ```text
   Newsletter links open the relevant storefront pages and may include campaign attribution subject to analytics consent. Each newsletter includes an unsubscribe link, and suppression is checked before future sends.
   ```

**Technical summary**

1. Next.js - display the newsletter form and confirmation
2. Our code / PostgreSQL - validate email and store consent and subscription status
3. Background workers / Amazon SES - send confirmation emails and newsletters
4. Our code - process unsubscribe links and suppress future sends

---

## Quick reference

| # | Journey | What this shows |
|---|---------|-----------------|
| 1 | AI Toy Finder | Plain-language search, refine chips, why each product fits |
| 2 | Browse | Shop by, trending / viral, filters |
| 3 | Add to cart | Options, personalisation, pincode, suggested add-ons |
| 4 | Gift checkout | Gift Studio, delivery to recipient, sign-in, UPI / COD, WhatsApp |
| 5 | Simple checkout | Own address, sign-in or guest, payment |
| 6 | Track order | Login, My orders, messages, COD on delivery |
| 7 | Bulk / return gifts | Quantities, quote when needed, checkout |
| 8 | Gift expert | WhatsApp help, then purchase on site |
| 9 | Newsletter | Email signup |
