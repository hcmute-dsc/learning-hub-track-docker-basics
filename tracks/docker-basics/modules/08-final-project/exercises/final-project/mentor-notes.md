## Khi review bài này

Làm theo thứ tự, ghi điểm cho từng tiêu chí của rubric:

1. **Đọc README như người lạ.** Chỉ dùng những gì README ghi. Thiếu bước tạo `.env` hay sai lệnh là trừ ở tiêu chí README, kể cả khi bạn tự đoán ra được.
2. **Clone mới và chạy:** `cp .env.example .env`, `docker compose up -d --wait`, `docker compose ps`. Mọi service phải healthy. Mở các trang trong README.
3. **Kiểm tra image tự build:** `docker compose exec <service> whoami` không phải `root`. Đọc Dockerfile: có multi-stage khi ứng dụng cần build; stage cuối không chứa bộ công cụ build.
4. **Bí mật:** đọc `compose.yaml`, `.env.example` tìm chuỗi giống mật khẩu thật; `git log --all --oneline -- .env` không in gì; `docker compose exec <service> ls -a <thư-mục-app>` không thấy `.env`.
5. **GHCR:** `docker logout ghcr.io` rồi `docker pull` image trong README, chạy thử với cổng ghi trong README.
6. **PR:** có review của bạn khác với nhận xét cụ thể, và thành viên đã trả lời hoặc sửa.
7. Xong thì `docker compose down -v` và xóa image đã pull.

## Lỗi hay gặp

- Thiếu file trong Git (thường là CSS, ảnh, `go.sum`): chạy trên máy thành viên được, bản clone thì không. Gợi ý tự kiểm tra trên bản clone mới.
- `hello` hoặc service tự build mãi `unhealthy`: healthcheck gọi cổng bên ngoài (8081) thay vì cổng bên trong (8080). Gợi ý `docker inspect --format "{{json .State.Health}}" <container>`.
- Package GHCR còn private nên `pull` báo `denied`.
- Mã theo dõi Umami trỏ `localhost:3000`: đúng như bài học, không trừ điểm.
- Lộ bí mật trong lịch sử: góp ý **riêng tư**, hướng dẫn đổi mọi mật khẩu, thu hồi token. Không nêu giá trị bí mật trong nhận xét công khai.

## Hướng B (ứng dụng riêng)

- Chấm theo cùng rubric. Không chấm chất lượng code ứng dụng, chỉ chấm việc đóng gói và vận hành.
- Ngôn ngữ thông dịch (Python, Node) không bắt buộc multi-stage nếu không có bước build; nhưng phải có user không phải root và `.dockerignore` (ví dụ bỏ `node_modules`, `.venv`).

## Góp ý nên tránh

- Đòi deploy lên máy chủ thật, Kubernetes, reverse proxy, HTTPS hay CI: ngoài phạm vi track.
- Chấm thiết kế giao diện.

## Khi nhận xét

Nêu ít nhất một điểm làm tốt và một điểm nên sửa, gắn với tiêu chí cụ thể. Bài chưa đạt: ghi rõ tiêu chí nào và cách kiểm tra lại, để thành viên sửa và nộp lại.
