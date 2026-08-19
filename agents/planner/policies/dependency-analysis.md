# Dependency Analysis Policy

Quy định về việc phân tích sự phụ thuộc dữ liệu (Data Dependency) giữa các Task để đảm bảo Đồ thị (DAG) chạy đúng thứ tự logic.

## 1. Bản chất của Sự Phụ Thuộc
Sự phụ thuộc (Dependency) trong hệ thống Đa tác tử không chỉ là thứ tự thời gian, mà chủ yếu là **Sự Phụ Thuộc Về Mặt Dữ Liệu (Data Contract)**. 
- Task B phụ thuộc vào Task A có nghĩa là: **Task B CẦN `expectedOutput` (ví dụ: file code, schema, API spec) của Task A làm đầu vào (input) để có thể hoạt động.**

## 2. Quy tắc Thiết lập Phụ thuộc (Dependency Rules)
1. **Tránh Phụ Thuộc Vòng (No Circular Dependencies):** Đồ thị phải luôn là DAG (Directed Acyclic Graph). Nếu Task A đợi B, B đợi C, thì C tuyệt đối không được đợi A. Nếu vi phạm, hệ thống sẽ Deadlock.
2. **Khai báo Tường minh:** Sử dụng mảng `dependencies: ["task-id-1", "task-id-2"]` bên trong object của task bị phụ thuộc.
3. **Phụ thuộc Chuỗi (Chaining):** Nếu Task C phụ thuộc B, và B phụ thuộc A, thì C chỉ cần khai báo phụ thuộc B. Hệ thống Workflow Engine sẽ tự động nội suy ra A. Đừng khai báo thừa (C phụ thuộc cả A và B) trừ khi C thực sự cần tham chiếu trực tiếp output từ A.

## 3. Các Mẫu Phụ Thuộc Phổ Biến (Common Dependency Patterns)
- **Mẫu Chuỗi Tuyến Tính (Linear Chain):** 
  `DB Schema (A) -> Backend API (B) -> Frontend UI (C)`.
- **Mẫu Hình Quạt (Fan-out):**
  Một Task gốc tạo ra dữ liệu bản lề cho nhiều task khác làm song song. 
  `API Contract (A) -> Backend (B) & Frontend (C)`.
- **Mẫu Thu Thập (Fan-in):**
  Nhiều task chạy xong mới kích hoạt một task tổng hợp cuối cùng.
  `Backend (A) & Frontend (B) -> End-to-End Test (C)`.

## 4. Xử lý Lỗi Phụ Thuộc (Anti-Patterns)
- **Cố gắng song song hóa ép buộc:** Để hai task hoàn toàn độc lập chạy cùng lúc nhưng thực chất chúng cùng ghi/sửa chung một file mã nguồn (Có thể gây ra Git Merge Conflict hoặc File Lock). Hãy chuyển chúng thành phụ thuộc tuần tự.
