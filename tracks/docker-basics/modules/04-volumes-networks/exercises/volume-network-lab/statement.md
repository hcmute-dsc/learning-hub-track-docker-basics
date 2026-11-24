Làm lại các thí nghiệm của hai bài "Volume" và "Network" trên máy bạn, đúng thứ tự trong bài. Đánh dấu từng mục khi xong.

Mục tiêu không phải gõ lại lệnh, mà là **thấy tận mắt**: dữ liệu mất, dữ liệu còn, tên được tìm thấy, tên không được tìm thấy. Mỗi lần thấy kết quả, tự hỏi: "lớp ghi hay volume? cùng network hay không?".

## Tự kiểm tra hiểu bài

Vẽ ra giấy (hoặc nói thành lời) hai sơ đồ, không nhìn bài:

1. Container `db` lần 1, container `db` lần 2, và volume `pgdata`: mũi tên nào nối với cái gì?
2. Network `lab` có `db` và `psql`; một container ở ngoài; máy của bạn. Ai gọi được ai, qua tên gì, có cần `-p` không?

So lại với sơ đồ trong bài. Vẽ đúng là bạn đã sẵn sàng cho Compose ở chương 5.

<details>
<summary>Gợi ý nếu bị lỗi</summary>

- `the database system is starting up`: chờ vài giây rồi chạy lại lệnh `exec`.
- `relation "notes" does not exist` dù đã có volume: kiểm tra đường dẫn `/var/lib/postgresql` và tên volume `pgdata` trong cả hai lệnh `run`.
- `could not translate host name "db"`: container `psql` thiếu `--network lab`.
- `volume is in use` khi dọn: xóa container `db` trước.
- Vẫn kẹt: nhắn mentor, công khai hay **nhắn riêng** đều được.

</details>
