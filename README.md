# product data extraction: How to Pull Prices, Stock and Specs at Scale Without Managing Proxies

Product data extraction means pulling structured fields — title, price, currency, stock status, SKU, variants, images, ratings — off product pages and turning them into usable data. The hard part is rarely the parsing. It's getting the page at all: e-commerce sites rotate layouts, sit behind Cloudflare or PerimeterX, and rate-limit anything that looks like a bot. That's exactly the layer ScraperAPI sells: you send a URL with your API key, it handles proxies, CAPTCHAs and browser rendering, and hands back the HTML or, for major marketplaces, ready-made JSON.

This guide covers how the extraction actually works, what it costs across every current plan, and the credit math most reviews skip — the part that decides whether a $49 plan is enough or a $475 one is still too small.

> **Quick verdict:** most people doing product data extraction on Amazon or Walmart should start on **Startup ($149/mo, 1M credits)** after testing on the free plan. If you're only monitoring a handful of products, Hobby at $49 covers 20,000 Amazon product requests a month — plenty for personal price tracking. 👉 [Check current ScraperAPI plans and prices](https://www.scraperapi.com/pricing?fp_ref=coupons)

## What product data extraction actually involves

A product page has two layers, and both can break your pipeline.

The visible layer is the fields you want: name, brand, price, availability, SKU or GTIN, variant options, image URLs, ratings, category, specs. Search results in this space converge on the same advice — treat `name` and `price` as required fields and give everything else safe defaults, so one missing value doesn't crash an entire feed.

The invisible layer is what makes people search for "product data extraction" in the first place. Amazon shows different prices by region. Walmart loads content with JavaScript. Sites protected by Cloudflare or Datadome will serve a challenge page instead of the product. A raw `requests.get()` in Python gets you maybe a dozen pages before the bans start.

So a realistic setup needs three things: a way to fetch pages reliably (proxy rotation, CAPTCHA solving, JS rendering), a way to parse them (your own code, or a structured endpoint), and a way to run it on a schedule at volume. ScraperAPI packages the first part as a service and offers shortcuts for the second.

## Three ways to extract product data with ScraperAPI

ScraperAPI gives you one API key and several ways to use it, depending on how much you want to build yourself.

**1. The standard scraping API.** You send your target URL to `https://api.scraperapi.com` with your key, and get the page HTML back — proxies, CAPTCHA handling and retries included. It works for web pages, API endpoints, images, PDFs and other files, with a 50MB request size limit. There's also a proxy port (`proxy-server.scraperapi.com:8001`) if your existing scraper already speaks the proxy protocol, plus SDKs for some languages. Official docs recommend setting a 70-second timeout in your application, especially for heavily protected domains.

**2. Structured Data Endpoints.** For the big marketplaces, you can skip parsing entirely. Send an ASIN to the Amazon product endpoint and get back JSON or CSV with the fields already separated. The official docs show a real response for a Sony PXW-Z280 camcorder (ASIN B07G4J7TY5): price ($6,054.95), shipping ("FREE"), stock status ("Only 8 left in stock - order soon"), star rating and review count, Best Sellers Rank, model number, manufacturer, category path, and the full image gallery. There are equivalent endpoints for Amazon search results and offers, Walmart products and search, and Google Shopping. 👉 [Try the Amazon Product Scraper endpoint](https://www.scraperapi.com/solutions/structured-data/amazon-products-scraper/?fp_ref=coupons) if your targets live on Amazon.

**3. DataPipeline and Async Scraper, for volume.** DataPipeline is the no-code route: pick the Amazon Products template, paste in your ASIN list (up to 10,000 per project), choose a webhook or dashboard download, and set a schedule. The Async Scraper takes a single POST request and fires millions of URLs through it, returning results via webhook — that's the enterprise-scale option. Both are included on paid plans.

A practical detail worth knowing: geo-targeting is included on every plan, and it matters for product data. Pointing your request at a different Amazon TLD or source country returns localized pricing and shipping — useful if you're tracking prices across markets.

## ScraperAPI plans and pricing: the full table

Here's the complete plan lineup from the official pricing page, in USD. Every paid plan includes JS rendering, premium proxies, JSON auto-parsing, CAPTCHA and anti-bot bypass, custom sessions and headers, automatic retries, unlimited bandwidth, and a 99.9% uptime guarantee.

| Plan | Monthly price | Credits / month | Concurrent threads | Geo-targeting | Get started |
| --- | --- | --- | --- | --- | --- |
| Hobby | $49 | 100,000 | 20 | US & EU only | [ Start on Hobby](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | $149 | 1,000,000 | 50 | US & EU only | [ Start on Startup](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | $299 | 3,000,000 | 100 | Country-level | [ Start on Business](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | $475 | 5,000,000 | 200 | Country-level | [ Start on Scaling](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | $975 | 10,500,000 | 300 | Country-level | [ Start on Professional](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | $1,975 | 21,500,000 | 500 | Country-level | [ Start on Advanced](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | Custom | 22,000,000+ | 500+ | Country-level | [ Contact sales](https://www.scraperapi.com/pricing?fp_ref=coupons) |

Annual billing takes 10% off every tier — Hobby drops to $44.10/mo, Startup to $134.10, Scaling to $427.50. Note the geo-targeting split: if you need to pull product data from specific countries outside the US and EU, Hobby and Startup won't do it; you need Business ($299) or above.

## The credit math most guides get wrong

Here's the part that decides your real budget: ScraperAPI bills in credits, and one request does not equal one credit.

A standard page costs 1 credit. Amazon costs 5. Google and Bing (including subdomains) cost 25. LinkedIn costs 30. And if a site sits behind bot protection like Cloudflare, Datadome or PerimeterX, bypassing it adds another 10 credits per request. Add-on features like premium proxies and JS rendering consume extra credits on top — third-party testing puts premium at +10 and ultra-premium at +30, so the sticker credit count is a ceiling, not a guarantee.

Run the numbers on the $49 Hobby plan's 100,000 credits:

- 100,000 plain HTML pages, or
- 20,000 Amazon product requests, or
- 4,000 Google search pulls, or
- about 6,600 protected e-commerce pages once the +10 bypass fee kicks in

That gap is why "10 million credits" can mean very different things depending on your target sites. Two built-in tools help you plan before you commit: the Domain Cost Estimator in your dashboard shows the exact cost for any URL, and you can set a `max_cost` cap per scrape so a single heavy request can't drain your balance.

> If you're budgeting a monitoring project, divide your monthly credit allowance by your target site's cost per request first. A "million credits" plan is 200,000 Amazon lookups — or 40,000 once those pages sit behind extra protection.

## The free plan: what 1,000 credits actually gets you

ScraperAPI's free tier is a permanent plan, not just a countdown. Signing up gets you 1,000 monthly credits with a maximum of 5 concurrent connections, no credit card required, and there's a 7-day trial with 5,000 credits stacked on top when you register. 👉 [Create a free account and get your API key](https://www.scraperapi.com/signup?fp_ref=coupons)

In practice, 1,000 monthly credits covers:

- a personal price-monitoring script — a handful of Amazon products checked daily runs roughly 200 requests a month at 5 credits each
- occasional SERP checks — about 40 Google queries per month
- prototyping and API testing against your target sites before you pay for anything

What it won't cover: ultra-premium proxies, geo-targeting beyond the defaults, anything needing more than 5 parallel threads, or any single job heavy enough to eat 1,000 credits in one go. Treat the free plan as a sandbox for verifying success rates on your specific target sites, then upgrade once the math is confirmed.

## What happens when you hit your limits

Two failure modes matter, and both are relatively painless.

Concurrency: exceed your thread limit and ScraperAPI returns a 429 — the request is rejected, not queued, and you're not charged for it. Your code just needs to retry.

Credits: when you run out, scraping pauses until the next billing cycle by default. Nothing gets charged silently. If you'd rather keep going, there are two opt-in mechanisms: auto-upgrade (available on Hobby through Professional) automatically bumps you to the next plan when credits run out, and pay-as-you-go (Scaling and above) keeps scraping at a fixed per-credit rate up to a monthly spending cap you set. Annual plans can also renew early for a fresh batch of credits.

## How ScraperAPI compares to the alternatives

Third-party comparison roundups put ScraperAPI's starting price at $49/user/month, sitting in the middle of the scraping-API market: Apify lists plans from $29/month with a free plan but no free trial, Oxylabs starts around $30/month with a trial but no free plan, Zyte at $100/month with a trial, and Bright Data at $499/month with neither. (These figures come from a third-party review site, not the vendors' own pricing pages, so verify before deciding on an alternative.)

Where ScraperAPI's pitch is strongest for product data specifically: the structured endpoints for Amazon and Walmart remove the parsing work entirely, and the per-site credit pricing is transparent enough to model in a spreadsheet. The trade-off: if your targets are long-tail e-commerce sites rather than major marketplaces, you're paying the general API rate plus bypass fees, and a marketplace-specific or self-hosted approach could be cheaper. ScraperAPI claims a pool of 40M+ IPs across more than 50 countries and says its service handled over 11 billion requests in the last 30 days, with 100% CCPA and GDPR compliance.

## FAQ

**Does ScraperAPI work without writing code?**
Yes, two ways. DataPipeline lets you build scheduled Amazon product jobs from templates — submit an ASIN list, pick a delivery method, done. And the structured endpoints return finished JSON/CSV, so if you can make an HTTP request (or use a tool that can), you get parsed product data. There's also a community n8n node for no-code workflow tools, and LangChain/MCP integrations if you're wiring it into AI agents.

**How many product pages can I extract per month on each plan?**
Depends entirely on the target site. At 5 credits per Amazon page: Hobby ≈ 20,000 pages, Startup ≈ 200,000, Business ≈ 600,000, Scaling ≈ 1,000,000. Plain-HTML shops at 1 credit get you five times those numbers. Use the dashboard's Domain Cost Estimator for your actual targets before picking a tier.

**Is there a refund or trial?**
The confirmed offers are the permanent free plan (1,000 credits/month) and a 7-day trial with 5,000 credits, both without a credit card. A refund policy isn't published on the pricing page, so if that matters to you, ask support before committing to an annual plan.

**What data do I get from the Amazon endpoint?**
The documented response includes price, shipping cost, availability and stock quantity, star rating and review count, Best Sellers Rank, ASIN and model number, brand, manufacturer, product dimensions and weight, category path, feature bullets, full description, and all product images — returned as JSON or CSV.

## Which setup fits your situation

If you're tracking a few dozen products for yourself, start free and stay there until the 1,000 credits get tight, then Hobby at $49 is the natural step. If you're running price monitoring or competitor tracking on Amazon or Walmart as part of a business, Startup at $149 gives you 200,000 Amazon lookups a month and 50 concurrent threads, which handles most serious monitoring schedules. If you need country-level pricing across multiple markets, jump to Business at $299 — the geo-targeting on Hobby and Startup won't reach. And if you're pulling millions of pages, the Async Scraper on Scaling and above is the intended path, or contact sales for a custom 22M+ credit setup.

One last check before you pick a tier: list your target domains, multiply each request by its credit cost (1 for plain pages, 5 for Amazon, 25 for Google, plus 10 where bot protection applies), and confirm the monthly total fits with 30% headroom. Then 👉 [pick your plan on the ScraperAPI pricing page](https://www.scraperapi.com/pricing?fp_ref=coupons) and start with the free credits to validate your success rates before the first invoice lands.
