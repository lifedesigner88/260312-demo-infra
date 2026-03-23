# rr7-fullstack

Deployment folder for the single Node app serving `rr7-fullstack.sejongclass.kr`.

- Runs a single app container.
- Uses an external database.
- Non-secret values are in `.env.example`.
- The 4 shared secrets are read from the root `../.env`.
- Public exposure and TLS are handled by the shared `caddy/`.

## Included Files

- `compose.yaml`: Compose file that binds the Node app to a localhost port only.
- `.env.example`: Template for new environments.

## Deployment

```bash
cd rr7-fullstack
cp .env.example .env
docker compose pull
docker compose up -d
```

Replace the following values in `.env` with real values:

- `RR7_FULLSTACK_IMAGE` — default is `ghcr.io/lifedesigner88/250818-sejongclass-node:latest`
- `BASE_URL` — if needed
- `PORT` and `HOST_PORT` — if the app port differs
- Non-secret demo values as required

Check status:

```bash
docker compose ps
docker compose logs -f app
```
