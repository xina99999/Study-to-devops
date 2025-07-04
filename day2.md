![CI](https://github.com/xina99999/node-api/actions/workflows/ci.yml/badge.svg)

## 🗓️ **Ngày 2 – Giao Task cụ thể: Viết pipeline CI với GitHub Actions**

### 🎯 Mục tiêu:

* Biết cấu trúc cơ bản của workflow GitHub Actions
* Tự động chạy **build/test** khi push code hoặc mở Pull Request
* Hiển thị kết quả CI trên GitHub UI

---

## ✅ **Tổng quan workflow hôm nay**

Bạn sẽ:

1. Tạo file workflow `.yml` trong repo GitHub
2. Chạy CI khi có push hoặc pull request
3. Dùng một project (Laravel, Node.js, Python…) để **build + test tự động**
4. Hiển thị trạng thái ✅/❌ trên GitHub

---

## 🧩 **Task 1: Chuẩn bị repo test CI**

### Nếu bạn dùng project Laravel:

> Clone sẵn rồi ở ngày 1 → tiếp tục dùng.

### Nếu bạn muốn dùng Python cho đơn giản (gợi ý):

```bash
git clone https://github.com/TheAlgorithms/Python.git
cd Python
```

Hoặc Node.js:

```bash
git clone https://github.com/scotch-io/node-api.git
cd node-api
```

---

## 🧩 **Task 2: Tạo workflow GitHub Actions**

### 📂 Tạo thư mục & file mới:

```bash
mkdir -p .github/workflows
nano .github/workflows/ci.yml
```

### ✍️ Nội dung mẫu cho **Node.js**:

```yaml
name: Node.js CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
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
        run: npm test
```


---

## 🧩 **Task 3: Commit và push**

```bash
git add .github/workflows/ci.yml
git commit -m "add basic CI pipeline with GitHub Actions"
git push origin main
```

→ Vào GitHub repo của bạn → tab **Actions** → thấy CI đang chạy tự động.

---

## 🧩 **Task 4 (Bonus): Gắn badge build status vào README.md**

Thêm vào đầu file `README.md`:

```markdown
![CI](https://github.com/your-username/devops-learning/actions/workflows/ci.yml/badge.svg)
```
![CI](https://github.com/xina99999/node-api/actions/workflows/ci.yml/badge.svg)
---

## 📘 Tài liệu học thêm

* GitHub Actions Quickstart: [https://docs.github.com/en/actions/quickstart](https://docs.github.com/en/actions/quickstart)
* Marketplace actions: [https://github.com/marketplace?type=actions](https://github.com/marketplace?type=actions)

---

## ✅ Kết quả kỳ vọng trong ngày

| Mục                                  | Kết quả mong muốn    |
| ------------------------------------ | -------------------- |
| ✅ Có file `.github/workflows/ci.yml` | Đúng syntax          |
| ✅ Push xong thì GitHub chạy CI       | Tab "Actions" có job |
| ✅ Test pass / fail hiện rõ           | Có log build/test    |
| ✅ (Bonus) Gắn badge CI vào README    | Hiển thị trạng thái  |


