## Khi review bài này

Làm theo thứ tự, ghi chú cho từng tiêu chí của rubric:

1. **Đọc README như người lạ.** Chỉ dùng những gì README ghi. Thiếu bước tạo `.env` hay sai lệnh là trừ ở tiêu chí README, kể cả khi bạn tự đoán ra được.
2. **Clone mới và chạy:** `cp .env.example .env`, `docker compose up -d --wait`, `docker compose ps`. Cả ba service phải healthy. Mở `localhost:8080`, `localhost:3000`. Thử thêm `WEB_PORT=8081` vào `.env`, `up -d --wait`, mở `localhost:8081`. Xong thì `docker compose down -v`.
3. **Lịch sử `.env`:** trong thư mục đã clone, `git log --all --oneline -- .env` phải không in gì. Nếu có, hướng dẫn thành viên đổi mọi mật khẩu từng nằm trong đó; xóa file ở commit mới không đủ. Góp ý **riêng tư** (DM), không nêu mật khẩu trong nhận xét công khai.
4. **Đọc `compose.yaml`:** tìm chuỗi giống mật khẩu, `depends_on` có `condition: service_healthy`, `db` không có `ports`, image không dùng `latest`.
5. **Mục Gỡ lỗi:** chỉ cần lỗi thật, mô tả đủ ba phần (triệu chứng, cách tìm, cách sửa). Không chấm "lỗi có khó không".

## Lỗi hay gặp

- Healthcheck tự viết gọi sai cổng hoặc dùng lệnh không có trong image, nên service không bao giờ healthy và `up --wait` báo `is unhealthy`. Gợi ý `docker inspect --format "{{json .State.Health}}" <container>`.
- `.env.example` chứa đúng mật khẩu thành viên dùng thật. Nhắc đó cũng là lộ mật khẩu.
- `.env` tạo bằng `echo >` trong Windows PowerShell 5.1 (mã hóa UTF-16), Compose báo `unexpected character`. Chỉ cần tạo lại bằng VS Code.
- Mã theo dõi Umami trỏ `localhost:3000`: đúng như bài học, không trừ điểm.

## Góp ý nên tránh

- Đòi đưa lên máy chủ thật, dùng Kubernetes, reverse proxy hay HTTPS: ngoài phạm vi track.
- Đòi multi-stage cho trang tĩnh: không cần.
