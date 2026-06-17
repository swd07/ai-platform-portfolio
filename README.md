# AI Integrator / Platform Architect — Portfolio

I design and ship **production AI/ML systems end-to-end** — from architecture and data
modelling through ML pipelines, backend services, infrastructure, and frontend. I work as the
**sole technical owner** of several AI products: I make the architectural decisions, write the
code across the stack, run the GPU inference infrastructure, and operate the systems in
production.

My focus areas are **computer vision pipelines**, **LLM agent / function-calling layers**,
**real-time voice**, and **ERP/system integration** — with a strong bias toward measurable
results, honest evaluation, and safe, gated rollouts rather than ship-and-pray.

> **What this portfolio shows:** for each project — the problem, the architecture, my role, the
> stack, and concrete results. Infrastructure is described at the level of *technology class*
> (e.g. "self-hosted S3-compatible object storage", "NVIDIA H200 GPU"), not specific deployments.

---

## Table of contents

| Project | One-liner | Deep-dive |
|---|---|---|
| **Chaban** | AI sales-management + CV merchandising platform for a large FMCG / dairy producer | [projects/chaban.md](projects/chaban.md) |
| **Shelf Detection** | Computer-vision merchandising service + mobile app (share-of-shelf analytics) | [projects/shelf-detection.md](projects/shelf-detection.md) |
| **BOOMi** | Beverage brand — 3D web experience, generative video, social Business API integration | [projects/boomi.md](projects/boomi.md) |
| **Jarvis** | Real-time streaming voice assistant (STT → LLM → TTS) | [projects/jarvis.md](projects/jarvis.md) |

---

## Chaban — AI Sales-Management & CV Merchandising Platform

**Problem.** A large FMCG / dairy producer ran field sales and merchandising across a wide retail
network on manual, fragmented processes. The goal: digitize order capture, merchandising
compliance, and analytics into one platform that integrates with the company's ERP.

**Architecture.** The platform evolved from an early monolith into a set of modular services
behind a single API layer:
- **Order & sales management** backend (Python / FastAPI, PostgreSQL) covering the full
  order lifecycle.
- **ERP integration** with a 1C-based ERP over **SOAP web services** — bidirectional document
  exchange (orders, catalogues, reference data) with a traceable source-of-truth mapping.
- **Operations control panel** — monitoring, deployment, and health surfacing for the running
  services.
- **LLM agent layer** — a function-calling assistant exposing **~23 tools** over the platform's
  data and operations, so non-technical users can query and act in natural language.

**My role.** Sole technical owner: system architecture, the full backend, the data model, the
ERP/SOAP integration, the agent/tool layer, and production deployment and operations.

**Stack.** Python, FastAPI, PostgreSQL, SOAP/ERP integration, LLM function-calling, Docker,
process-based service orchestration.

**Scale & results.**
- **~2,000 retail outlets** served.
- **500–700 orders/day** flowing through the platform.
- **~50 active users** (field sales + office).
- Replaced manual order entry and spreadsheet reporting with an integrated, ERP-synced flow.

→ [Full write-up](projects/chaban.md)

---

## Shelf Detection — Merchandising Computer-Vision Service + Mobile App

**Problem.** Measure on-shelf reality — share of shelf, assortment coverage, competitor presence,
package/format mix — directly from photos taken by field merchandisers, at scale, without manual
tagging.

**Architecture.** A staged, asynchronous CV pipeline:
- **Mobile capture app** → ingestion API → durable **work queue** → async worker.
- **Object detection** (YOLO / open-vocabulary detection) to localize product packs on the shelf.
- **OCR + Vision-Language model** pass to read brand/label text from each crop.
- **Visual embeddings** (DINOv2 ViT-L/14) feeding a **KNN gallery** of confirmed products.
- **Retrieval-augmented matching** over a **vector database (Qdrant)** for SKU identification.
- **Rule-based fusion + guardrail layer** that combines OCR, retrieval, and visual signals, and
  deliberately abstains (honest "Unknown") rather than emitting confident-but-wrong labels.
- **Self-hosted GPU inference** on an **NVIDIA H200** for detection, embeddings, OCR, and VLM.

**My role.** Designed and built the entire pipeline: detection integration, the OCR/VLM and
embedding services, the retrieval/matching layer, the guardrail and within-shelf disambiguation
logic, the training/evaluation harness, and the catalog-normalization tooling. I treat the LLM as
a **teacher/auditor** for labeling, never as uncontrolled production inference.

**Stack.** PyTorch, YOLO / open-vocabulary detectors, DINOv2, Vision-Language OCR, Qdrant,
FastAPI, PostgreSQL, self-hosted S3-compatible object storage, Docker.

**Results.**
- Detection **F1 improved 0.68 → 0.91 on unseen (out-of-sample) photos**.
- **Catalog normalization 34 → 20 categories**, removing duplicated/ambiguous classes.
- **Eliminated ~300 false positives** via a targeted regex/normalization fix in the label path.
- Trained package classifier reaching **~92% overall accuracy** with leak-free, grouped-by-image
  evaluation and cross-store stress testing.
- Engineered a **shadow → active rollout discipline** (every model/guardrail change runs in
  shadow and is measured on a real population before it can affect production metrics).

→ [Full write-up](projects/shelf-detection.md)

---

## BOOMi — Beverage Brand: 3D Web, Generative Video, Social Integration

**Problem.** Launch a consumer beverage brand with a premium digital presence and an automatable
social-media channel.

**Architecture.**
- **Marketing site** — Next.js + React + **React Three Fiber** (real-time 3D product/scene
  rendering in the browser), with a scroll-driven hero and a full brand-identity system
  (palette, typography, motion).
- **Generative video creative** — an image-to-video / text-to-video pipeline used to produce
  cinematic product clips, with a draft-then-final cost discipline.
- **Social Business API integration** — a FastAPI wrapper around a social-media **Business API**
  for programmatic publishing, with token lifecycle handling.
- **Self-hosted media delivery** for brand assets behind TLS.

**My role.** Full-stack build (frontend + backend), the 3D/creative front-end, the generative
video tooling, and the social API integration and deployment.

**Stack.** Next.js, React, React Three Fiber, TypeScript, FastAPI, Python, nginx, generative
video models, static-hosting + TLS.

**Results.** Live production site with an interactive 3D hero and brand system; a working
programmatic social-publishing integration; a repeatable generative-video workflow for brand
creative.

→ [Full write-up](projects/boomi.md)

---

## Jarvis — Real-Time Streaming Voice Assistant

**Problem.** A hands-free operational assistant that feels conversational — low enough latency
for natural back-and-forth, with the platform's tools available by voice.

**Architecture.**
- **Streaming pipeline** built on **Pipecat**: streaming **STT → LLM → TTS**, with a **fallback
  voice** for resilience when the primary TTS is unavailable.
- **WebRTC** transport for real-time audio in the browser.
- **Profile-based prompts and tools** — the same engine serves distinct personas (e.g.
  operational vs. executive) with different system prompts and tool subsets, routed at the
  transport layer.

**My role.** Pipeline integration, persona/profile routing, and **latency / time-to-first-byte
(TTFB) reduction** work across the STT → LLM → TTS path.

**Stack.** Pipecat, WebRTC, streaming STT/TTS, LLM, Python.

**Results.** A working real-time voice assistant with measurable latency/TTFB improvements and
multi-persona routing on a shared pipeline.

→ [Full write-up](projects/jarvis.md)

---

## Infrastructure & Engineering Practices

- **Self-hosted GPU inference** on **NVIDIA H200** — detection, embeddings, OCR, and
  vision-language and language models served locally for cost control and data residency.
- **Self-hosted services** across the stack: relational database (**PostgreSQL**),
  **S3-compatible object storage**, **vector database (Qdrant)**, reverse proxy / TLS,
  process-based service orchestration.
- **Containerization** with Docker for reproducible model/service deployment.
- **Feature flagging & safe rollouts** — a disciplined **shadow → active** model: every model or
  guardrail change runs in shadow, is measured on a real population, and is promoted only behind
  an explicit gate, with one-step rollback always available.
- **Evaluation rigor** — leak-free, grouped-by-image train/test splits; out-of-sample and
  cross-store stress tests; population-level validation (never curated subsets) before any
  production promotion.
- **CI / deploy tooling** — scripted build/deploy, health checks, and process supervision with
  automatic restart and restart-storm guards.
- **Secret hygiene** — identified and remediated committed secrets, migrated credentials to
  environment variables, and established least-privilege, read-only database access for analytics
  and agents.

---

## Tech Stack

**Languages:** Python, TypeScript / JavaScript, SQL.

**ML / CV:** PyTorch, YOLO & open-vocabulary object detection, DINOv2 visual embeddings,
Vision-Language models, OCR, retrieval-augmented generation (RAG), ArcFace metric learning,
classifier training & calibration.

**LLM / Agents:** function-calling / tool-use agent layers, multi-tool orchestration, LLM-as-judge
evaluation, real-time voice (Pipecat, streaming STT/LLM/TTS).

**Backend:** FastAPI, REST APIs, async work queues, SOAP/ERP (1C) integration.

**Data:** PostgreSQL, Qdrant (vector DB), S3-compatible object storage.

**Frontend:** Next.js, React, React Three Fiber, WebRTC.

**Infra / Ops:** NVIDIA H200 GPU inference, Docker, nginx, process orchestration, feature
flagging, CI/deploy scripting, observability & health monitoring.

---

> **Contact:** _add your name, email, and links here._
> This portfolio describes systems I architected and own as the sole technical engineer.
> Infrastructure details are intentionally generalized to the technology-class level.
