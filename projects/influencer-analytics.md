# Influencer Discovery & Vetting Pipeline

> A data-engineering pipeline that finds and vets micro-influencers for a consumer-brand launch —
> scraping candidate accounts, scoring them on engagement quality, and producing a ranked,
> bot-filtered shortlist. Built to support the **BOOMi** beverage launch.

## Problem

Influencer marketing for a regional brand launch is dominated by two failure modes: paying for
**inflated follower counts** and paying for **fake engagement** (bots, engagement pods). The goal
was a repeatable, low-cost pipeline that surfaces genuine **micro-influencers** in the brand's
launch region — small enough to be affordable and authentic, with real, local audiences — and
filters out the noise automatically.

## Architecture

A multi-stage funnel, each stage cheaper-first to keep scraping cost down:

```
hashtag / geo seeds
   → stage 1: candidate discovery (managed scraping API)        # broad pool by tag/region
   → stage 2: profile-metrics filter                            # follower band + engagement rate
   → stage 3: comment-authenticity analysis                     # detect bots / engagement pods
   → ranked, vetted shortlist + per-candidate report (HTML)
```

- **Discovery:** seed the search from a curated set of region/topic hashtags via a managed
  scraping API, building a broad candidate pool.
- **Profile-metrics filter:** keep only accounts in the target **micro-influencer follower band
  (~2k–5k)** with a **healthy engagement rate (≥ ~4%)** — the sweet spot for authentic reach.
- **Comment-authenticity stage:** a second pass pulls recent comments and analyzes them to
  distinguish a real, conversational audience from bot/pod activity — the step that separates a
  worthwhile micro-influencer from a vanity-metrics one.
- **Reporting:** generate a per-candidate report (metrics + qualitative read) as HTML for human
  final selection.

## My role

Designed and built the full pipeline end-to-end: the staged scraping orchestration, the
filtering/scoring logic, the comment-authenticity analysis, the reporting, and the cost controls.

## Stack

`Python` · `managed scraping API` · `engagement-rate analytics` · `HTML reporting` · `cost-budgeted
batch orchestration`

## Results

- Reduced a broad pool of **~970 candidate accounts → ~30 qualifying → a vetted shortlist of ~19**
  genuine micro-influencers with live, authentic audiences.
- Documented an honest **~3% hit-rate** for the target band — a realistic planning input the brand
  could budget against, rather than an inflated promise.
- Ran the full discovery → vetting cycle for **~$14 in scraping cost**, making influencer sourcing
  repeatable on a small-brand budget.

## Engineering highlights

- **Cheaper-first funnel:** ordering stages so the expensive scraping calls only run on candidates
  that already passed cheap filters — explicit per-stage cost budgeting.
- **Authenticity over vanity metrics:** the comment-analysis stage is the differentiator —
  follower count and engagement rate alone are gameable, so I added a qualitative pass to catch
  bot/pod patterns.
- **Honest reporting:** surfacing the real hit-rate and per-candidate evidence so selection is a
  human decision on transparent data, not a black-box score.
