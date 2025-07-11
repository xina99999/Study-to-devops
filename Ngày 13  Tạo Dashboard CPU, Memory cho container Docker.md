

---

## 🗓 **Ngày 13 – Tạo Dashboard CPU, Memory cho container Docker**

### 🎯 **Mục tiêu**:

Biết cách **giám sát Docker containers** (Laravel App, MySQL, Nginx,...) về mặt **CPU, Memory, Network, Disk I/O** và trực quan hóa bằng Grafana.

---

## 🔧 Các bước chi tiết:

---

### **1. Cài đặt cAdvisor (Google Container Monitoring Tool)**

Chạy cAdvisor để thu thập metrics container:

```bash
docker run -d \
  --name=cadvisor \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  -p 8080:8080 \
  gcr.io/cadvisor/cadvisor
```

> Truy cập cAdvisor tại: `http://<EC2_PUBLIC_IP>:8080`
> (Sẽ thấy danh sách container + stats trực tiếp)

---

### **2. Cập nhật cấu hình Prometheus để scrape dữ liệu từ cAdvisor**

Chỉnh file `prometheus.yml`:

```yaml
scrape_configs:
  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']
```

> Nếu chạy Prometheus ngoài container, dùng `localhost:8080` hoặc IP container cAdvisor

Sau đó **reload Prometheus** hoặc restart container Prometheus để nhận cấu hình mới.

---

### **3. Import Dashboard có sẵn từ Grafana.com**

Truy cập Grafana → **+ (Create) → Import**

Nhập các dashboard ID phổ biến:

| Dashboard Name            | ID      |
| ------------------------- | ------- |
| Docker Container Metrics  | `193`   |
| cAdvisor Full Container   | `1150`  |
| Docker and system metrics | `179`   |
| Docker Host Overview      | `12271` |

Sau đó:

* Chọn Data Source là Prometheus
* Nhấn **Import**

---

### **4. Tạo dashboard thủ công (tuỳ chọn)**

Nếu muốn tự tạo dashboard:

* Chọn **+ → Dashboard → Add Panel**
* Query ví dụ (CPU %):

```prometheus
rate(container_cpu_usage_seconds_total{name=~".+"}[1m]) * 100
```

* Query Memory (MiB):

```prometheus
container_memory_usage_bytes{name=~".+"} / 1024 / 1024
```

Chọn:

* Visualization: **Time series**
* Legend: `{{name}}`
* Repeat for each container

---

### **5. Tuỳ chỉnh hiển thị**

* Group theo `container` hoặc `image`
* Thêm **threshold** cảnh báo màu
* Format **Bytes** thành **MiB** cho dễ nhìn
* Lưu Dashboard với tên: `Docker Container Monitoring`

---

### ✅ **Kết quả mong đợi**

* Dashboard hiển thị thông tin CPU, RAM của từng container Laravel, MySQL, Nginx,...
* Có thể theo dõi performance theo thời gian thực
* Đã tích hợp Prometheus → cAdvisor → Grafana

![Screenshot from 2025-07-10 10-04-32](https://github.com/user-attachments/assets/cab07822-d613-48cd-82db-12b961f20e6e)

