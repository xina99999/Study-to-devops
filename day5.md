

## 🗓️ **Ngày 5 – Docker hóa Laravel trên VPS (hoặc local)**

### 🎯 Mục tiêu:

* Viết `Dockerfile` cho Laravel app
* Tạo container Laravel + PHP + nginx + MySQL
* Test chạy Laravel trên `localhost:80` hoặc IP VPS

---

## ✅ Cấu hình Docker hóa Laravel (có thể dùng `docker-compose`)

Laravel cần:

| Thành phần | Dùng gì                     |
| ---------- | --------------------------- |
| PHP        | `php:8.1-fpm`               |
| Web server | `nginx` (proxy đến PHP-FPM) |
| DB         | `mysql:5.7` hoặc `mariadb`  |
| App        | Laravel source của bạn      |
| Volume     | Mount code vào container    |

---

## 📂 Cấu trúc thư mục chuẩn

```
laravel-project/
├── Dockerfile
├── docker-compose.yml
├── nginx/
│   └── default.conf
├── .env
├── laravel_source/  → Laravel source code
```

---

## 🧩 **Bước 1: Dockerfile (Laravel App – PHP)**

Tạo `Dockerfile` trong thư mục Laravel:

```FROM php:8.2-fpm

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl \
    libonig-dev \
    libxml2-dev \
    libzip-dev \
    libpq-dev \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www

CMD ["php-fpm"]


```

---

## 🧩 **Bước 2: Cấu hình nginx reverse proxy**

Tạo `nginx/default.conf`:

```server {
    listen 80;
    index index.php index.html;
    server_name localhost;

    root /var/www/public;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
    }

    location ~ /\.ht {
        deny all;
    }
}

```

---

## 🧩 **Bước 3: docker-compose.yml**

```version: '3.8'

services:
  app:
    build:
      context: .
    container_name: laravel_app
    restart: unless-stopped
    volumes:
      - ./laravel_source:/var/www
    depends_on:
      - mysql
    networks:
      - laravel

  webserver:
    image: nginx:alpine
    container_name: nginx
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - ./laravel_source:/var/www
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - app
    networks:
      - laravel

  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: unless-stopped
    ports:
      - "3306:3306"
    environment:
      MYSQL_DATABASE: laravel
      MYSQL_ROOT_PASSWORD: root
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - laravel

networks:
  laravel:

volumes:
  mysql_data:

```

---

## 🧩 **Bước 4: Copy Laravel source vào thư mục `src/`**

```bash
mv your-laravel-folder ./laravel_source
```

---

## 🧩 **Bước 5: Chạy thử**

```bash
docker compose up -d --build
```

Mở trình duyệt:

* Nếu local: [http://localhost:80](http://localhost:80)
* Nếu VPS: [http://your-vps-ip:80](http://your-vps-ip:80)

---

## 🧩 **Bước 6: Setup Laravel**

Vào container PHP để chạy các lệnh setup:

```bash
docker exec -it laravel_app bash
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate   # nếu cần
exit
```

---
## 🧩 **Bước 7 : Di chuyển file .env vào trong larvarel_source**

##  🧩 **Bước 8 : Import database**

```bash
docker cp sql_laravel_nhan.sql mysql:/tmp/
docker exec -it mysql sh -c "mysql -u root -proot laravel < /tmp/sql_laravel_nhan.sql"
```

---
# Mở rộng cài thêm SSL và cấu hình port 443 cho HTTPS
---

## ✅ Bước 9: Chuẩn bị thư mục và mount volume chứa chứng chỉ SSL

Tạo thư mục chứa SSL ngoài project để dễ chia sẻ vào container:

```bash
sudo mkdir -p /etc/letsencrypt
sudo mkdir -p /var/lib/letsencrypt
```

Chạy Certbot trực tiếp trên máy host (ngoài container):

```bash
sudo apt update
sudo apt install certbot
```

Tạm dừng container `nginx` để giải phóng port 80:

```bash
docker stop nginx
```

Yêu cầu cấp chứng chỉ:

```bash
sudo certbot certonly --standalone -d domain_name
```

> Khi được yêu cầu nhập email và đồng ý, hãy chọn `Y` và nhập email thật để nhận thông báo hết hạn.

---

## ✅ Bước 10: Cấu hình Nginx cho SSL

### 📄 File: `nginx/default.conf`

```nginx
# Chuyển HTTP sang HTTPS
server {
    listen 80;
    server_name domain_name;
    return 301 https://$host$request_uri;
}

# HTTPS server
server {
    listen 443 ssl;
    server_name domain_name;

    ssl_certificate /etc/letsencrypt/live/domain_name/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/domain_name/privkey.pem;

    root /var/www/public;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

---

## ✅ Bước 11: Cập nhật `docker-compose.yml`

Gắn volume chứa chứng chỉ vào container nginx:

```yaml
  webserver:
    image: nginx:alpine
    container_name: nginx
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./laravel_source:/var/www
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
      - /etc/letsencrypt:/etc/letsencrypt:ro
    depends_on:
      - app
    networks:
      - laravel
```

---

## ✅ Bước 12: Khởi động lại Docker

```bash
docker compose down
docker compose up -d
```

---

## ✅ Bước 13: Kiểm tra HTTPS

Dùng `curl` hoặc trình duyệt:

```bash
curl -v https://domain_name
```

---

## ⚠️ Lưu ý:

* Chứng chỉ Let's Encrypt chỉ có hiệu lực 90 ngày → bạn nên cấu hình cron tự động gia hạn hoặc thỉnh thoảng chạy lệnh:

  ```bash
  sudo certbot renew
  ```

---



## ✅ Kết quả kỳ vọng:

| Mục                               | Kết quả                   |
| --------------------------------- | ------------------------- |
| ✅ Laravel chạy qua Docker         | Truy cập `localhost:80` |
| ✅ Dockerfile Laravel hoạt động    | `docker build` OK         |
| ✅ Web server + PHP + DB tách biệt | 3 container rõ ràng       |
| ✅ Có volume mount Laravel source  | Có thể code trực tiếp     |
| ✅ Có thể deploy lên VPS           | Dùng VPS IP:80          |

---



