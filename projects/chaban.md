# AI Chaban2 — Commercial Operating Platform for Field Sales & Distribution

> A production field-sales and commercial-operations platform for a dairy producer: offline mobile workflows for sales reps, bidirectional 1C ERP integration, KPI and motivation, management BI, forecasting, merchandising computer vision, and self-hosted AI services.

![Chaban platform architecture](../assets/chaban-architecture.png)

## What the platform is

AI Chaban2 is primarily a **field-sales and commercial-operations platform**, not an AI demo.
Its production core is the daily workflow of field sales representatives: routes, GPS-stamped visits,
orders, cash receipts, returns, customer debt, stock visibility, audits and notes — synchronized with
1C ERP and available offline.

Above that transactional layer sit:

- a **12-scheme KPI and motivation engine**;
- a **13-tab management BI suite**;
- daily demand forecasting and planning support;
- a production computer-vision merchandising pipeline;
- self-hosted LLM agents and infrastructure monitoring.

By August 2026, **98% of company orders in 1C carried a platform-generated identifier** — about
**9k orders/month** — making Chaban2 the primary order-entry channel for the commercial team.

## Users & scale

- **42 accounts**, including **21 active field reps**, 4 supervisors, 3 merchandisers, managers,
  analyst, directors and platform administrators.
- Approximately **1,500 active retail outlets** in a typical month.
- **9–11k orders/month** in the ERP; 8,699 platform orders during 1–27 Aug 2026.
- About **13k GPS-stamped visits/month**; 50k+ visits recorded in total.
- **30k+ cash receipts** and **~10k returns** posted through the platform since early 2026.
- **103 GB PostgreSQL**, approximately **278M rows** across the production database.
- **6k+ merchandising photos**, ~320k OCR calls, one H200 used for self-hosted AI inference.

## Field Sales

The PWA is the working application for field sales representatives. It supports:

- daily route and customer list;
- mandatory visit start/end with GPS before order entry;
- customer card with debt and unpaid invoices;
- product catalog, price types, discounts, agreements and VAT;
- warehouse availability;
- order creation, editing and history;
- cash receipt creation with FIFO allocation against invoices;
- product returns by invoice or manually;
- OOS marks and shelf-price capture;
- store photos and store attributes;
- outlet audit checklist;
- notes, surveys and Web Push reminders for unsent documents;
- personal KPI / plan-vs-fact views and online chat.

Orders, cash receipts and returns are posted from the platform into **1C ERP** through SOAP services.

## Offline Mobile

The field-sales PWA is **local-first**, not just an application shell with cached pages.

Dexie / IndexedDB stores:

- clients;
- SKUs and prices;
- price types, discounts and agreements;
- unpaid invoices and debt-related data;
- order, receipt and return drafts;
- visit/GPS events and notes;
- a typed transactional outbox.

A 13-step prefetch downloads working reference data before field use. The outbox handles
`order | receipt | return | visit_start | visit_end | client_gps` with exponential backoff,
20 attempts, dead-letter state, send locks and UUID request IDs. Server-side duplicate controls
protect document posting.

**45.8k of 46.5k recorded platform orders were created in offline mode.** Reps can continue the
core sales workflow without connectivity; KPI and chat remain online-only.

## KPI & Motivation

Twelve production motivation schemes are computed from ERP-backed sales, debt and planning data.
They cover areas including:

- overdue receivables / debt;
- returns;
- sales plans by product category;
- distribution / MML;
- supplier programs;
- contract programs;
- special commercial tasks;
- total compensation calculation.

Thresholds are editable in the UI, field reps see their own results on mobile, and supervisors /
managers receive role-scoped views. A rep rating provides plan-attainment comparison.

## BI & Management Analytics

The management dashboard contains **13 production analytical areas** with common filters and
role/team scoping:

- sales overview: volume, revenue, gross profit, plan %, active customers;
- plan / fact and motivation;
- forecast;
- service level and under-fulfilment reasons;
- geographic / district analysis;
- customer clusters A–F;
- visit map and route coverage;
- ABC/XYZ product analysis;
- receivables aging;
- returns by reason / rep / product / customer;
- store-card completeness;
- churn / customer-risk analysis;
- configurable pivot/report builder.

The platform can also generate management PPTX reports. Dashboard query results are cached in Redis
for 180 seconds; supervisors are scoped to their own teams.

## Forecasting & Planning

A daily **Prophet-based demand forecast** runs by SKU with holiday, promotion and weather inputs,
then allocates forecast quantities down to customers and feeds planning / customer recommendations.
A separate "today's norm" calculation tells reps what volume is needed to hit monthly targets.

This feature is intentionally not oversold: measured forecast quality is currently weak
(**WAPE ~57% in the audited evaluation**) and is treated as an area for improvement rather than a
success metric. Procurement and production planning are not part of the production platform.

## Merchandising AI

The merchandising module is a separate native Android workflow integrated into the same business
platform:

**Kotlin / Compose terminal (Room + WorkManager, offline capture)**
→ ingest API (JWT, SHA-256 dedup, MinIO)
→ analysis queue
→ GPU pipeline
→ annotation / metrics store
→ office control center.

The recognition stack combines:

- GroundingDINO product detection;
- Qwen2.5-VL OCR / package reading;
- Qwen3-Embedding-8B + Qdrant dense catalog retrieval;
- DINOv2 visual k-NN;
- fine-tuned ArcFace retrieval;
- deterministic multimodal fusion and evidence gates.

Model and gate changes move through **off → shadow → active** using stratified golden sets and
pre-defined acceptance / kill thresholds.

Audited results:

- **95.8% brand precision**;
- **73.1% SKU precision** end-to-end;
- **6k+ photos** processed;
- pilot scope of **40 outlets / 3 merchandising users**.

The office control center exposes share of shelf, assortment presence, competitors, price-tag
analysis, review/unknown inbox and catalog management. The engineering pipeline is production;
market coverage remains pilot-scale.

→ [Detailed retrieval & CV case study](shelf-detection.md)

## AI Agents & Operations

The platform includes self-hosted, tool-calling agents rather than unconstrained chatbots:

- **Director agent** — SQL tools over sales, plans, receivables, returns and merchandising data;
- **KPI agent** — server-scoped access to individual KPI data with numeric output validation and
  deterministic fallback;
- **Security / infrastructure bot** — 31 read-only operational tools plus minute-level monitoring
  and alerts into the corporate chat;
- **Dashboard assistant** — answers from the selected dashboard context rather than unrestricted DB access.

The agent infrastructure is production, but current business adoption is low; it should not be
represented as a heavily used daily workflow.

## 1C ERP Integration

The integration is bidirectional.

### 1C → Chaban2

1C pushes **15 document/data types** into the platform as JSON envelopes, including orders,
shipments/sales, receipts, receivables, stock, prices, agreements, discounts, clients, products and
sales representatives. More than **145k inbound envelopes** were present in the audited production
system. Processing uses business-key upserts and per-type rollback boundaries.

### Chaban2 → 1C

The platform posts:

- orders;
- cash receipts;
- returns

through SOAP web services, then reconciles platform documents against the ERP mirror.

In Aug 2026, **98% of ERP orders originated from the platform**. Recorded order-send failure rate was
**1.7%**, with failed sends reprocessed and no remaining failed documents in the audited snapshot.

## Business Impact — Verified Only

The strongest measurable effect is **workflow adoption**, not claimed sales uplift.

- Platform-originated share of company ERP orders grew from effectively zero at the start of 2026
  to **98% by Aug 2026**.
- The system processed approximately **9k orders/month**, 30k+ cash receipts and ~10k returns.
- Field execution became measurable through **50k+ GPS-stamped visits**.
- The platform exposes debt, plans, KPI and management analytics from a shared ERP-backed data model.
- Self-hosted AI inference removed production dependence on external AI APIs; the exact financial
  saving was not baselined.

### What is deliberately *not* claimed

- No claim that the platform caused sales growth: YoY volume growth began before platform order
  adoption, so causality is not supported by the data.
- No claim for hours/FTE/cost saved because there is no reliable before/after baseline.
- No claim that internal chat replaced WhatsApp/phone workflows; human chat usage is limited.
- No claim that AI agents are used by leadership every day; current usage is low.

## Architecture

```text
Field reps / supervisors / managers / merchandisers / directors
        ↓
PWA / Web / Android / Chat
        ↓
nginx
        ↓
Next.js route handlers + FastAPI services
        ↓
PostgreSQL / Redis / Qdrant / MinIO
        ↓
1C ERP (JSON inbound + SOAP outbound)

AI / ML host:
vLLM Qwen2.5-VL-72B + Qwen3.6-35B
Qwen3-Embedding-8B
GroundingDINO · DINOv2 · ArcFace · OCR · Whisper
        ↓
H200 self-hosted inference
```

## My Role

**Technical Owner / platform architect / Architecture Review Board chair.** I owned the platform
architecture and production contour, ran architecture/governance decisions, operated the production
hosts/backups/secrets, and remained hands-on in key engineering areas including merchandising CV
guardrails/evaluation, security monitoring, ERP exchange fixes and production handover.

The core business platform was implemented by a development team under this architecture and delivery
process. Git identity before July 2026 does not reliably separate individual authorship, so this case
study deliberately describes the platform as **architected and technically owned**, not personally
written line-by-line by one engineer.

## Tech Stack

`Python` · `FastAPI` · `SQLAlchemy` · `Next.js` · `React` · `Dexie / IndexedDB` · `Socket.IO` ·
`PostgreSQL 16` · `PgBouncer` · `Redis` · `Qdrant` · `MinIO` · `Kotlin` · `Jetpack Compose` ·
`Room` · `WorkManager` · `1C SOAP / JSON integration` · `vLLM` · `Qwen2.5-VL` · `Qwen3.6` ·
`Qwen3-Embedding` · `GroundingDINO` · `DINOv2` · `ArcFace` · `PyTorch` · `Prophet` · `nginx` ·
`PM2` · `systemd` · `Docker` · `Prometheus / Grafana`
