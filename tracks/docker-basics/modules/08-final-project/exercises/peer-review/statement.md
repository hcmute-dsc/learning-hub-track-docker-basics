Review pull request dự án cuối của một bạn cùng track, theo bài "Nộp qua pull request và review bài của bạn khác". Đánh dấu từng mục khi xong.

## Tìm người để review

Mentor sẽ ghép cặp trong kênh Discord của track. Chưa được ghép thì tự hỏi trong kênh: "Ai cần người review dự án cuối không?". Không tìm được ai, hoặc ngại: **nhắn riêng** mentor, mentor sẽ ghép giúp.

## Cách làm

Làm đúng 5 bước ở mục 2 của bài học. Bước nào bị kẹt (README thiếu, lệnh lỗi) thì **đó chính là góp ý**: ghi lại lệnh bạn chạy và thông báo lỗi bạn thấy.

Viết nhận xét trực tiếp trên pull request của bạn mình (tab **Files changed** để bình luận từng dòng, hoặc **Conversation** để nhận xét chung).

## Tự kiểm tra

Đọc lại nhận xét của bạn trước khi gửi, hỏi ba câu:

- Người nhận có biết **sửa ở đâu** không?
- Có **lý do** hoặc thông báo lỗi kèm theo không?
- Có ít nhất **một điểm làm tốt** không?

<details>
<summary>Gợi ý nếu bị lỗi</summary>

- Cổng trùng với stack của chính bạn đang chạy: `docker compose down` stack của bạn trước, hoặc đặt `WEB_PORT` khác trong `.env`.
- Máy chậm khi chạy hai stack: chỉ chạy một stack mỗi lúc.
- Không chắc góp ý của mình đúng: hỏi mentor trước khi gửi, công khai hay riêng đều được.

</details>
