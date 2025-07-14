

---

## 📘 **Lesson Learned – DevOps Pipeline 21 ngày**

### 👨‍💻 Thực hiện bởi: Nguyễn Duy Trọng Nhân

**GitHub:** [xina99999](https://xina99999/Study-to-devops)

---

### 🧭 **Mục tiêu dự án**

> Triển khai quy trình DevOps hoàn chỉnh cho một ứng dụng Laravel từ GitHub lên VPS bằng Docker, giám sát hệ thống với Prometheus + Grafana, cấu hình alert về Discord và thiết lập CI/CD với GitHub Actions.

---

## 🧩 **1. Tổng quan các bước đã thực hiện**

| Ngày  | Nội dung chính                             |
| ----- | ------------------------------------------ |
| 1–2   | Git cơ bản, GitHub Flow                    |
| 3–4   | CI/CD với GitHub Actions                   |
| 5–6   | Docker build Laravel, push DockerHub       |
| 7–8   | Cấu hình VPS, cài Docker                   |
| 9–10  | Deploy Laravel từ GitHub lên VPS           |
| 11–13 | Monitoring Prometheus + Dashboard CPU/RAM  |
| 14–15 | Quản lý Volume + Alert Discord             |
| 16    | Giám sát MySQL + log container với Loki    |
| 17–20 | GitHub Action SSH deploy, rollback, tối ưu |
| 21    | Tổng kết, tự đánh giá, lesson learned      |

---

## 🚀 **2. Những điều làm được**

✅ CI/CD full flow với GitHub Actions + SSH
✅ Tự tạo Dockerfile Laravel + nginx + MySQL
✅ Monitoring container bằng Prometheus + Grafana
✅ Alert gửi Discord khi CPU tăng cao
✅ Push image Docker lên DockerHub cá nhân
✅ Dùng volume để tránh mất dữ liệu DB
✅ Hiểu được workflow của 1 hệ thống sản phẩm nhỏ production-ready

---

## ⚠️ **3. Những lỗi gặp & cách khắc phục**

| Lỗi                      | Cách xử lý                                                                        |
| ------------------------ | --------------------------------------------------------------------------------- |
| Laravel error 500        | Check `storage/logs/laravel.log`, lỗi kết nối DB do sai `DB_HOST`                 |
| GitHub Actions SSH lỗi   | Thiếu khai báo `SSH_PRIVATE_KEY`, fix bằng `ssh-agent`                            |
| Docker build fail do PHP | Laravel yêu cầu PHP 7.4, fix lại `FROM php:7.4-fpm`                               |
| Push DockerHub fail      | Thiếu `docker tag`, fix bằng `docker tag laravel_app xina9999/laravel_app:latest` |
| Alert không gửi          | Quên config `Discord Webhook`, thêm vào Prometheus AlertManager                   |

---

## 🔍 **4. Tự đánh giá kỹ năng**

| Kỹ năng              | Mức độ làm chủ (1–5 ⭐) |
| -------------------- | ---------------------- |
| Docker               | ⭐⭐⭐⭐                   |
| Git / GitHub Actions | ⭐⭐⭐⭐                   |
| Linux VPS            | ⭐⭐⭐                    |
| Prometheus / Grafana | ⭐⭐⭐                    |
| CI/CD thực chiến     | ⭐⭐⭐⭐                   |
| Troubleshooting      | ⭐⭐⭐⭐                   |

---

## 🛠️ **5. Kỹ thuật & công cụ đã dùng**

* **Laravel 8 + MySQL 5.7**
* **Docker, Docker Compose**
* **GitHub Actions**
* **Prometheus, Node Exporter**
* **Grafana (Dashboard + Alert)**
* **AlertManager gửi Discord**
* **GitHub + VPS Ubuntu 22.04**
* **Custom Dockerfile + volume + SSL nội bộ**

---

## 🔮 **6. Hướng phát triển tiếp**

* ✅ Thêm test tự động trong pipeline (`phpunit`)
* ✅ Triển khai HTTPS bằng Let's Encrypt
* ✅ Tự động backup MySQL lên Google Drive / S3
* ✅ Dùng Docker Registry riêng (Harbor)
* ✅ Terraform hóa toàn bộ hệ thống

---

## ✍️ **7. Kết luận cá nhân**

> Sau 21 ngày triển khai từ đầu đến cuối, mình tự tin có thể:
>
> * Xây dựng 1 hệ thống production nhỏ
> * Tự động hóa deploy
> * Phân tích lỗi khi có sự cố
> * Truyền đạt rõ ràng từng bước setup cho người khác

Mình sẵn sàng apply các vị trí **DevOps Fresher**, **System Admin**, hoặc **Web Infrastructure Intern**.

---

