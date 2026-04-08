# 260312-demo-infra

Infrastructure repository for running demo apps and a shared Caddy reverse proxy via GitHub Actions + SSH + Docker Compose.

This repo does not build app images directly. It pulls images built in each app repo from GHCR, then injects environment variables on the server at runtime.

Only 4 real secrets are stored in the root `.env`:

- `SEJONG_SECRET_KEY`
- `RESEND_API_KEY`
- `DEMO_DATABASE_URL`
- `DEMO_SUPABASE_KEY`

This file is gitignored; the tracking template lives at the root `.env.example`. Per-service non-secret values are committed to `.env.example`-style files in each subfolder only when needed.

## Structure

- `vue-spring/`
  - Runs MariaDB, MinIO, backend, db-seed, and frontend.
  - `db-seed` runs after backend is up and seeds the admin account and demo data.
  - App defaults are baked into the image, so `compose.yaml` alone is enough to start without a separate `.env`.
  - Only the frontend is publicly exposed; `/api` and `/storage` are proxied internally by the frontend.
- `rr7-fullstack/`
  - A single Node app using an external database.
  - Non-secret values are in `.env.example`; shared secrets come from the root `.env`.
- `team-fit/`
  - Frontend, backend, and ai-worker are pulled from the `asm17-team-fit` app repo's GHCR images.
  - Compose, container, and runtime naming use `team-fit`, while the GitHub repo slug remains `asm17-team-fit`.
  - Caddy publishes `asm17.huposit.kr`, routing `/api` to the backend and other traffic to the frontend.
- `team-310/`
  - Frontend, backend, and ai-worker are pulled from the `team-310` app repo's GHCR images.
  - Compose, container, and runtime naming use `team-310`.
  - Caddy publishes `team-310.huposit.kr`, routing `/api` to the backend and other traffic to the frontend.
  - The frontend image must be built with `VITE_API_BASE_URL=/api` for this proxy pattern to work correctly.
- `caddy/`
  - `vue-spring.huposit.kr`
  - `rr7-fullstack.sejongclass.kr`
  - `asm17.huposit.kr`
  - `team-310.huposit.kr`
  - Shared reverse proxy that routes the above domains to localhost ports.
- `.github/workflows/`
  - Deployment workflows that sync each folder to the server via SSH and run `docker compose pull && docker compose up -d`.
- `AGENTS.md`
  - Operational notes to keep humans and agents in the same context.

## Deployment Principles

- No `build` directives in Compose.
- Operate exclusively by pulling GHCR images.
- App stacks bind to localhost ports only; Caddy is the sole public-facing component.
- `vue-spring` keeps app defaults in the image; Compose handles deployment wiring only.
- `rr7-fullstack` assembles its runtime env from `.env.example + root .env`.

## References

- `vue-spring` deployment notes: `vue-spring/README.md`
- `rr7-fullstack` deployment notes: `rr7-fullstack/README.md`
- `team-fit` deployment notes: `team-fit/README.md`
- `team-310` deployment notes: `team-310/README.md`
- `caddy` deployment notes: `caddy/README.md`
- Automation/context documentation: `AGENTS.md`
