# vue-spring

`vue-spring.huposit.kr`용 demo 앱 배포 폴더입니다.

- 로컬 빌드 없이 GHCR 이미지를 pull 해서 실행합니다.
- `mariadb`, `minio`, `backend`, `db-seed`, `frontend`를 Compose로 관리합니다.
- `db-seed`는 `backend`가 먼저 뜬 뒤 실행되어 admin 계정과 데모 데이터가 보강됩니다.
- `frontend`는 `db-seed` 완료를 기다리지 않고 함께 뜹니다.
- 외부 공개는 shared `caddy/`가 담당하고, 이 스택은 localhost 포트에만 바인딩됩니다.

## 파일

- `compose.yaml`: GHCR 이미지 pull 기준 실행 정의
- `.env.example`: compose, backend, frontend 값을 한 파일에서 관리하는 예시값
- `seed-rich.sql`: base seed 뒤에 추가로 넣는 데모 데이터

## 배포 절차

```bash
cd vue-spring
cp .env.example .env
docker compose --env-file .env pull
docker compose --env-file .env up -d
```

확인:

```bash
docker compose --env-file .env ps
docker compose --env-file .env logs -f backend
docker compose --env-file .env logs -f db-seed
```

`db-seed`가 `Exited (0)`이고 `backend`, `frontend`가 `Up`이면 정상입니다.
