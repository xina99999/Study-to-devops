
---

## 🗓️ Ngày 10 – **Cài đặt Prometheus bằng Docker, cấu hình `scrape_interval`**

---

### 🎯 **Mục tiêu**

* Chạy Prometheus bằng Docker trên server.
* Hiểu và chỉnh `scrape_interval` để điều khiển tần suất thu thập dữ liệu.
* Làm quen với Prometheus UI và khái niệm metric.

---

## 🧩 **Chi tiết các Task**

---

### 🔹 1. Tạo thư mục project Prometheus

```bash
mkdir -p ~/prometheus_setup
cd ~/prometheus_setup
```

---

### 🔹 2. Tạo file cấu hình Prometheus: `prometheus.yml`

```yaml
global:
  scrape_interval: 15s  # ⬅️ Điều chỉnh thời gian lấy dữ liệu mỗi 15 giây

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
```

> Bạn có thể đổi `15s` thành `5s`, `30s`, `1m` tùy vào nhu cầu giám sát.

---

### 🔹 3. Tạo `Dockerfile` (không bắt buộc nếu dùng image chính thức)

Bỏ qua bước này nếu bạn chỉ cần chạy Prometheus từ Docker Hub.

---

### 🔹 4. Tạo `docker-compose.yml`

```yaml
version: '3.8'

services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
```

---

### 🔹 5. Chạy Prometheus bằng Docker Compose

```bash
docker compose up -d
```

Kiểm tra container:

```bash
docker ps
```

---

### 🔹 6. Truy cập Prometheus UI

Mở trình duyệt và vào địa chỉ:

```
http://<IP>:9090
```

---

### 🔹 7. Kiểm tra cấu hình đã hoạt động

* Trên giao diện Prometheus, vào tab **Status → Targets**.
* Nếu thấy `localhost:9090` là **UP**, thì Prometheus đã hoạt động đúng.

---

### 🔹 8. Tìm hiểu metric cơ bản

Tại giao diện web Prometheus, bạn có thể thử một vài câu truy vấn PromQL như:

* `up` → Kiểm tra service nào đang online.
* `process_cpu_seconds_total` → Xem mức sử dụng CPU.
* `prometheus_http_requests_total` → Số lượng HTTP request đến Prometheus.

---

## ✅ Kết quả mong đợi

| Kiểm tra             | Mô tả                                    |
| -------------------- | ---------------------------------------- |
| Prometheus container | Đang chạy, port 9090 mở                  |
| UI hoạt động         | Truy cập được tại `http://<IP>:9090` |
| scrape\_interval     | Được áp dụng đúng theo cấu hình          |
| Biểu đồ              | Có thể truy vấn metric trên giao diện    |

---

## 🔧 Gợi ý nâng cao sau ngày 10

* Cấu hình Prometheus giám sát các container khác như: Laravel App, MySQL, Nginx,...
* Kết hợp với **Grafana** để hiển thị biểu đồ đẹp.
* Cài Node Exporter để theo dõi tài nguyên hệ thống (RAM, CPU, Disk).

---
![Screenshot from 2025-07-09 09-14-03](https://github.com/user-attachments/assets/126e3b86-77f9-4c1f-a0f0-9eb40d2570c6)

