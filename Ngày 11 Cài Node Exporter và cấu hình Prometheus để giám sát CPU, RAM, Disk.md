

## 🗓️ Ngày 11 – **Cài Node Exporter và cấu hình Prometheus để giám sát CPU, RAM, Disk**

---

### 🎯 **Mục tiêu**

* Chạy Node Exporter để thu thập metric hệ thống.
* Thêm cấu hình `node_exporter` vào Prometheus.
* Theo dõi tình trạng CPU, RAM, Disk từ Prometheus UI.

---

## 🧩 **Chi tiết các Task**

---

### 🔹 1. Tạo thư mục `node_exporter`

```bash
mkdir -p ~/prometheus_setup/node_exporter
cd ~/prometheus_setup/node_exporter
```

---

### 🔹 2. Tạo file `docker-compose.yml` cho Node Exporter

```yaml
version: '3.8'

services:
  node_exporter:
    image: prom/node-exporter
    container_name: node_exporter
    ports:
      - "9100:9100"
    restart: unless-stopped
```

Chạy Node Exporter:

```bash
docker compose up -d
```

---

### 🔹 3. Kiểm tra Node Exporter hoạt động

Truy cập trên trình duyệt:

```
http://<IP>:9100/metrics
```

Nếu hiển thị nhiều dòng như `node_cpu_seconds_total`, `node_memory_MemAvailable_bytes`,... nghĩa là đã chạy thành công.

---

### 🔹 4. Cập nhật `prometheus.yml` để thêm job `node_exporter`

Vào thư mục `~/prometheus_setup` và chỉnh sửa `prometheus.yml` như sau:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['host.docker.internal:9100']
```

**📌 Lưu ý:**

* Nếu Prometheus và Node Exporter **trong cùng một Docker host**, dùng `host.docker.internal:9100`.
* Nếu Node Exporter chạy bên ngoài Docker, bạn dùng IP thật hoặc `localhost:9100`.

---

### 🔹 5. Khởi động lại Prometheus để áp dụng cấu hình

```bash
cd ~/prometheus_setup
docker compose down
docker compose up -d
```

---

### 🔹 6. Kiểm tra trên Prometheus UI

Truy cập:

```
http://<IP>:9090/targets
```

Bạn sẽ thấy 2 target:

* `localhost:9090` → Prometheus
* `host.docker.internal:9100` → node\_exporter

---

### 🔹 7. Truy vấn metric trong Prometheus UI

Vào tab **Graph**, thử các truy vấn:

| Metric                             | Ý nghĩa                      |
| ---------------------------------- | ---------------------------- |
| `node_cpu_seconds_total`           | Tổng thời gian CPU xử lý     |
| `node_memory_MemAvailable_bytes`   | Dung lượng RAM còn trống     |
| `node_filesystem_avail_bytes`      | Dung lượng đĩa còn trống     |
| `node_load1`                       | Load trung bình trong 1 phút |
| `node_network_receive_bytes_total` | Lưu lượng nhận qua mạng      |

---

## ✅ Kết quả mong đợi

| Mục                        | Kết quả                             |
| -------------------------- | ----------------------------------- |
| Node Exporter              | Chạy tại `http://<IP>:9100`     |
| Prometheus                 | Đã thêm job `node_exporter`         |
| Metrics hệ thống           | Có thể truy vấn được CPU, RAM, Disk |
| Tự động cập nhật mỗi `15s` | Theo `scrape_interval`              |

---

## 🚀 Gợi ý tiếp theo cho Ngày 13

> **Tích hợp Grafana để vẽ biểu đồ giám sát trực quan**:

* Cài Grafana bằng Docker.
* Kết nối với Prometheus.
* Import dashboard giám sát CPU, RAM, Disk.

