# Applied AI Engineer / AI Solutions Architect — Portfolio

I design and ship **production AI and business systems end-to-end** — from product discovery,
backend/data architecture and enterprise integrations to retrieval/CV pipelines, self-hosted GPU
inference, LLM agents, mobile/web interfaces, evaluation and production operations.

My strongest work sits at the intersection of **Applied AI, product engineering and solutions
architecture**: taking an operational problem, turning it into a system people actually use, and
measuring what works instead of stopping at a demo.

For the main commercial platform below I serve as **Technical Owner / platform architect** and
remain hands-on in architecture, AI, evaluation, integrations and production operations. Other
projects include systems I built independently end-to-end.

> Most production code is private because it contains proprietary data, integrations and
> infrastructure details. This repository contains sanitized case studies, architecture,
> measurable results and selected implementation details.

---

## Featured work

| Project | What it is | Evidence / deep dive |
|---|---|---|
| **AI Chaban2** | Commercial operating platform: offline field sales, 1C ERP, KPI, BI, forecasting, merchandising AI and agents | [Portfolio case study](projects/chaban.md) |
| **Retail Shelf Detection** | Production retrieval + multimodal recognition with Qdrant, DINOv2/ArcFace, guardrails and abstention | **[Technical case-study repository](https://github.com/swd07/retail-shelf-detection)** |
| **AIOps Monitoring Agent** | Deterministic infrastructure watcher + LLM responder + mobile incident alerts | [Case study](projects/infra-monitoring-agent.md) |
| **Jarvis** | Real-time streaming voice assistant: STT → LLM → TTS over WebRTC | [Case study](projects/jarvis.md) |
| **Fitness Marathon Platform** | 0→1 coach/client product with private media, chat, RBAC and full-stack delivery | [Case study](projects/fitness-platform.md) |
| **BOOMi** | 3D consumer web experience, generative video and social API integration | [Case study](projects/boomi.md) |
| **Social Media Intelligence** | Influencer discovery + Instagram/Search/traffic analytics | [Case study](projects/social-media-intelligence.md) |

---

## AI Chaban2 — Commercial Operating Platform

![Chaban platform architecture](assets/chaban-architecture.png)

A production **field-sales and commercial-operations platform** for an FMCG manufacturer. Its core
is an offline-first working application for field sales reps, integrated bidirectionally with 1C ERP.
On top of the transactional layer sit KPI/motivation, management BI, forecasting, merchandising CV,
self-hosted AI services and operational agents.

**Verified production scale**

- **21 active field reps** and about **1,500 active retail outlets** in a typical month.
- **9–11k company orders/month**; by Aug 2026 **98% of ERP orders carried a platform-generated ID**.
- About **13k GPS-stamped visits/month**; 50k+ field visits recorded.
- **30k+ cash receipts** and **~10k returns** posted through the platform since early 2026.
- **12 KPI/motivation schemes** and a **13-area management BI suite** with role/team scoping.
- **103 GB PostgreSQL / ~278M rows**, plus Redis, Qdrant, object storage and self-hosted GPU services.

**Field-sales product**

The PWA covers the rep's daily route, GPS visit start/end, client card, debt and unpaid invoices,
product catalog, prices/discounts/agreements, stock visibility, order entry, cash receipts, returns,
OOS/shelf-price capture, audits, notes, personal KPI and push reminders.

It is genuinely **local-first**: a Dexie/IndexedDB store prefetches working reference data and a typed
transactional outbox handles orders, receipts, returns and visit/GPS events with backoff, dead-letter
handling, send locks and duplicate controls. **45.8k of 46.5k recorded platform orders were created
offline.**

**ERP / analytics / AI**

- Bidirectional **1C ERP** exchange: 15 inbound document types plus outbound SOAP posting of
  orders, receipts and returns with reconciliation.
- **13 management analytics areas**: sales, plan/fact, service level, receivables aging, returns,
  geographic coverage, client clusters, ABC/XYZ, churn risk, visit coverage and configurable pivot.
- Daily Prophet-based demand forecasting is live, but current measured accuracy is weak
  (**WAPE ~57%**), so it is treated as an improvement area rather than a headline result.
- Merchandising CV and self-hosted LLM/tool-calling services sit on the same platform infrastructure.

**Business impact I can prove:** the platform became the company's primary order-entry channel and
made field execution measurable. I do **not** attribute the company's sales growth to the platform:
the growth trend started before platform adoption, and there is no clean baseline for hours/FTE cost
savings.

**My role:** Technical Owner / platform architect and ARB chair; architecture and major technology
decisions, production ownership, AI/retrieval/evaluation work, ERP/infrastructure decisions and
engineering governance, working with the delivery team rather than claiming all platform code as
individual authorship.

→ **[Full Chaban product / architecture case study](projects/chaban.md)**

---

## Retail Shelf Detection — Production Retrieval & Multimodal AI

[![Live shelf pipeline output](assets/shelf-detection-live.jpg)](https://github.com/swd07/retail-shelf-detection)

A production-engineered merchandising pipeline that converts shelf photos into brand/SKU,
share-of-shelf, assortment and competitor analytics. The system combines:

`GroundingDINO → Qwen2.5-VL OCR → Qwen3-Embedding → Qdrant retrieval → DINOv2 / ArcFace → deterministic fusion → guardrails → SKU / brand / unknown`

The LLM **does not choose the final SKU**. Independent evidence is fused deterministically, and the
system abstains when confidence is insufficient.

**Key evidence**

- **95.8% brand precision / 73.1% SKU precision** on confirmed end-to-end evaluation.
- **1,345 entries** in the production vector-retrieval catalog; broader merchandising catalog has
  ~1.5k own + competitor SKUs.
- **~320k OCR calls** and **~108k ArcFace shadow evaluations** processed.
- **6k+ shelf photos** processed; current rollout is a **pilot across 40 outlets / 3 merch users**.
- Golden sets, cross-store validation, recall@K, FPR-anchored precision, pre-registered kill gates,
  a **47k-box replay harness**, and `off → shadow → active` rollout.

This portfolio intentionally does **not** duplicate the full technical write-up.

→ **[Open the authoritative technical case study + runnable examples](https://github.com/swd07/retail-shelf-detection)**

---

## AIOps Monitoring Agent

A hybrid monitoring system combining deterministic detectors with an LLM responder. It watches
application services, PM2 processes, database/integration signals, GPU/host health and AI endpoints
on a **60-second loop**, deduplicates incidents and pushes actionable alerts into mobile chat.

**Evidence:** 13 detector classes, ~22 health-checked endpoints plus host/process signals; production
security/infra watcher generated 152 alerts in Aug 2026.

→ [Full write-up](projects/infra-monitoring-agent.md)

---

## Jarvis — Real-Time Voice Assistant

Streaming **STT → LLM → TTS** assistant built on Pipecat/WebRTC with tool access, profile-based
personas and fallback voice handling. Work focused on pipeline integration, routing and latency /
time-to-first-byte reduction.

→ [Full write-up](projects/jarvis.md)

---

## Fitness Marathon Platform — 0→1 Product

Next.js 15 + Payload CMS 3 + PostgreSQL coach/client platform for cohort-based fitness programs.
Built as an app-like product with trainer/client flows, private signed media, RBAC, group/direct
chat, review workflows and domain invariants in the data layer.

**Evidence:** 21 collections, 7 vertical delivery slices and **204 automated tests**, including IDOR,
CSRF, file-isolation and trainer-scope security coverage.

→ [Full write-up](projects/fitness-platform.md)

---

## BOOMi & Social Intelligence

Consumer-brand work spanning a React Three Fiber / Next.js 3D web experience, generative-video
workflow, Instagram Business/Graph API integration, influencer discovery and multi-source campaign
analytics.

→ [BOOMi](projects/boomi.md) · [Social Media Intelligence](projects/social-media-intelligence.md)

---

## Engineering strengths

- **Product / architecture:** discovery, 0→1 delivery, enterprise integration, mobile/web workflows,
  production ownership and technical governance.
- **Applied AI:** multimodal retrieval, computer vision, OCR/VLM, vector search, LLM agents,
  tool calling, self-hosted inference and real-time voice.
- **Evaluation:** golden sets, grouped/cross-store validation, recall@K, FPR-anchored precision,
  replay testing, pre-registered acceptance/kill thresholds and explicit abstention.
- **Safe rollout:** `off → shadow → active`, observability, health checks, rollback and incident
  monitoring.
- **Data / integration:** PostgreSQL, Qdrant, Redis, MinIO/S3-compatible storage, 1C SOAP/JSON.

## Tech stack

**AI / ML:** PyTorch · GroundingDINO · DINOv2 · ArcFace · Qwen2.5-VL · Qwen3-Embedding · vLLM · Qdrant · Prophet · Whisper  
**Backend / Data:** Python · FastAPI · PostgreSQL · Redis · MinIO · REST · SOAP / 1C  
**Frontend / Mobile:** TypeScript · Next.js · React · PWA · Dexie/IndexedDB · Kotlin · Jetpack Compose · Room · WorkManager  
**Realtime / Infra:** Socket.IO · WebRTC · Docker · nginx · PM2/systemd · NVIDIA H200 · Prometheus/Grafana

---

## Contact

**Eduard Kharaev**  
GitHub: [swd07](https://github.com/swd07)  
Email: [haraev87@gmail.com](mailto:haraev87@gmail.com)  
Telegram: [@Edharaev](https://t.me/Edharaev)
