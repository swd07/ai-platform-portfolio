# Chaban — AI Sales-Management & CV Merchandising Platform

> AI sales-management + computer-vision merchandising platform for **a large FMCG / dairy
> producer**, owned and built end-to-end as the sole technical engineer.

![Chaban platform architecture](../assets/chaban-architecture.png)

## Problem

The producer ran field sales and in-store merchandising across a large retail network on manual,
fragmented tooling — paper/spreadsheet order capture, no structured merchandising compliance, and
analytics disconnected from the company's ERP. The objective was a single platform that:

- captures and manages orders through their full lifecycle,
- integrates bidirectionally with the company's **1C-based ERP**,
- gives operations a control surface for monitoring and deployment,
- and lets non-technical staff query and act on the data in natural language.

## Architecture

The system evolved from an initial monolith into modular services behind a unified API:

- **Order & sales management** — Python / FastAPI backend over PostgreSQL, covering order
  creation, validation, status flow, and reporting.
- **ERP integration (SOAP)** — bidirectional document exchange with the 1C ERP over SOAP web
  services. A key engineering lesson here was establishing a **single source-of-truth mapping**
  for document identity (tracing a concrete document end-to-end rather than guessing from
  heuristics), which removed a whole class of reconciliation bugs.
- **Operations control panel** — surfaces service health, deployments, and security/monitoring
  signals for the running platform.
- **LLM agent layer** — a **function-calling assistant exposing ~23 tools** over the platform's
  data and operations. Users ask questions and trigger actions in natural language; the agent
  maps intent to the correct tool with structured arguments.
- **Two mobile clients** — an installable **PWA** (Next.js) for field sales reps (order capture and
  web-push alerts) and a **native Android (Kotlin)** app for merchandisers (in-store shelf photo
  capture, feeding the computer-vision pipeline).
- **Real-time messenger** — an in-house **Socket.IO** chat with `dm` / `group` / `channel` / `bot` /
  `support` room types, online presence, attachments, role-based access control, and web-push when
  the chat is closed — available on both web and mobile. It also serves as the delivery channel for
  automated agent alerts (bot rooms): the monitoring agent, for example, posts incidents there so
  they arrive in the operator's mobile chat.

## My role

**Sole technical owner.** I made the architectural decisions and implemented the full stack:
backend services, data model, the SOAP/ERP integration, the agent/tool layer, the control panel
integration, and production deployment and operations.

## Stack

`Python` · `FastAPI` · `PostgreSQL` · `SOAP / 1C ERP integration` · `LLM function-calling` ·
`Docker` · `process-based service orchestration`

## Scale & results

- **~2,000 retail outlets** served by the platform.
- **500–700 orders/day** processed.
- **~50 active users** across field sales and office.
- Replaced manual order entry and disconnected reporting with an integrated, ERP-synced flow,
  with a natural-language agent layer on top for ad-hoc querying and operations.

## Engineering highlights

- **Integration discipline:** diagnosing ERP exchange issues by tracing concrete document
  identifiers end-to-end instead of relying on heuristics.
- **Agent design:** a bounded, well-typed tool surface (~23 tools) rather than an open-ended
  free-text agent — predictable, auditable, and safe to expose to non-technical users.
- **Operability:** health monitoring, supervised processes, and a deployment control surface so a
  single engineer can run the platform reliably.
- **Operated, not just built:** the platform runs under a separate self-hosted
  [monitoring / alerting agent](infra-monitoring-agent.md) that watches services, PM2, GPU, and
  inference endpoints and pushes incidents to a mobile chat.
