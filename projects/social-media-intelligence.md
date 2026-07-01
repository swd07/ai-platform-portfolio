# AI-Assisted Social Media Intelligence & Campaign Analytics

> A social-media intelligence and campaign analytics workflow for a consumer beverage brand - combining influencer discovery, Instagram Business/Graph API integration, Apify-based data extraction, website analytics, search visibility metrics, and AI-assisted reporting.

## Problem

For a regional consumer-brand launch, the business needed more than basic social-media activity. It needed a practical way to:

* discover relevant local micro-influencers,
* filter out inflated follower counts and fake engagement,
* track audience activity during promotions and brand events,
* understand how campaigns affect website traffic, search visibility, and Instagram growth,
* and turn scattered marketing data into actionable internal reports.

The challenge was to build a low-cost, repeatable workflow that could support real marketing decisions instead of relying on vanity metrics.

## Architecture

The system combines three connected layers:

```text
influencer discovery
   → candidate filtering and authenticity checks
   → Instagram / website / search metrics collection
   → campaign analytics and anomaly detection
   → internal reports for business review
```

### Influencer discovery & vetting

* Seeded discovery from regional hashtags, geo/context signals, and beverage-brand launch criteria.
* Filtered candidate accounts by follower band, engagement rate, and local relevance.
* Added comment-authenticity analysis to detect bot-like activity, engagement pods, and weak audience quality.
* Generated ranked shortlists and per-candidate reports for human final review.

### Social Business API & Instagram workflow

* Integrated Instagram Business/Graph API workflows for brand-account operations.
* Implemented a publishing flow for Reels using the standard container → polling → publish lifecycle.
* Added account-level metric collection such as follower count and media/account activity.
* Built API-level operations around controlled access, token handling, and reliable execution.

### Campaign analytics & reporting

* Integrated website traffic metrics from Yandex Metrika: visits, users, new vs. returning users, traffic sources, devices, age segments, and conversion goals.
* Integrated Google Search Console metrics: clicks, impressions, CTR, average position, and branded vs. non-branded search visibility.
* Added Instagram follower-growth tracking and day-by-day follower delta analysis.
* Built period-over-period comparison to show whether a campaign performs better or worse than the previous comparable period.
* Added spike detection using a rolling statistical window to highlight unusual changes in traffic or follower activity.

### AI-assisted workflow

* Used AI-assisted development and analysis workflows to iterate on data extraction, reporting logic, metric interpretation, and business-facing summaries.
* The goal was not to let an LLM make uncontrolled marketing decisions, but to accelerate analysis and turn raw metrics into clearer reports for the business.

## My role

Designed and implemented the data-collection and analytics workflow: influencer discovery, Apify-based extraction, Instagram API operations, website/search metrics aggregation, campaign reporting logic, and AI-assisted analysis.

The full BOOMi internal platform is a separate product, but this case study focuses specifically on the social-media intelligence and campaign analytics layer that I built around the brand's marketing activity.

## Stack

`Python` · `Apify` · `Instagram Business / Graph API` · `Yandex Metrika API` · `Google Search Console API` · `marketing analytics` · `campaign reporting` · `AI-assisted analysis`

## Results

* Reduced a broad pool of approximately 970 candidate accounts to about 30 qualifying profiles and a vetted shortlist of around 19 genuine micro-influencers.
* Documented an honest low hit-rate for the target micro-influencer band, giving the business a realistic planning input instead of inflated expectations.
* Ran the discovery and vetting cycle at low scraping cost, making influencer sourcing repeatable for a small-brand launch.
* Added structured reporting for website traffic, search visibility, Instagram follower growth, and campaign-period comparison.
* Helped connect brand-promotion activity with measurable audience-engagement signals instead of relying only on subjective marketing impressions.

## Engineering highlights

* **Cheaper-first funnel:** expensive data extraction runs only after cheaper filters reduce the candidate pool.
* **Authenticity over vanity metrics:** follower count and engagement rate are treated as insufficient alone; comment quality and audience signals matter.
* **Multi-source reporting:** website, search, and Instagram metrics are combined into one campaign-review workflow.
* **Spike detection:** unusual changes in traffic or follower activity are surfaced automatically for review.
* **AI-assisted analysis, not uncontrolled automation:** AI tools support development and reporting, while final marketing decisions remain human-reviewed.
