
---

## 🗓 **Ngày 16 – Giám sát MySQL + Loki (log container)**

### 🎯 **Mục tiêu:**

* Giám sát **MySQL**: kết nối Prometheus exporter để lấy thông số hiệu năng.
* Giám sát **log của container** Laravel thông qua **Loki** và hiển thị trên **Grafana**.

---

## 📌 **Phần 1 – Giám sát MySQL bằng `mysqld_exporter`**

### 1.1. Cấu hình user monitoring trong MySQL container

```sql
CREATE USER 'exporter'@'%' IDENTIFIED BY 'exporter_password';
GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'%';
FLUSH PRIVILEGES;
```

> Có thể login vào container:

```bash
docker exec -it mysql_db mysql -u root -p
```

---

### 1.2. Thêm `mysqld_exporter` vào `docker-compose.yml`

```yaml
  mysqld_exporter:
    image: prom/mysqld-exporter
    container_name: mysqld_exporter
    environment:
      - DATA_SOURCE_NAME=exporter:exporter_password@(mysql_db:3306)/
    ports:
      - "9104:9104"
    depends_on:
      - db
```

---

### 1.3. Cập nhật cấu hình Prometheus `prometheus.yml`

```yaml
  - job_name: 'mysql'
    static_configs:
      - targets: ['mysqld_exporter:9104']
```

---

### 1.4. Reload Prometheus hoặc restart container

```bash
docker restart prometheus
```

---

### ✅ Kiểm tra

* Truy cập: `http://<EC2_IP>:9090`
* Query:

```promQL
mysql_global_status_threads_connected
```

---

## 📌 **Phần 2 – Thu thập log Laravel bằng Loki**

### 2.1. Thêm dịch vụ **Loki** và **Promtail** vào `docker-compose.yml`

```yaml
  loki:
    image: grafana/loki:2.9.2
    container_name: loki
    ports:
      - "3100:3100"
    command: -config.file=/etc/loki/local-config.yaml

  promtail:
    image: grafana/promtail:2.9.2
    container_name: promtail
    volumes:
      - ./laravel_source/storage/logs:/var/log/laravel
      - ./promtail-config.yml:/etc/promtail/promtail.yml
    command: -config.file=/etc/promtail/promtail.yml
```

---

### 2.2. Tạo file cấu hình `promtail-config.yml`

```yaml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: laravel_logs
    static_configs:
      - targets:
          - localhost
        labels:
          job: laravel
          __path__: /var/log/laravel/*.log
```

---

### 2.3. Kết nối Loki vào Grafana

1. Truy cập Grafana `http://<EC2_IP>:3000`
2. Vào: **Connections > Data Sources > Add data source**
3. Chọn: **Loki**
4. URL: `http://loki:3100`
5. Bấm **Save & Test**

---

### 2.4. Tạo Dashboard log trong Grafana

* Truy cập **Explore**
* Chọn **Data Source: Loki**
* Query mẫu:

```
{job="laravel"}
```

* Hoặc filter lỗi:

```
{job="laravel"} |= "error"
```

---

## ✅ Kết quả mong đợi

* Các chỉ số MySQL như kết nối, query slow… hiển thị trong Prometheus.
* Laravel logs được hiển thị realtime trong Grafana.
* Có thể tạo dashboard theo dõi error log hoặc warning log của Laravel.

---


