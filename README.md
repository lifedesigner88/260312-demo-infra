# demo-infra

AWS Lightsail Ubuntu 24.04 LTS 4GB 1대에서 `Docker Compose + Caddy + MinIO`로 면접용 데모 2개를 운영하기 위한 배포 전용 레포입니다.

## 구성

- `vue-spring.sejongclass.kr` -> `vue` 컨테이너
- `vue-spring.sejongclass.kr/api/*` -> `spring` 컨테이너
- `vue-spring-file.sejongclass.kr` -> `minio` 컨테이너
- `rr7-fullstack.sejongclass.kr` -> `rr7-fullstack` 컨테이너
- 앱 이미지는 `vue`, `spring`, `rr7-fullstack` 3개를 각 앱 레포에서 빌드 후 GHCR public image로 가져옵니다.
- 이 레포는 `build`보다 `docker compose pull && docker compose up -d` 운영에 맞춰져 있습니다.

## 파일 역할

- `docker-compose.yml`: Caddy, `vue`, `spring`, `rr7-fullstack`, MinIO를 한 번에 띄우는 실행 정의
- `Caddyfile`: 3개 도메인과 `vue-spring`의 `/api/*` 경로를 각 컨테이너로 reverse proxy 하는 라우팅 규칙
- `.env.example`: 도메인, GHCR 이미지명 3개, 앱 내부 포트, 외부 MinIO 볼륨명, MinIO 비밀값 예시
- `.gitignore`: 실제 `.env`와 로그 파일이 커밋되지 않도록 제외
- `README.md`: Lightsail 서버 배포 절차와 운영 메모

## 사전 준비

1. Lightsail 인스턴스에 Docker Engine과 Compose plugin을 설치합니다.
2. Lightsail 방화벽에서 `80`, `443` 포트를 엽니다.
3. Cloudflare DNS를 `DNS only`(회색 구름)로 두고 아래 3개 레코드를 모두 서버 공인 IP로 연결합니다.
4. GHCR public image 주소와 MinIO 비밀값을 준비합니다.
5. MinIO 데이터용 Docker volume은 이 레포 밖에서 별도로 준비합니다.

## 배포 절차

```bash
git clone <this-repo>
cd 260312-demo-infra
cp .env.example .env
```

`.env`에서 아래 값만 먼저 채우면 됩니다.

- `VUE_IMAGE`
- `SPRING_IMAGE`
- `RR7_FULLSTACK_IMAGE`
- `MINIO_DATA_VOLUME`
- `MINIO_ROOT_PASSWORD`
- 필요하면 `VUE_PORT`, `SPRING_PORT`, `RR7_FULLSTACK_PORT`

그 다음 서버에서 실행합니다.

```bash
git pull
docker compose pull
docker compose up -d
```

상태 확인:

```bash
docker compose ps
docker compose logs -f caddy
```

## 운영 메모

- Caddy가 `80/443`에서 인증서 발급과 reverse proxy를 처리합니다.
- Cloudflare는 반드시 `DNS only`여야 Caddy가 직접 인증서를 발급받기 쉽습니다.
- `vue-spring.sejongclass.kr`은 기본적으로 `vue`로 가고, `/api/*`만 `spring`으로 프록시됩니다.
- `vue-spring-file.sejongclass.kr`은 현재 MinIO의 S3 API endpoint로 연결됩니다.
- `MINIO_DATA_VOLUME`은 외부 Docker volume 이름입니다. 이 compose가 새로 만들지 않고 기존 볼륨을 참조합니다.
- MinIO 콘솔(`9001`)은 외부 도메인에 연결하지 않았습니다. 면접용 2주 운영 기준으로 단순성을 우선한 구성입니다.
- 앱 이미지 내부 포트가 다르면 `.env`의 `VUE_PORT`, `SPRING_PORT`, `RR7_FULLSTACK_PORT`만 수정하면 됩니다.
