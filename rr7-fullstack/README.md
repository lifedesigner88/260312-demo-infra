# rr7-fullstack

`rr7-fullstack.sejongclass.kr` 요청을 받는 단일 Node 앱 배포 폴더입니다.

- 앱은 1개만 띄웁니다.
- DB는 외부를 사용합니다.
- 비시크릿 값은 `.env.example`에 있습니다.
- 공통 시크릿 4개는 루트 `../.env`에서 읽습니다.
- 외부 공개와 TLS는 shared `caddy/`가 담당합니다.

## 포함 파일

- `compose.yaml`: Node 앱을 localhost 포트에만 바인딩하는 compose
- `.env.example`: 새 환경용 템플릿

## 배포 절차

```bash
cd rr7-fullstack
cp .env.example .env
docker compose pull
docker compose up -d
```

`.env`에서 아래 값은 실제 값으로 바꿔야 합니다.

- `RR7_FULLSTACK_IMAGE`
- 필요하면 `BASE_URL`
- 앱 포트가 다르면 `PORT`와 `HOST_PORT`
- 비시크릿 데모값

상태 확인:

```bash
docker compose ps
docker compose logs -f app
```
