---
uid: d72fb58b-0094-43c1-8758-1afbb01fdb31
slug: compose-basics
title: "compose.yaml đầu tiên"
estimatedMinutes: 35
objectives:
  - "Giải thích Docker Compose làm thay bạn những việc gì"
  - "Đọc và viết compose.yaml có một service build từ Dockerfile"
  - "Dùng docker compose up, ps, logs, down"
prerequisites:
  - docker-basics/dockerfile/first-dockerfile
  - docker-basics/volumes-networks/networks
authors: [github:TienNHM]
terms: [Compose, compose.yaml, YAML, service, up, down]
---

# compose.yaml đầu tiên

Chương 4 chạy hai container nói chuyện với nhau đã cần cả chục lệnh, phải gõ đúng thứ tự, đúng từng cờ. Bỏ sót một `--network` là hỏng. **Docker Compose** (*compose* = soạn, sắp xếp, như soạn một bản nhạc cho cả dàn nhạc) giải quyết việc đó.

## 1. Compose làm gì cho bạn

Bản chất: Compose **không làm được gì mà lệnh `docker` không làm được**. Nó đọc một file mô tả, rồi tự gõ thay bạn đúng những lệnh của chương 1–4:

| Bạn viết trong `compose.yaml` | Compose tự làm thay | Bằng tay ở chương trước |
| --- | --- | --- |
| danh sách `services` | tạo **một network chung** cho cả project | `docker network create` |
| tên service (`web`, `db`) | dùng tên đó làm **tên máy** trong network | `--name`, `--network` |
| `build: .` | build image | `docker build -t …` |
| `ports` | mở cổng | `-p` |
| `environment` | đặt biến môi trường | `-e` |
| `volumes` | tạo và gắn volume | `docker volume create`, `-v` |
| `depends_on` | khởi động theo thứ tự | tự nhớ chạy cái nào trước |

Ví von: chương 1–4 bạn là người **tự gọi từng nhạc công** vào chơi. Với Compose, bạn viết **bản tổng phổ**, nhạc trưởng Compose đọc và điều khiển cả dàn.

![Bên trái là file compose.yaml khai báo ba service web, umami, db và volume umami-db. Mũi tên up dẫn sang một network my-site_default do Compose tự tạo, trong đó web mở localhost:8080, umami mở localhost:3000 và gọi db bằng tên db:5432, db không mở cổng và gắn volume umami-db. Mũi tên down quay lại: down xóa container và network nhưng volume vẫn còn, down -v xóa luôn volume.](../../../assets/compose-overview.png)

## 2. YAML trong 1 phút

`compose.yaml` viết bằng **YAML** (đọc là "dem-mồ"), một định dạng file cấu hình. Ba luật đủ dùng:

```yaml
ten: gia-tri            # cặp khóa: giá trị
cha:
  con: gia-tri          # thụt lề 2 dấu cách = nằm trong "cha"
danh-sach:
  - muc-mot             # gạch đầu dòng = một phần tử của danh sách
  - muc-hai
```

**Đọc hiểu:** khóa và giá trị cách nhau bởi `: ` (hai chấm **và** một dấu cách). Thụt lề **bằng dấu cách, không dùng phím Tab**, và mọi dòng cùng cấp phải thụt bằng nhau. Sai thụt lề là lỗi hay gặp nhất. `#` mở đầu chú thích.

## 3. Một service

Ở thư mục `my-site` (cạnh `Dockerfile` của chương 2), tạo file `compose.yaml`:

```yaml
services:
  web:
    build: .
    ports:
      - "8080:80"
```

**Đọc hiểu:**

- `services`: danh sách các thành phần của ứng dụng. Mỗi mục là một **service** (*dịch vụ*). Mỗi service chạy thành một (hoặc nhiều) container.
- `web`: tên service. Cũng là **tên máy** trong network Compose tạo (như `--name db` ở chương 4).
- `build: .`: build image từ `Dockerfile` trong thư mục hiện tại (`.` là build context, như chương 2). Dùng image có sẵn thì ghi `image: nginx:1.30-alpine` thay cho `build`.
- `ports`: danh sách cổng, giống `-p 8080:80`. Luôn để trong **dấu nháy kép**: không có nháy, YAML có thể hiểu nhầm một số dạng `a:b` thành số.

So với gõ tay `docker build -t …` rồi `docker run -d --name … -p 8080:80 …`, file này ngắn hơn và **nằm trong Git**: ai clone repo về cũng chạy được giống bạn.

## 4. Các lệnh

```bash
docker compose up -d
```

**Đọc hiểu:** `up` (*dựng lên*): build image nếu chưa có, tạo network, tạo và chạy mọi service. `-d` chạy nền, như chương 1. Output cho thấy Compose làm từng bước:

```text
 Network my-site_default  Created
 Container my-site-web-1  Started
```

Tên do Compose đặt theo **tên thư mục** (`my-site`, gọi là tên project): network `my-site_default`, container `my-site-web-1` (số `1` vì một service có thể chạy nhiều bản).

Mở <http://localhost:8080>: trang của bạn.

```bash
docker compose ps
docker compose logs web
docker compose logs -f web
```

**Đọc hiểu:** giống `docker ps` và `docker logs` của chương 1, nhưng chỉ cho các service **trong project này**, và gọi bằng **tên service** (`web`) thay vì tên container. `-f` theo dõi liên tục, Ctrl+C để thoát.

```bash
docker compose down
```

**Đọc hiểu:** `down` (*hạ xuống*): dừng và xóa **container và network** của project. Image và volume vẫn giữ.

## 5. Sửa code thì sao?

Sửa `site/index.html` rồi:

```bash
docker compose up -d --build
```

**Đọc hiểu:** `--build` buộc Compose build lại image trước khi chạy, rồi tự thay container cũ bằng container mới. Ba lệnh `rm -f`, `build`, `run` ở chương 2 giờ thành một. Không có `--build`, Compose dùng lại image đã build lần trước và trang **không đổi**.

## 6. `docker compose`, không phải `docker-compose`

Bản cũ là chương trình riêng tên `docker-compose` (có gạch nối), file thường tên `docker-compose.yml`. Bản hiện tại là lệnh con `docker compose` (dấu cách), đi kèm Docker Desktop, đọc được cả hai tên file. Tài liệu cũ trên mạng còn dùng cách viết cũ; ý nghĩa giống nhau.

## 7. Sai lầm thường gặp

| Sai lầm | Hậu quả | Đúng |
| --- | --- | --- |
| Thụt lề bằng Tab, hoặc lệch một dấu cách | `did not find expected key` hoặc `… must be a mapping` | Dấu cách, đều nhau; VS Code hiện dấu chấm khi chọn văn bản |
| Chạy `docker compose` ở thư mục khác | `no configuration file provided: not found` | `cd` vào thư mục có `compose.yaml` |
| Sửa code nhưng quên `--build` | Trang không đổi | `docker compose up -d --build` |
| Nghĩ `down` xóa dữ liệu | | `down` giữ volume (bài sau); chỉ `down -v` mới xóa |

## 8. Ngoài thực tế

Ở công ty, gần như mọi repo có nhiều thành phần đều có `compose.yaml` ở gốc. Ngày đầu đi làm, lập trình viên mới chỉ cần đọc README, chạy `docker compose up`, là có cả hệ thống trên laptop. File này được review trong pull request như code: thêm một service mới (ví dụ Redis) là sửa `compose.yaml`, cả nhóm pull về là có.

Compose chủ yếu dùng trên **máy dev và máy test**. Hệ thống lớn chạy trên nhiều máy chủ thường dùng công cụ khác (như Kubernetes), nhưng ý tưởng vẫn giống: mô tả bằng file, công cụ làm phần còn lại.

## 9. Khi bị lỗi

| Lỗi | Vì sao | Cách sửa |
| --- | --- | --- |
| `no configuration file provided: not found` | Đứng sai thư mục, hoặc file tên khác | `cd my-site`; tên file `compose.yaml` |
| `did not find expected key` (kèm số dòng `L4`) / `must be a mapping` | Sai thụt lề, hoặc thiếu dấu cách sau `:` | Xem đúng dòng được báo, so với mẫu; `docker compose config` kiểm tra file mà không chạy gì |
| `port is already allocated` | Container `my-site` của chương 2 còn chạy, giữ cổng 8080 | `docker rm -f my-site` |
| Lỗi khác, hoặc chưa hiểu vì sao | | Nhắn mentor, công khai hay **nhắn riêng** đều được (tab **Cài đặt** → "Hỏi ở đâu") |

## Tóm tắt

- Compose đọc `compose.yaml` và tự làm những việc bạn đã gõ tay ở chương 1–4.
- YAML: `khóa: giá trị`, thụt lề bằng dấu cách, `-` cho danh sách.
- `up -d` dựng, `ps` xem, `logs` đọc log, `down` gỡ; sửa code thì `up -d --build`.
- Tên service là tên máy trong network của project.
