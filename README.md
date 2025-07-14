# 🚀 Study-to-DevOps – Hành trình học DevOps qua Laravel

Dự án này là nhật ký học tập và thực hành DevOps trong 5 tuần, xoay quanh việc triển khai ứng dụng **Laravel** với các công cụ như GitHub Actions, Docker, AWS EC2, Prometheus, Grafana và nhiều hơn nữa.

---

## 📆 Lộ trình học theo tuần

| Tuần | Chủ đề chính                         | Kết quả mong đợi                     |
|------|--------------------------------------|--------------------------------------|
| 🟦 Tuần 1 | Git, CI/CD, Docker cơ bản           | Nắm kỹ năng cơ bản DevOps & Docker   |
| 🟩 Tuần 2 | DockerHub, EC2, triển khai ứng dụng | Tự tay build, push, run app trên EC2 |
| 🟨 Tuần 3 | Giám sát hệ thống                  | Đọc hiểu metrics, tạo dashboard      |
| 🟥 Tuần 4 | CI/CD Full Auto với SSH            | Pipeline triển khai hoàn chỉnh       |
| 🟪 Tuần 5 | Tổng kết, chuẩn hóa & demo          | Hoàn thiện và demo toàn hệ thống     |

---

## 📦 Tuần 1: Git, CI/CD, Docker hóa ứng dụng

| Ngày | Task                                               | Mục tiêu                                 |
|------|----------------------------------------------------|------------------------------------------|
| 1    | Tìm hiểu Git, GitHub, clone repo mẫu               | Biết dùng Git clone, commit, push        |
| 2    | Viết GitHub Actions pipeline build + test đơn giản | Tự động hóa kiểm tra ứng dụng            |
| 3    | Hoàn thiện workflow + thêm notify (email/slack)    | Quản lý kết quả CI/CD                    |
| 4    | Học Docker cơ bản: build, run, port mapping        | Làm quen Docker CLI                      |
| 5    | Viết Dockerfile cho project, test local            | Đóng gói ứng dụng Laravel với Docker     |


---

## 🐳 Tuần 2: DockerHub + Deploy AWS EC2

| Ngày | Task                                                | Mục tiêu                                 |
|------|-----------------------------------------------------|------------------------------------------|
| 6    | Đăng ký DockerHub, push image lên registry          | Quản lý Docker Image                     |
| 7    | Viết README.md hướng dẫn run Docker image           | Giao tiếp kỹ thuật tốt                   |
| 8    | Tạo EC2 instance, SSH và cài Docker                 | Làm chủ EC2                              |
| 9    | Pull image từ DockerHub, run thử, mở port           | Deploy thủ công                          |
| 10   | Viết tài liệu triển khai EC2 & xử lý lỗi firewall   | Mô tả rõ ràng hạ tầng triển khai         |



---

## 📊 Tuần 3: Giám sát hệ thống

| Ngày | Task                                                | Mục tiêu                                 |
|------|-----------------------------------------------------|------------------------------------------|
| 11   | Cài Prometheus bằng Docker, chỉnh scrape interval   | Làm quen giám sát metric                 |
| 12   | Cài Grafana, connect Prometheus datasource          | Hiển thị dữ liệu Prometheus              |
| 13   | Tạo dashboard CPU, Memory cho container             | Visual hóa thông tin hệ thống            |
| 14   | Cấu hình lưu config + persistent volume             | Làm quen với volumes                     |
| 15   | Viết hướng dẫn sử dụng & báo cáo                    | Trình bày quy trình giám sát             |


---

## 🚀 Tuần 4: CI/CD full auto + SSH Deploy

| Ngày | Task                                                | Mục tiêu                                 |
|------|-----------------------------------------------------|------------------------------------------|
| 16   | Tìm hiểu GitHub Actions deploy qua SSH              | Làm chủ pipeline                         |
| 17   | Tạo SSH key, thêm vào GitHub Secrets                | Bảo mật và tự động hóa                   |
| 18   | Viết `deploy.yml`: build → push → SSH → run         | Triển khai hoàn chỉnh                    |
| 19   | Test pipeline toàn diện, xử lý lỗi                  | Quản lý lifecycle container              |
| 20   | Tạo script rollback khi deploy lỗi                  | Tư duy production-safe                   |



---

## 🧪 Tuần 5: Tổng kết & Nâng cao

| Ngày | Task                                                | Mục tiêu                                 |
|------|-----------------------------------------------------|------------------------------------------|
| 21   | Review toàn bộ quy trình, note lesson learned       | Tự đánh giá quá trình học                |
| 22   | Chuẩn hóa tài liệu: README.md, sơ đồ pipeline       | Giao tiếp rõ ràng                        |
| 23   | Chuẩn bị demo nội bộ (quay video/thuyết trình)      | Trình bày chuyên nghiệp                  |
| 24   | Fix bug cuối, hỏi mentor (nếu cần)                  | Hoàn thiện sản phẩm                      |
| 25   | Demo trước team + nhận feedback                     | Kết thúc giai đoạn đào tạo               |



---

## 📘 Ghi chú

- Bạn có thể xem các ngày học chi tiết trong thư mục `docs/`
- Mỗi ngày là một file markdown ví dụ: `docs/day01.md`, `day02.md`,...
- Định dạng chuẩn giúp dễ chia sẻ, bảo trì và mở rộng

---

## 📄 Giấy phép

Tài liệu dành cho mục tiêu học tập cá nhân, chia sẻ không lợi nhuận.

---
## 📘 Mở rộng


## 💬 Góp ý?

Nếu bạn thấy hữu ích, hãy ⭐ repo hoặc mở PR đóng góp thêm nội dung nhé!
