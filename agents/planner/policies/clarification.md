# Clarification Policy (Chính sách Yêu cầu Làm rõ)

Quy định cách Planner Agent xử lý các yêu cầu mơ hồ, mâu thuẫn hoặc thiếu thông tin đầu vào mang tính quyết định, dẫn đến việc không thể lập kế hoạch (Task DAG) một cách an toàn.

## 1. Khi nào kích hoạt Clarification Policy?
Planner Agent **BẮT BUỘC** dừng việc lập kế hoạch và kích hoạt chính sách này khi rơi vào một trong các trường hợp sau:

- **Ngưỡng Confidence:** Điểm tự tin rơi vào vùng Rủi ro / Mơ hồ ($0.3 \le \text{Confidence} < 0.5$).
- **Thiếu Ranh giới (Missing Boundaries):** Yêu cầu (Instruction) quá rộng, không xác định rõ điểm dừng (Ví dụ: "Làm cho app chạy nhanh hơn", "Cải thiện UI").
- **Mâu thuẫn Logic (Logical Conflict):** Người dùng yêu cầu hai điều trái ngược nhau hoặc yêu cầu vi phạm trực tiếp các tiêu chuẩn (Architecture/Security) hiện có của hệ thống.
- **Thiếu Input Cốt lõi:** Yêu cầu làm một tính năng phức tạp nhưng hoàn toàn không mô tả luồng nghiệp vụ (Business Flow), phân quyền (Roles), hoặc cấu trúc dữ liệu cơ bản.

## 2. Quy trình Xử lý (Delegation Workflow)

Khi thỏa mãn điều kiện ở phần 1, Planner Agent KHÔNG ĐƯỢC "đoán mò" (hallucinate) để cố sinh ra một bản kế hoạch. Thay vào đó, phải thực hiện các bước:

1. **Dừng sinh DAG:** Tuyệt đối không sinh ra mảng `tasks` hay `dependencies`. Bỏ qua bước 6, 7, 8 trong SOP. Đặt đồ thị DAG ở trạng thái trống (`dag = null` hoặc rỗng).
2. **Cập nhật Trạng thái:** Đặt `planStatus: "NEED_CLARIFICATION"`.
3. **Đóng gói Câu hỏi (Clarification Request):** 
   - Tổng hợp danh sách các câu hỏi cụ thể, trọng tâm vào những điểm mù (blind spots) ngăn cản việc lập kế hoạch.
   - Phân tích và chỉ ra chính xác điểm mâu thuẫn logic (nếu có).
   - Đóng gói danh sách này vào trường `clarificationRequest` (hoặc thông qua `risks`/`assumptions` nếu schema yêu cầu).
4. **Chuyển giao (Return to Orchestrator):** Đóng gói `ExecutionPlanContract` và gửi về Orchestrator. 

## 3. Hành vi của Hệ thống (System Context)

Khi Orchestrator nhận được gói tin có `planStatus: "NEED_CLARIFICATION"`, nó sẽ điều hướng quy trình như sau:
1. Đọc nội dung các câu hỏi cần làm rõ từ Planner.
2. Điều hướng (Route) Task hiện tại sang **BA Agent** (Business Analyst Agent). BA Agent sẽ sử dụng các câu hỏi này để chủ động phỏng vấn người dùng.
3. Chỉ sau khi BA Agent chốt được Requirement Spec rõ ràng, Orchestrator mới tái kích hoạt (Re-invoke) Planner Agent với ngữ cảnh đã được nâng cấp (Updated Context). Lúc này Planner sẽ lập kế hoạch dựa trên Spec mới.
