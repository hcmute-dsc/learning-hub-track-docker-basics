## Khi review bài này

- Clone repo và chạy đúng các lệnh trong README, không sửa gì. Không chạy được thì chưa đạt tiêu chí đầu tiên, dù Dockerfile trông đúng.
- Sau khi chạy, mở `http://localhost:8080/Dockerfile`, `/README.md`, `/.git/config`. Cả ba phải là 404. Nếu thành viên dùng `COPY . /usr/share/nginx/html` thì các file này lộ ra: chỉ ra bằng chính URL đó, rồi gợi ý tách `site/`.
- Xem `FROM`: `nginx` hay `nginx:latest` là chưa đạt tiêu chí image nền.
- Repo portfolio có sẵn cấu trúc khác (ví dụ trang ở `docs/` hay `public/`) thì `COPY docs/ /usr/share/nginx/html/` là đúng; đừng bắt đổi tên thư mục.
- Phần giải thích `docker history`: chỉ cần thành viên chỉ ra được dòng `COPY` là của mình và các dòng dưới là của nginx. Không cần dùng thuật ngữ chính xác.

## Lỗi hay gặp

- File tên `Dockerfile.txt` do Windows ẩn đuôi file.
- Link CSS hay ảnh viết hoa/thường lệch tên file: Windows không phân biệt, nhưng Linux trong container thì có, nên trang mất CSS hay ảnh trong container. Gợi ý `docker exec <tên> ls /usr/share/nginx/html` để so tên.
- Có `CMD` hoặc `RUN nginx` thừa làm container thoát ngay. Gợi ý xem `docker logs`.

## Góp ý nên tránh

- Đòi multi-stage build, `.dockerignore` hay user không phải root: đó là chương 3, ngoài phạm vi bài này.
- Chấm thiết kế giao diện trang web. Bài này chấm việc đóng gói.
