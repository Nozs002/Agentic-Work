# Confidence Evaluation Policy

Quy định cách thức đánh giá mức độ tự tin (Confidence Score) của thuật toán lập kế hoạch (Planner Agent) và các hành động rẽ nhánh tương ứng.

## 1. Mục đích
Confidence Score (thang điểm từ 0.0 đến 1.0) là thước đo định lượng để Planner Agent tự đánh giá mức độ chắc chắn của bản thân trước khi xuất bản một Kế hoạch Thực thi (Execution Plan). Mục tiêu là ngăn chặn "ảo giác" (hallucination) và tránh việc Agent tự đoán mò (guess) khi thiếu thông tin cơ sở.

## 2. Các Yếu Tố Đánh Giá (Scoring Factors)
Planner Agent phải tính toán Confidence Score dựa trên các yếu tố sau:
1. **Độ rõ ràng của Yêu cầu (Instruction Clarity):** Yêu cầu của người dùng có cụ thể không? Có bị mâu thuẫn nội tại không? Có Definition of Done rõ ràng không?
2. **Sự đầy đủ của Bối cảnh (Context Completeness):** Dữ liệu `contextData` (mã nguồn, tài liệu liên quan) cung cấp có đủ để đưa ra giải pháp kỹ thuật không?
3. **Độ phức tạp (Complexity & Risk):** Bài toán có quá lớn, ẩn chứa nhiều rủi ro (breaking changes, db migration, bảo mật) chưa được lường trước không?

## 3. Ngưỡng Ra Quyết Định (Thresholds & Actions)

### 🟢 Vùng An Toàn (Confidence $\ge$ 0.8)
- **Đánh giá:** Thông tin hoàn toàn rõ ràng, bối cảnh đầy đủ, rủi ro trong tầm kiểm soát.
- **Hành động:** 
  - Tiến hành phân rã và vẽ đồ thị Task DAG.
  - Đóng gói `ExecutionPlanContract` với trạng thái `planStatus: "READY"`.
  - Trả về cho Orchestrator để tiến hành phân phối Task.

### 🟡 Vùng Thiếu Tri Thức (0.5 $\le$ Confidence $<$ 0.8)
- **Đánh giá:** Yêu cầu khá rõ ràng nhưng đang thiếu một số chi tiết kỹ thuật hoặc bối cảnh phụ trợ (ví dụ: cần tham khảo một API nội bộ, xem file cấu trúc database, hoặc đọc log).
- **Hành động:**
  - **Tạm dừng** vẽ đồ thị Task DAG.
  - Đóng gói `ExecutionPlanContract` với trạng thái `planStatus: "NEED_KNOWLEDGE"`.
  - Bổ sung trường `knowledgeRequest` chứa các yêu cầu cụ thể cần RAG (Retrieval-Augmented Generation).
  - Trả về Orchestrator để Orchestrator tự điều phối gọi Knowledge Agent.

### 🟠 Vùng Rủi Ro / Mơ Hồ (0.3 $\le$ Confidence $<$ 0.5)
- **Đánh giá:** Yêu cầu quá sơ sài, chứa mâu thuẫn về mặt logic nghiệp vụ, hoặc thiếu ranh giới rõ ràng khiến Planner không thể tự quyết định giải pháp.
- **Hành động:**
  - Kích hoạt [Clarification Policy](./clarification.md).
  - Trả về `planStatus: "NEED_CLARIFICATION"` để Orchestrator chuyển giao cho BA Agent phỏng vấn và làm rõ yêu cầu.

### 🔴 Vùng Nguy Hiểm (Confidence $<$ 0.3)
- **Đánh giá:** Cực kỳ thiếu thông tin, hoặc bài toán tiềm ẩn rủi ro hệ thống đặc biệt nghiêm trọng (VD: xóa/sửa đổi dữ liệu diện rộng, đụng chạm đến core framework mà không có hướng dẫn cụ thể).
- **Hành động:**
  - Kích hoạt cơ chế **Human-in-the-Loop** (Chờ con người xác nhận).
  - Đóng gói `ExecutionPlanContract` với trạng thái `planStatus: "ASK_USER"`.
  - Ghi rõ lý do vào trường `risks` và yêu cầu Orchestrator dừng lập kế hoạch, chờ người dùng can thiệp trực tiếp.

## 4. Báo Cáo Confidence
Bất kể Kế hoạch nằm ở vùng nào, Planner Agent **BẮT BUỘC** phải luôn đính kèm giá trị `confidence` (số thập phân) cùng các mảng `risks`, `assumptions` vào bên trong `ExecutionPlanContract`. Không bao giờ được phép lược bỏ thông số này.
