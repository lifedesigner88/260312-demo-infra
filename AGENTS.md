# AGENTS.md

이 파일은 이 레포를 다루는 사람과 에이전트가 같은 맥락을 유지하기 위한 운영 메모다.

## 레포 목적

- 이 레포는 앱 소스를 빌드하는 곳이 아니다.
- 배포 대상 이미지는 GHCR에서 pull 한다.
- 배포 방식은 `GitHub Actions + SSH + Docker Compose`다.
- 앱 레벨의 실제 시크릿은 루트 `.env`의 아래 4개만 관리한다.
  - `SEJONG_SECRET_KEY`
  - `RESEND_API_KEY`
  - `DEMO_DATABASE_URL`
  - `DEMO_SUPABASE_KEY`
- 나머지 데모용 값은 각 서비스 폴더의 `.env.example` 계열 파일에 둔다.

## 서비스 구조

### vue-spring

- 위치: `vue-spring/`
- 구성: `mariadb`, `minio`, `minio-init`, `backend`, `db-seed`, `frontend`
- `db-seed`는 `backend`가 먼저 떠서 기본 데이터가 들어간 뒤 실행되어야 한다.
- `frontend`는 `db-seed` 완료를 기다릴 필요가 없고 `backend`와 병렬로 떠도 된다.
- demo 용도라 단일 `vue-spring/.env.example`에 infra, backend, frontend 값을 함께 둔다.
- 대신 CORS 값은 실제 프론트 도메인 기준으로 맞춰야 한다.

### rr7-fullstack

- 위치: `rr7-fullstack/`
- 구성: 단일 Node 앱 1개
- DB는 외부에 있다.
- 앱 비시크릿 값은 `rr7-fullstack/.env.example`에 둔다.
- 4개 공통 시크릿은 루트 `../.env`에서 읽는다.

### caddy

- 위치: `caddy/`
- 공유 reverse proxy다.
- 라우팅 대상:
  - `vue-spring.huposit.kr` -> `vue-spring frontend`
  - `vue-spring.huposit.kr/api/*` -> `vue-spring backend`
  - `vue-spring-s3.huposit.kr` -> `vue-spring minio api`
  - `rr7-fullstack.sejongclass.kr` -> `rr7-fullstack app`
- Caddy는 host network를 사용하고 localhost에만 바인딩된 각 서비스 포트를 프록시한다.

## 배포 원칙

- Compose에서 `build`를 쓰지 않는다.
- 앱 이미지는 모두 GHCR 이미지 변수로 받는다.
- 서비스별 compose는 localhost 포트에만 publish 하고 외부 공개는 Caddy만 담당한다.
- 실제 서버 비밀값은 루트 `.env` 한 파일로만 유지한다.

## GitHub Actions 시크릿/변수 기준

- 공통 시크릿:
  - `DEPLOY_HOST`
  - `DEPLOY_USER`
  - `DEPLOY_SSH_KEY`
  - 선택: `GHCR_USERNAME`
  - 선택: `GHCR_TOKEN`
- 앱 공통 시크릿:
  - `SEJONG_SECRET_KEY`
  - `RESEND_API_KEY`
  - `DEMO_DATABASE_URL`
  - `DEMO_SUPABASE_KEY`
- 선택 변수:
  - `DEPLOY_BASE_DIR`

## 수정 시 주의

- `vue-spring/compose.yaml`의 seed 순서는 깨지면 안 된다.
- `vue-spring`은 `.env.example -> .env` 복사 후 그 한 파일로 compose와 runtime을 같이 푼다.
- 루트 `.env`는 실제 값이 들어가므로 커밋하지 않는다.
- `caddy/Caddyfile`을 바꿀 때는 서비스 host port와 함께 봐야 한다.
