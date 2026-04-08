# team-310

Deployment folder for the `team-310` demo stack.

- Source app repo: `https://github.com/lifedesigner88/team-310`
- GHCR images:
- `ghcr.io/lifedesigner88/team-310-frontend:latest`
- `ghcr.io/lifedesigner88/team-310-backend:latest`
- `ghcr.io/lifedesigner88/team-310-ai-worker:latest`
- Compose, network, and container naming use `team-310`.
- Caddy publishes `team-310.huposit.kr`, routing `/api` to the backend and other traffic to the frontend.

## Important

This deployment assumes the frontend image was built with `VITE_API_BASE_URL=/api`.

The upstream app repo currently defines `VITE_API_BASE_URL=http://localhost:8310` as the frontend Dockerfile default, and its GHCR publish workflow does not override that build argument. If the current `ghcr.io/lifedesigner88/team-310-frontend:latest` image was published from that default workflow, browser API requests will still target localhost and the deployed site will not work correctly until the app repo republishes the frontend image with `/api` baked in.

If the `team-310` GHCR packages are private, the deployment workflow also needs `TEAM_310_GHCR_USERNAME` and `TEAM_310_GHCR_TOKEN` secrets so the remote server can run `docker login ghcr.io` before `docker compose pull`.

## Deployment

```bash
cd team-310
cp .env.example .env
docker compose pull
docker compose up -d
```
