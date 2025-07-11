

---

# ✅ Ngày 18: Tạo Script Rollback Khi Deploy Lỗi (Dùng VPS)

## 🎯 Mục tiêu

* Viết shell script `rollback.sh` để khôi phục phiên bản ứng dụng **trước khi deploy**
* Lưu trữ thông tin version: `current_version`, `last_version`
* Sử dụng trong môi trường VPS triển khai Laravel qua Docker

---

## 🧱 Môi trường mẫu trên VPS

Giả định VPS của bạn đã triển khai Laravel như sau:

| Thành phần        | Giá trị                                                |
| ----------------- | ------------------------------------------------------ |
| App               | Laravel chạy bằng Docker image `xina99999/myapp:<tag>` |
| Deploy Dir        | `/home/ubuntu/deploy-app/`                             |
| File cấu hình     | `docker-compose.yml`, `.env.production`                |
| Container Laravel | `laravel-app`                                          |

---

## 📁 Cấu trúc thư mục triển khai (trên VPS)

```
/home/ubuntu/deploy-app/
├── deploy.sh            # Script deploy app
├── rollback.sh          # Script rollback
├── docker-compose.yml   # Docker config
├── .env.production      # Laravel config
├── current_version      # Lưu version hiện tại (v3...)
└── last_version         # Lưu version trước (v2...)
```

---

## ⚙️ File `deploy.sh`

Đảm bảo bạn có script deploy ghi lại version cũ → để rollback được.

```bash
#!/bin/bash
set -e

VERSION=$1

if [ -z "$VERSION" ]; then
  echo "❌ Bạn chưa nhập version cần deploy."
  exit 1
fi

cd /home/ubuntu/deploy-app

# Ghi lại version trước
if [ -f current_version ]; then
  cp current_version last_version
fi
echo "$VERSION" > current_version

# Cập nhật docker-compose
sed -i "s|image: xina99999/myapp:.*|image: xina99999/myapp:$VERSION|g" docker-compose.yml

# Pull image mới
docker pull xina99999/myapp:$VERSION

# Triển khai
docker compose down
docker compose up -d

echo "✅ Deploy version $VERSION thành công."
```

---

## 🔁 File `rollback.sh`

```bash
#!/bin/bash
set -e

cd /home/ubuntu/deploy-app

# Kiểm tra version trước
if [ ! -f last_version ]; then
  echo "❌ Không có phiên bản trước để rollback."
  exit 1
fi

ROLLBACK_VERSION=$(cat last_version)
echo "🔁 Bắt đầu rollback về version: $ROLLBACK_VERSION"

# Cập nhật docker-compose.yml
sed -i "s|image: xina99999/myapp:.*|image: xina99999/myapp:$ROLLBACK_VERSION|g" docker-compose.yml

# Pull lại image nếu cần
docker pull xina99999/myapp:$ROLLBACK_VERSION

# Khởi động lại ứng dụng
docker compose down
docker compose up -d

# Cập nhật version
echo "$ROLLBACK_VERSION" > current_version

echo "✅ Rollback thành công về version $ROLLBACK_VERSION"
```

---

## 🧪 Cách sử dụng

### Deploy version mới:

```bash
./deploy.sh v3
```

### Nếu lỗi → rollback:

```bash
./rollback.sh
```

---

