Làm lại hai bài của chương 3 trên máy bạn, rồi áp dụng vào repo trang web của bạn. Đánh dấu từng mục khi xong.

## Việc cần làm

1. Trong thư mục `hello-go`, build cả hai image theo bài "Multi-stage build" và ghi lại kích thước. Số của bạn có thể khác bài học một chút; quan trọng là thấy chênh lệch nhiều lần.
2. Chạy `hello-go:multi` với `-p 8080:8080`, gọi thử bằng trình duyệt hoặc `curl` (Windows: `curl.exe`).
3. Làm thí nghiệm `.env` ở bài ".dockerignore và user không phải root": trước và sau khi có `.dockerignore`.
4. So sánh `whoami` của hai image.
5. Mở repo trang web của bài dự án chương 2, thêm file `.dockerignore`:

   ```text
   .git
   **/.env
   ```

   Build lại và kiểm tra trang vẫn chạy. Commit và push file này.

## Tự kiểm tra hiểu bài

Trả lời được ba câu này bằng lời của bạn là đạt:

- Vì sao `hello-go:multi` nhỏ hơn `hello-go:single` nhiều như vậy?
- Nếu không có `.dockerignore`, file `.env` đi vào image theo đường nào?
- Kẻ xấu chạy được lệnh trong container `hello-go:multi` thì bị chặn những việc gì mà ở `hello-go:single` họ làm được?

<details>
<summary>Gợi ý nếu bị lỗi</summary>

- `"/out/hello": not found`: thiếu `--from=build` trong `COPY`.
- `port is already allocated`: container `hello` cũ còn chạy; `docker rm -f hello`.
- `/src` không có `.env` ngay từ đầu: kiểm tra bạn đã tạo `.env` trong đúng thư mục `hello-go`, và đang build bằng `-f Dockerfile.single`.
- Vẫn kẹt: nhắn mentor, công khai hay **nhắn riêng** đều được.

</details>
