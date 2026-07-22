# Fitness Marathon Platform - Coach/Client Coaching System

> A full-stack platform prototype for running women's online fitness marathons: a coach publishes
> daily meal plans and workout programs, participants submit photo food reports and track
> progress, and the coach reviews everything from a dedicated cabinet.

## Problem

A fitness coach runs cohort-based online marathons (nutrition + home workouts) entirely through
messengers: meal plans as images, food-photo reports lost in chat scroll, weight tracking in
spreadsheets. The goal: a single platform where the coach operates the marathon and participants
get an app-like daily experience - built as a production-grade prototype to validate the product
before a full build-out.

## Architecture

- **One codebase, two circuits:** Next.js 15 (App Router) with **Payload CMS 3** embedded
  natively - the CMS provides collections, auth, and an admin panel inside the same Next.js
  process, backed by PostgreSQL. No separate backend service to operate.
- **Domain model (21 collections):** programs -> cohorts (start date, single timezone) -> calorie
  groups -> enrollments. Daily meal plans store **one shared menu with per-calorie-tier portions**
  (6 tiers), published day-by-day by the coach with a completeness gate; workouts attach to
  program days, not dates.
- **Private file layer:** photo reports and progress photos never get public URLs - files are
  served through an authorizing endpoint with **HMAC-signed, expiring links**, per-collection
  RBAC re-checks, EXIF stripping, and WebP re-encoding on upload; orphan cleanup on failed
  writes.
- **Review workflow:** food photo reports flow through submitted -> reviewed / needs-changes
  states with coach comments; resubmission is owner-gated and enrollment-scoped.
- **Chat:** group threads (per calorie group) and direct coach-participant threads,
  auto-provisioned by enrollment hooks; membership-based access, unread counts, monotonic-id
  polling cursor; membership reconciles automatically when a participant changes group or the
  group changes coach.
- **Trainer cabinet:** overview dashboard (review queue, unread, today's activity, weekly
  compliance), participant lists with batch-loaded display names and group context, read-only
  activity monitoring with filters.
- **RBAC throughout:** participant / trainer / curator / admin roles; group-scoped trainer
  visibility (a trainer sees only their groups' participants), CSRF origin checks, direct
  CMS-REST mutations blocked in favor of domain services.

## My role

Sole engineer: architecture, data model, all backend domain services, the private file layer,
both frontend circuits (participant mobile-first UI + trainer cabinet), seed/demo tooling, and
the full test suite. Work was organized in **7 vertical slices**, each shipped as a separate PR
through an external review gate (two to three review rounds per slice were the norm).

## Stack

`Next.js 15` · `Payload CMS 3` · `TypeScript (strict)` · `PostgreSQL` · `Tailwind` ·
`sharp` · `Vitest` · `Playwright` · `Docker Compose`

## Results

- Complete working prototype: participant daily flow (meal plan -> workout -> weight -> photo
  reports -> chat -> knowledge base -> activity tracking) and coach flow (dashboard -> report
  review -> dialogs -> activity monitoring) - accepted by an end-to-end scripted walkthrough.
- **204 automated tests**: 170 unit + integration (run twice back-to-back against one test DB to
  catch state pollution) and 34 Playwright e2e, including dedicated **security suites** (IDOR,
  CSRF, cross-collection file access, trainer scope isolation, upload size/type abuse).
- Idempotent demo seed with realistic data (7 participants, pre-filled review queue in three
  states, chats, 3 days of activity) - the stand resets to a clean demo state with one command.
- A competitor teardown (Trainerize / FitStars / MyFitnessPal) and a token-based design system
  (light + dark themes, live-switchable) prepared for the phase-2 UI.

## Engineering highlights

- **Invariants live in the data layer, not the UI:** enrollment uniqueness, publication gates,
  and activity-entry validation are enforced in CMS hooks and partial unique indexes - admin
  CRUD cannot produce states the domain forbids.
- **Security-first file handling:** signed URLs + per-collection RBAC on the serving endpoint
  proved cheap to build early and impossible to retrofit later; cross-collection key reuse is
  explicitly tested to fail.
- **Vertical-slice delivery with review gates** kept a one-week build honest: every slice landed
  with its tests, and review findings (e.g., a partial-update hook wiping a search index, a
  polling cursor losing same-millisecond messages) were fixed before the next slice started.
- **Test discipline as a feature:** the "x2 consecutive runs" rule surfaced idempotency bugs that
  a single CI pass would have shipped.
