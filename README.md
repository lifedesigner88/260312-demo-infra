# 260312-demo-infra

GitHub Actions + SSH + Docker Compose 방식으로 두 개의 데모 앱과 공유 Caddy를 운영하는 인프라 레포입니다.

이 레포는 앱 이미지를 직접 빌드하지 않습니다. 각 앱 레포에서 빌드된 이미지를 GHCR에서 pull 한 뒤, 서버에서 환경변수를 주입해 실행합니다.

루트 `.env`에는 실제 시크릿 4개만 둡니다.

- `SEJONG_SECRET_KEY`
- `RESEND_API_KEY`
- `DEMO_DATABASE_URL`
- `DEMO_SUPABASE_KEY`

이 파일은 gitignore 대상이고, 추적용 템플릿은 루트 `.env.example`에 둡니다. 서비스별 비시크릿 값이 필요한 경우에만 각 폴더의 `.env.example` 계열 파일에 커밋합니다.

## 구조

- `vue-spring/`
  - MariaDB, MinIO, backend, db-seed, frontend를 띄웁니다.
  - `db-seed`는 backend가 먼저 뜬 뒤 실행되어 admin 계정과 데모 데이터가 보강됩니다.
  - 앱 기본 설정은 이미지에 들어 있어 별도 `.env` 없이도 `compose.yaml`만으로 뜹니다.
  - 외부에서는 frontend만 공개하고 `/api`, `/storage`는 frontend가 내부 프록시합니다.
- `rr7-fullstack/`
  - 외부 DB를 사용하는 단일 Node 앱입니다.
  - 비시크릿 값은 `.env.example`, 공통 시크릿은 루트 `.env`로 분리합니다.
- `caddy/`
  - `vue-spring.huposit.kr`
  - `rr7-fullstack.sejongclass.kr`
  - 위 진입점을 localhost 포트로 라우팅하는 공유 reverse proxy입니다.
- `.github/workflows/`
  - 각 폴더를 SSH로 서버에 동기화하고 `docker compose pull && docker compose up -d`를 실행하는 배포 워크플로가 있습니다.
- `AGENTS.md`
  - 사람과 에이전트가 같은 맥락을 유지하기 위한 운영 메모입니다.

## 배포 원칙

- Compose에서 `build`를 사용하지 않습니다.
- GHCR 이미지 pull 기반으로만 운영합니다.
- 앱 스택은 localhost 포트에만 바인딩하고 외부 공개는 Caddy만 담당합니다.
- `vue-spring`은 앱 기본 설정을 이미지에 두고 compose는 배포 wiring만 유지합니다.
- `rr7-fullstack`는 실제 운영 env를 `.env.example + 루트 .env` 조합으로 구성합니다.

## 참고

- `vue-spring` 배포 설명은 `vue-spring/README.md`
- `rr7-fullstack` 배포 설명은 `rr7-fullstack/README.md`
- `caddy` 배포 설명은 `caddy/README.md`
- 자동화/맥락 문서는 `AGENTS.md`
