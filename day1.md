

## 🗓️ **Ngày 1 – Giao Task cụ thể: Làm quen Git, GitHub và clone project**

### 🎯 Mục tiêu cuối ngày:

* Sử dụng thành thạo các lệnh Git cơ bản (clone, add, commit, push)
* Hiểu cách làm việc với GitHub (repo, branch, pull request)
* Có một project mẫu **Laravel hoặc Node.js** đã clone về máy và commit thử

---

## 🧩 Task 1: Cài đặt và cấu hình Git

### ✅ Việc cần làm:

* Cài đặt Git trên máy (nếu chưa có):

  ```bash
  sudo apt install git     # Ubuntu/Debian
  git --version
  ```

* Cấu hình Git lần đầu:

  ```bash
  git config --global user.name "Tên của bạn"
  git config --global user.email "email@example.com"
  ```

* Kiểm tra lại:

  ```bash
  git config --list
  ```

---

## 🧩 Task 2: Clone một repo mẫu từ GitHub

### ✅ Việc cần làm:

1. Truy cập repo mẫu (gợi ý):

   * Laravel: [https://github.com/laravel/laravel](https://github.com/laravel/laravel)
   * NodeJS API: [https://github.com/scotch-io/node-api](https://github.com/scotch-io/node-api)

2. Mở terminal và clone:

   ```bash
   git clone https://github.com/laravel/laravel.git
   cd laravel
   ```

3. Kiểm tra `.git` có tồn tại không:

   ```bash
   ls -a
   ```

---

## 🧩 Task 3: Tạo branch riêng và thử commit thay đổi

### ✅ Việc cần làm:

1. Tạo nhánh mới:

   ```bash
   git checkout -b my-first-commit
   ```

2. Thêm file `hello.txt`:

   ```bash
   echo "Xin chào DevOps!" > hello.txt
   ```

3. Add và commit:

   ```bash
   git add hello.txt
   git commit -m "add hello.txt with greeting"
   ```

---

## 🧩 Task 4: Tạo GitHub repo riêng & push lên

### ✅ Việc cần làm:

1. Truy cập GitHub → New repository

   * Tên: `devops-learning`
   * Không chọn `Initialize with README`

2. Push code:

   ```bash
   git remote add origin https://github.com/your-username/devops-learning.git
   git push -u origin my-first-commit
   ```

3. Truy cập GitHub và kiểm tra repo đã lên thành công.

---

## 🧩 Task 5 (Tùy chọn): Tạo Pull Request từ branch

1. Trên GitHub, vào `Pull Request → New Pull Request`
2. So sánh `my-first-commit` với `main`
3. Viết mô tả: “Thêm file hello.txt để test commit”

---

## 📘 Gợi ý tổng hợp tài liệu học

* Git chính thức: [https://git-scm.com/doc](https://git-scm.com/doc)
* GitHub Basic: [https://docs.github.com/en/get-started/quickstart](https://docs.github.com/en/get-started/quickstart)
* Git Cheat Sheet (PDF): [https://education.github.com/git-cheat-sheet-education.pdf](https://education.github.com/git-cheat-sheet-education.pdf)

---

## ✅ Kết quả kỳ vọng trong ngày

| Mục                          | Kết quả mong muốn              |
| ---------------------------- | ------------------------------ |
| ✅ Đã cài Git                 | `git --version` OK             |
| ✅ Clone repo mẫu thành công  | `cd laravel`                   |
| ✅ Tạo và commit nhánh riêng  | Có file `hello.txt` đã commit  |
| ✅ Push repo riêng lên GitHub | Public repo: `devops-learning` |
| ✅ Tạo Pull Request (nếu có)  | Hiểu cách gửi merge request    |

---

