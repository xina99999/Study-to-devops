
## 🎯 **Mục tiêu**

* Tạo EC2 instance Ubuntu trên AWS.
* SSH vào máy chủ.
* Cài đặt Docker và Docker Compose.
* Kiểm tra môi trường sẵn sàng để chạy container.

---

## 🧩 **Chi tiết các task Ngày 8**

### 🔹 **1. Tạo EC2 Instance (Ubuntu)**

* Vào [AWS EC2 Dashboard](https://console.aws.amazon.com/ec2/)
* Click `Launch Instance`
* Chọn:

  * **Name**: `laravel-deploy`
  * **AMI**: Ubuntu Server 22.04 LTS (hoặc 20.04)
  * **Instance type**: `t2.micro` (Free Tier)
  * **Key pair (login)**: Tạo mới hoặc chọn key `.pem` sẵn có
  * **Network settings**:

    * Cho phép cổng: `22`, `80`, `443`, `8000` (nếu chạy Laravel port custom)
  * **Storage**: >= 10 GB
* Bấm `Launch Instance`

---

### 🔹 **2. SSH vào EC2 Instance**

* Đảm bảo `.pem` file có quyền phù hợp:

```bash
chmod 400 your-key.pem
```

* SSH vào EC2:

```bash
ssh -i "your-key.pem" ubuntu@<EC2-PUBLIC-IP>
```

> 📌 Gợi ý: EC2 IP có thể lấy từ AWS Console → Instances → Public IPv4 address.

---

### 🔹 **3. Cài đặt Docker**

Chạy các lệnh sau trong EC2:

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

# Add Docker GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Add Docker repo
echo \
  "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Cài Docker Engine
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Bật Docker service
sudo systemctl enable docker
sudo systemctl start docker

# Thêm quyền cho user hiện tại (tùy chọn)
sudo usermod -aG docker ubuntu
```

> 🌀 Nếu thêm user vào nhóm `docker`, bạn cần `exit` rồi SSH lại để áp dụng.

---

### 🔹 **4. Kiểm tra Docker**

```bash
docker --version
docker run hello-world
```

### 🔹 **5. (Tùy chọn) Cài Docker Compose nếu cần riêng**

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.7/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

---

## ✅ Kết quả mong đợi

* EC2 Ubuntu server hoạt động.
* Đăng nhập SSH thành công.
* Docker và Docker Compose hoạt động bình thường.
* Sẵn sàng `docker pull` hoặc `docker run`.

---


