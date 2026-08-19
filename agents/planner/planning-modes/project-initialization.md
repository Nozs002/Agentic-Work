# Project Initialization Mode

Quy trình lập kế hoạch chuyên biệt cho giai đoạn khởi tạo dự án mới hoặc một microservice/module hoàn toàn mới.

## Đặc điểm của Mode
- **Tính chất DAG:** Thường có tính tuần tự cao ở giai đoạn đầu (ví dụ: Phải có Architecture Spec -> Khởi tạo Repository -> Cài đặt Database -> Cấu hình CI/CD). Ít có khả năng chạy song song ngay từ đầu.
- **Rủi ro:** Cấu hình sai nền tảng sẽ ảnh hưởng toàn bộ kiến trúc dự án về sau.
- **Vai trò chủ đạo (Assigned Roles):** `ARCHITECT_AGENT`, `DEVOPS_AGENT`, `DATABASE_AGENT`.

## Hướng dẫn Phân rã (Decomposition Guidelines)
Khi nhận diện `instruction` thuộc loại khởi tạo dự án, Planner phải ưu tiên tạo các sub-tasks theo thứ tự ưu tiên sau:
1. **System Design / Architecture:** Task đầu tiên luôn là giao cho `ARCHITECT_AGENT` chốt kiến trúc, công nghệ và thư mục (Folder Structure).
2. **Infrastructure / Database:** Task tiếp theo là thiết kế DB Schema hoặc cài đặt môi trường.
3. **Core Scaffolding:** Task thiết lập boilerplate code và các base classes.
4. **CI/CD & Tooling:** (Tùy chọn) Task thiết lập linter, test framework, pipelines.

## Lưu ý Quan trọng
- Không chia nhỏ đến mức độ tính năng (Feature) trong lần chạy này. Mục tiêu chỉ là có một bộ khung (Skeleton) chạy được.
