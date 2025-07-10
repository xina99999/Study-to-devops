

---

## 🗓 **Ngày 15 – Cảnh báo Grafana + gửi Telegram/Email**

### 🎯 **Mục tiêu**:

* Tạo các **Alert Rule** trong Grafana dựa trên dữ liệu từ Prometheus.
* **Gửi cảnh báo tự động** khi vượt ngưỡng CPU, RAM, disk...
* Tích hợp **Telegram Bot API** hoặc **SMTP Email** để gửi cảnh báo ra ngoài.

---

## 🧩 **1. Kích hoạt Alerting trong Grafana**

> Grafana >= v8+ đã có sẵn alerting system mới.

### Bước 1: Truy cập Grafana

```
http://<IP>:3000
```

Login bằng:

* Username: `admin`
* Password: `admin` (hoặc như bạn đã đổi)

---

## ⚙️ **2. Tạo Contact Point (Telegram hoặc Email)**

### 🔹 2.1. Telegram Alert

#### a. Tạo Telegram Bot:

1. Mở Telegram, tìm `@BotFather`.
2. Gửi lệnh `/newbot`.
3. Đặt tên và nhận **Bot Token**.

#### b. Lấy Chat ID:

1. Nhắn 1 tin nhắn đến bot bạn vừa tạo.
2. Truy cập:

```
https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates
```

3. Tìm `chat.id` từ JSON trả về.

#### c. Thêm Telegram vào Grafana:

* Vào **Alerting > Contact Points > New contact point**
* Chọn **Telegram**.
* Nhập:

  * Bot Token
  * Chat ID
  * Tin nhắn tùy chỉnh: `⚠️ Alert: ${ruleName} - ${state}`

---

### 🔹 2.2. SMTP Email Alert

#### a. Cấu hình `grafana.ini` (nếu chưa có GUI email):

```ini
[smtp]
enabled = true
host = smtp.gmail.com:587
user = your_email@gmail.com
password = your_app_password
from_address = your_email@gmail.com
from_name = Grafana Alerts
skip_verify = false
```

> Sau đó **restart Grafana**:

```bash
docker restart <grafana_container_name>
```

#### b. Tạo Contact Point

* Vào **Alerting > Contact Points**
* Chọn **Email**
* Nhập email nhận cảnh báo

---

## 📊 **3. Tạo Alert Rule trên Dashboard**

### Ví dụ: CPU usage alert

#### a. Vào dashboard chứa biểu đồ CPU

#### b. Click vào biểu đồ → **Edit**

#### c. Chuyển sang tab **Alert** (hoặc “Alerting”)

#### d. Thêm điều kiện:

* Ví dụ:

  * Expression: `avg(rate(node_cpu_seconds_total{mode!="idle"}[1m])) * 100`
  * If `value` is above `80` for `1m`

#### e. Gán Alert vào **Contact Point** đã tạo (Telegram/Email)

#### f. Bấm **Save**

---

## 🧪 **4. Kiểm tra và mô phỏng cảnh báo**

* Giả lập CPU cao bằng cách chạy:

```bash
yes > /dev/null &
```

* Quan sát biểu đồ và chờ alert được kích hoạt.

---

## ✅ **Kết quả mong đợi**

* Khi CPU hoặc RAM vượt ngưỡng, **cảnh báo tự động được gửi** về Telegram hoặc Email.
* Có thể quản lý nhiều alert rule trên nhiều dashboard.

---

## 📦 Ghi chú thêm

* Grafana cũng hỗ trợ Slack, Webhook, Discord, Opsgenie, PagerDuty, v.v.
* Có thể export/import alert rules.
* Alerts được lưu trong **SQLite DB** nội bộ của Grafana hoặc database backend nếu có.

---

