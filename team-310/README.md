# team-310

Deployment folder for the `team-310` demo stack.

- Source app repo: `https://github.com/lifedesigner88/team-310`
- Frontend image: `ghcr.io/lifedesigner88/team-310-frontend:${TEAM_310_IMAGE_TAG}`
- Backend image: `ghcr.io/lifedesigner88/team-310-backend:${TEAM_310_IMAGE_TAG}`
- AI worker image: `ghcr.io/lifedesigner88/team-310-ai-worker:${TEAM_310_IMAGE_TAG}`
- Compose, network, and container naming use `team-310`.
- Caddy publishes `huposit.kr`, routing `/api` and `/api/*` to the backend without stripping the prefix and routing everything else to the frontend.

## Runtime contract

- `frontend`
  - nginx serves static assets on container port `80`
  - same-origin API calls go to `/api`
- `backend`
  - listens on container port `8310`
  - is exposed only on localhost and should be reached publicly through Caddy `/api` and `/api/*`
  - bootstraps the PostgreSQL schema on startup
- `ai-worker`
  - has no public port
  - must share the same `DATABASE_URL` as the backend
  - needs the same Supabase Storage settings as the backend
  - needs `OPENAI_API_KEY` for the encounter demo worker flow
  - can also use `ANTHROPIC_API_KEY` for Anthropic chat models if you want that path enabled

## Required env

- `TEAM_310_IMAGE_TAG`
  - Use `main` for the moving head or `sha-<shortsha>` for a pinned rollout.
- `DATABASE_URL`
  - Shared by backend and ai-worker.
- `JWT_SECRET_KEY`
  - Must be at least 32 bytes.
- `RESEND_API_KEY`
- `RESEND_FROM`
- `FRONTEND_APP_URL`
- `SUPABASE_URL`
- `SUPABASE_SECRET_KEY`
- `SUPABASE_STORAGE_BUCKET`
  - Defaults to `encounter-audio`.
- `OPENAI_API_KEY`
  - Required only by `ai-worker`.
- `ANTHROPIC_API_KEY`
  - Optional for Anthropic chat models in `ai-worker`.
- `TEAM310_RESET_DB_ON_BOOTSTRAP`
  - Keep `false` in production.

`LOCAL_STORAGE_ROOT` is intentionally not configured in this deployment bundle, so production uses Supabase Storage instead of filesystem fallback.

If the `team-310` GHCR packages are private, the deployment workflow can also use `TEAM_310_GHCR_USERNAME` and `TEAM_310_GHCR_TOKEN` before `docker compose pull`.

## Deployment

```bash
cd team-310
cp .env.example .env
docker compose pull
docker compose up -d
```
