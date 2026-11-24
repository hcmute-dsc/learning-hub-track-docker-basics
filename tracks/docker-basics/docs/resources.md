## Tài liệu chính thức của Docker

- [Docker docs: Get started](https://docs.docker.com/get-started/): nhập môn chính thức, có phần giải thích image, container, registry.
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/): mọi lệnh trong Dockerfile (chương 2–3).
- [Docker build cache](https://docs.docker.com/build/cache/): layer và cache, kỹ hơn chương 2.
- [Multi-stage builds](https://docs.docker.com/build/building/multi-stage/) và [Build context, .dockerignore](https://docs.docker.com/build/concepts/context/): chương 3.
- [Volumes](https://docs.docker.com/engine/storage/volumes/) và [Networking](https://docs.docker.com/engine/network/): chương 4.
- [Compose file reference](https://docs.docker.com/reference/compose-file/): mọi khóa trong `compose.yaml` (chương 5–6).
- [Startup order trong Compose](https://docs.docker.com/compose/how-tos/startup-order/): `depends_on` và `condition: service_healthy` (chương 6).
- [Environment variables trong Compose](https://docs.docker.com/compose/how-tos/environment-variables/): `.env`, `env_file`, `${...}` (chương 6).

## Image dùng trong track

Trang mỗi image có danh sách tag, biến môi trường và thư mục dữ liệu:

- [nginx](https://hub.docker.com/_/nginx), [postgres](https://hub.docker.com/_/postgres), [alpine](https://hub.docker.com/_/alpine), [golang](https://hub.docker.com/_/golang).
- [Umami docs](https://umami.is/docs) và [Umami trên GitHub](https://github.com/umami-software/umami): cài đặt, mã theo dõi, các bản phát hành.

## Registry

- [Working with the Container registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry): GHCR, đăng nhập, quyền và visibility (chương 7).
- [Managing your personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens): tạo và thu hồi PAT.
- [Docker Hub quickstart](https://docs.docker.com/docker-hub/quickstart/): đưa image lên Docker Hub.
- [Semantic Versioning](https://semver.org/lang/vi/): quy ước đánh số phiên bản, có bản tiếng Việt.

## Học tiếp

- [Play with Docker](https://labs.play-with-docker.com/): thử lệnh Docker trên trình duyệt.
- Muốn để máy tự build và đẩy image mỗi lần push code: học track **GitHub Actions và Docker** (`actions-docker`) trên Learning Hub, chương 7 làm đúng việc đó.
