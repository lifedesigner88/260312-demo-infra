# team-fit

Deployment folder for the `team-fit` demo stack.

- Source app repo: `https://github.com/lifedesigner88/asm17-team-fit`
- GHCR images:
- `ghcr.io/lifedesigner88/team-fit-frontend:latest`
- `ghcr.io/lifedesigner88/team-fit-backend:latest`
- `ghcr.io/lifedesigner88/team-fit-ai-worker:latest`
- Compose, network, and container naming use `team-fit`.
- The GitHub repository slug stays `asm17-team-fit`.

## Deployment

```bash
cd team-fit
cp .env.example .env
docker compose pull
docker compose up -d
```
