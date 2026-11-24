Chạy trang web của bạn cùng Umami và Postgres bằng Docker Compose, theo bài "Trang web + Umami + Postgres". Đánh dấu từng mục khi xong.

Làm trong repo trang web của bài dự án chương 2 (thư mục có `Dockerfile` và `site/`). **Chưa commit** `compose.yaml` của bài này lên GitHub nếu bạn đã đổi mật khẩu trong đó thành mật khẩu thật; chương 6 sẽ chuyển mật khẩu ra `.env` trước khi commit.

## Tự kiểm tra hiểu bài

Mục thứ ba của danh sách: trả lời bằng lời của bạn, "vì sao lần đầu Umami không kết nối được database, và vì sao lần sau lại được?". Nếu log của bạn **không** có lần thất bại (máy nhanh), thử lại từ đầu bằng `docker compose down -v` rồi `docker compose up -d`: lần khởi tạo database đầu tiên là lúc chậm nhất.

<details>
<summary>Gợi ý nếu bị lỗi</summary>

- `umami` `Restarting` mãi: `docker compose logs umami`. Thường là sai tên máy trong `DATABASE_URL` (phải là `db`) hoặc mật khẩu không khớp `POSTGRES_PASSWORD`.
- `password authentication failed`: bạn đã đổi mật khẩu sau khi volume được tạo. `docker compose down -v` rồi `up -d` (mất dữ liệu Umami, không sao vì đang thử).
- Cổng 8080 hoặc 3000 đã bị dùng: đổi số bên trái trong `ports`, ví dụ `"8081:80"`.
- Không thấy lượt xem: kiểm tra đã dán mã theo dõi vào `<head>`, đã `up -d --build`, và mở trang trên chính máy chạy Umami.
- Vẫn kẹt: nhắn mentor, công khai hay **nhắn riêng** đều được.

</details>
