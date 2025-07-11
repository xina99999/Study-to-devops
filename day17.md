

---

## 🗓 **Ngày 17 – Tìm hiểu GitHub Actions deploy qua SSH (với VPS)**

### 🎯 **Mục tiêu:**

* Tự động **deploy Laravel app từ GitHub lên VPS** mỗi khi push code lên nhánh `main`.
* Làm chủ CI/CD pipeline qua SSH sử dụng **GitHub Actions**.

---

## 🔧 **Yêu cầu trước**

* Đã có Laravel project dùng Docker và được push lên GitHub.
* VPS đã cài:

  * Docker
  * Docker Compose
  * Laravel app đã từng chạy thành công
* Có thể SSH vào VPS từ local bằng key (không dùng mật khẩu).

---

## ✅ **Task chi tiết**

### 1️⃣ **Tạo SSH Key để dùng cho GitHub Actions**

```bash
ssh-keygen -t rsa -b 4096 -C "github-actions@deploy" -f deploy_key
```

Kết quả tạo ra:

* `deploy_key`: private key (sẽ upload lên GitHub Secrets)
* `deploy_key.pub`: public key (sẽ copy lên VPS)

---

### 2️⃣ **Cài public key lên VPS**

```bash
cat deploy_key.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

> Lưu ý: bạn nên SSH bằng user có quyền `git pull`, `docker-compose`, ví dụ: `root`, `deploy`, hoặc user có trong nhóm `docker`.

---

### 3️⃣ **Thêm thông tin vào GitHub Secrets**

Vào GitHub repo → **Settings → Secrets and variables → Actions → New repository secret**, thêm các biến:

| Secret Name       | Giá trị ví dụ               |
| ----------------- | --------------------------- |
| `SSH_PRIVATE_KEY` | Nội dung của `deploy_key`   |
| `HOST`            | `123.45.67.89` *(IP VPS)*   |
| `PORT`            | `22` *(thường là 22)*       |
| `USERNAME`        | `root` *(hoặc user deploy)* |

---

### 4️⃣ **Tạo file workflow GitHub Actions**

Tạo file `.github/workflows/deploy.yml` trong Laravel repo:

```yaml
name: Deploy Laravel via SSH

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Setup SSH Agent
        uses: webfactory/ssh-agent@v0.9.0
        with:
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}

      - name: SSH into VPS and deploy
        env:
          HOST: ${{ secrets.HOST }}
          USERNAME: ${{ secrets.USERNAME }}
          PORT: ${{ secrets.PORT }}
        run: |
          ssh -o StrictHostKeyChecking=no -p $PORT $USERNAME@$HOST << 'EOF'
            cd ~/project_laravel

            echo "Pulling latest code from GitHub..."
            git pull origin main

            echo "Bringing down old containers..."
            docker compose down

            echo "Building and starting new containers..."
            docker compose up -d --build

            echo "Deployment completed."
          EOF

```

---

### 5️⃣ **Push lên GitHub để kiểm tra**

```bash
git add .
git commit -m "Test deploy to VPS via GitHub Actions"
git push origin main
```

Sau khi push, kiểm tra tab **Actions** trên GitHub để theo dõi quá trình deploy.

---

## 🧪 Kiểm tra kết quả

Trên VPS, chạy:

```bash
docker ps
```

Hoặc mở trình duyệt vào `http://<VPS-IP>:<PORT>` để xem Laravel site chạy.

---

## ✅ Kết quả mong đợi:

* Mỗi lần push lên nhánh `main`, GitHub sẽ:

  * Tự động SSH vào VPS.
  * Pull code mới.
  * Dừng và chạy lại Docker app Laravel.
* CI/CD đơn giản, tự động, miễn phí.

---

📌 **Mẹo bảo mật:**

* Không commit key vào repo.
* Key này chỉ dùng để deploy, không dùng để đăng nhập chính.

