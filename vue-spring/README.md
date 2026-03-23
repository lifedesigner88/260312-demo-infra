# vue-spring

`vue-spring/` is a deployment bundle that runs the app repo's infra handoff directly from this infrastructure repo.

- App defaults are baked into the image, so no separate env file is needed for basic startup.
- Core runtime container names: `kip-mariadb`, `kip-minio`, `kip-backend`, `kip-frontend`.
- Only two application images are used: `ghcr.io/lifedesigner88/kip-backend:demo` and `ghcr.io/lifedesigner88/kip-frontend:demo`.
- `db-seed` is a one-shot service that injects `seed-rich.sql` after the backend's default seed completes.
- `db-seed` automatically skips if a `seed-rich v1` marker already exists.
- `minio-init` and `db-seed` exiting with `Exited (0)` after auto-run is expected and normal.
- External access is handled by the shared `caddy/`; this stack binds to localhost ports only.

## Required Files

- `compose.yaml`
- `seed-rich.sql`

## Recommended Usage

From the repo root:

```bash
docker compose -f vue-spring/compose.yaml pull
docker compose -f vue-spring/compose.yaml up -d
docker compose -f vue-spring/compose.yaml ps -a
```

Expected healthy state:

- `kip-backend` — `Up`
- `kip-mariadb` — `Up`
- `kip-minio` — `Up`
- `minio-init` — `Exited (0)`
- `db-seed` — `Exited (0)`
- `kip-frontend` — `Up`

`kip-backend` and `kip-frontend` use `pull_policy: always`, so the latest `demo` tag is checked on every `pull` or `up`.

## Domain

- Service: `https://vue-spring.huposit.kr`
- Only the frontend is exposed externally.
- The frontend internally proxies `/api` -> `kip-backend:8080` and `/storage` -> `kip-minio:9000`.

## Re-seeding Demo Data

```bash
docker compose -f vue-spring/compose.yaml rm -f db-seed
docker compose -f vue-spring/compose.yaml up db-seed
```

## Notes

- No separate `.env` file or environment variable injection is required.
- Running compose alone brings up `kip-mariadb`, `kip-minio`, `kip-backend`, `kip-frontend`, and the one-shot init containers.
