# BOOMi — Beverage Brand: 3D Web, Generative Video & Social Integration

> A consumer beverage brand's digital presence: an interactive 3D marketing site, a
> generative-video creative workflow, and a programmatic social-publishing integration.

## Problem

Launch a beverage brand online with a premium, memorable web experience and an automatable
social-media channel — built and operated by one engineer.

## Architecture

- **Marketing site** — Next.js + React with **React Three Fiber** for real-time 3D rendering in
  the browser (product/scene visuals, a scroll-driven hero), plus a complete brand-identity system
  (palette, typography, motion language). Served as a production build behind a reverse proxy with
  TLS.
- **Generative video creative** — an image-to-video / text-to-video workflow for cinematic product
  clips. I established a **draft-then-final cost discipline** (iterate at low resolution, render
  finals at high resolution) after measuring how quickly high-res iteration burns budget.
- **Social Business API integration** — a FastAPI wrapper around a social-media **Business API**
  for programmatic content publishing, including access-token lifecycle handling (refresh/rotation).
- **Self-hosted media delivery** — brand media assets served from a dedicated static host behind
  TLS, so the publishing integration can fetch assets over public HTTPS.

## My role

Full-stack build and operations: the 3D/creative front-end, the backend wrapper and social
integration, the generative-video tooling, the brand-identity implementation, and deployment.

## Stack

`Next.js` · `React` · `React Three Fiber` · `TypeScript` · `FastAPI` · `Python` · `nginx` ·
`generative video models` · `static media hosting + TLS`

## Results

- A **live production marketing site** with an interactive 3D hero and a coherent brand system.
- A **working programmatic social-publishing integration** over a Business API, with token
  lifecycle handled.
- A repeatable **generative-video creative workflow** with explicit cost controls.

## Engineering highlights

- **Real-time 3D on the web** with React Three Fiber, tuned for both desktop and mobile.
- **Cost-aware generative media:** measured per-render economics and codified a draft/final
  workflow that keeps creative iteration affordable.
- **Integration resilience:** access-token refresh/rotation so the publishing path keeps working
  without manual re-auth.
