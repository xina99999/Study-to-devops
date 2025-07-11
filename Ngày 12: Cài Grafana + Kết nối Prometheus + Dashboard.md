

---

### 🗓 Ngày 12: Cài Grafana + Kết nối Prometheus + Dashboard

---

### 🎯 Mục tiêu:

* Cài đặt Grafana.
* Kết nối với Prometheus đã cài trước đó (giả sử đã có container Prometheus).
* Tạo Dashboard theo dõi tài nguyên hệ thống hoặc Laravel app (nếu có exporter như Laravel Exporter, Node Exporter).

---

## 🔧 Các bước chi tiết:

---

### **1. Cài Grafana bằng Docker**

```bash
docker run -d \
  --name=grafana \
  -p 3000:3000 \
  -e "GF_SECURITY_ADMIN_USER=admin" \
  -e "GF_SECURITY_ADMIN_PASSWORD=admin" \
  grafana/grafana
```

> 📌 Truy cập: `http://<EC2_PUBLIC_IP>:3000`
> Đăng nhập bằng: `admin / admin`

---

### **2. Đảm bảo Prometheus đang chạy**

Nếu Prometheus chưa chạy, có thể dùng:

```bash
docker run -d \
  --name=prometheus \
  -p 9090:9090 \
  -v $PWD/prometheus.yml:/etc/prometheus/prometheus.yml \
  prom/prometheus
```

File `prometheus.yml` tối thiểu:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['<EC2_PRIVATE_IP>:9100']
```

> 📌 Thay `<EC2_PRIVATE_IP>` bằng IP container Node Exporter nếu có

---

### **3. Kết nối Prometheus vào Grafana**

* Truy cập Grafana `http://<EC2_PUBLIC_IP>:3000`
* Vào **Settings → Data Sources**
* Chọn **Add data source → Prometheus**
* Nhập URL: `http://<PROMETHEUS_CONTAINER_IP>:9090` hoặc `http://host.docker.internal:9090` nếu cùng host
* Nhấn **Save & Test**

---

### **4. Import Dashboard mẫu**

* Vào **+ → Import**
* Nhập **ID Dashboard** ví dụ:

  * **1860** – Node Exporter Full (theo dõi hệ thống)
  * **11074** – Laravel Metrics (nếu đã tích hợp Laravel Prometheus Exporter)
* Nhấn **Load**, chọn Prometheus làm source, rồi **Import**

---

### **5. Giám sát Laravel (nếu có exporter)**

Nếu Laravel có cài `proner/laravel-prometheus-exporter`:

* Laravel export metrics tại: `http://<APP>:8000/metrics`
* Thêm target vào `prometheus.yml`:

```yaml
- job_name: 'laravel'
  metrics_path: /metrics
  static_configs:
    - targets: ['<APP_CONTAINER_IP>:8000']
```

Sau đó reload Prometheus để thu thập dữ liệu.

---

### ✅ Kết quả mong đợi:

* Grafana chạy tại: `http://<EC2_PUBLIC_IP>:3000`
* Đã kết nối Prometheus
* Có ít nhất 1 dashboard giám sát CPU, RAM hoặc Laravel metrics

---


