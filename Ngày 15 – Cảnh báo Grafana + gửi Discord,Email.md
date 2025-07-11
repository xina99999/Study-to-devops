

---

## 🗓 **Ngày 15 – Cảnh báo Grafana + gửi Discord/Email**

### 🎯 **Mục tiêu**:

* Cấu hình hệ thống cảnh báo (Alerting) trong Grafana.
* Tạo **Contact Points** gửi cảnh báo khi vượt ngưỡng CPU, RAM, v.v.
* Gửi thông báo cảnh báo qua **Discord Webhook** hoặc **Email (SMTP)**.

---

## 🧩 **1. Kích hoạt Alerting trong Grafana**

> Grafana từ v8 trở lên có alerting system mới tích hợp trực tiếp trong GUI.

### Truy cập:

```
http://<IP>:3000
```

Đăng nhập:

* User: `admin`
* Pass: `admin` (hoặc pass đã thay)

---

## ⚙️ **2. Tạo Contact Point (Discord hoặc Email)**

### 🔹 2.1. Gửi cảnh báo qua Discord Webhook

#### a. Tạo Webhook trên Discord:

1. Truy cập Discord > Chọn server > Kênh text > `Edit Channel`.
2. Vào tab **Integrations** > **Webhooks** > `New Webhook`.
3. Đặt tên & chọn kênh > `Copy Webhook URL`.

#### b. Thêm Contact Point trong Grafana:

1. Vào menu: **Alerting > Contact Points > New contact point**.
2. Chọn **Webhook**.
3. Dán Webhook URL Discord vào trường `URL`.
4. Content type: `application/json`
5. Mẫu nội dung (JSON template):

```json
{
  "content": "⚠️ Alert: ${ruleName} is now ${state} - ${message}"
}
```
![Screenshot from 2025-07-10 17-25-11](https://github.com/user-attachments/assets/477cc315-98ef-458c-9a66-a59df1f36431)

---

### 🔹 2.2. Gửi cảnh báo qua Email (SMTP)

#### a. Cấu hình SMTP trong `grafana.ini` (nếu dùng self-hosted):
## Vào docker grafana chạy lệnh sau
```ini
/etc/grafana/grafana.ini
```
## Kiếm phần smtp điền thông tin sau

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

> Sau đó **khởi động lại container Grafana**:

```bash
docker restart <grafana_container_name>
```

#### b. Tạo Contact Point trong Grafana:

1. Vào **Alerting > Contact Points > New contact point**
2. Chọn loại `Email`
3. Nhập địa chỉ email nhận cảnh báo

---

## 📊 **3. Tạo Alert Rule trên Dashboard**

Ví dụ: Cảnh báo khi CPU vượt 80%

1. Mở Dashboard > chọn biểu đồ CPU
2. Click biểu đồ → `Edit`
3. Vào tab **Alert** (hoặc “Alerting”)
4. Thêm điều kiện:

   * Ví dụ:

     ```promQL
     100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[1m])) * 100)
     ```
   * If value > `80` trong 1 phút
5. Liên kết với **Contact Point** (Discord hoặc Email)
6. Bấm **Save**

---

## 🧪 **4. Mô phỏng cảnh báo**

* Tăng tải CPU tạm thời để kiểm tra:

```bash
yes > /dev/null &
```

* Quan sát biểu đồ và xem cảnh báo có được gửi đến Discord/Email không.

---

## ✅ **Kết quả mong đợi**

* Khi vượt ngưỡng CPU hoặc RAM:

  * Một thông báo cảnh báo gửi đến **Discord Webhook**.
  * Hoặc email được gửi đến địa chỉ đã đăng ký.

---

## 📦 Ghi chú nâng cao

* Discord Webhook có thể kèm thêm embed, hình ảnh...
* Có thể thiết lập nhiều loại cảnh báo cho từng dashboard: Disk, Load, Container count...
* Grafana còn hỗ trợ: Slack, Webhook, PagerDuty, Opsgenie, VictorOps...

---

