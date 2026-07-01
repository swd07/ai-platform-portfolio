# AIOps Monitoring Agent — Self-Hosted Infra Watcher + LLM Responder

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

- **Rule-based detectors** for service-down events, PM2 restart spikes, GPU power/health signals,
  remote host availability, chat delivery failures, and database/integration anomalies.
- **LLM responder layer** backed by a self-hosted Qwen model for summarizing incidents and helping
  interpret operational context.
- **Service registry** describing monitored applications, AI inference endpoints, and infrastructure
  components.
- **Mobile chat alerting** so incidents are delivered directly to the operator instead of being
  buried in logs or dashboards.
- **Production loop** that continuously checks the stack and emits actionable alerts when thresholds
  are crossed.

## My role

Designed and built the monitoring system end-to-end: detector logic, service registry,
LLM responder integration, alert formatting, chat delivery, and production operation.

## Stack

`Python` · `PM2 monitoring` · `self-hosted LLM` · `Qwen` · `GPU infrastructure monitoring` ·
`service health checks` · `chat-based alerting` · `Linux operations`

## Results

- Detected PM2 crash-loop incidents automatically using restart-rate thresholds.
- Monitored the full AI stack: application services, OCR/VLM/embedding endpoints, GPU-related
  infrastructure, and remote hosts.
- Delivered alerts into a mobile-readable chat channel, reducing reliance on manual SSH/log checks.
- Created a practical AIOps layer that allows one engineer to operate multiple production AI systems.

## Engineering highlights

- **Hybrid design:** deterministic detectors for reliability, LLM responder for explanation and
  operator-friendly summaries.
- **Crash-loop detection:** PM2 restart-rate checks catch broken deploys and unstable services before
  they stay invisible.
- **Operator-first alerting:** alerts go to a chat interface the operator actually reads, not only to
  server logs.
- **Self-hosted AI operations:** the same infrastructure philosophy applies to monitoring itself —
  local services, controlled dependencies, and no unnecessary SaaS coupling.
