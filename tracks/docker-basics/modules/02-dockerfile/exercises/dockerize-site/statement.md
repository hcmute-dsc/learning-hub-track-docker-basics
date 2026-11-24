Đóng gói trang web của bạn (portfolio, hoặc bộ khởi đầu `site/` ở bài "Dockerfile đầu tiên") thành một image Docker, và viết README để người khác làm lại được.

Bài chỉ cần những gì chương 1–2 đã dạy: `FROM`, `COPY`, `EXPOSE`, `docker build`, `docker run`, `docker tag`, `docker image ls`, `docker history`.

## Yêu cầu

1. Repo GitHub **public** có cấu trúc:

   ```text
   my-site/
   ├── site/          # index.html, style.css, ảnh…
   ├── Dockerfile
   └── README.md
   ```

2. `Dockerfile` dùng image nền có **tag cụ thể** (ví dụ `nginx:1.30-alpine`), chỉ chép thư mục `site/` vào `/usr/share/nginx/html/`.
3. Build và chạy được bằng đúng các lệnh ghi trong README; mở `http://localhost:8080` thấy trang đủ nội dung và CSS.
4. Có image tag phiên bản `my-site:1.0.0` (bằng `docker build -t` hai lần, hoặc `docker tag`).
5. Trang `http://localhost:8080/Dockerfile` và `http://localhost:8080/README.md` trả về **404**.

## README cần có mục "Chạy bằng Docker"

- lệnh `docker build` và `docker run`;
- output của `docker image ls my-site` (dán dạng text, trong khối code);
- output của `docker history my-site:1.0.0`, kèm một câu của bạn: dòng nào là layer của bạn, dòng nào đến từ image nền.

Không bắt buộc ảnh chụp. Nếu thêm ảnh, kiểm tra ảnh không lộ thông tin cá nhân (email, tên tài khoản khác).

## Nộp bài

Dán link repo (hoặc link pull request thêm Dockerfile vào repo portfolio của bạn).

Ngại để repo public? Nhắn riêng mentor để thống nhất cách nộp khác. Kẹt ở đâu cũng nhắn mentor, công khai hay riêng đều được.

## Tiêu chí chấm

Mentor clone repo, làm đúng theo README, rồi chấm theo rubric:

| Tiêu chí | Đạt khi |
| --- | --- |
| Build và chạy được (3) | Làm theo README là thấy trang ở cổng 8080, có CSS |
| Chỉ chép trang web (2) | `/Dockerfile`, `/README.md` trả về 404 |
| Image nền và tag (1) | `FROM` có tag cụ thể; có `my-site:1.0.0` |
| README (2) | Có lệnh, output `docker image ls` và `docker history`, giải thích layer bằng lời của bạn |
