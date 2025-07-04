
## 🗓️ **Ngày 3 – Giao Task cụ thể: Notification cho GitHub Actions**

### 🎯 Mục tiêu:

* Biết cách **tối ưu workflow**: phân chia rõ job, step
* **Thêm thông báo** khi build success/failure
* Gửi notification qua **email** (cơ bản) hoặc **Slack/Discord** (nâng cao)

---

## ✅ Tổng quan nội dung hôm nay

1. Refactor workflow hôm qua: chia job rõ hơn
2. Thêm logic báo lỗi khi test fail
3. Gửi email (cơ bản)
4. Gửi Slack notification (nâng cao – optional)

---

## 🧩 **Task 1: Refactor CI Workflow thành dạng rõ ràng hơn**

Mở file `.github/workflows/ci.yml`, chỉnh lại cho rõ ràng:

```yaml
name: Node.js CI Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    name: Run Unit Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: |
          npm test
```

> 💡 Nếu test fail, job sẽ dừng ở bước `Run tests`.

---

## 🧩 **Task 2: Gửi Email thông báo khi job thất bại**

### ✅ Cách đơn giản: Dùng action có sẵn `dawidd6/action-send-mail`

**Thêm bước cuối sau bước test:**

```yaml
      - name: Send Email on Failure
        if: failure()
        uses: dawidd6/action-send-mail@v3
        with:
          server_address: smtp.gmail.com
          server_port: 465
          username: ${{ secrets.EMAIL_USER }}
          password: ${{ secrets.EMAIL_PASS }}
          subject: GitHub Action Failed 🚨
          body: "Build failed on ${{ github.repository }} - commit ${{ github.sha }}"
          to: your-email@example.com
          from: GitHub CI
```

### ✅ Tạo secrets:

* `EMAIL_USER`: địa chỉ email gửi (ví dụ: `ci.bot@gmail.com`)
* `EMAIL_PASS`: App Password (nếu dùng Gmail → bật 2FA + App Password)
* Vào GitHub repo → Settings → Secrets → New Repository Secret

---

## 🧩 **Task 3 (Bonus): Gửi thông báo về Slack hoặc Discord**

### 🟦 Slack:

1. Tạo webhook URL Slack:

   * Vào Slack → App → Incoming Webhook → Tạo webhook mới

2. Thêm vào GitHub Secrets: `SLACK_WEBHOOK_URL`

3. Thêm bước cuối vào workflow:

```yaml
      - name: Notify Slack
        if: always()
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          fields: repo,message,commit,author
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

---

## 📘 Tài liệu tham khảo:

* [https://github.com/marketplace/actions/send-email](https://github.com/marketplace/actions/send-email)
* [https://github.com/8398a7/action-slack](https://github.com/8398a7/action-slack)
* [GitHub Actions expressions](https://docs.github.com/en/actions/learn-github-actions/expressions)

---

## ✅ Kết quả mong đợi hôm nay:

| Mục                                         | Kết quả    |
| ------------------------------------------- | ---------- |
| ✅ Workflow chia rõ ràng các bước            | ✔️         |
| ✅ Gửi email khi CI fail                     | ✔️         |
| ✅ Gửi Slack/Discord (nếu có)                | (optional) |
| ✅ Push lên GitHub, test thử CI fail/success | ✔️         |

---

## 🔁 Đề xuất kiểm thử:

* Cố tình sửa file test cho **test fail** → xem có nhận mail hoặc Slack không
* Sau đó sửa lại cho **pass lại** → check lại

---

* Screenshot email/slack notification (che thông tin nếu cần)
* Câu hỏi nếu có lỗi gửi mail/slack mình giúp debug luôn nhé!

Bạn dùng Gmail hay Slack để mình hỗ trợ tạo webhook/email dễ hơn?
