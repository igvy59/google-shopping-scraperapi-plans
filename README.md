# Google Shopping Scraper API: What One Request Returns, How Many Requests 100,000 Credits Buys, and Which Plan Fits (Full Plan Comparison)

ScraperAPI runs Google Shopping as a structured data endpoint. You send a query and a country code, and the response is JSON in which every row carries `position`, `title`, `price`, `extracted_price`, `source` and `delivery_options`. A Google request costs **25 credits** instead of the standard 1, so the number that decides your bill is how many queries you plan to run. The free plan's 1,000 monthly credits cover 40 of them.

Below is the full plan table, the credit-to-request math for every tier, what the endpoint documents about its parameters and response fields, and the places where a managed Google Shopping scraper API stops being the right purchase.

> **Quick pick:** Hobby is the first tier most teams can actually work on, with 100,000 credits a month (4,000 Google Shopping requests at 25 credits each), 20 concurrent threads and US plus EU geotargeting. A one-off research job doesn't need a paid plan at all. The 7-day trial gives you 5,000 credits, roughly 200 requests, and asks for no card.
> 👉 [Start the free trial and see Hobby's 100,000-credit allowance](https://www.scraperapi.com/pricing?fp_ref=coupons)

## Every ScraperAPI plan side by side

The credits, thread counts and geotargeting bands below are what the pricing page currently states. Dollar amounts shift between monthly and annual billing, so the pricing page is the place to read the live figure for your cycle. What doesn't move is the ratio between credits and Google requests, and that ratio is what you should budget against.

| Plan | API credits / month | Concurrent threads | Geotargeting | Billing | Start |
| --- | --- | --- | --- | --- | --- |
| Free | 1,000 | 5 | — | Monthly | [ Open the free plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Hobby | 100,000 | 20 | US & EU | Monthly or annual | [ Get Hobby for 100,000 credits](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | US & EU | Monthly or annual | [ Compare Startup against Hobby](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | 3,000,000 | 100 | Country-level | Monthly or annual | [ See Business with country-level targeting](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | 5,000,000 | 200 | Country-level | Monthly or annual | [ Check the Scaling tier](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | 10,500,000 | 300 | Country-level | Monthly or annual | [ View Professional pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | Country-level | Monthly or annual | [ View the Advanced tier](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 500+ | Country-level | Custom | [ Request an Enterprise quote](https://www.scraperapi.com/pricing?fp_ref=coupons) |

Two differences in that table matter more than the headline credits. First, geotargeting is capped at US and EU on Hobby and Startup; country-level targeting begins at Business. If you sell into Japan, Brazil or the Gulf, the cheap tiers won't give you the right storefront. Second, analytics history is limited to the last 30 days on Hobby and Startup and unlimited from Business upward, which decides whether you can look back at a price trend you didn't plan for.

## One request, what comes back

ScraperAPI's Google Shopping endpoint sits under Structured Data Endpoints in the API Playground. You pick the endpoint, enter a query such as `running shoes`, set the country code, and the panel on the right shows the cost of the job before you run it. The generated code is a single request with your API key in it.

The documented parameters are narrower than a full search API: `QUERY` and `APIKEY` are required; `COUNTRY_CODE` takes a two-letter code; `TLD` picks the Google domain and defaults to `com`, with options including `co.uk`, `ca`, `de`, `fr`, `it`, `co.jp`, `in`, `com.au`, `com.br` and `com.mx`; `OUTPUT_FORMAT` returns `json` by default or `csv`; `UULE` encodes a city; `NUM` sets results per page; `HL` and `GL` handle host and guest language; `IE` and `OE` set query and result encoding; `START` sets the offset so you can walk a result list.

Each entry in `shopping_results` gives you the position on the page, the product title, the formatted price string, the numeric `extracted_price`, the retailer name in `source`, and `delivery_options`. Use `extracted_price` for comparisons and keep `price` for display; parsing the string yourself invites rounding errors on currencies with different decimal conventions.

For anything beyond a single query, switch the Playground to Async and paste structured endpoint URLs one per line. Each job returns a `statusUrl`; poll it until the status reads `finished`, and the data sits in `response.body`. The documentation's own example drops that batch into a crontab running daily at 6am, which is the simplest way to build a price series without writing scheduler code.

## The credit math nobody puts in the plan table

Google and Bing sit in the SERP band at **25 credits per request**, subdomains included. That single number rewrites how you read every plan.

| Plan | Requests at 25 credits | Requests at 35 credits |
| --- | --- | --- |
| Free (1,000) | 40 | 28 |
| Trial (5,000) | 200 | 142 |
| Hobby (100,000) | 4,000 | 2,857 |
| Startup (1,000,000) | 40,000 | 28,571 |
| Business (3,000,000) | 120,000 | 85,714 |
| Scaling (5,000,000) | 200,000 | 142,857 |
| Professional (10,500,000) | 420,000 | 300,000 |
| Advanced (21,500,000) | 860,000 | 614,285 |

The 35-credit column is your realistic ceiling. JS rendering costs 10 credits, premium proxies cost 10, and the pricing page lists premium proxies combined with JS rendering at 25. Add either to a Google request and you're above the base rate. Plan for 35 and the base rate becomes a bonus rather than an overrun.

The free plan is the number worth sitting with. Forty Google Shopping requests won't get you through a single product category with any depth. It's enough to confirm the JSON shape, check that `source` returns the merchants you expect, and see how your target queries behave. It is not a pilot.

Three operational details change what happens at the limit:

- **429 is a concurrency error, not a credit error.** Your request is turned away, not queued and not billed. Lower the thread count and retry.
- **Running out of credits pauses scraping by default** until the monthly allowance renews. Auto-upgrade, which moves you to the next plan when credits hit zero, is opt-in on Hobby, Startup, Business, Scaling and Professional.
- **Pay-as-you-go is only on Scaling, Professional, Advanced and Enterprise**, at a fixed per-credit rate up to a monthly cap you set. Hobby users who burn through 100,000 credits mid-month have two options: wait, or turn on auto-upgrade and take the next tier.

Each response carries the actual cost of that request in a `sa-credit-cost` header, and you can set `max_cost` per scrape to put a ceiling on any single call. Check that header for your first week instead of trusting your own arithmetic. The Domain Cost Estimator in the dashboard does the same job before you spend anything.

There's a **7-day no-questions-asked refund policy** on paid plans, so a Hobby month you abandon after testing costs you nothing as long as you say so inside the week.

## Where the data gets thin

The Google Shopping endpoint returns product cards from the results page and stops there. There's no product description, no specification sheet, and no complete list of every seller offering the same item. If your use case needs specs, you need a second request against the product page itself.

The documented response fields are also shorter than what some competing Google Shopping scraper APIs return. Title, price, extracted price, source and delivery options cover price comparison and merchant monitoring. They don't cover star ratings, review counts, brand, original price, discount badges or a product identifier. Those fields exist on the page; the endpoint's documented schema just doesn't expose them. If your ranking model weights reviews, budget for either a different provider or your own detail-page pass.

Two behaviours of Google Shopping itself will bite you regardless of which API you choose:

**Results move between runs.** Google Shopping personalises by location, time and session, and third-party actor documentation is explicit that two runs minutes apart can return different products, prices and ordering. Any price history you build has to be keyed on a stable product identifier, not on `position`. Rank 4 today is a different item tomorrow.

**Discount data is not price history.** The "was" price and percentage badge only appear on products Google itself has flagged as reduced. A product that has held the same price for a year has no history to extract from a single run. Real price history means running the same queries on a schedule and storing the results yourself, which is why the Async-plus-cron workflow matters more than any single feature on the pricing page.

**Filtering happens on your side.** The endpoint accepts country, domain, city, language, page size and offset. There's no price range, free-shipping, on-sale or condition parameter in the documentation. Third-party actor write-ups note that Google accepts only one filter condition per request anyway, with the rest validated client-side against each card, which explains why two providers can return different product sets for what looks like the same filter. Pull the data, then filter it in your own code where you can see exactly what happened.

👉 [Put the 100,000-credit Hobby plan through a real query load](https://www.scraperapi.com/pricing?fp_ref=coupons)

## Cost per thousand products, not per thousand calls

Comparing these services on price per request flatters whichever one charges per call. Compare cost per thousand usable products instead.

At 25 credits a Google request and roughly 50 to 70 cards per results page (a figure third-party actor docs quote for Google Shopping pages generally), a 4,000-request Hobby allowance touches somewhere in the low hundreds of thousands of product cards before deduplication. Deduplication is the part people forget: the same Nike shoe appears across several merchants, and a category run collapses hard once you group by product.

Pay-per-item models invert the maths. Apify's parsebird actor bills only for products saved to the dataset, with compute and proxy costs folded into that price, so a run that returns 40 clean items costs 40 units and nothing more. If your queries are narrow and your filters are tight, per-item pricing is predictable. If you're sweeping a category across ten countries to see what exists, per-request pricing usually wins because you're paying for the sweep, not the survivors.

## How ScraperAPI compares to SerpApi and the Apify actors

All four options return Google Shopping data as JSON. They differ in what they charge for, what they filter server-side, and how deep the schema goes.

|  | ScraperAPI | SerpApi | Apify (parsebird) | Apify (confidential_gnat) |
| --- | --- | --- | --- | --- |
| Billing unit | 25 credits per Google request | Per search request | Per product saved | Per product saved |
| Server-side filters | None documented beyond country, domain, city, language, offset | `min_price`, `max_price`, `free_shipping`, `on_sale`, `small_business`, `sort_by` | Price, on-sale, free shipping, free returns, condition, store list | Price range only |
| Localisation | `COUNTRY_CODE`, `TLD`, `UULE`, `HL`, `GL` | `location`, `google_domain`, `gl`, `hl`, `device` | 29 countries | Country and language codes |
| Response depth | Position, title, price, extracted price, source, delivery | Adds `product_id`, `rating`, `reviews`, `thumbnail`, `extensions`, inline and categorised result blocks | Brand, original price, discount badge, rating, review count, condition, returns, five Google identifiers | Current and previous price, discount, merchant, rating, review count, shipping, `productId` |
| Batch and scheduling | Async batch with `statusUrl`, cron-friendly | Single GET per query | Apify schedules and API | Apify schedules, cross-run caching |

SerpApi is the pick when you need rating, review count and `product_id` in the same response as the price, and when server-side price and shipping filters save you a processing step. Its repository is new and carries no stars, so treat the code samples as documentation rather than as evidence of a large user base.

The parsebird actor suits catalogue work: brand, condition, original price, percentage off and five separate Google identifiers give you the structure to match offers across merchants, and the 29-country coverage beats ScraperAPI's country-level tier for breadth. It explicitly does not open product detail panels, so specs still require another tool.

The confidential_gnat actor is the one to read closely if you plan to write your own scraper. Its documentation states plainly that Google Shopping returns no products to unproxied requests, answering with a JavaScript shell or an unusual-traffic check, and that product images are frequently null because Google lazy-loads thumbnails and top-of-page items are consistently among the missing. Those constraints apply to every provider, ScraperAPI included.

## Which plan to buy

Match the tier to geotargeting first, then to volume:

- **Testing the endpoint, or a single research sprint:** the 7-day trial, 5,000 credits, about 200 requests. No card.
- **US or EU only, a few thousand queries a month:** Hobby at 100,000 credits (4,000 requests, 2,857 with rendering).
- **US or EU, tens of thousands of queries:** Startup at 1,000,000 credits.
- **Any market outside the US and EU, or you need unlimited analytics history:** Business, which is where country-level targeting starts.
- **Need ratings, reviews or product IDs without a second scraping pass:** none of the ScraperAPI tiers solve that. Look at SerpApi or the parsebird actor and compare on cost per thousand usable products.

👉 [Compare all eight ScraperAPI tiers on the pricing page](https://www.scraperapi.com/pricing?fp_ref=coupons)

Two things to confirm before you pay. Count your real query volume including pagination, because every page of results is another 25 credits, and a 200-query sweep at five pages each is 1,000 requests, not 200. Then check whether the country you sell into falls inside US and EU, since that single line in the plan table separates Hobby from Business by a factor of thirty in credits.
