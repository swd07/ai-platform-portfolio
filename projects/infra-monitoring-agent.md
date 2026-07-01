# AIOps Monitoring Agent - Self-Hosted Infra Watcher + LLM Responder

> A self-hosted monitoring and incident-response assistant for production AI infrastructure:
> rule-based detectors, LLM-assisted diagnostics, PM2/service/GPU checks, and mobile chat alerts.

## Problem

Running several AI and business-critical services as a solo technical owner creates a practical
operations problem: failures must be detected quickly, explained clearly, and surfaced in a place
where they will actually be seen.

The system needed to monitor not only application health, but also AI inference endpoints,
PM2 process stability, GPU infrastructure, remote hosts, and database/integration signals.

## Architecture

The agent combines deterministic detectors with an LLM responder:

- **13 independent detectors** - service-down, PM2 crash-loop (restart-rate spikes), memory leaks,
  GPU power/thermal/VRAM anomalies, disk-full, remote-host unreachability, database anomalies, ERP
  (1C) send-failures, and OCR empty-rate, plus security detectors (HTTP brute-force, 429 spikes,
  5xx spikes, SSH brute-force).
- **~22 health-checked endpoints** in the service registry - 6 backend APIs, 5 web frontends, 6 AI
  inference services (LLM agent, VLM, OCR, embeddings, object detection, DINOv2), and core infra
  (S3-compatible object storage, vector DB, PostgreSQL, connection pooler, Redis) - plus every PM2
  process, GPU, disk, and remote hosts.
- **LLM responder layer** backed by a self-hosted Qwen model for summarizing incidents and helping
  interpret operational context.
- **Mobile chat alerting** so incidents are delivered directly to the operator instead of being
  buried in logs or dashboards.
- **60-second production loop** (cron-driven) that re-checks the whole stack every minute, with
  threshold + consecutive-failure counters and dedup so a flapping service alerts once, not every tick.

## My role

Designed and built the monitoring system end-to-end: detector logic, service registry,
LLM responder integration, alert formatting, chat delivery, and production operation.

## Stack

`Python` · `PM2 monitoring` · `self-hosted LLM` · `Qwen` · `GPU infrastructure monitoring` ·
`service health checks` · `chat-based alerting` · `Linux operations`

## Results

- **~22 endpoints + full host** under continuous watch: 6 APIs, 5 web frontends, 6 AI inference
  services, object storage, vector DB, PostgreSQL/pooler/Redis - plus every PM2 process, GPU
  (temperature / VRAM / power), disk, and remote hosts.
- **13 detectors** running on a **60-second loop**, so incidents surface within roughly one cycle
  instead of being discovered hours later in logs.
- **Threshold-tuned to cut alert fatigue** - e.g. PM2 crash-loop fires at ≥3 restarts/min, a service
  is only flagged after ≥3 consecutive down-checks, GPU at 85 °C / 97 % VRAM / 98 % power - combined
  with dedup so a flapping component alerts once.
- **Alerts delivered to a dedicated mobile chat channel**, removing the need for manual SSH or
  log-tailing to notice failures.
- A practical AIOps layer that lets **one engineer operate multiple production AI systems** across
  application, inference, and infrastructure tiers.

### Example incident

A preview web process entered a crash-loop, restarting **~222 times per minute** against a threshold
of 3. The detector caught it on the next 60-second cycle and pushed a **🚨 PM2 CRASH LOOP** alert to
the mobile chat with the process name and restart counts - turning an otherwise invisible,
CPU-burning failure into an immediate, actionable notification instead of a problem found hours later.

## Engineering highlights

- **Hybrid design:** deterministic detectors for reliability, LLM responder for explanation and
  operator-friendly summaries.
- **Crash-loop detection:** PM2 restart-rate checks catch broken deploys and unstable services before
  they stay invisible.
- **Operator-first alerting:** alerts go to a chat interface the operator actually reads, not only to
  server logs.
- **Self-hosted AI operations:** the same infrastructure philosophy applies to monitoring itself -
  local services, controlled dependencies, and no unnecessary SaaS coupling.
