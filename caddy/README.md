# caddy

공유 reverse proxy 배포 폴더입니다.

- `vue-spring.huposit.kr` -> `vue-spring frontend`
- `vue-spring.huposit.kr/api/*` -> `vue-spring backend`
- `vue-spring-s3.huposit.kr` -> `vue-spring minio api`
- `rr7-fullstack.sejongclass.kr` -> `rr7-fullstack app`

## 배포 절차

```bash
cd caddy
cp .env.example .env
docker compose pull
docker compose up -d
```

Caddy는 host network를 사용하므로, 각 앱 stack이 localhost 포트에 먼저 떠 있어야 합니다.
