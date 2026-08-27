# Shelf Detection — Production Retrieval & Multimodal AI

> Portfolio summary. The authoritative technical case study, evaluation notes and runnable examples
> live in the dedicated repository:
>
> **[github.com/swd07/retail-shelf-detection](https://github.com/swd07/retail-shelf-detection)**

![Live pipeline output](../assets/shelf-detection-live.jpg)

## What it does

A production-engineered merchandising pipeline turns field shelf photos into structured:

- share-of-shelf;
- brand / SKU recognition;
- assortment and competitor presence;
- price-tag and package evidence;
- explicit `unknown` when evidence is insufficient.

The production path is fully self-hosted:

```text
Android capture
→ ingest API / queue
→ GroundingDINO
→ Qwen2.5-VL OCR
→ Qwen3-Embedding-8B
→ Qdrant dense retrieval
→ attribute reranking
→ DINOv2 + ArcFace visual retrieval
→ deterministic fusion / guardrails
→ brand / SKU / unknown
→ merchandising analytics
```

The LLM reads text/package evidence but **does not make the final SKU decision**.

## Verified evidence

- **Brand precision: 95.8%**
- **SKU precision: 73.1% end-to-end**
- **1,345 entries** in the production vector-retrieval catalog
- **~320k OCR calls**
- **~108k ArcFace shadow evaluations**
- **6k+ shelf photos** processed
- Current rollout: **pilot across 40 outlets / 3 merch users**

Evaluation and rollout include stratified golden sets, cross-store validation, Recall@K,
FPR-anchored precision, Wilson intervals, pre-registered acceptance / kill thresholds, a
**47k-box production replay harness**, and `off → shadow → active` promotion.

## My role

I own the technical architecture and production rollout of this AI subsystem and have been
hands-on in retrieval/matching, OCR/VLM and embedding services, multimodal fusion, guardrails,
evaluation methodology and production infrastructure. The broader Chaban platform is delivered
with an engineering team under my Technical Owner / platform-architect role.

## Go deeper

The dedicated repository contains the detailed architecture, retrieval internals, guardrail design,
evaluation methodology, production lessons, metric-learning experiments and runnable synthetic
examples.

→ **[Retail Shelf Detection — technical case study](https://github.com/swd07/retail-shelf-detection)**  
→ [Back to the full portfolio](../README.md)
