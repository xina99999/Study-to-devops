

## 🗓️ **Ngày 5 – Docker hóa Laravel trên VPS (hoặc local)**

### 🎯 Mục tiêu:

* Viết `Dockerfile` cho Laravel app
* Tạo container Laravel + PHP + nginx + MySQL
* Test chạy Laravel trên `localhost:8000` hoặc IP VPS

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
├── src/  → Laravel source code
```

---

## 🧩 **Bước 1: Dockerfile (Laravel App – PHP)**

Tạo `Dockerfile` trong thư mục Laravel:

```Dockerfile
FROM php:8.1-fpm

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential libpng-dev libjpeg-dev libonig-dev libxml2-dev zip unzip curl git \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set working dir
WORKDIR /var/www

# Copy app source code
COPY . .

# Install dependencies
RUN composer install

# Set permissions
RUN chown -R www-data:www-data /var/www \
    && chmod -R 755 /var/www/storage

EXPOSE 9000
CMD ["php-fpm"]
```

---

## 🧩 **Bước 2: Cấu hình nginx reverse proxy**

Tạo `nginx/default.conf`:

```nginx
server {
    listen 80;
    index index.php index.html;
    root /var/www/public;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /var/www/public$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

---

## 🧩 **Bước 3: docker-compose.yml**

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    image: laravel-app
    container_name: laravel_app
    restart: unless-stopped
    working_dir: /var/www
    volumes:
      - ./src:/var/www
    depends_on:
      - db

  web:
    image: nginx:latest
    container_name: nginx_web
    restart: unless-stopped
    ports:
      - "8000:80"
    volumes:
      - ./src:/var/www
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - app

  db:
    image: mysql:5.7
    container_name: mysql_db
    restart: unless-stopped
    environment:
      MYSQL_DATABASE: laravel
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_USER: laravel
      MYSQL_PASSWORD: secret
    ports:
      - "3306:3306"
    volumes:
      - dbdata:/var/lib/mysql

volumes:
  dbdata:
```

---

## 🧩 **Bước 4: Copy Laravel source vào thư mục `src/`**

```bash
mv your-laravel-folder ./src
```

---

## 🧩 **Bước 5: Chạy thử**

```bash
docker compose up -d --build
```

Mở trình duyệt:

* Nếu local: [http://localhost:8000](http://localhost:8000)
* Nếu VPS: [http://your-vps-ip:8000](http://your-vps-ip:8000)

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

## ✅ Kết quả kỳ vọng:

| Mục                               | Kết quả                   |
| --------------------------------- | ------------------------- |
| ✅ Laravel chạy qua Docker         | Truy cập `localhost:8000` |
| ✅ Dockerfile Laravel hoạt động    | `docker build` OK         |
| ✅ Web server + PHP + DB tách biệt | 3 container rõ ràng       |
| ✅ Có volume mount Laravel source  | Có thể code trực tiếp     |
| ✅ Có thể deploy lên VPS           | Dùng VPS IP:8000          |

---



Nếu bạn muốn, mình có thể tạo **template repo GitHub Laravel + Docker + Nginx** sẵn cho bạn clone.

Bạn muốn triển khai trực tiếp trên VPS ngay hôm nay chứ? Nếu có, mình sẽ hỗ trợ từng bước cho bạn lên production!
