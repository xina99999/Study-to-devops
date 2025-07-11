
---

## 🗓 **Ngày 14 – Cấu hình lưu config + persistent volume**

### 🎯 **Mục tiêu**:

Hiểu và sử dụng **volume trong Docker** để:

* Lưu **cấu hình Prometheus, Grafana**.
* Lưu **dữ liệu MySQL** và tránh mất dữ liệu khi container bị xóa.

---

## 🔧 Các bước chi tiết

---

### **1. Hiểu cơ bản về volume Docker**

* **Volume** là nơi Docker lưu trữ dữ liệu **ngoài container**, thường nằm ở `/var/lib/docker/volumes/`.
* Dùng volume giúp:

  * Dữ liệu vẫn còn khi container bị xoá.
  * Dễ backup, restore.
  * Chia sẻ dữ liệu giữa các container.

---

### **2. Tạo volume cho MySQL**

**Docker Compose đoạn cấu hình ví dụ:**

```yaml
services:
  db:
    image: mysql:5.7
    container_name: mysql
    restart: unless-stopped
    volumes:
      - mysql_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: laravel

volumes:
  mysql_data:
```

📌 Sau khi chạy:

```bash
docker-compose up -d
```

→ Docker sẽ tạo volume tên `mysql_data` chứa dữ liệu MySQL.

🧪 Kiểm tra:

```bash
docker volume ls
docker volume inspect mysql_data
```

---

### **3. Tạo volume cho Prometheus**

#### a. Cấu trúc thư mục:

```
project_monitoring/
├── prometheus/
│   └── prometheus.yml
```

#### b. docker-compose.yml:

```yaml
services:
  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
    ports:
      - "9090:9090"

volumes:
  prometheus_data:
```

> Volume `prometheus_data` sẽ chứa **series data + time series + history** của Prometheus.

---

### **4. Tạo volume cho Grafana**

```yaml
services:
  grafana:
    image: grafana/grafana
    volumes:
      - grafana_data:/var/lib/grafana
    ports:
      - "3000:3000"

volumes:
  grafana_data:
```

> Volume `grafana_data` giúp bạn **giữ dashboard, cấu hình người dùng**, datasource,...

---

### **5. Backup volume (tuỳ chọn)**

Để backup 1 volume, ví dụ `mysql_data`:

```bash
docker run --rm -v mysql_data:/volume -v $(pwd):/backup alpine \
  tar czf /backup/mysql_backup.tar.gz -C /volume .
```

Restore lại:

```bash
docker run --rm -v mysql_data:/volume -v $(pwd):/backup alpine \
  tar xzf /backup/mysql_backup.tar.gz -C /volume
```

---

### ✅ **Kết quả mong đợi**

* Prometheus, Grafana giữ lại cấu hình khi stop/start container.
* MySQL giữ lại dữ liệu khi xoá container.
* Biết cách quản lý volume bằng lệnh `docker volume`.

---

