Đưa image trang web của bạn lên GHCR và chạy nó ở một nơi không có mã nguồn. Đánh dấu từng mục khi xong.

## Các bước

1. Làm theo bài "Push image lên GHCR": tạo PAT, `docker login ghcr.io`, `docker tag`, `docker push` bản `1.0.0`.
2. Sửa một chữ trong trang web (sửa lỗi nhỏ), build lại, đặt tag theo semver (bài "Phiên bản, digest và pull"), push kèm `latest`.
3. Kiểm tra image không có `.env`: `docker run --rm ghcr.io/<tên-github>/my-site:<tag> ls -a /usr/share/nginx/html`. Rồi đổi package sang **public**.
4. `docker logout ghcr.io`, rồi pull và chạy ở một trong ba nơi: máy khác, Codespaces/Play with Docker, hoặc chính máy bạn sau khi xóa các image `ghcr.io/…`.
5. Thêm vào README mục **Chạy từ GHCR** với lệnh `docker pull` và `docker run`, và link tới package.

## An toàn với token

- Không dán token vào bài nộp, README, Discord, ảnh chụp màn hình.
- Chụp màn hình terminal để hỏi mentor? Kiểm tra trong ảnh không có token. `Login Succeeded` thì không lộ gì.
- Lỡ lộ: thu hồi ngay trên GitHub (Settings → Developer settings → Personal access tokens), tạo token mới. Nhắn riêng mentor nếu cần giúp, không ai trách bạn.

## Tự kiểm tra hiểu bài

Trả lời được bằng lời của bạn:

- Bản sửa ở bước 2 bạn đặt tag gì, vì sao không phải số khác?
- Sau bước 2, tag `latest` và tag `1.0.0` trỏ vào image nào?
- Vì sao ở bước 4 cần `docker logout` trước khi pull?

<details>
<summary>Gợi ý nếu bị lỗi</summary>

- `repository name must be lowercase`: viết thường tên GitHub trong tên image.
- `denied: permission_denied` khi push: token thiếu `write:packages`; tạo token mới.
- `denied` khi pull sau khi logout: package còn private; đổi visibility.
- `no matching manifest for linux/arm64/v8`: xem tab **Cài đặt**, mục 4.
- Vẫn kẹt: nhắn mentor, công khai hay **nhắn riêng** đều được. Che token trước khi gửi.

</details>
