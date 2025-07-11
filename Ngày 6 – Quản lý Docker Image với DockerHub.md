
## 🗓️ Ngày 6 – Quản lý Docker Image với DockerHub

### 🎯 **Mục tiêu:**

* Tạo tài khoản DockerHub
* Build và push Docker Image lên DockerHub
* Quản lý các image version, tag, xóa image cũ
* Sử dụng lại image từ DockerHub trên máy khác

---

### ✅ **Task chi tiết**

#### 🔹 Bước 1: Tạo tài khoản DockerHub

* Truy cập: [https://hub.docker.com/](https://hub.docker.com/)
* Nhấn **Sign Up**, điền:

  * Username (đây sẽ là phần prefix cho image, ví dụ: `xnhan/laravel-app`)
  * Email và mật khẩu
* Kích hoạt tài khoản qua email

---

#### 🔹 Bước 2: Đăng nhập Docker từ máy local/VPS

```bash
docker login
```

👉 Nhập DockerHub username và password

---

#### 🔹 Bước 3: Build Docker Image

Giả sử bạn có Dockerfile trong thư mục `project_laravel`:

```bash
docker build -t username/laravel-app:latest .
```

> Ghi chú:
>
> * `username` là username DockerHub
> * `laravel-app` là tên image
> * `:latest` là tag (có thể dùng version như `:v1`)

---

#### 🔹 Bước 4: Push Image lên DockerHub

```bash
docker push username/laravel-app:latest
```

Nếu thành công bạn sẽ thấy image xuất hiện tại:
👉 https://hub.docker.com/repository/docker/username/laravel-app

---

#### 🔹 Bước 5: Xoá image local (để kiểm tra pull lại)

```bash
docker rmi username/laravel-app:latest
```

---

#### 🔹 Bước 6: Pull Image từ DockerHub

```bash
docker pull username/laravel-app:latest
docker run -d --name laravel_app -p 8000:80 username/laravel-app:latest
```

---

#### 🔹 Bước 7: Quản lý image tag & version

* Khi cập nhật Dockerfile, build lại image với tag mới:

```bash
docker build -t username/laravel-app:v2 .
docker push username/laravel-app:v2
```

* Bạn có thể:

  * Gắn lại tag `latest` nếu cần:

    ```bash
    docker tag username/laravel-app:v2 xnhan/laravel-app:latest
    docker push username/laravel-app:latest
    ```
  * Xoá image local:

    ```bash
    docker rmi username/laravel-app:v1
    ```

---

#### 🔹 Bước 8: (Tuỳ chọn) Tự động cập nhật image từ GitHub (CI/CD)

* Liên kết GitHub Repo → DockerHub
* Cấu hình Auto-build khi push code

---

### 📁 Kết quả mong đợi

* DockerHub có repo `xnhan/laravel-app`
* Có ít nhất 1 tag: `latest`
* Có thể chạy container từ image public đó trên mọi máy khác

---


