

---

## 🗓️ Ngày 9 – **Deploy Laravel lên EC2 từ GitHub bằng Docker**

---

### 🎯 **Mục tiêu**

* Clone project Laravel từ GitHub vào EC2.
* Cấu hình Docker + docker-compose để chạy Laravel + MySQL + Nginx.
* Truy cập được Laravel từ trình duyệt qua IP EC2.

---

## 🧩 **Chi tiết các Task**

---

### 🔹 1. SSH vào EC2

```bash
ssh -i your-key.pem ubuntu@<EC2-PUBLIC-IP>
```

---

### 🔹 2. Cài đặt Docker và Docker Compose (nếu chưa có)

```bash
sudo apt update
sudo apt install -y docker.io docker-compose
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

**(Bạn cần logout & login lại sau khi chạy `usermod`)**

---

### 🔹 3. Cài git và clone dự án từ GitHub

```bash
sudo apt install -y git
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
```

**📌 Ví dụ:**

```bash
git clone https://github.com/xina9999/laravel-docker-app.git
cd laravel-docker-app
```

---

### 🔹 4. Cấu trúc dự án nên có:

```
laravel-docker-app/
├── docker-compose.yml
├── Dockerfile
├── nginx/
│   └── default.conf
└── laravel_source/ ← hoặc toàn bộ mã nguồn Laravel nếu không tách thư mục
```

Nếu bạn **không chia riêng thư mục `laravel_source/`**, hãy điều chỉnh lại `Dockerfile` và `docker-compose.yml` cho đúng `.` là context.

---

### 🔹 5. Build và chạy Docker container

```bash
docker compose up -d --build
```

Xem container:

```bash
docker ps
```

---

### 🔹 6. Cấp quyền cho Laravel

```bash
docker exec -it laravel_app bash
cd /var/www
chmod -R 775 storage bootstrap/cache
chown -R www-data:www-data .
exit
```

---

### 🔹 7. Migrate cơ sở dữ liệu

```bash
docker exec -it laravel_app bash
php artisan migrate
```

---

### 🔹 8. Truy cập trang Laravel

Trên trình duyệt:

```
http://<EC2-PUBLIC-IP>
```

---


```

---

## ✅ Kết quả mong đợi

* Laravel app truy cập được tại `http://<EC2-IP>`.
* Đã migrate được database.
* Docker image hoạt động bình thường.
* Không lỗi 500, 403, 404 nếu cấu hình đúng.

---

👉 Nếu bạn muốn **auto deploy mỗi lần push code lên GitHub**, bạn có thể dùng **GitHub Actions + SSH deploy script**. Nếu cần, mình sẽ viết luôn cho bạn nhé!
