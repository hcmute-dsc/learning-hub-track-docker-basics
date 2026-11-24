Cài Docker theo tab **Cài đặt** của track, rồi làm lại các bước trong bài "Docker để làm gì?" và "Chạy container đầu tiên và mở cổng". Đánh dấu từng mục khi xong.

Bài chỉ dùng các lệnh đã học: `docker run` (với `--rm`, `-d`, `--name`, `-p`), `docker pull`, `docker ps` và `docker rm -f`.

## Gửi ảnh hello-world

Chụp màn hình terminal có dòng `Hello from Docker!` và gửi vào kênh Discord của track. Mentor dùng danh sách này để biết ai cần giúp cài đặt. Ngại đăng công khai thì **nhắn riêng** ảnh cho mentor (tab **Cài đặt** → "Hỏi ở đâu"), cũng được tính.

## Câu tự giải thích

Mục cuối của danh sách: hãy nói (hoặc viết ra giấy) vì sao `-p 80:8080` không mở được trang nginx. Không cần nộp, nhưng nếu chưa nói được thì đọc lại mục 3 của bài "Chạy container đầu tiên và mở cổng".

<details>
<summary>Gợi ý nếu bị lỗi</summary>

- `WSL 2 installation is incomplete` hoặc `Virtualization support not detected`: tab **Cài đặt** → "Khi bị lỗi".
- `Cannot connect to the Docker daemon`: mở Docker Desktop và chờ **Engine running**.
- `port is already allocated`: cổng đang bị dùng; `docker ps` để tìm, `docker rm -f <tên>`, hoặc đổi cổng bên trái.
- Máy không cài được: dùng GitHub Codespaces (tab **Cài đặt**, mục 5). Chụp màn hình terminal trong Codespaces cũng được.
- Vẫn kẹt: nhắn mentor, công khai hay riêng đều được.

</details>
