# Chaban — Commercial Operating System for FMCG

> The commercial **operating system** of a large FMCG producer: one platform where field
> sales reps, merchandisers, supervisors, managers, analysts, and leadership all do their
> daily work - with AI woven through it, not bolted on. Owned and built end-to-end as the
> sole technical engineer.

![Chaban platform architecture](../assets/chaban-architecture.png)

## Business impact

- **~2,000 retail outlets** and **500-700 orders/day** flow through the platform into the
  1C ERP - order capture went from paper and phone calls to a ~2-minute digital flow.
- **One system for every commercial role** (~50 daily field users plus office and leadership):
  reps sell from an offline-first PWA, merchandisers feed the CV pipeline from a native
  Android app, managers steer by a 26-tab BI dashboard, leadership sees KPI in real time.
- **A single ERP-synced source of truth** replaced spreadsheets, calls, and disconnected
  reports - with natural-language agents on top so non-technical staff query data and
  trigger actions by asking.

## One platform, every role

| Role | Where they work | What they get |
|---|---|---|
| Field sales reps (~50) | Offline-first **PWA** | orders, client cards, debts, stock, personal KPI, chat - fully functional without connectivity |
| Merchandisers | **Native Android (Kotlin)** app | in-store shelf photo capture feeding the [CV pipeline](https://github.com/swd07/retail-shelf-detection) |
| Supervisors | Web | per-rep performance, returns, client coverage |
| Managers & analysts | **BI dashboard - 26 tabs** | plan/fact, ABC/XYZ, debt, returns, service level, nine KPI/motivation views, forecast accuracy |
| Leadership | Real-time dashboards + reports | company-wide KPI, share of shelf, forecast vs. fact |
| Operations | Control panel + [monitoring agent](infra-monitoring-agent.md) | service health, deploys, incidents pushed to mobile chat |
| Everyone | Built-in **messenger** | dm/group/channel/bot rooms; agents post alerts where people already talk |

## Problem

The producer ran field sales and in-store merchandising across a large retail network on manual,
fragmented tooling - paper/spreadsheet order capture, no structured merchandising compliance, and
analytics disconnected from the company's ERP. The objective was a single platform that:

- captures and manages orders through their full lifecycle,
- integrates bidirectionally with the company's **1C-based ERP**,
- gives operations a control surface for monitoring and deployment,
- and lets non-technical staff query and act on the data in natural language.

## Architecture

The system evolved from an initial monolith into modular services behind a unified API:

- **Order & sales management** - Python / FastAPI backend over PostgreSQL, covering order
  creation, validation, status flow, and reporting.
- **ERP integration (SOAP)** - bidirectional document exchange with the 1C ERP over SOAP web
  services. A key engineering lesson here was establishing a **single source-of-truth mapping**
  for document identity (tracing a concrete document end-to-end rather than guessing from
  heuristics), which removed a whole class of reconciliation bugs.
- **Operations control panel** - surfaces service health, deployments, and security/monitoring
  signals for the running platform.
- **LLM agents** - a set of **function-calling assistants** (an in-platform KPI/SQL agent plus
  separate director and per-rep agents) exposing **~20+ tools in total** over the platform's data
  and operations. Users ask questions and trigger actions in natural language; each agent maps
  intent to the correct tool with structured arguments.
- **Two mobile clients** - an installable **PWA** (Next.js) for field sales reps (order capture and
  web-push alerts) and a **native Android (Kotlin)** app for merchandisers (in-store shelf photo
  capture, feeding the computer-vision pipeline).
- **Real-time messenger** - an in-house **Socket.IO** chat with `dm` / `group` / `channel` / `bot` /
  `support` room types, online presence, attachments, role-based access control, and web-push when
  the chat is closed - available on both web and mobile. It also serves as the delivery channel for
  automated agent alerts (bot rooms): the monitoring agent, for example, posts incidents there so
  they arrive in the operator's mobile chat.

## My role

**Sole technical owner.** I made the architectural decisions and implemented the full stack:
backend services, data model, the SOAP/ERP integration, the agent/tool layer, the control panel
integration, and production deployment and operations.

## Stack

`Python` · `FastAPI` · `PostgreSQL` · `SOAP / 1C ERP integration` · `LLM function-calling` ·
`Next.js` · `PWA` · `Kotlin (Android)` · `Socket.IO` · `Web Push (VAPID)` · `Docker` ·
`process-based service orchestration`

## Engineering highlights

- **Integration discipline:** diagnosing ERP exchange issues by tracing concrete document
  identifiers end-to-end instead of relying on heuristics.
- **Agent design:** bounded, well-typed tool surfaces (~20+ tools across the assistants) rather than
  an open-ended free-text agent - predictable, auditable, and safe to expose to non-technical users.
- **Operability:** health monitoring, supervised processes, and a deployment control surface so a
  single engineer can run the platform reliably.
- **Operated, not just built:** the platform runs under a separate self-hosted
  [monitoring / alerting agent](infra-monitoring-agent.md) that watches services, PM2, GPU, and
  inference endpoints and pushes incidents to a mobile chat.
