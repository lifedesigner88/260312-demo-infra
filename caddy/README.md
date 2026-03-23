# caddy

Shared reverse proxy deployment folder.

- `vue-spring.huposit.kr` -> `vue-spring frontend`
- `rr7-fullstack.sejongclass.kr` -> `rr7-fullstack app`

## Deployment

```bash
cd caddy
cp .env.example .env
docker compose pull
docker compose up -d
```

Caddy uses host networking, so each app stack must already be running on its localhost port before starting Caddy.
