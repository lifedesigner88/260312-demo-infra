# team-310

Deployment folder for the `team-310` demo stack.

- Source app repo: `https://github.com/lifedesigner88/team-310`
- GHCR images:
- `ghcr.io/lifedesigner88/team-310-frontend:latest`
- `ghcr.io/lifedesigner88/team-310-backend:latest`
- `ghcr.io/lifedesigner88/team-310-ai-worker:latest`
- Compose, network, and container naming use `team-310`.
- Caddy publishes `huposit.kr`, routing `/api` to the backend and other traffic to the frontend.

## Important

This deployment expects current upstream GHCR images built after `d6cac06`, where:

- the frontend defaults `VITE_API_BASE_URL` to `/api`
- auth routes are served under `/api/auth/*`
- chat routes are served under `/api/chat/*`

Because of that, the shared Caddy route must proxy `team-310` `/api/*` requests to the backend without stripping the `/api` prefix.

The ai-worker image fix landed upstream in `66d4517`, so redeploying against refreshed GHCR `latest` images should pick up the corrected Docker entrypoint without any infra-side build changes.

If the `team-310` GHCR packages are private, the deployment workflow also needs `TEAM_310_GHCR_USERNAME` and `TEAM_310_GHCR_TOKEN` secrets so the remote server can run `docker login ghcr.io` before `docker compose pull`.

## Deployment

```bash
cd team-310
cp .env.example .env
docker compose pull
docker compose up -d
```
