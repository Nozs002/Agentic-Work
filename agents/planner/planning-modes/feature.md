# Feature Planning Mode

Quy trình lập kế hoạch chuyên biệt cho việc phát triển một tính năng mới hoàn chỉnh (End-to-End).

## Đặc điểm của Mode
- **Tính chất DAG:** Khuyến khích tối đa việc **thực thi song song (Parallel Execution)** để giảm thời gian hoàn thành. Ví dụ: Backend API và Frontend UI có thể làm song song nếu đã chốt API Contract (Mock).
- **Rủi ro:** Thiếu đồng bộ giữa Backend và Frontend, hoặc tích hợp lỗi, UX kém.
- **Vai trò chủ đạo (Assigned Roles):** `BA_AGENT` (nếu cần spec), `DATABASE_AGENT`, `CODER_AGENT` (Backend/Frontend), `QA_AGENT`.

## Hướng dẫn Phân rã (Decomposition Guidelines)
Khi lập kế hoạch cho một Feature, Planner Agent tuân theo quy trình mẫu:
1. **Data Layer:** Task 1: Thiết kế và cập nhật DB Schema (Nếu có thay đổi CSDL).
2. **API Contract (Tùy chọn nhưng khuyến nghị):** Task 2: Định nghĩa API JSON Schema (Swagger/OpenAPI) làm bản lề.
3. **Implementation (Parallel):** 
   - Task 3a: `CODER_AGENT` làm Backend dựa trên API Contract.
   - Task 3b: `CODER_AGENT` làm Frontend dựa trên API Contract.
4. **Quality Assurance:** Task 4: `QA_AGENT` viết Unit Test hoặc E2E Test cho tính năng vừa làm.

## Lưu ý Quan trọng
- Luôn kiểm tra xem `instruction` đã có đủ UI/UX requirement hay Business rules chưa. Nếu quá lỏng lẻo, phải rẽ nhánh sang Clarification Policy.
- Gom các task độc lập vào chung một mảng `parallelGroups` trong `ExecutionPlanContract`.
