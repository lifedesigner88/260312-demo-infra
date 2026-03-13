# vue-spring

`vue-spring/`은 앱 레포의 infra handoff를 이 인프라 레포에서 그대로 운영할 수 있게 정리한 배포 번들입니다.

- 앱 기본 설정은 이미지에 들어 있어 기본 실행에 별도 env 파일이 필요 없습니다.
- 런타임 핵심 컨테이너 이름은 `kip-mariadb`, `kip-minio`, `kip-backend`, `kip-frontend`입니다.
- 애플리케이션 이미지는 `ghcr.io/lifedesigner88/kip-backend:demo`, `ghcr.io/lifedesigner88/kip-frontend:demo` 두 개만 사용합니다.
- `db-seed`는 backend 기본 시드가 끝난 뒤 `seed-rich.sql`을 한 번 더 주입하는 one-shot 서비스입니다.
- `db-seed`는 `seed-rich v1` 흔적이 이미 있으면 자동으로 skip 합니다.
- `minio-init`, `db-seed`는 자동 실행 후 `Exited (0)` 상태가 정상입니다.
- 외부 공개는 shared `caddy/`가 담당하고, 이 스택은 localhost 포트에만 바인딩됩니다.

## 필수 파일

- `compose.yaml`
- `seed-rich.sql`

## 권장 사용 순서

레포 루트에서:

```bash
docker compose -f vue-spring/compose.yaml pull
docker compose -f vue-spring/compose.yaml up -d
docker compose -f vue-spring/compose.yaml ps -a
```

- `kip-backend`가 `Up`
- `kip-mariadb`가 `Up`
- `kip-minio`가 `Up`
- `minio-init`가 `Exited (0)`
- `db-seed`가 `Exited (0)`
- `kip-frontend`가 `Up`

위 상태면 정상입니다.

`kip-backend`, `kip-frontend`는 `pull_policy: always`라서 `pull` 또는 `up` 시 최신 `demo` 태그를 먼저 확인합니다.

## 도메인 기준

- 서비스: `https://vue-spring.huposit.kr`
- 외부에서는 frontend 하나만 보면 됩니다.
- frontend가 내부적으로 `/api` -> `kip-backend:8080`, `/storage` -> `kip-minio:9000`로 프록시합니다.

## 더미데이터만 다시 넣는 경우

```bash
docker compose -f vue-spring/compose.yaml rm -f db-seed
docker compose -f vue-spring/compose.yaml up db-seed
```

## 참고

- 별도 `.env` 파일이나 환경변수 주입이 필요하지 않습니다.
- compose 실행만으로 `kip-mariadb`, `kip-minio`, `kip-backend`, `kip-frontend`와 one-shot 초기화 컨테이너가 함께 올라옵니다.
