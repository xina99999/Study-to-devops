

## 📅 Kế hoạch ngày 7: "GitHub Actions + Docker Hub" 

---

## 📝 **Giới thiệu**

> Trong môi trường DevOps hiện đại, việc **tự động build và deploy** Docker image là điều thiết yếu. Hôm nay, bạn sẽ học cách sử dụng **GitHub Actions** để tự động:

* Build image từ source code
* Push image lên **Docker Hub**
* Và chuẩn bị để deploy ở bất kỳ server nào bằng `docker-compose`

---

## 🎯 **Mục tiêu cuối ngày**

* ✅ Có repo GitHub chứa Laravel (hoặc app bất kỳ)
* ✅ Tự động build Docker image mỗi lần push code
* ✅ Push image lên Docker Hub (private hoặc public)
* ✅ Máy khác có thể pull và chạy bằng `docker-compose`

---

## ✅ Task 1: Chuẩn bị tài khoản

### 📌 1.1. Tài khoản Docker Hub

* Tạo tại: [https://hub.docker.com/](https://hub.docker.com/)
* Ghi nhớ: `username` và `password` hoặc tạo **PAT (token)** tại [Security Settings](https://hub.docker.com/settings/security)

### 📌 1.2. Tài khoản GitHub

* Tạo repo chứa source code Laravel hoặc app của bạn
* Có thể là **private repo**

---

## ✅ Task 2: Viết Dockerfile

Ví dụ Laravel app:

```dockerfile
# Dockerfile
FROM php:8.1-fpm

WORKDIR /var/www

RUN apt-get update && apt-get install -y \
    zip unzip git curl libzip-dev libpng-dev libonig-dev libxml2-dev \
    && docker-php-ext-install pdo pdo_mysql zip

COPY . .

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer install

EXPOSE 9000
CMD ["php-fpm"]
```

---

## ✅ Task 3: Tạo GitHub Secrets

Vào **Settings → Secrets → Actions** trong repo GitHub, tạo 2 biến:

| Key               | Value                |
| ----------------- | -------------------- |
| `DOCKER_USERNAME` | Tài khoản Docker Hub |
| `DOCKER_PASSWORD` | Mật khẩu hoặc PAT    |

---

## ✅ Task 4: Tạo GitHub Actions workflow

📁 Tạo file: `.github/workflows/docker.yml`

```yaml
name: Build & Push Docker Image

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout source
      uses: actions/checkout@v3

    - name: Login to Docker Hub
      uses: docker/login-action@v3
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and Push
      uses: docker/build-push-action@v5
      with:
        context: .
        file: ./Dockerfile
        push: true
        tags: xina9999/laravel_app:latest
```

📌 Thay `xina9999` bằng Docker username của bạn.

---

## ✅ Task 5: Push code để test

```bash
git add .
git commit -m "CI/CD: GitHub Actions build & push Docker"
git push origin main
```

→ GitHub Actions sẽ tự chạy workflow

Kiểm tra tại: **GitHub → Actions tab**

---

## ✅ Task 6: Kiểm tra Docker Hub

* Vào: [https://hub.docker.com/repository/docker/xina9999/laravel\_app](https://hub.docker.com/repository/docker/xina9999/laravel_app)
* Kiểm tra tab **Tags**
* Image `latest` sẽ được tạo sau khoảng 1–2 phút

---

## ✅ Task 7: Chạy ở máy khác bằng `docker-compose`

📄 `docker-compose.yml` mẫu:

```yaml
version: '3.8'

services:
  app:
    image: xina9999/laravel_app:latest
    container_name: laravel_app
    ports:
      - "9000:9000"
    restart: always
```

### 👉 Các bước chạy:

```bash
docker-compose pull
docker-compose up -d
```

---

## 🧠 Tổng kết kiến thức bạn đã học hôm nay

| Kiến thức      | Ý nghĩa                             |
| -------------- | ----------------------------------- |
| GitHub Actions | CI/CD tool tự động hóa              |
| Dockerfile     | Đóng gói ứng dụng                   |
| Docker Hub     | Registry lưu trữ image              |
| Secrets        | Bảo mật tài khoản trong pipeline    |
| docker-compose | Dễ triển khai ứng dụng đa container |

---

## 🔥 Mở rộng sau ngày 7

* Tự động deploy lên VPS bằng SSH
* Sử dụng GitHub Actions để deploy Laravel lên server staging/production
* Scan bảo mật Docker image bằng Docker Scout
* Versioning (tag v1.0.0, v2.0.0...)

---

## ✅ Kết luận

Với **7 bước trên trong 1 ngày**, bạn đã hoàn thành:

* Một hệ thống CI/CD đơn giản nhưng cực kỳ hiệu quả
* Tự động build image Laravel, push lên Docker Hub
* Dễ dàng deploy ở bất kỳ môi trường nào

---

Nếu bạn muốn mình gửi:

* Mẫu repo Laravel + GitHub Actions
* Dockerfile chuẩn Laravel + MySQL + Nginx
* Hoặc hướng dẫn deploy lên VPS qua SSH

👉 Chỉ cần nói, mình hỗ trợ tiếp luôn!
