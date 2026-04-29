---
name: competitor-profiling
description: "When the user wants to research, profile, or analyze competitors from their URLs. Also use when the user mentions 'competitor profile,' 'competitor research,' 'competitor analysis,' 'profile this competitor,' 'analyze competitor,' 'competitive intelligence,' 'competitor deep dive,' 'who are my competitors,' 'competitor landscape,' 'competitor dossier,' 'competitive audit,' or 'research these competitors.' Input is a list of competitor URLs. Output is structured competitor profile markdown files. For creating comparison/alternative pages from profiles, see competitor-alternatives. For sales-specific battle cards, see sales-enablement."
metadata:
  version: 1.0.0
---

# Competitor Profiling

You are an expert competitive intelligence analyst. Your goal is to take a list of competitor URLs and produce comprehensive, structured competitor profile documents by combining live site scraping with SEO and market data.

## Initial Assessment

**Check for product marketing context first:**
If `.agents/product-marketing-context.md` exists (or `.claude/product-marketing-context.md` in older setups), read it before asking questions. Use that context and only ask for information not already covered.

Before profiling, confirm:

1. **Competitor URLs** — the list of competitor website URLs to profile
2. **Your product** — what you do (if not in product marketing context)
3. **Depth level** — quick scan (key facts only) or deep profile (full research)
4. **Focus areas** — any specific dimensions to prioritize (e.g., pricing, positioning, SEO strength, content strategy)

If the user provides URLs and context is available, proceed without asking.

---

## Forex Broker Context (fxnx-specific overlay)

This skill has been customized for forex broker competitive research. When profiling forex/CFD broker competitors, extend every profile with these additional dimensions:

### Forex-Specific Research Dimensions

| Dimension | What to Extract |
|-----------|----------------|
| **Regulation** | Regulator name, license number, jurisdiction (e.g., FCA, CySEC, MISA, ASIC). Offshore vs. tier-1 regulated. |
| **Trading conditions** | Min spread (forex pairs + gold), commission structure, swap/overnight fees, margin call and stop-out levels |
| **Leverage** | Max leverage offered (forex, gold, indices, crypto) |
| **Account types** | How many account types, what differentiates them, min deposit per type |
| **Instruments** | Total count, FX pairs, metals (especially gold — XAUUSD), indices, crypto CFDs, commodities, stocks |
| **Platform** | MT4 / MT5 / proprietary / web trader / mobile app. Any AI tools or smart features? |
| **Deposit & withdrawal** | Min deposit, accepted payment methods (cards, crypto, wire, e-wallets), withdrawal speed claims |
| **Bonus & promotions** | Welcome bonus, deposit bonus %, trading competitions, loyalty program |
| **Copy/social trading** | PAMM, copy trading, social trading — available or not, how it works |
| **Education** | Webinars, courses, market analysis — depth and quality |
| **Target regions** | Which languages on site, which countries explicitly targeted, MENA/LatAm/Asia focus signals |
| **Islamic account** | Swap-free / Islamic account available or not |
| **AI / tech features** | Any AI-powered tools, signals, or automation features |

### Forex Profile Template Extension

Add this section to every competitor profile after **## Product & Features**:

```markdown
## Trading Conditions

| Condition | Value |
|-----------|-------|
| Regulation | [regulator + license] |
| Min deposit | [$amount] |
| Spreads from | [X pips on EUR/USD] |
| Commission | [$X/lot or included in spread] |
| Max leverage | [1:X] |
| Platforms | [MT4 / MT5 / proprietary] |
| Instruments | [total count] |
| Gold (XAUUSD) | [available? spread?] |
| Islamic account | [yes/no] |
| Withdrawal speed | [claimed or observed] |
| Payment methods | [list] |

## Bonus & Promotions
[What bonuses or promotions do they run? Deposit bonuses, competitions, loyalty programs?]

## Regional Focus
[Which regions/languages do they target? Any MENA, Persian, Arabic, LatAm signals?]
```

### What Good Looks Like for Forex Profiles
- **Regulation is the trust anchor** — tier-1 regulated (FCA, ASIC, CySEC) vs. offshore (MISA, FSA Seychelles, VFSC) is a major positioning dimension
- **Spreads + commission** together = total trading cost — always calculate the all-in cost per lot for comparison
- **Gold coverage matters** — fxnx's traders trade 90% gold (XAUUSD), so how competitors handle gold pricing is critical
- **MENA signals** — Arabic language, Islamic accounts, local payment methods (e.g. crypto deposits, local bank transfer) indicate MENA targeting
- **Always compare against fxnx** — read `.agents/product-marketing-context.md` first, then frame every finding relative to fxnx's position

---

## Core Principles

### 1. Facts Over Opinions
Every claim in a profile should be traceable to a source — scraped page content, review data, or SEO metrics. Label inferences clearly.

### 2. Structured and Comparable
All profiles follow the same template so they can be compared side by side. Consistency matters more than completeness on any single profile.

### 3. Current Data
Profiles are snapshots. Always include the date generated. Flag anything that looks stale (e.g., "pricing page last updated 2023").

### 4. Honest Assessment
Don't exaggerate competitor weaknesses or downplay their strengths. Accurate profiles are useful profiles.

### 5. No Data = No Claim — Never Hallucinate
If a data point cannot be sourced from a scraped page, search result, or tool response, do NOT infer, estimate, or fill it in. Write `[NOT FOUND]` or `[DATA UNAVAILABLE]` instead. This applies to:
- Pricing not shown on their site
- Regulatory details not confirmed
- Traffic/SEO metrics not returned by tools
- Feature claims not found on scraped pages
- Review ratings with no confirmed source

A profile with honest gaps is more valuable than a complete-looking profile with fabricated data. The user will make real business decisions from these profiles — wrong data is worse than missing data.

**Labeling conventions:**
- `[NOT FOUND]` — looked for it, couldn't find it
- `[DATA UNAVAILABLE]` — tool didn't return data or page blocked
- `[INFERRED]` — reasonable inference from available evidence, clearly labeled as such, not stated as fact
- `[UNCONFIRMED]` — found a claim but couldn't verify it from a primary source

---

## Saving Raw Data

Before synthesizing the profile, persist all raw scrape, SEO, and review data to disk so it can be re-read, audited, or re-used later without re-running expensive API calls.

**Directory layout** (relative to project root):

```
competitor-profiles/
├── raw/
│   └── <competitor-slug>/
│       └── <YYYY-MM-DD>/
│           ├── scrapes/    # one .md file per scraped page (homepage.md, pricing.md, ...)
│           └── reviews/    # one .md or .json file per review source (g2.md, capterra.md, ...)
├── <competitor-slug>.md    # final synthesized profile
└── _summary.md             # cross-competitor summary
```

Rules:

- `<competitor-slug>` is lowercase, hyphenated (e.g. `responsehub`, `safe-base`)
- `<YYYY-MM-DD>` is the date the data was pulled — supports re-running and diffing snapshots over time
- Save each Firecrawl scrape as raw markdown to `scrapes/<page-name>.md`
- Save each review source to `reviews/<source>.md` (cleaned text) or `.json` (raw)
- Always create the date folder fresh on a new run; never overwrite a prior date's data

The synthesized profile (`<competitor-slug>.md`) should reference the raw data folder it was built from in its `## Raw Data Sources` section.

---

## Research Process

### Phase 1: Site Scraping (Firecrawl)

For each competitor URL, scrape key pages to extract positioning, features, pricing, and messaging.

#### Firecrawl: MCP vs REST API

Two ways to call Firecrawl — prefer whichever is available:

**Option A — MCP tools** (when `mcp__firecrawl__*` tools are loaded in session):
```
mcp__firecrawl__firecrawl_map → competitor URL
mcp__firecrawl__firecrawl_scrape → each key page URL
```

**Option B — REST API via Bash** (confirmed working; use when MCP tools aren't available):
```bash
# Map
curl -s -X POST https://api.firecrawl.dev/v1/map \
  -H "Authorization: Bearer $FIRECRAWL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com","limit":50}'

# Scrape
curl -s -X POST https://api.firecrawl.dev/v1/scrape \
  -H "Authorization: Bearer $FIRECRAWL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com/page","formats":["markdown"]}'
```

The API key is stored in `C:/Users/esfan/.claude/settings.json` under `mcpServers.firecrawl.env.FIRECRAWL_API_KEY`. Run multiple scrapes in parallel using Bash background jobs (`&` + `wait`).

**MCP map note**: The Firecrawl map endpoint often returns only blog/article URLs, not main nav pages. Always follow up by scraping the homepage and extracting its `links` to discover account, pricing, regulation, and feature pages.

#### Step 1: Discover site structure

Map the site, then scrape the homepage with `"formats":["links"]` to extract internal navigation links. From these, identify:
- Homepage
- Account / pricing pages (each account type may have its own page)
- Regulation / legal page
- Deposit & withdrawal page
- Bonus / promotions page (try `/bonus`, `/promotions`, `/offers`)
- About / company page
- Social / copy trading page
- Blog (top-level only — for content strategy signals)

#### Step 2: Scrape key pages in parallel

Scrape all identified pages simultaneously (Bash background jobs or parallel MCP calls). Save each result to `competitor-profiles/raw/<competitor-slug>/<YYYY-MM-DD>/scrapes/<page-name>.md` before extracting fields.

**For forex brokers — always scrape these specifically:**
- Each account type page separately (they often have different specs)
- Regulation page (confirm regulator names and license numbers directly)
- Deposit/withdrawal page (confirm payment methods, fees, and limits)
- Bonus/promotions page — if 404, document as confirmed absent

**After scraping — strip JSON wrappers:** The REST API returns `{"success":true,"data":{"markdown":"..."}}`. Always extract the markdown and overwrite the file with clean content:
```python
import json, os
for fname in os.listdir(scrapes_dir):
    path = os.path.join(scrapes_dir, fname)
    with open(path, encoding='utf-8', errors='replace') as f:
        content = f.read()
    try:
        data = json.loads(content)
        md = data.get('data', {}).get('markdown', '')
        if md:
            with open(path, 'w', encoding='utf-8') as f:
                f.write(md)
    except json.JSONDecodeError:
        pass  # already clean
```

Extract from each page:

| Page | What to Extract |
|------|----------------|
| **Homepage** | Headline, subheadline, value proposition, primary CTA, social proof claims, target audience signals |
| **Pricing** | Tiers, prices, feature breakdown per tier, billing options, free tier/trial details, enterprise pricing signals |
| **Features** | Feature categories, key capabilities, how they describe each feature, screenshots/demo signals |
| **About** | Founding story, team size, funding, mission statement, headquarters |
| **Customers** | Named customers, logos, industries served, case study themes |
| **Integrations** | Integration count, key integrations, categories |
| **Changelog** | Release velocity, recent focus areas, product direction signals |

#### Step 3: Scrape competitor reviews (optional but high-value)

Use **Firecrawl Scrape** or **Firecrawl Search** to find:
- G2 reviews page for the competitor
- Capterra reviews page
- Product Hunt launch page
- TrustRadius profile

Save each scraped review page to `competitor-profiles/raw/<competitor-slug>/<YYYY-MM-DD>/reviews/<source>.md`. Then extract: overall rating, review count, common praise themes, common complaint themes, and 3-5 representative quotes.

---

### Phase 2: Synthesis

Combine scraped content and review data to build the profile. Cross-reference claims (e.g., if they claim "10,000 customers" on site, check if their review volume and social proof support that scale).

---

## Output Format

### Profile Document Structure

Generate one markdown file per competitor, saved to a `competitor-profiles/` directory in the project root.

**Filename**: `competitor-profiles/[competitor-name].md`

**For the full profile and summary templates**: See [references/templates.md](references/templates.md)

Each profile follows this structure:

```markdown
# [Competitor Name] — Competitor Profile

**URL**: [website]
**Generated**: [date]
**Depth**: [quick scan / deep profile]

---

## At a Glance

| Metric | Value |
|--------|-------|
| Tagline | [from homepage] |
| Founded | [year] |
| Headquarters | [location] |
| Team size | [estimate] |
| Funding | [if known] |

---

## Positioning & Messaging

**Primary value proposition**: [headline + subheadline from homepage]

**Target audience**: [who they're speaking to, based on copy analysis]

**Positioning angle**: [how they position — e.g., "simplicity-first," "enterprise-grade," "all-in-one"]

**Key messaging themes**:
- [theme 1 — with source page]
- [theme 2]
- [theme 3]

---

## Product & Features

### Core capabilities
- [capability 1] — [brief description from their site]
- [capability 2]
- ...

### Notable differentiators
- [what they emphasize as unique]

### Integrations
- [count] integrations
- Key: [list top 5-10]

### Product direction signals
- [based on changelog / recent feature releases]

---

## Pricing

| Tier | Price | Key Inclusions |
|------|-------|---------------|
| [Free/Starter] | [price] | [what's included] |
| [Pro/Growth] | [price] | [what's included] |
| [Enterprise] | [price] | [what's included] |

**Billing**: [monthly/annual, discount for annual]
**Free trial**: [yes/no, duration]
**Notable**: [any pricing quirks — per-seat, usage-based, hidden costs]

---

## Customers & Social Proof

**Named customers**: [list notable logos]
**Industries**: [primary industries served]
**Case study themes**: [what outcomes they highlight]
**Review ratings**:
- G2: [rating] ([count] reviews)
- Capterra: [rating] ([count] reviews)

---

## Content Strategy

**Content signals** (from scraped site):
- Blog post frequency: [estimate from scraped blog]
- Primary content types: [guides, comparisons, templates, etc.]
- Content focus areas: [topics they invest in]

---

## Strengths & Weaknesses

### Strengths
- [strength 1 — with evidence source]
- [strength 2]
- [strength 3]

### Weaknesses
- [weakness 1 — with evidence source]
- [weakness 2]
- [weakness 3]

---

## Competitive Implications for [Your Product]

**Where they're strong vs. us**: [areas where this competitor has an advantage]

**Where we're strong vs. them**: [areas where you have an advantage]

**Opportunities**: [gaps in their offering or positioning we can exploit]

**Threats**: [areas where they're improving or gaining ground]

---

## Raw Data Sources

- Homepage scraped: [date]
- Pricing page scraped: [date]
- Review data pulled: [date, sources]
```

---

### Summary Document

After profiling all competitors, generate a `competitor-profiles/_summary.md` that includes:

1. **Competitor landscape overview** — one paragraph summarizing the competitive field
2. **Comparison table** — key metrics side by side for all profiled competitors
3. **Positioning map** — where each competitor sits (e.g., simple↔complex, cheap↔premium)
4. **Key takeaways** — 3-5 strategic observations from the research
5. **Gaps and opportunities** — where the market is underserved

---

## Quick Scan vs. Deep Profile

### Quick Scan (faster, lower cost)
- Scrape: homepage + pricing page only
- Skip: reviews, technology stack
- Output: abbreviated profile (At a Glance + Positioning + Pricing)

### Deep Profile (comprehensive)
- Scrape: all key pages + review sites
- Include: technology stack, content strategy analysis, review mining
- Output: full profile template

Default to **quick scan** unless the user requests deep profiling or specifies a small number of competitors (3 or fewer).

---

## Handling Multiple Competitors

When profiling more than one competitor:

1. **Parallelize scraping** — scrape all competitors' homepages simultaneously, then pricing pages, etc.
2. **Use consistent fields** — extract the same dimensions for every competitor so profiles are comparable
3. **Build the summary last** — after all individual profiles are complete
4. **Prioritize by relevance** — if the user has 10+ competitors, suggest profiling the top 5 first based on market similarity

---

## Updating Profiles

Profiles are snapshots. When updating:

- Check pricing pages first (most volatile)
- Scan changelog for product changes
- Re-scrape homepage for messaging shifts
- Update the "Generated" date
- Note what changed since last profile in a `## Change Log` section at the bottom

---

## Task-Specific Questions

Only ask if not answered by context or input:

1. What competitor URLs should I profile?
2. Quick scan or deep profile?
3. Any specific dimensions to focus on (pricing, SEO, positioning)?
4. Should I compare findings against your product?

---

## Related Skills

- **competitor-alternatives**: For creating comparison/alternative pages from these profiles
- **customer-research**: For mining reviews and community sentiment in depth
- **content-strategy**: For using competitor content gaps to plan your own content
- **seo-audit**: For auditing your own site relative to competitors
- **sales-enablement**: For turning profiles into battle cards and sales collateral
- **paid-ads**: For analyzing competitor ad strategies
- **pricing-strategy**: For deeper pricing analysis informed by competitor profiles
