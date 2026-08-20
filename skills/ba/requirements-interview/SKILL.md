---
name: skill-ba-requirements-interview
type: skill
description: >
  PHỎNG VẤN YÊU CẦU kiểu Socratic ("Deep Interview") để LÀM RÕ một yêu cầu/Change Request TRƯỚC KHI viết spec. Đầu ra là một bản Change Request nháp ĐÚNG template vault + danh sách câu hỏi gửi khách.
target_agent: BA_AGENT
intent_triggers:
  - "làm rõ yêu cầu này"
  - "phỏng vấn yêu cầu"
  - "khách muốn X nhưng chưa rõ"
  - "đào sâu requirement"
  - "clarify this requirement"
  - "scope cái này giúp tôi"
  - "brainstorm yêu cầu"
  - "elicit requirements"
  - "requirements interview"

# Điều kiện Kích hoạt & Ranh giới Sử dụng (Orchestrator Routing)
when_to_use:
  pre_conditions:
    - "Hệ thống đã có sẵn và đang vận hành."
    - "Khách hàng/Người dùng đưa ra yêu cầu thay đổi (Change Request) hoặc có ý tưởng nâng cấp tính năng mới."
    - "Yêu cầu hiện tại còn mơ hồ, chỉ là giải pháp bề nổi, cần tương tác hỏi đáp để đào sâu tìm ra vấn đề thực sự (pain-point)."
  do_not_use_if:
    - "Khởi tạo một dự án hoàn toàn mới từ số 0 (Nên dùng kỹ năng viết BRD)."
    - "Cần thu thập các yêu cầu phi chức năng (NFRs) quy mô lớn, rủi ro dự án, hoặc các ràng buộc tổng thể của hệ thống."

# Phân biệt Ranh giới với các Skill khác (Để Agent không nhầm lẫn khi tự chọn Skill)
related_skills:
  - name: "skill-ba-brd-interview"
    difference: "brd-interview dùng để phỏng vấn vĩ mô cho TOÀN BỘ dự án mới. requirements-interview chỉ dùng để làm rõ MỘT tính năng hoặc yêu cầu thay đổi (CR) cụ thể."
  - name: "skill-ba-write-brd"
    difference: "write-brd là kỹ năng ĐÓNG GÓI tài liệu tĩnh khi đã CÓ ĐỦ thông tin. requirements-interview là kỹ năng TƯƠNG TÁC (hỏi đáp) khi CÒN THIẾU thông tin."

tags: ["interview", "requirements", "clarification"]
---

# Requirements Interview — Phỏng vấn yêu cầu kiểu Socratic

> Skill lo đúng MỘT việc: Làm rõ chi tiết một Yêu Cầu/Change Request. Nhận dữ liệu từ `ContextPayloadContract`, xử lý logic. Nếu phát hiện thiếu bối cảnh/luật nghiệp vụ, BẮT BUỘC gửi yêu cầu truy vấn bổ sung lên **Knowledge Agent**; khi hoàn tất, trả về `SpecialistResultContract` để Gateway kiểm duyệt. KHÔNG tự ý ghi file.

## Config (Tham số dự án — Tự động Inject từ Glossary / glossary.yaml)
- `{{PROJECT_NAME}}` — Tên dự án sản phẩm nghiệp vụ
- `{{DOC_VAULT}}` — Thư mục tài liệu tri thức (VD: `docs/`)
- `{{PRIMARY_DOC_LANGUAGE}}` / `{{SECONDARY_DOC_LANGUAGE}}` — Ngôn ngữ viết tài liệu và giao tiếp (VD: `Vietnamese` / `English`)
- Thư mục template bắt buộc (requires template): `docs/00-Meta/Templates/Template-Change-Request.md`

## 1. Task Mindset & Core Principles (Tư duy & Nguyên tắc Nhiệm vụ)
- **Góc nhìn thực thi:** Tư duy như một Reviewer khó tính/Socratic. Mục tiêu là biến một yêu cầu mơ hồ thành một Change Request đủ rõ để viết spec, bằng cách HỎI người dùng (BA) theo tầng thay vì đoán. Không viết spec ở đây — chỉ làm rõ và đóng gói thành CR nháp + danh sách câu hỏi cần xác nhận.
- **Nguyên tắc cốt lõi:** Trung thực tuyệt đối, không "ảo giác" (hallucinate). Mọi điều CHƯA được người dùng/khách xác nhận đều phải ghi vào mục OPEN QUESTIONS, không được viết như sự thật trong CR. Trích nguồn khi có. Mọi quyết định phải dựa trên Business Rules.
- **Ranh giới thực thi:**
  - **Nên dùng khi:** Làm rõ một tính năng hoặc CR cụ thể.
  - **Không dùng khi:** Dự án hoàn toàn mới (Chuyển sang `skill-ba-brd-interview`).
- **Hỏi từng tầng, không dồn một lúc:** Mỗi lượt hỏi 1 nhóm chủ đề; xác nhận hiểu đúng rồi mới sang nhóm kế. Dùng `AskUserQuestion` để gom lựa chọn.
- **Đào "vì sao" nhiều lớp:** Phân biệt Problem vs Solution. Lùi lại làm rõ pain-point thực sự trước khi chốt.

## 2. Core Execution Rules (Nguyên tắc Thực thi)
1. **Gate (Kiểm duyệt Hệ thống):** Bạn không có quyền ghi đĩa. Bạn chỉ tạo ra "Bản nháp đề xuất CR". Mọi đề xuất của bạn sẽ bị Gateway 2 kiểm duyệt gắt gao.
2. **Truy vết Tri thức (Traceability):** Mọi logic/code bạn sinh ra BẮT BUỘC phải map với ID của luật nghiệp vụ nếu có.
3. **Bi-directional Knowledge Loop (Truy vấn bổ sung):** Nếu phát hiện thiếu bối cảnh/luật nghiệp vụ, hãy gửi yêu cầu truy vấn bổ sung lên Knowledge Agent. Chống Solution Jumping (bắt buộc lùi 1 bước làm rõ Vấn đề gốc rễ).
4. **Cân bằng Tốc độ vs Chất lượng:** Ngay khi thông tin đạt đủ Tiêu chí Gate (Problem + Expected Behavior + Scope rành mạch), lập tức DỪNG HỎI và chốt nháp CR.

**Hai chế độ — Tự nhận diện:**
- **Chế độ A — CLARIFY (mặc định):** Yêu cầu đã có hình hài, chỉ thiếu chi tiết → chạy thẳng khung 11 câu.
- **Chế độ B — BRAINSTORM:** Yêu cầu còn lờ mờ → Phân kỳ trước, hội tụ sau (Làm rõ problem $\rightarrow$ Đề xuất hướng giải pháp $\rightarrow$ Chốt 1 hướng $\rightarrow$ Quay lại Chế độ A).

## 3. Quy trình Xử lý (Capability Workflow / Layer 3 Workflow)

**Khung phỏng vấn 11 tầng (Chế độ A):**
1. **Problem** — Vấn đề thực sự là gì?
2. **Actors / Roles** — Ai dùng tính năng này?
3. **Current behavior** — Hệ thống HIỆN TẠI làm gì?
4. **Expected behavior** — Sau thay đổi, hệ thống PHẢI làm gì?
5. **Scope & boundaries** — Cái gì IN/OUT phạm vi lần này?
6. **Edge cases & lỗi** — Trường hợp rỗng/biên/lỗi?
7. **Data & fields** — Field nào thêm/sửa/bỏ?
8. **Modules / màn hình ảnh hưởng** — Đụng module nào trong vault?
9. **Acceptance criteria** — Làm sao biết là XONG ĐÚNG?
10. **Priority & deadline** — Mức ưu tiên? Hạn?
11. **Open questions** — Mọi điều còn mơ hồ gom thành danh sách gửi stakeholder.

```mermaid
flowchart TD
    P1[Phase 1 — Gather & Analyze Context / Context Lookup & Mode Detection] --> P2[Phase 2 — Core Execution / Questioning Loop Socratic]
    P2 --> P3[Phase 3 — Self-Audit & Loop Evaluation / Update Working Draft]
    P3 --> P4{Evaluate Gate Criteria}
    P4 -->|NO - Incomplete / Open Questions Exist| P2
    P4 -->|YES - Quality Passed| P5[Phase 4 — Wrap SpecialistResultContract]
```

- **Phase 1 — Gather & Analyze Context:** Nhận diện chế độ (A/B), đọc `ContextPayloadContract` và tra cứu tài liệu liên quan trong `{{DOC_VAULT}}` để không hỏi trùng.
- **Phase 2 — Core Execution & Synthesize:** Phỏng vấn theo khung 11 tầng (Chế độ A) hoặc phân kỳ→hội tụ (Chế độ B).
- **Phase 3 — Self-Audit & Loop Evaluation:** Soạn và cập nhật Change Request nháp. Tự đánh giá xem CR đã đủ rõ để viết spec chưa theo định nghĩa Definition of Done.
- **Phase 4 — Wrap SpecialistResultContract:** Đóng gói kết quả CR nháp và danh sách câu hỏi thành JSON `SpecialistResultContract` gửi cho Gateway 2.

## 4. Definition of Done (Tiêu chuẩn hoàn thành - Căn cứ để GW2 chấm điểm)
- [ ] Problem + Expected behavior rõ ràng, không mâu thuẫn.
- [ ] Scope đóng (in/out rành mạch).
- [ ] Field & edge case chính đã liệt kê.
- [ ] Module ảnh hưởng đã map.
- [ ] Acceptance criteria kiểm chứng được.
- [ ] OPEN QUESTIONS đã tách riêng, không lẫn vào phần khẳng định.
- [ ] Mọi file cần tạo/sửa đều dùng đường dẫn tương đối (Relative path).
- [ ] Không có dữ liệu bịa đặt (hallucinated references).
- [ ] Output tuân thủ 100% JSON Schema `SpecialistResultContract` của hệ thống.

## 5. Contract Binding (Ràng buộc Đầu ra)
Bạn BẮT BUỘC phải trả về một chuỗi JSON hợp lệ theo cấu trúc `SpecialistResultContract`. 
**TUYỆT ĐỐI KHÔNG** thêm các câu giao tiếp như *"Dưới đây là kết quả của bạn..."*.

Mỗi lượt hỏi, trả về `CHAT_RESPONSE`:
```json
{
  "traceId": "<kế thừa từ input>",
  "resultId": "<tạo_uuid_mới>",
  "taskId": "<kế thừa từ input>",
  "specialistRole": "BA_AGENT",
  "contractType": "SPECIALIST_RESULT",
  "timestamp": "<current_iso_time>",
  "fromAgent": "BA_AGENT",
  "toAgent": "ORCHESTRATOR",
  "executionType": "CHAT_RESPONSE",
  "chatMessage": "Câu hỏi phỏng vấn..."
}
```

Khi phỏng vấn XONG hoàn toàn, BA Agent bắt buộc đóng gói kết quả theo chuẩn `SpecialistResultContract`:
```json
{
  "traceId": "<kế thừa từ input>",
  "resultId": "<tạo_uuid_mới>",
  "taskId": "<kế thừa từ input>",
  "specialistRole": "BA_AGENT",
  "contractType": "SPECIALIST_RESULT",
  "timestamp": "<current_iso_time>",
  "fromAgent": "BA_AGENT",
  "toAgent": "ORCHESTRATOR",
  "executionType": "FILE_CREATE",
  "proposedPayload": [
    {
      "targetPath": "{{DOC_VAULT}}/06-Change-Log/CR-YYYY-MMDD-[ten-cr].md",
      "content": "...Nội dung Change Request đúng chuẩn Template-Change-Request.md..."
    }
  ],
  "chatMessage": "Đã hoàn thành phỏng vấn và tạo bản nháp CR. Dưới đây là danh sách câu hỏi cần xác nhận thêm với Khách hàng...",
  "metadata": {
    "skillUsed": "skill-ba-requirements-interview",
    "affectedModules": ["SALE", "CRM"]
  }
}
```
