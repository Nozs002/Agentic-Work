# Change Request (CR) Planning Mode

Quy trình lập kế hoạch chuyên biệt cho các yêu cầu thay đổi logic nghiệp vụ, nâng cấp, hoặc tái cấu trúc (Refactor) hệ thống hiện tại.

## Đặc điểm của Mode
- **Tính chất DAG:** Phụ thuộc rất nặng vào hệ thống cũ. Đòi hỏi phải phân tích tác động (Impact Analysis) cẩn thận trước khi code. Rất ít cơ hội song song ở giai đoạn đầu.
- **Rủi ro:** Rủi ro cao nhất (Breaking Changes). Dễ làm hỏng các tính năng khác đang hoạt động tốt (Regression).
- **Vai trò chủ đạo (Assigned Roles):** `ARCHITECT_AGENT` (Impact Analysis), `CODER_AGENT`, `QA_AGENT`.

## Hướng dẫn Phân rã (Decomposition Guidelines)
Thay vì nhảy vào code ngay, DAG của CR phải luôn bắt đầu bằng việc đọc và phân tích mã nguồn cũ:
1. **Impact Analysis (Phân tích tác động):** Yêu cầu Agent đọc code cũ để khoanh vùng chính xác những file/module nào sẽ bị ảnh hưởng bởi sự thay đổi này.
2. **Implementation (Sửa code):** Cập nhật code tại các khu vực đã khoanh vùng một cách cẩn thận.
3. **Regression Testing:** Cập nhật test cũ hoặc viết thêm Test mới để đảm bảo hệ thống không bị "vỡ" ở các tính năng không liên quan.

## Lưu ý Quan trọng
- **Độ Tự tin (Confidence):** Thường rất thấp nếu không có đủ `contextData`. Nếu chưa có code cũ/ngữ cảnh trong context, Planner **BẮT BUỘC** phải rẽ nhánh trả về `planStatus: "NEED_KNOWLEDGE"` (Yêu cầu Knowledge Agent cung cấp code) trước khi vẽ DAG.
