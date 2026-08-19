# Task Decomposition Policy

Quy định và hướng dẫn cách Planner Agent phân rã (decomposition) bài toán phức tạp thành các đơn vị Task nguyên tử (Atomic Tasks).

## 1. Nguyên Tắc Cốt Lõi (Core Principles)
- **Tính Nguyên Tử (Atomicity):** Mỗi task chỉ nên giải quyết MỘT trách nhiệm duy nhất (Single Responsibility). Ví dụ: Không gộp việc "Thiết kế DB" và "Viết API" vào cùng một task.
- **Giới hạn Độ Mịn (Granularity Limit):** Khuyến nghị phân rã từ 1 đến tối đa 7 sub-tasks cho một Đồ thị (DAG). Nếu vượt quá số lượng này, Planner nên đánh giá lại xem có cần kích hoạt Phase Plan (Epic Escalation Policy) không.
- **Tập Trung Vào "Cái Gì" (WHAT), Không Phải "Thế Nào" (HOW):** Task description chỉ nên định nghĩa mục tiêu và ranh giới, để lại phần thiết kế giải pháp chi tiết cho các Specialist Agents tự suy luận.

## 2. Tiêu Chuẩn Đầu Ra Của Một Task
Mỗi sub-task được phân rã phải có đầy đủ các thông tin sau trong JSON (theo `ExecutionPlanContract`):
- `taskId`: Định danh duy nhất (vd: `task-01-db`, `task-02-backend`).
- `description`: Mô tả rõ ràng công việc cần làm.
- `assignedRole`: Phải gán đúng vai trò (VD: `CODER_AGENT`, `DATABASE_AGENT`, `QA_AGENT`). Tuyệt đối không để trống.
- `expectedOutput`: Xác định rõ "Definition of Done" của task này (VD: "Schema file được tạo", "API Endpoint trả về 200 OK").

## 3. Các Anti-Pattern Cần Tránh
- **Task Quá To (Monolithic Task):** "Làm toàn bộ tính năng Đăng nhập" (Phải tách thành DB, Backend, Frontend).
- **Task Quá Nhỏ (Micro-Tasking):** "Tạo file A", "Tạo file B" (Hãy gộp thành "Khởi tạo cấu trúc thư mục module X").
- **Gán sai Role:** Giao việc viết unit test cho `DATABASE_AGENT` thay vì `QA_AGENT` hoặc `CODER_AGENT`.
