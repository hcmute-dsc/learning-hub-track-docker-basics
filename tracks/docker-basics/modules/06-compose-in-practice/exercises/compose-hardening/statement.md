Hoàn thiện repo trang web của bạn để **người khác clone về là chạy được** cả trang web, Umami và Postgres, không cần hỏi bạn câu nào, và không lộ mật khẩu nào.

Bài dùng những gì chương 2–6 đã dạy. Làm tiếp trên repo của bài dự án chương 2.

## Yêu cầu

1. Repo có:

   ```text
   my-site/
   ├── site/
   ├── Dockerfile
   ├── .dockerignore      # có .git và .env
   ├── compose.yaml       # không có mật khẩu
   ├── .env.example       # tên biến, giá trị giả
   ├── .gitignore         # có .env
   └── README.md
   ```

2. `compose.yaml` chạy ba service `web`, `umami`, `db` như bài "Healthcheck":
   - mọi image có tag cụ thể;
   - mật khẩu lấy từ `.env` bằng `${...}` và `env_file`;
   - healthcheck cho cả ba service; `umami` dùng `depends_on` với `condition: service_healthy`;
   - `db` có named volume, không có `ports`;
   - cổng web viết `"${WEB_PORT:-8080}:80"`.
3. `.env` **chưa từng** được commit (kể cả ở commit cũ).
4. `docker compose up -d --wait` trên một bản **clone mới** chạy xong với cả ba service healthy.

## README cần có

- **Chạy trên máy:** từ `git clone` tới mở `localhost:8080` và `localhost:3000`, có bước `cp .env.example .env` (ghi cả lệnh PowerShell `Copy-Item`), và nhắc đổi mật khẩu admin của Umami.
- **Đổi cổng:** cách dùng `WEB_PORT` khi 8080 bị chiếm.
- **Gỡ lỗi:** kể **một** lỗi bạn đã thật sự gặp ở chương 5–6: triệu chứng (dán vài dòng log, **đã che mật khẩu**), bạn tìm ra nguyên nhân bằng lệnh gì, sửa thế nào.

## Tự kiểm tra trước khi nộp

Làm trên một thư mục **mới clone**, để chắc chắn không phụ thuộc file chỉ có trên máy bạn:

```bash
git clone https://github.com/<tài-khoản>/<repo>.git kiem-tra
cd kiem-tra
cp .env.example .env
docker compose up -d --wait
docker compose ps
git log --all --oneline -- .env
docker compose down -v
```

Lệnh `git log --all --oneline -- .env` phải **không in gì**: nghĩa là chưa có commit nào từng chứa `.env`. Nếu có, nhắn mentor để được hướng dẫn: cần đổi mọi mật khẩu từng nằm trong đó.

## Nộp bài

Dán link repo GitHub public (hoặc link pull request). Ngại để repo public, hoặc lỡ commit mật khẩu và chưa biết xử lý: **nhắn riêng** mentor, không sao cả.

## Tiêu chí chấm

| Tiêu chí | Đạt khi |
| --- | --- |
| Chạy được (3) | Clone mới, tạo `.env` từ mẫu, `up -d --wait` là cả ba healthy |
| Bí mật (3) | `compose.yaml` không có mật khẩu; `.env` trong `.gitignore` và `.dockerignore`; lịch sử Git không có `.env` |
| Healthcheck, thứ tự, volume (2) | Đủ healthcheck, `condition: service_healthy`, named volume cho `db` |
| Tag và cổng (1) | Tag cụ thể; `WEB_PORT=8081` trong `.env` là đổi được cổng |
| README (2) | Người lạ làm theo được; mục Gỡ lỗi là lỗi thật, có cách tìm và cách sửa |
