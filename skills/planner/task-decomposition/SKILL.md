---
name: task-decomposition
description: Kỹ năng phân tích ngữ cảnh, phân rã mục tiêu thành Đồ thị Công việc (Task DAG) và lập Bản Kế Hoạch Thực Thi (Execution Plan) chuẩn LangGraph.
---

# 🎯 Capability: Task Decomposition & Planning

## 1. Overview
Kỹ năng này hướng dẫn Planner Agent cách tư duy theo luồng 10 bước chuẩn LangGraph để phân rã một yêu cầu phức tạp thành một Bản Kế Hoạch Thực Thi (Execution Plan) hoàn chỉnh, có đánh giá rủi ro, giả định và xác định các nhóm thi công song song (Parallel Groups).

## 2. Capability Workflow (Luồng Tư duy LangGraph)

Khi được gọi, hãy tuần tự thực hiện tư duy theo các bước sau trước khi output:

1. **Receive Goal & Context:** Đọc kỹ `instruction` và `contextData`.
2. **Validate:** Kiểm tra tính hợp lệ của chỉ thị.
3. **Need Knowledge?:** 
   - Kiểm tra xem mình có đang bị "mù" thông tin dự án không.
   - Nếu thiếu bối cảnh kỹ thuật (không biết cấu trúc DB, không rõ luồng API): DỪNG LẬP KẾ HOẠCH ngay lập tức. Đóng gói JSON với `planStatus: "NEED_KNOWLEDGE"` và liệt kê các chủ đề cần hỏi vào mảng `knowledgeRequest.topics` để Orchestrator tự điều phối đi hỏi Knowledge Agent. (Lưu ý: Không tạo `dag` trong trường hợp này).
   - Nếu đã ĐỦ bối cảnh (Context Data đã được nạp): Đi tiếp bước 4.
4. **Analyze Goal:** Viết ra `goal` (mục tiêu cốt lõi) và `successCriteria` (danh sách tiêu chí nghiệm thu toàn bộ quá trình).
5. **Identify Domains:** Gán `taskCategory` linh hoạt (ví dụ: `implementation`, `architecture`, `business-analysis`...). Tuyệt đối không bị gò bó bởi enum.
6. **Split Tasks:** Phân rã bài toán thành các sub-tasks. *Bắt buộc tuân thủ tiêu chuẩn tại* `../../agents/planner/policies/decomposition.md`. Tùy thuộc vào bản chất yêu cầu, hãy tra cứu các chế độ lập kế hoạch tại `../../agents/planner/planning-modes/` (Ví dụ: `feature.md`, `bug-fix.md`).
   - Chỉ định nghĩa **WHAT** (Làm cái gì) + **WHO** (Giao cho ai), tuyệt đối không thiết kế **HOW** (Giải pháp chi tiết).
   - Gán `assignedRole` và `expectedOutput` (Kỳ vọng đầu ra).
7. **Dependency Analysis:** Xác định `dependencies`. Đảm bảo Đồ thị không lặp vòng (Acyclic) bằng cách tham chiếu `../../agents/planner/policies/dependency-analysis.md`.
8. **Parallel Analysis:** Bất kỳ task nào không bị ràng buộc phụ thuộc lẫn nhau $\rightarrow$ Gom chung vào một mảng trong `parallelGroups`. Hướng dẫn chi tiết tại `../../agents/planner/policies/parallel-execution.md`.
9. **Evaluate Plan (Risk & Confidence):** Đánh giá rủi ro, giả định và độ tự tin (Confidence). *Bắt buộc tuân thủ* `../../agents/planner/policies/confidence.md`. Nếu Prompt mâu thuẫn, áp dụng `../../agents/planner/policies/clarification.md`.
10. **Finalize:** Đóng gói JSON theo chuẩn `ExecutionPlanContract`. Nếu mọi thứ trơn tru, `planStatus` phải là `"READY"` và bắt buộc có `dag`.

## 3. Quy tắc Vàng (Golden Rules)

- **Zero-Disk Access:** Bạn KHÔNG có quyền chạy lệnh terminal hay đọc ghi file. Chỉ suy luận (Reasoning) hoàn toàn dựa trên Context được cấp sẵn.
- **Agent Decides Skill (Autonomy):** Bạn chỉ có quyền gán Role (`assignedRole`) cho một Task. Tuyệt đối KHÔNG gán hay bắt ép Specialist Agent phải dùng Skill cụ thể nào. Bọn chúng tự có não để chọn Skill.
- **No P2P Communication:** Đích đến của JSON Plan luôn luôn là `toAgent: "ORCHESTRATOR"`. Không bao giờ gửi trực tiếp lệnh cho Agent khác.
- **Epic Escalation:** Nếu bài toán cần hơn 7 sub-tasks, nó quá bự. Hãy chia Phase và gán một task cho BA Agent đi làm rõ hoặc Architect Agent đi thiết kế kiến trúc trước.
