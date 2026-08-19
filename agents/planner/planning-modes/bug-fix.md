# Bug Fix Planning Mode

Quy trình lập kế hoạch chuyên biệt cho việc phát hiện, khoanh vùng và sửa lỗi (Bug/Defect) đang tồn tại.

## Đặc điểm của Mode
- **Tính chất DAG:** Ngắn, lặp đi lặp lại nhanh (Short Iteration). Ưu tiên tái hiện lỗi trước khi sửa.
- **Rủi ro:** Gây ra lỗi mới trong lúc sửa lỗi cũ (Chữa lợn lành thành lợn què).
- **Vai trò chủ đạo (Assigned Roles):** `QA_AGENT` (Tái hiện lỗi), `CODER_AGENT` (Fix lỗi).

## Hướng dẫn Phân rã (Decomposition Guidelines)
Mô hình lý tưởng nhất cho Bug Fix DAG là phương pháp **Test-Driven Development (TDD)**:
1. **Reproduce & Test (Khoanh vùng):** Task 1 giao cho `QA_AGENT` hoặc `CODER_AGENT` viết một Unit Test tự động thất bại (Fail) chứng minh sự tồn tại của lỗi, dựa trên mô tả.
2. **Fix Code (Sửa lỗi):** Task 2 giao cho `CODER_AGENT` sửa mã nguồn sao cho bài Test ở Task 1 chuyển từ Fail sang Pass.
3. **Verify:** Chạy lại toàn bộ test suite hiện hành để đảm bảo không bị regression.

## Lưu ý Quan trọng
- Kế hoạch phải cực kỳ tập trung, không đính kèm việc "tiện tay" refactor hay thêm tính năng mới vào DAG của Bug Fix.
- Nếu mô tả bug (Bug report) không có các bước tái hiện (Steps to reproduce) hoặc Error Logs, Planner nên cân nhắc kích hoạt [Clarification Policy](../policies/clarification.md) để hỏi người dùng/hệ thống cung cấp thêm chi tiết trước khi giải quyết.
