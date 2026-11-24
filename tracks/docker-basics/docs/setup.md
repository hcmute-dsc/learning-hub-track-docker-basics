Làm các bước dưới đây **một lần**, trước bài học đầu tiên. Mất khoảng 30–60 phút, lâu nhất là lần đầu trên Windows. Track không cần biết GitHub Actions; chỉ cần Git cơ bản (clone, commit, push).

## 1. Chuẩn bị chung

**Terminal.** Trên Windows, track này dùng **PowerShell** (mở Start, gõ "PowerShell"). macOS và Linux dùng **Terminal**. Lệnh nào khác nhau giữa hai loại, bài học sẽ ghi cả hai.

Trên Windows, **đừng dùng Git Bash** để gõ lệnh Docker. Git Bash tự đổi các đường dẫn bắt đầu bằng `/` (như `/usr/share/nginx/html`) thành đường dẫn Windows (`C:/Program Files/Git/usr/share/...`), làm lệnh báo `No such file or directory`.

**Git.**

- **Windows**: tải [Git for Windows](https://git-scm.com/download/win), giữ các lựa chọn mặc định.
- **macOS**: chạy `xcode-select --install` trong Terminal.
- **Ubuntu**: `sudo apt install git`.

Rồi khai báo tên và email. Git ghi chúng vào mỗi commit; thiếu thì commit báo lỗi `Author identity unknown`. Dùng email của tài khoản GitHub:

```bash
git config --global user.name "Tên của bạn"
git config --global user.email "email-github-cua-ban@example.com"
```

**Tài khoản GitHub** ([đăng ký](https://github.com/signup)): để nộp bài và, ở chương 7, đưa image lên GitHub Container Registry. Tham gia **Discord của CLB** để hỏi bài.

**Trình soạn thảo:** [VS Code](https://code.visualstudio.com/) hoặc bất kỳ trình nào bạn quen.

**Mấy lệnh khác nhau trên Windows** (gặp trong bài thì quay lại đây):

| Việc | macOS / Linux | Windows PowerShell |
| --- | --- | --- |
| gọi HTTP | `curl` | `curl.exe` (gõ `curl` sẽ ra một lệnh khác của PowerShell) |
| chép file | `cp a b` | `Copy-Item a b` (hoặc `cp a b`, PowerShell hiểu `cp`) |
| đặt biến môi trường | `export X=1` | `$env:X = "1"` |
| tìm chương trình giữ cổng 8080 | `lsof -i :8080` | `netstat -ano \| findstr :8080` |

## 2. Cài Docker

**Máy cần:** RAM 8 GB trở lên (4 GB chạy được nhưng chậm ở chương 5–6, khi chạy ba container cùng lúc), ổ đĩa trống ít nhất 10 GB.

### Windows: Docker Desktop với WSL2

Cần Windows 10 bản 22H2 trở lên, hoặc Windows 11, 64-bit.

1. **Bật ảo hóa (virtualization).** Mở Task Manager → **Performance** → **CPU**, xem dòng **Virtualization**. Nếu là **Disabled**, khởi động lại máy, vào BIOS/UEFI (thường bấm `F2`, `F10`, `Del` hoặc `Esc` lúc vừa bật máy, tuỳ hãng) và bật mục **Intel Virtualization Technology (VT-x)** hoặc **AMD SVM Mode**. Lưu, khởi động lại.
2. **Cài WSL2** (*Windows Subsystem for Linux*, hệ thống con Linux cho Windows). Mở PowerShell bằng **Run as administrator**:

   ```powershell
   wsl --install
   ```

   Khởi động lại máy khi được hỏi. Máy đã có WSL thì chạy `wsl --update`.
3. Tải [Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/), cài với lựa chọn **Use WSL 2 instead of Hyper-V**.
4. Mở Docker Desktop, chờ tới khi góc dưới bên trái báo **Engine running**.

Vì sao cần WSL2: container cần **kernel Linux** (phần lõi của hệ điều hành Linux). Windows không có, nên Docker Desktop chạy một máy Linux nhỏ trong WSL2. Bài 1 của chương 1 giải thích kỹ hơn.

### macOS: Docker Desktop

Tải [Docker Desktop for Mac](https://docs.docker.com/desktop/setup/install/mac-install/), chọn đúng bản cho chip của máy (menu Apple → **About This Mac**):

- **Chip Apple** (M1, M2, M3…): bản **Apple Silicon**.
- **Chip Intel**: bản **Intel**.

Kéo Docker vào Applications, mở nó, chờ biểu tượng cá voi trên thanh menu báo đang chạy.

### Linux (Ubuntu): Docker Engine

Cài Docker Engine theo [hướng dẫn chính thức cho Ubuntu](https://docs.docker.com/engine/install/ubuntu/), gồm các gói `docker-ce` và `docker-compose-plugin`. Rồi cho tài khoản của bạn dùng Docker mà không cần `sudo`:

```bash
sudo usermod -aG docker $USER
```

**Đọc hiểu:** `usermod` = *user modify* (sửa tài khoản); `-aG docker` = *append to Group*, thêm bạn vào nhóm `docker`; `$USER` là tên tài khoản đang đăng nhập. Đăng xuất rồi đăng nhập lại để có hiệu lực.

## 3. Tự kiểm tra

Chạy **một lệnh** này trong PowerShell hoặc Terminal:

```bash
docker run --rm hello-world
```

Kết quả mong đợi (lần đầu có thêm vài dòng `Pulling…` ở trên):

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

**Đọc hiểu:** `docker run` tạo và chạy một container từ image `hello-world`; máy chưa có image thì Docker tự tải về. `--rm` (*remove*) tự xóa container khi nó chạy xong. Thấy dòng `Hello from Docker!` nghĩa là cả chuỗi đều chạy: lệnh `docker`, chương trình nền của Docker, mạng để tải image, và kernel Linux.

Kiểm tra thêm Compose (dùng từ chương 5):

```bash
docker compose version
```

Thấy `Docker Compose version v2.x` trở lên là được.

## 4. Chip Apple Silicon (arm64)

Mac chip Apple dùng kiến trúc **arm64**, khác với đa số máy Windows (**amd64**). Mọi image trong track (`nginx`, `postgres`, `alpine`, `golang`, `umami`) đều có bản arm64, nên chạy bình thường.

Gặp image cũ chỉ có bản amd64, Docker báo `no matching manifest for linux/arm64/v8`. Khi đó thêm `--platform linux/amd64` (với Compose: `platform: linux/amd64` trong service). Image chạy qua giả lập, chậm hơn nhưng dùng được.

## 5. Máy yếu hoặc không cài được

Không cài được Docker (máy 4 GB RAM, máy trường không cho bật ảo hóa, Windows quá cũ) thì dùng Docker trên trình duyệt:

- **GitHub Codespaces** (khuyên dùng): tạo một repo trên GitHub → nút **Code** → tab **Codespaces** → **Create codespace on main**. Terminal trong Codespaces có sẵn `docker` và `docker compose`. Tài khoản cá nhân có một lượng giờ miễn phí mỗi tháng; **dừng codespace khi không dùng** (trang [github.com/codespaces](https://github.com/codespaces)) để khỏi tốn giờ. Cổng như 8080 hiện ở tab **Ports**, bấm vào để mở trang.
- **[Play with Docker](https://labs.play-with-docker.com/)**: đăng nhập bằng tài khoản Docker Hub, mỗi phiên dùng được vài giờ rồi tự xóa. Hợp để thử lệnh ở chương 1–4, không hợp để lưu bài.

Máy chạy được nhưng chậm: tắt các stack không dùng (`docker compose down`), và trên Windows có thể giới hạn RAM cho WSL bằng file `%UserProfile%\.wslconfig`:

```ini
[wsl2]
memory=3GB
```

Chạy `wsl --shutdown` trong PowerShell rồi mở lại Docker Desktop để áp dụng.

## 6. Khi bị lỗi

| Lỗi | Cách sửa |
| --- | --- |
| `Virtualization support not detected` hoặc Docker Desktop không khởi động | Bật ảo hóa trong BIOS (mục 2, bước 1) |
| `WSL 2 installation is incomplete` hoặc `WSL update failed` | PowerShell admin: `wsl --update`, rồi khởi động lại máy |
| `Cannot connect to the Docker daemon` / `error during connect` | Docker chưa chạy. Mở Docker Desktop và chờ **Engine running**. Ubuntu: `sudo systemctl start docker` |
| `permission denied while trying to connect to the Docker daemon socket` (Ubuntu) | Chưa vào nhóm `docker`: chạy `usermod` ở mục 2 rồi đăng nhập lại |
| `Bind for 0.0.0.0:8080 failed: port is already allocated` | Cổng 8080 đang bị dùng. `docker ps` tìm container đang giữ cổng rồi `docker rm -f <tên>`; không phải container thì tìm chương trình giữ cổng (bảng ở mục 1), hoặc đổi cổng, ví dụ `-p 8081:80` |
| `No such file or directory` với đường dẫn lạ như `C:/Program Files/Git/usr/...` | Đang dùng Git Bash. Chuyển sang PowerShell |
| Lỗi khác, hoặc chưa hiểu vì sao | Hỏi mentor theo mục 7 bên dưới, công khai hay **nhắn riêng** đều được |

## 7. Hỏi ở đâu

**Không có câu hỏi nào là ngớ ngẩn.** Mentor viết track này vì muốn bạn hỏi. Câu bạn đang thắc mắc, chắc chắn có người khác cũng đang thắc mắc.

- **Hỏi chung:** kênh Discord của track trên server CLB HCMUTE DSC. Hỏi ở đây thì người khác cũng học được từ câu trả lời.
- **Hỏi riêng:** ngại hỏi trước mọi người thì nhắn riêng (DM) cho mentor của track trên Discord: **@TienNHM**. Hỏi riêng cũng hoàn toàn bình thường.
- **Sau khi nộp bài:** trả lời ngay trong mục "Trao đổi với mentor" dưới nhận xét của bài.

Mọi người trong CLB theo [Quy tắc ứng xử](https://github.com/hcmute-dsc/.github/blob/master/CODE_OF_CONDUCT.md): không ai được chê câu hỏi của bạn, và câu hỏi riêng được giữ kín. Bị đối xử không đúng thì nhắn riêng Ban chủ nhiệm trên Discord.

Khi hỏi, gửi kèm: bạn đang ở bài nào, lệnh đã chạy, kết quả `docker --version`, và ảnh chụp lỗi. Track này có mật khẩu (file `.env`, chương 6) và token GitHub (chương 7): **che phần đó** trước khi gửi ảnh hay dán log. Lỡ dán token rồi thì thu hồi nó ngay (chương 7 chỉ cách), không sao cả.
