# Building a Prospecting Platform: Lead Generation + Lead Research

Research document — options, architecture, and recommendations for building an app/platform
that not only generates leads but performs automated research on each lead.

_Last updated: July 2026_

## 1. What we're building

A prospecting platform has three layers:

1. **Data layer (identify + enrich)** — find companies/people matching an Ideal Customer
   Profile (ICP), then enrich them with firmographics, contact info, tech stack, funding,
   hiring signals, news.
2. **Research/intelligence layer** — the differentiator. An AI research agent fans out to
   web search, LinkedIn, news, job boards, and enrichment APIs, then synthesizes a
   structured brief per lead: company overview, buying triggers, talking points, fit score.
3. **Action layer** — CRM sync, outreach sequencing, meeting booking, and the UI where
   users review researched leads.

Industry consensus in 2026: orchestration is the easy part — **the data layer is where
platforms win or lose**.

## 2. Three ways to achieve it

### Option A — Assemble from existing tools (fastest, no/low code)

Use Apollo or Clay (or both) as the engine, and build only a thin layer on top.

- **Apollo.io**: all-in-one — ~275M contact database, enrichment, sequencing, dialer,
  AI features. Professional tier ≈ $99/user/mo. Best for operational simplicity.
- **Clay**: a workflow engine orchestrating 100+ data providers (Apollo, Clearbit,
  FullEnrich, Cognism, LinkedIn…) in "waterfall enrichment" cascades, with AI columns
  (Claude/GPT) for per-lead research. Best for custom ICPs and deep research; credit
  pricing punishes sloppy workflows.
- **Common hybrid**: Apollo for list building → Clay for waterfall enrichment + AI research
  → push to CRM.

**Pros:** live in days; proven data. **Cons:** not "your platform" — you rent it; per-seat/
per-credit costs scale badly; limited differentiation.

### Option B — Build your own platform on data APIs (recommended)

Own the product; buy the data. Your app orchestrates third-party data APIs plus an LLM
research agent.

Key data APIs to combine (waterfall style — try cheapest/most accurate first, fall back):

| Provider | Strength | Notes |
|---|---|---|
| Apollo API | Contacts + firmographics breadth | Good starting database |
| People Data Labs | Person/company profiles at scale | ~$0.20/profile |
| Explorium | Firmographic accuracy leader (~97.8% benchmark) | Built for AI-agent consumption |
| Cognism | GDPR-safe, human-verified mobiles (EU) | 2–3× connect rates on phones |
| Hunter / Dropcontact / FullEnrich | Email finding + verification | Cheap waterfall steps |
| Web search + scraping (e.g. Tavily, Firecrawl) | Fresh signals, news, site content | Feeds the research agent |

The research agent (Claude API) takes each enriched lead, gathers sources, and produces a
structured brief + fit score. Store everything; re-run research on triggers (funding round,
job posting, leadership change).

**Pros:** full ownership, custom research depth per your field, defensible product.
**Cons:** months not days; you manage data contracts, dedup, compliance.

### Option C — Full custom data collection (own crawlers/scrapers)

Build your own crawling of registries, directories, LinkedIn, niche sources for your field.

**Pros:** unique data no competitor has — strongest moat if your field is niche and poorly
covered by mainstream B2B databases.
**Cons:** slowest, highest legal exposure (ToS, GDPR/CCPA), constant maintenance.
Usually done *later*, on top of Option B, for the niche sources the big providers miss.

## 3. Recommended architecture (Option B)

```
        ┌──────────────────────────────────────────────┐
        │  UI (Next.js/React) — lead lists, briefs,     │
        │  fit scores, review queue, CRM sync status    │
        └──────────────▲───────────────────────────────┘
                       │ REST/GraphQL
        ┌──────────────┴───────────────────────────────┐
        │  API backend (FastAPI / Node)                 │
        │  ICP definitions · lead pipeline · scoring    │
        └───────┬──────────────────────────┬───────────┘
                │                          │
   ┌────────────▼────────────┐   ┌─────────▼─────────────┐
   │ Enrichment orchestrator │   │ AI research agent      │
   │ (waterfall: PDL →       │   │ (Claude API + web      │
   │ Apollo → Hunter → …)    │   │ search/scrape tools)   │
   └────────────┬────────────┘   └─────────┬─────────────┘
                │      job queue (Celery/BullMQ/Temporal) │
        ┌───────▼──────────────────────────▼───────────┐
        │  Postgres (leads, companies, research briefs, │
        │  provenance/source URLs, consent/opt-outs)    │
        └──────────────────────────────────────────────┘
```

Key design decisions:

- **Job queue for everything** — enrichment and research are slow, rate-limited, and
  flaky; run them async with retries (Temporal or Celery/BullMQ).
- **Provenance on every field** — store which provider supplied each datum and when;
  needed for quality debugging *and* GDPR subject-access requests.
- **Waterfall enrichment** — call providers in cost/accuracy order, stop at first hit.
- **Research briefs as structured JSON** (overview, signals, triggers, talking points,
  score + rationale), rendered in the UI — not free-form text blobs.
- **Human-in-the-loop** — hybrid teams outperform fully autonomous agents ~69% of the
  time; the platform fills and researches the funnel, humans approve and engage.

## 4. Compliance (non-negotiable from day one)

- GDPR/CCPA: document legal basis, honor opt-outs, keep suppression lists, DSAR support.
- Prefer GDPR-safe providers (e.g. Cognism) for EU prospecting.
- Respect provider ToS — especially around LinkedIn-derived data.

## 5. Suggested phased roadmap (fits this repo's agile process)

- **Sprint 0 — Validate with Option A**: run the exact workflow manually in Apollo + Clay
  for 50 leads in your field. Learn what "good research" means before writing code.
- **Sprint 1 — MVP pipeline**: ICP form → Apollo/PDL search → Postgres → simple lead list UI.
- **Sprint 2 — Waterfall enrichment**: add 2–3 providers with fallback + email verification.
- **Sprint 3 — AI research agent**: Claude + web search per lead → structured brief + score.
- **Sprint 4 — Actions**: CRM export (HubSpot/Salesforce), re-research triggers, review queue.

Example user stories (matching `.github/ISSUE_TEMPLATE/user-story.md`):

- *As a* sales user, *I need* to define my ICP (industry, size, geo, keywords) *so that*
  the platform only surfaces relevant leads.
- *As a* sales user, *I need* an AI-generated research brief on each lead *so that* I can
  personalize outreach without manual research.
- *As a* compliance owner, *I need* every contact to carry source provenance and opt-out
  status *so that* we can honor GDPR/CCPA requests.

## 6. Domain signal model — corporate, security & training video production

The target field for this platform. These are the concrete buying signals and triggers the
research agent and fit-scoring engine should detect, and where each signal comes from.

### Timing / financial triggers

| Signal | Why it matters | Data source in the platform |
|---|---|---|
| Q4 budget roll-over into Q1 | Unspent capex gets released before "use-it-or-lose-it" closes | Firmographics (strong prior FY) + fiscal calendar |
| Calendar-aligned fiscal year → Q1 | New annual budgets approved and released in Q1 | Firmographic enrichment |
| Office opening / facility expansion | Immediate need for security video integration + facility training videos | Press-release & news monitoring (research agent web search) |
| New VP of HR or CMO | New execs revamp training content / corporate videos to make their mark | Leadership-change tracking (enrichment APIs, LinkedIn signals) |

### Behavioral buying signals

| Signal | Why it matters | Data source |
|---|---|---|
| Active paid search / social / YouTube ads | Proves an established video marketing budget | Ad-library lookups (research agent) |
| Visits to portfolio / pricing pages | High-intent account behavior | B2B visitor tracking integration (Dealfront / Leadfeeder) |
| Case-study downloads, video ad views | Decision-maker engagement | Own-site analytics + marketing automation webhook |
| Rapid hiring of frontline or compliance staff | Urgent need for standardized onboarding/training videos | Job-postings monitoring (job board APIs) |

### Qualification framework (BANT)

Encode as explicit fields on each lead, feeding the fit score:

- **Budget** — discusses investment range (vs. only asking for ballparks)
- **Authority** — contact is a decision-maker: Marketing Director, HR Manager, Operations Director
- **Need** — named pain point: high employee turnover (training videos), new compliance standards (compliance videos), new facility (security video)
- **Timeline** — concrete deadline: product launch, trade show, compliance audit

### Implications for the build

- The prototype's rule-based fit rubric should be re-weighted around these signals
  (expansion news, exec change, compliance hiring velocity, ad spend, Q1 window).
- The research agent's per-lead brief should explicitly report: fiscal-year alignment,
  recent expansion/relocation news, leadership changes, hiring velocity in
  frontline/compliance roles, and active ad campaigns.
- Sprint backlog addition: integrate a website visitor-tracking source (Dealfront or
  Leadfeeder API) as an inbound intent feed alongside outbound enrichment.

## Sources

- [Clay vs Apollo 2026 — Knowlee](https://www.knowlee.ai/compare/clay-vs-apollo)
- [Clay vs Apollo.io 2026 — Modern Inbound](https://moderninbound.com/blog/clay-vs-apollo)
- [Best B2B Data Enrichment APIs for AI Agents — Explorium](https://www.explorium.ai/blog/data-for-gtm/best-b2b-data-enrichment-api-for-ai-agents/)
- [Waterfall Enrichment: Clay vs ZoomInfo vs Apollo — DevCommX](https://www.devcommx.com/blogs/waterfall-enrichment-clay-vs-zoominfo-vs-apollo)
- [Building a Lead Research Agent — SEM Nexus](https://semnexus.com/building-a-lead-research-agent-step-by-step-implementation)
- [AI Lead Generation Agent: What Works in 2026 — Prospeo](https://prospeo.io/s/ai-lead-generation-agent)
- [9 Best B2B Data Enrichment Tools — Demandbase](https://www.demandbase.com/blog/b2b-data-enrichment-tools/)
