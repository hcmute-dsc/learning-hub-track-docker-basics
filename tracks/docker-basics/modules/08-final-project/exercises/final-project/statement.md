Đóng gói một ứng dụng nhiều service bằng Docker Compose, đưa image của bạn lên GHCR, và nộp qua pull request. Làm theo bài "Hướng dẫn dự án cuối" (chọn hướng A hoặc B) và bài "Nộp qua pull request".

## Yêu cầu

Đủ mọi dòng trong bảng ở mục 1 của bài "Hướng dẫn dự án cuối":

- ít nhất 3 service trong `compose.yaml`, có một database;
- ít nhất một image tự build: multi-stage nếu cần build, user không phải root, có `.dockerignore`;
- database có named volume, không mở `ports`;
- bí mật trong `.env` ngoài Git, có `.env.example`;
- healthcheck cho mọi service, `depends_on` với `condition: service_healthy`;
- mọi image có tag cụ thể;
- image tự build có trên GHCR, tag semver, package public;
- README đủ 7 mục.

## Nộp bài

1. Làm trên nhánh `final-project`, mở pull request vào `main` của repo bạn.
2. Nhờ một bạn cùng track review (bài tập "Review dự án của bạn khác"), và trả lời các góp ý.
3. Dán **link pull request** vào ô nộp bài.

Trong mô tả PR, thêm mục **Bằng chứng**:

- output `docker compose ps` trên bản clone mới, mọi service `healthy` (dán dạng text);
- link package trên GHCR;
- link tới bình luận review của bạn khác.

**Không dán token, mật khẩu hay nội dung `.env`** ở bất kỳ đâu. Ngại để repo public, chưa tìm được người review, hay lỡ commit bí mật: **nhắn riêng** mentor, sẽ có cách khác.

## Tiêu chí chấm

Mentor clone repo, làm đúng theo README, rồi chấm theo rubric 100 điểm:

| Tiêu chí | Điểm | Đạt khi |
| --- | --- | --- |
| Chạy được | 25 | Clone mới, tạo `.env` từ mẫu, `up -d --wait` là mọi service healthy |
| Dockerfile | 20 | Multi-stage khi cần, user không phải root, `.dockerignore`, tag cụ thể |
| Compose | 15 | Healthcheck, `service_healthy`, named volume, database không mở cổng |
| Bí mật | 15 | Không có bí mật trong Git, lịch sử, hay image |
| Registry | 10 | Image public trên GHCR, tag semver, pull được khi chưa đăng nhập |
| README | 10 | Người lạ làm theo được, có sơ đồ và mục gỡ lỗi |
| Quy trình nhóm | 5 | Nộp bằng PR, có review của bạn khác, đã phản hồi |
