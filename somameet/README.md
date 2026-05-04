# somameet

Deployment folder for the SomaMeet Sejong demo stack.

- Source app repo: `https://github.com/soma17th-ai31/SomaMeet_Sejong`
- Frontend image: `ghcr.io/soma17th-ai31/somameet_sejong-frontend:${SOMAMEET_IMAGE_TAG}`
- Backend image: `ghcr.io/soma17th-ai31/somameet_sejong-backend:${SOMAMEET_IMAGE_TAG}`
- Caddy publishes `somameet.sejongclass.kr` to the frontend on localhost port `3410`.

## Runtime contract

- `frontend`
  - nginx serves static assets on container port `80`
  - proxies `/api/` to the compose-internal `backend:8410/api/`
  - is the only service published on a localhost host port
- `backend`
  - FastAPI app listening on container port `8410`
  - has no host port
  - connects to the internal Postgres service at `postgres:5432`
- `postgres`
  - uses `postgres:16-alpine`
  - persists data in the `postgres_data` named volume
  - has no host port

## Required env

- `SOMAMEET_IMAGE_TAG`
  - Use `main` for the moving head or another GHCR tag for a pinned rollout.
- `FRONTEND_HOST_PORT`
  - Defaults to `3410`; Caddy must point at the same value.
- `POSTGRES_PASSWORD`
  - Fixed to `somameet-demo-password` by the deployment workflow for this demo.
- `FRONTEND_ORIGIN`
  - Defaults to `https://somameet.sejongclass.kr`.
- `APP_TIMEZONE`
  - Defaults to `Asia/Seoul`.
- `OPENAI_API_KEY`
  - The only SomaMeet app secret required by the deployment workflow.
- `OPENAI_MODEL`
  - Optional; defaults to `gpt-5.4-mini`.

The SomaMeet GHCR packages are public, so the deployment workflow does not perform a remote `docker login`.

## Deployment

```bash
cd somameet
cp .env.example .env
docker compose pull
docker compose up -d
```

Before public traffic works, DNS for `somameet.sejongclass.kr` must point at the deployment server.
