

---

## 🗓️ **Ngày 4 – Giao Task cụ thể: Docker cơ bản**

### 🎯 **Mục tiêu:**

* Hiểu được container là gì và Docker hoạt động ra sao
* Biết cách dùng các lệnh Docker cơ bản: `build`, `run`, `stop`, `rm`, `exec`, `logs`
* Biết **port mapping**, tạo container chạy **app demo**
* Biết thao tác với **Docker Hub**

---

## ✅ TỔNG QUAN CÁC TASK HÔM NAY:

1. Cài đặt Docker (nếu chưa có)
2. Chạy container đơn giản từ image có sẵn (ngay lập tức thấy kết quả)
3. Build image từ Dockerfile
4. Dùng port mapping để truy cập app trên browser
5. Thao tác quản lý container
6. Push lên Docker Hub (giao lưu với vòng 5)

---

## 🧩 **Task 1: Cài đặt Docker (nếu chưa)**

### Với Ubuntu:

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
```

Kiểm tra:

```bash
docker --version
docker run hello-world
```

---

## 🧩 **Task 2: Chạy container từ image có sẵn**

Chạy một web server cực nhẹ:

```bash
docker run -d -p 8080:80 nginx
```

→ Truy cập trình duyệt: `http://localhost:8080` (hoặc `http://IP:8080` nếu dùng server)

Dừng container:

```bash
docker ps             # xem container đang chạy
docker stop [ID]      # dừng container
docker rm [ID]        # xóa container
```

---

## 🧩 **Task 3: Build image từ Dockerfile đơn giản**

### ✍️ Tạo Dockerfile:

```bash
mkdir my-app && cd my-app
echo "<h1>Hello from Docker!</h1>" > index.html
nano Dockerfile
```

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

### 🛠 Build image:

```bash
docker build -t my-nginx .
```

### 🚀 Run thử:

```bash
docker run -d -p 8081:80 my-nginx
```

→ Mở trình duyệt: `http://localhost:8081` → thấy “Hello from Docker!”

---

## 🧩 **Task 4: Thao tác quản lý container**

| Lệnh                                | Mục đích                 |
| ----------------------------------- | ------------------------ |
| `docker ps -a`                      | Liệt kê tất cả container |
| `docker images`                     | Liệt kê tất cả image     |
| `docker logs [container_id]`        | Xem log                  |
| `docker exec -it [container_id] sh` | Vào bên trong container  |

---

## 🧩 **Task 5: (Bonus) Push image lên Docker Hub**

1. Đăng ký tài khoản: [https://hub.docker.com](https://hub.docker.com)
2. Login trong terminal:

   ```bash
   docker login
   ```
3. Tag image:

   ```bash
   docker tag my-nginx yourusername/my-nginx:v1
   ```
4. Push:

   ```bash
   docker push yourusername/my-nginx:v1
   ```

---

## ✅ Kết quả kỳ vọng trong ngày:

| Mục                                  | Đầu ra mong muốn             |
| ------------------------------------ | ---------------------------- |
| ✅ Biết cài và kiểm tra Docker        | `docker run hello-world`     |
| ✅ Chạy container Nginx qua port 8080 | `http://localhost:8080`      |
| ✅ Tạo image từ Dockerfile            | `docker build -t my-nginx .` |
| ✅ Chạy container tự build            | `http://localhost:8081`      |
| ✅ Biết dừng, xoá, exec container     | `docker stop`, `docker exec` |
| ✅ (Bonus) Push lên DockerHub         | `docker push ...`            |

---

## 📘 Tài liệu gợi ý:

* [https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)
* [Docker cheat sheet PDF](https://dockerlabs.collabnix.com/docker/cheatsheet/)

---

