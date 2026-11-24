---
uid: 2e831257-9a9c-41ae-a4d6-8d80624240f2
slug: volumes
title: "Volume: dữ liệu sống sót khi xóa container"
estimatedMinutes: 45
objectives:
  - "Giải thích vì sao dữ liệu trong container mất khi xóa container"
  - "Chạy Postgres với named volume và kiểm tra dữ liệu còn sau khi tạo lại container"
  - "Phân biệt named volume và bind mount"
prerequisites:
  - docker-basics/dockerfile/layers-cache
authors: [github:TienNHM]
terms: [volume, mount, anonymous volume, named volume, inspect, bind mount, persist]
---

# Volume: dữ liệu sống sót khi xóa container

Trang web tĩnh xóa container rồi chạy lại vẫn y nguyên, vì mọi thứ nằm trong image. **Database** thì khác: dữ liệu sinh ra **trong lúc chạy** (người dùng đăng ký, bài viết mới…). Chương 5 dùng Postgres để lưu số liệu truy cập; mất dữ liệu mỗi lần tạo lại container là không chấp nhận được.

Bài này dùng **Postgres** (PostgreSQL), một database phổ biến, qua image `postgres:18-alpine`.

## 1. Vì sao dữ liệu mất: nhớ lại lớp ghi

Chương 2: mỗi container có một **lớp ghi** mỏng nằm trên các layer chỉ đọc của image. Với đa số image, file container tạo ra nằm ở lớp ghi đó, và `docker rm` bỏ lớp ghi đi thì **dữ liệu đi theo**.

Image database như `postgres` cẩn thận hơn một chút: nó **tự khai báo** thư mục dữ liệu là chỗ cần volume (mục 4 giải thích). Kết quả cuối với bạn vẫn giống nhau: chạy lại bằng một container mới thì **không thấy dữ liệu cũ**. Thí nghiệm dưới đây cho thấy điều đó, rồi mục 4 chỉ ra dữ liệu cũ thật ra đang nằm ở đâu.

## 2. Thử: không có volume

```bash
docker run -d --name db -e POSTGRES_PASSWORD=matkhau postgres:18-alpine
```

**Đọc hiểu:**

- Image `postgres` **bắt buộc** có biến môi trường `POSTGRES_PASSWORD` (chương 1, `-e`): mật khẩu của tài khoản quản trị `postgres`. Thiếu thì container dừng ngay, `docker logs db` báo `Database is uninitialized and superuser password is not specified`.
- Không có `-p`: ta chỉ làm việc với Postgres qua `docker exec`, không cần mở cổng ra máy.

Chờ khoảng 5 giây cho Postgres khởi động, rồi tạo bảng và thêm một dòng:

```bash
docker exec db psql -U postgres -c "CREATE TABLE notes (id serial PRIMARY KEY, body text); INSERT INTO notes (body) VALUES ('Xin chao Docker');"
```

```text
CREATE TABLE
INSERT 0 1
```

**Đọc hiểu:** `psql` là công cụ dòng lệnh của Postgres, có sẵn trong image. `-U postgres` = *user* postgres. `-c "…"` = *command*, chạy câu SQL trong ngoặc: tạo bảng `notes` có hai cột (`id` tự tăng, `body` là chữ), rồi thêm một dòng. Không cần biết SQL, chỉ cần biết đây là "ghi dữ liệu".

Xóa container, chạy lại y hệt, tìm dữ liệu:

```bash
docker rm -f db
docker run -d --name db -e POSTGRES_PASSWORD=matkhau postgres:18-alpine
docker exec db psql -U postgres -c "SELECT * FROM notes;"
```

```text
ERROR:  relation "notes" does not exist
```

**Mất**, ít nhất là với container mới. (Gặp `the database system is starting up` thì chờ vài giây rồi chạy lại lệnh `exec`.)

Container `db` thứ hai là container **mới hoàn toàn**: nó không có gì của container cũ. Bảng `notes` vẫn đang nằm đâu đó trên máy, nhưng container mới không được nối tới chỗ đó. Mục 4 giải thích, và chỉ cách dọn chỗ dữ liệu bị bỏ rơi này.

## 3. Volume: ổ đĩa rời cắm vào container

**Volume** là một vùng lưu trữ do Docker quản lý, nằm **ngoài** container. Ví von: một **ổ cứng rời** (USB). Bạn **mount** (*gắn*, như cắm USB) nó vào một thư mục trong container; mọi thứ ghi vào thư mục đó thật ra ghi lên ổ rời. Vứt container đi, ổ rời vẫn còn, cắm vào container mới là có lại dữ liệu.

![Sơ đồ volume như ổ cứng rời. Bên trái, container db lần 1 bị gạch chéo sau docker rm -f db, container và lớp ghi của nó mất. Ở giữa là volume pgdata chứa bảng notes, nằm ngoài container nên vẫn còn. Bên phải, container db lần 2 mới tạo được mount lại volume pgdata vào /var/lib/postgresql bằng -v pgdata:/var/lib/postgresql, và SELECT trên bảng notes thấy dữ liệu còn nguyên. Chú thích cuối hình: không gắn volume có tên thì mỗi container mới nhận một chỗ chứa mới, trống, và dữ liệu cũ bị bỏ rơi.](../../../assets/volume-drive.png)

```mermaid
flowchart LR
  C1["Container db (lần 1)<br/>đã xóa"] -. "đã ghi vào" .-> V[("Volume pgdata<br/>nằm ngoài container")]
  C2["Container db (lần 2)<br/>mới tạo"] -- "mount vào<br/>/var/lib/postgresql" --> V
```

| | Lớp ghi của container | Volume |
| --- | --- | --- |
| Nằm ở đâu | Trong container | Ngoài container, Docker quản lý |
| Xóa container thì | **Mất** | **Còn** |
| Dùng cho | File tạm, log | Dữ liệu cần **persist** (*tồn tại lâu dài*): database, file người dùng tải lên |

## 4. Volume vô danh: dữ liệu cũ đã đi đâu?

Image `postgres` có dòng `VOLUME /var/lib/postgresql` trong Dockerfile của nó. Dòng đó nghĩa là: "thư mục này phải nằm trên volume". Nếu bạn không gắn volume nào, Docker **tự tạo** một volume không tên, gọi là **anonymous volume** (*volume vô danh*), cho **mỗi** container mới.

```bash
docker volume ls
```

```text
DRIVER    VOLUME NAME
local     7954c3c8bfa2efa650e954230a8c9b10fbebf1107939c470aef07dc1e977ef08
local     503764a4379c26beb6135c7562f6c2a71eefb661f5f5923b7d1395bcb41eca92
```

**Đọc hiểu:** hai volume có tên là một chuỗi mã dài (mã của bạn sẽ khác, và có thể có thêm volume của dự án khác): một của container `db` đầu tiên (có bảng `notes`, nay bị bỏ rơi), một của container `db` đang chạy (trống). Vì vậy container mới không thấy dữ liệu: nó được cắm một ổ rời **mới, trống**, còn ổ cũ nằm lăn lóc và vẫn chiếm ổ đĩa.

Bài học: không đặt tên thì không ai tìm lại được volume đó. Dọn chúng:

```bash
docker rm -f -v db
docker volume ls
docker volume rm <chuỗi-mã-của-volume-còn-lại>
```

**Đọc hiểu:** `docker rm -v` (*volumes*) xóa container **kèm** các volume vô danh của nó. Volume vô danh của container đầu tiên (đã bị `rm -f` mà không có `-v`) thì phải xóa tay bằng `docker volume rm` với đúng chuỗi mã `docker volume ls` in ra. Chỉ xóa volume bạn chắc chắn là của bài thực hành này.

| | Lớp ghi của container | Volume vô danh | Named volume |
| --- | --- | --- | --- |
| Tạo khi | luôn có | image khai báo `VOLUME` mà bạn không gắn gì | bạn đặt tên: `-v pgdata:…` |
| Tên | | chuỗi mã ngẫu nhiên | tên bạn chọn |
| Container mới dùng lại được | không | không (mỗi container một cái mới) | **có** |
| Xóa container thì | mất | còn, bị bỏ rơi (trừ khi `rm -v`) | còn |

## 5. Làm lại với named volume

```bash
docker volume create pgdata
docker run -d --name db -e POSTGRES_PASSWORD=matkhau -v pgdata:/var/lib/postgresql postgres:18-alpine
```

**Đọc hiểu:**

- `docker volume create pgdata`: tạo một **named volume** (*volume có tên*) tên `pgdata`. (Bỏ bước này cũng được: `-v` tự tạo nếu chưa có. Tách ra cho rõ.)
- `-v pgdata:/var/lib/postgresql`: `-v` = *volume*, mount volume `pgdata` vào thư mục `/var/lib/postgresql` trong container. Dạng chung: `-v tên-volume:đường-dẫn-trong-container`.
- Vì sao thư mục đó? Đó là nơi Postgres 18 lưu dữ liệu. Mỗi image ghi rõ thư mục dữ liệu trên trang Docker Hub của nó. (Postgres 17 trở về trước dùng `/var/lib/postgresql/data`.)

Làm lại thí nghiệm (chờ 5 giây sau lệnh `run` rồi mới `exec`):

```bash
docker exec db psql -U postgres -c "CREATE TABLE notes (id serial PRIMARY KEY, body text); INSERT INTO notes (body) VALUES ('Xin chao Docker');"
docker rm -f db
docker run -d --name db -e POSTGRES_PASSWORD=matkhau -v pgdata:/var/lib/postgresql postgres:18-alpine
docker exec db psql -U postgres -c "SELECT * FROM notes;"
```

```text
 id |      body
----+-----------------
  1 | Xin chao Docker
(1 row)
```

**Còn nguyên.** Container `db` thứ hai là container mới hoàn toàn, nhưng nó cắm lại đúng ổ `pgdata`.

## 6. Quản lý volume

```bash
docker volume ls
docker volume inspect pgdata
```

**Đọc hiểu:** `ls` liệt kê volume. Lệnh inspect (*xem xét kỹ*) in mọi chi tiết của một đối tượng Docker (volume, container, image) dạng JSON, trong đó `Mountpoint` là chỗ Docker cất dữ liệu bên trong máy Linux của Docker Desktop. Bạn không cần đụng vào đó.

Xóa volume (**chưa làm bây giờ**, bài sau dùng tiếp):

```bash
docker volume rm pgdata
```

`docker volume rm` xóa **vĩnh viễn**. Volume đang được container dùng thì không xóa được; xóa container trước. Với database thật, sao lưu trước (ví dụ bằng `pg_dump`).

**Một điều hay gây lỗi:** Postgres chỉ đặt mật khẩu **lần đầu** nó khởi tạo một volume trống. Volume đã có dữ liệu thì Postgres bỏ qua `POSTGRES_PASSWORD`. Đổi mật khẩu trong lệnh `run` mà dùng lại volume cũ thì mật khẩu **vẫn là mật khẩu cũ**. Chương 6 gặp lại lỗi này.

## 7. Bind mount: gắn thẳng thư mục trên máy bạn

`-v` còn nhận **đường dẫn trên máy bạn** thay cho tên volume. Kiểu này gọi là **bind mount** (*gắn trói*). Ví dụ, đứng trong thư mục `my-site` của chương 2:

```bash
docker run -d --name live -p 8082:80 -v ./site:/usr/share/nginx/html nginx:1.30-alpine
```

**Đọc hiểu:** thư mục `site/` trên máy bạn được gắn thẳng vào thư mục nginx phục vụ. Mở <http://localhost:8082>: trang của bạn, **không cần build image**. Sửa `site/index.html` rồi tải lại trình duyệt: thấy ngay. Dọn: `docker rm -f live`.

| | Named volume | Bind mount |
| --- | --- | --- |
| Viết | `-v pgdata:/đường/dẫn` | `-v ./site:/đường/dẫn` (bắt đầu bằng `./`, `/` hoặc `C:\`) |
| Dữ liệu ở đâu | Docker quản lý, bạn không cần biết | Thư mục bạn chọn trên máy |
| Hợp cho | Database, dữ liệu của ứng dụng | Đang phát triển, muốn sửa file thấy ngay |
| Rủi ro | Ít | Phụ thuộc đường dẫn và quyền file trên Windows/macOS; container ghi được thẳng vào máy bạn |

Track này dùng **named volume cho database**, và bind mount chỉ để thử.

## 8. Sai lầm thường gặp

| Sai lầm | Hậu quả | Đúng |
| --- | --- | --- |
| Chạy database không có volume | Mất hết dữ liệu khi tạo lại container | Luôn `-v tên:thư-mục-dữ-liệu` |
| Gắn volume sai thư mục (ví dụ `/data`) | Postgres vẫn ghi vào chỗ cũ, volume rỗng, dữ liệu vẫn mất | Xem trang image để biết thư mục dữ liệu |
| Nghĩ `docker rm` xóa luôn volume | Volume còn, chiếm ổ đĩa | `docker volume ls` và dọn khi chắc chắn |
| Đổi `POSTGRES_PASSWORD` với volume cũ | Sai mật khẩu | Dùng mật khẩu cũ, hoặc xóa volume (mất dữ liệu) |

## 9. Ngoài thực tế

Ở công ty, database thật thường **không** chạy trong container trên laptop, mà là dịch vụ có người quản lý (máy chủ riêng, hoặc dịch vụ cloud), có sao lưu tự động. Container Postgres với volume là cách **mỗi lập trình viên có một database riêng trên máy** để code và test, giống hệt phiên bản trên máy chủ.

Quy tắc chung của nhóm: **không lưu dữ liệu quan trọng trong lớp ghi của container**. Container được coi là đồ dùng xong vứt (chương 1); dữ liệu phải nằm ở volume hoặc ở dịch vụ bên ngoài.

## 10. Khi bị lỗi

| Lỗi | Vì sao | Cách sửa |
| --- | --- | --- |
| `Database is uninitialized and superuser password is not specified` | Thiếu `-e POSTGRES_PASSWORD=…` | Thêm vào lệnh `run` |
| `the database system is starting up` | Postgres chưa khởi động xong | Chờ vài giây, chạy lại |
| `relation "notes" does not exist` sau khi đã có volume | Gắn sai thư mục, hoặc gõ sai tên volume (tạo ra volume mới rỗng) | So với lệnh trong bài; `docker volume ls` |
| `volume is in use` khi xóa volume | Còn container dùng nó | `docker rm -f db` trước |
| Lỗi khác, hoặc chưa hiểu vì sao | | Nhắn mentor, công khai hay **nhắn riêng** đều được (tab **Cài đặt** → "Hỏi ở đâu") |

## Tóm tắt

- Dữ liệu ghi trong container nằm ở lớp ghi, mất khi xóa container.
- Image khai báo `VOLUME` (như `postgres`) mà bạn không gắn gì thì mỗi container nhận một volume vô danh mới, trống; volume cũ bị bỏ rơi. `docker rm -v` xóa kèm nó.
- Volume là "ổ rời" ngoài container: `-v tên-volume:/thư-mục`. Postgres 18 dùng `/var/lib/postgresql`.
- Bind mount gắn thư mục trên máy bạn: tiện khi đang phát triển.
- Mật khẩu Postgres chỉ được đặt lần đầu khởi tạo volume.

Chưa dọn vội: bài sau dùng tiếp container `db` và volume `pgdata`.
