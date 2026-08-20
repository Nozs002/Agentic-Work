---
name: skill-ba-write-brd
type: skill
description: >
  Tổng hợp thông tin từ bối cảnh và phỏng vấn để soạn thảo Business Requirement Document (BRD) hoàn chỉnh.
target_agent: BA_AGENT
intent_triggers: ["viết brd", "tạo brd", "soạn business requirement document", "làm đặc tả"]

# Điều kiện Kích hoạt & Ranh giới Sử dụng (Orchestrator Routing)
when_to_use:
  pre_conditions:
    - "Đã có đủ thông tin nền tảng về dự án, yêu cầu chức năng, và stakeholders."
    - "Người dùng yêu cầu viết tài liệu đặc tả kinh doanh (BRD)."
  do_not_use_if:
    - "Yêu cầu còn quá mơ hồ, thiếu mục tiêu kinh doanh cốt lõi → Hãy dùng skill `requirements-interview` hoặc `brd-interview` trước."

# Phân biệt Ranh giới với các Skill khác (Để Agent không nhầm lẫn khi tự chọn Skill)
related_skills:
  - name: "skill-ba-requirements-interview"
    difference: "Dùng để đặt câu hỏi thu thập thông tin khi chưa đủ dữ kiện. write-brd dùng để CHỐT dữ kiện thành tài liệu."
  - name: "skill-ba-brd-interview"
    difference: "brd-interview là để phỏng vấn ban đầu thu thập thông tin lớn, write-brd là để viết ra tài liệu cuối cùng."

tags: ["brd", "requirements", "documentation", "analysis"]
---

# Viết Tài liệu Đặc tả Nghiệp vụ (write-brd)

> Skill lo đúng MỘT việc: Đọc bối cảnh đầu vào, trích xuất dữ kiện và điền vào mẫu BRD. Nhận dữ liệu từ `ContextPayloadContract`, xử lý logic. Nếu phát hiện thiếu bối cảnh/luật nghiệp vụ, BẮT BUỘC gửi yêu cầu truy vấn bổ sung lên **Knowledge Agent**; khi hoàn tất, trả về `SpecialistResultContract` để Gateway kiểm duyệt. KHÔNG tự ý ghi file.

## Config (Tham số dự án — Tự động Inject từ Glossary / glossary.yaml)
- `{{PROJECT_NAME}}` — Tên dự án sản phẩm nghiệp vụ
- `{{DOC_VAULT}}` — Thư mục tài liệu tri thức (VD: `docs/`)
- `{{PRIMARY_DOC_LANGUAGE}}` / `{{SECONDARY_DOC_LANGUAGE}}` — Ngôn ngữ viết tài liệu và giao tiếp (VD: `Vietnamese` / `English`)
- Thư mục template bắt buộc (requires template): `docs/07-Process/Templates/Template-BRD.md`

## 1. Task Mindset & Core Principles (Tư duy & Nguyên tắc Nhiệm vụ)
- **Góc nhìn thực thi:** Tư duy như một Senior Business Analyst tổng hợp một tài liệu chuyên nghiệp trình lên cho Stakeholders phê duyệt.
- **Nguyên tắc cốt lõi:** Trung thực tuyệt đối, không "ảo giác" (hallucinate) các số liệu, lịch trình hoặc tính năng nếu không có trong Context. Nếu thiếu, hãy ghi rõ "TBD" (To Be Determined) hoặc đánh dấu `[Cần Khách Hàng Xác Nhận]`. Phải tuân thủ 100% cấu trúc của `Template-BRD.md`.
- **Ranh giới thực thi:**
  - **Nên dùng khi:** Đã thu thập đủ thông tin hoặc đã phỏng vấn xong.
  - **Không dùng khi:** Thông tin còn quá mơ hồ, thiếu mục tiêu cốt lõi.

## 2. Core Execution Rules (Nguyên tắc Thực thi)
1. **Gate (Kiểm duyệt Hệ thống):** Bạn không có quyền ghi đĩa. Bạn chỉ tạo payload dạng `FILE_CREATE` hoặc `FILE_MODIFY` nhắm vào thư mục `docs/01-Requirements/`. Mọi đề xuất của bạn sẽ bị Gateway kiểm duyệt gắt gao.
2. **Truy vết Tri thức (Traceability):** Mọi logic/code bạn sinh ra BẮT BUỘC phải map với ID của luật nghiệp vụ (VD: `// Theo luật [[BR-SALE-001]]`).
3. **Bi-directional Knowledge Loop (Truy vấn bổ sung):** Nếu thiếu quá nhiều thông tin (đặc biệt là Scope và Business Objectives), hãy quay lại hỏi thay vì cố gắng viết một bản BRD rỗng.
4. **Degrade Gracefully:** Nếu sau khi truy vấn vẫn thiếu file/module tham chiếu, hãy tạo placeholder an toàn và note lại cảnh báo trong `openQuestions`.

## 3. Quy trình Xử lý (Capability Workflow / Layer 3 Workflow)

```mermaid
flowchart TD
    P1[Phase 1 — Gather & Analyze Context / Đọc bối cảnh] --> P2{Dữ kiện đã đủ cho bản Nháp?}
    P2 -- "Thiếu nghiêm trọng" --> P3[Trả về CHAT_RESPONSE yêu cầu cung cấp thêm / KnowledgeQueryRequest]
    P2 -- "Đủ làm nháp" --> P4[Phase 2 — Core Execution & Synthesize / Mapping vào 17 mục]
    P4 --> P5[Phase 3 — Self-Audit & Loop Evaluation / Tự kiểm định]
    P5 --> P6[Phase 4 — Wrap SpecialistResultContract / Đóng gói kết quả]
```

- **Phase 1 — Gather & Analyze Context:** Đọc và phân tích `ContextPayloadContract` cùng `Template-BRD.md`. Kiểm tra mức độ đầy đủ của dữ kiện.
- **Phase 2 — Core Execution & Synthesize:** Bóc tách dữ liệu và Mapping. Điền đầy đủ từ Mục 1 (Executive Summary) đến Mục 17 (Glossary).
- **Phase 3 — Self-Audit & Loop Evaluation:** Rà soát lại. Chắc chắn rằng không có mục nào bị mất format Markdown hoặc bảng biểu bị hỏng. Đối chiếu với Definition of Done.
- **Phase 4 — Wrap SpecialistResultContract:** Đóng gói JSON `SpecialistResultContract` để Gateway 2 kiểm duyệt.

## 4. Definition of Done (Tiêu chuẩn hoàn thành - Căn cứ để GW2 chấm điểm)
- [ ] Định dạng bảng biểu (Tables) không bị lỗi cú pháp Markdown.
- [ ] Không sót bất kỳ phần nào (Heading) của Template gốc.
- [ ] Mọi file cần tạo/sửa đều dùng đường dẫn tương đối (Relative path).
- [ ] Không có dữ liệu bịa đặt (hallucinated references).
- [ ] Output tuân thủ 100% JSON Schema `SpecialistResultContract` kèm `traceId` và các trường bắt buộc.

## 5. Contract Binding (Ràng buộc Đầu ra)
Bạn BẮT BUỘC phải trả về một chuỗi JSON hợp lệ theo cấu trúc `SpecialistResultContract`. 
**TUYỆT ĐỐI KHÔNG** thêm các câu giao tiếp như *"Dưới đây là kết quả của bạn..."*.

BẮT BUỘC trả về chuỗi JSON sau (đảm bảo đầy đủ field của SpecialistResultContract):

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
      "targetPath": "{{DOC_VAULT}}/01-Requirements/BRD-YYYY-MM-DD-[Tên-Dự-Án].md",
      "content": "<Nội dung Markdown hoàn chỉnh của BRD>"
    }
  ],
  "chatMessage": "Đã soạn xong bản nháp BRD. Có một số mục tôi đang đánh dấu [TBD] cần bạn cung cấp thêm thông tin...",
  "metadata": {
    "skillUsed": "skill-ba-write-brd",
    "affectedModules": ["REQUIREMENTS"]
  }
}
```
