---
name: skill-<role>-<name-kebab>
type: skill
intentCategory: CODE_GEN # Enum: REQUIREMENTS_REFINEMENT | CODE_GEN | BUG_FIX | ARCHITECTURE_DESIGN | DOCUMENTATION | TESTING
description: >
  Mô tả chi tiết mục tiêu, bài toán giải quyết và hoàn cảnh áp dụng của Skill để Orchestrator match chính xác ý định...
target_agent: <CODER_AGENT | BA_AGENT | ARCHITECT_AGENT | TESTER_AGENT>
intent_triggers: ["<trigger 1>", "<trigger 2>"]

# Điều kiện Kích hoạt & Ranh giới Sử dụng (Orchestrator Routing)
when_to_use:
  pre_conditions:
    - "<Điều kiện tiền đề 1 — VD: Yêu cầu mơ hồ chưa đủ rõ để viết spec>"
  do_not_use_if:
    - "<Điều kiện từ chối 1 — VD: Yêu cầu đã đầy đủ thông tin → Chuyển sang skill write-spec>"

# Phân biệt Ranh giới với các Skill khác (Tránh Orchestrator gọi nhầm)
related_skills:
  - name: "skill-<role>-<other-skill-kebab>"
    difference: "<Mô tả điểm KHÁC BIỆT CỐT LÕI giữa skill này và skill lân cận>"

required_contracts: ["ContextPayloadContract", "SpecialistResultContract"]
tags: ["<tag 1>", "<tag 2>"]
---

# <Tên Skill> — <1 dòng mục đích>

> Skill lo đúng MỘT việc: <...>. Nhận dữ liệu từ `ContextPayloadContract`, xử lý logic. Nếu phát hiện thiếu bối cảnh/luật nghiệp vụ, BẮT BUỘC gửi yêu cầu truy vấn bổ sung lên **Knowledge Agent**; khi hoàn tất, trả về `SpecialistResultContract` để Gateway kiểm duyệt. KHÔNG tự ý ghi file.

## Config (Tham số dự án — Tự động Inject từ Glossary / glossary.yaml)
- `{{PROJECT_NAME}}` — Tên dự án sản phẩm nghiệp vụ
- `{{DOC_VAULT}}` — Thư mục tài liệu tri thức (VD: `docs/`)
- `{{PRIMARY_DOC_LANGUAGE}}` / `{{SECONDARY_DOC_LANGUAGE}}` — Ngôn ngữ viết tài liệu và giao tiếp (VD: `Vietnamese` / `English`)

## 1. Task Mindset & Core Principles (Tư duy & Nguyên tắc Nhiệm vụ)
- **Góc nhìn thực thi:** <Mô tả góc nhìn đặc thù khi chạy skill này — VD: Tư duy như một Reviewer khó tính / Đặt câu hỏi kiểu Socratic>.
- **Nguyên tắc cốt lõi:** Trung thực tuyệt đối, không "ảo giác" (hallucinate). Mọi quyết định phải dựa trên Business Rules được cung cấp (`[[BR-xxx]]`). Nếu thiếu dữ liệu, hãy ghi chú vào phần `openQuestions` thay vì tự bịa ra.
- **Ranh giới thực thi:**
  - **Nên dùng khi:** <Tình huống phù hợp nhất>.
  - **Không dùng khi:** <Tình huống không phù hợp — Chuyển sang Skill tương ứng>.

## 2. Core Execution Rules (Nguyên tắc Thực thi)
1. **Gate (Kiểm duyệt Hệ thống):** Bạn không có quyền ghi đĩa. Bạn chỉ tạo ra "Bản nháp đề xuất" (Proposed Payload). Mọi đề xuất của bạn sẽ bị Gateway 2 kiểm duyệt gắt gao.
2. **Truy vết Tri thức (Traceability):** Mọi logic/code bạn sinh ra BẮT BUỘC phải map với ID của luật nghiệp vụ (VD: `// Theo luật [[BR-SALE-001]]`).
3. **Bi-directional Knowledge Loop (Truy vấn bổ sung):** Nếu phát hiện thiếu bối cảnh/luật nghiệp vụ trong `ContextPayloadContract`, hãy gửi yêu cầu truy vấn bổ sung (`KnowledgeQueryRequest`) lên Knowledge Agent để lấy thêm file/subgraph thay vì tự suy đoán.
4. **Degrade Gracefully:** Nếu sau khi truy vấn vẫn thiếu file/module tham chiếu, hãy tạo placeholder an toàn và note lại cảnh báo trong `openQuestions`, KHÔNG làm crash luồng.

## 3. Quy trình Xử lý (Capability Workflow / Layer 3 Workflow)

Mỗi Skill được mô hình hóa thành một **Capability Workflow Graph (Layer 3 Workflow)** với các nút xử lý, điều kiện kiểm định và vòng lặp tự hoàn thiện (Loop):

```mermaid
flowchart TD
    P1[Phase 1 — Gather & Analyze Context] --> P2[Phase 2 — Core Execution & Synthesize]
    P2 --> P3[Phase 3 — Self-Audit & Loop Evaluation]
    P3 -->|NO - Incomplete or Needs Query| P1
    P3 -->|YES - Quality Passed| P4[Phase 4 — Wrap SpecialistResultContract]
```

- **Phase 1 — Gather & Analyze Context:** Đọc kỹ `userPrompt` và phân tích các `businessRules`, `subgraphs` do Knowledge Agent cung cấp. Nếu phát hiện thiếu bối cảnh, gửi `KnowledgeQueryRequest` bổ sung.
- **Phase 2 — Core Execution & Synthesize:** Thiết kế giải pháp / Viết mã nguồn / Phân tích Yêu cầu.
- **Phase 3 — Self-Audit & Loop Evaluation:** Đối chiếu giải pháp vừa làm với Definition of Done. Nếu phát hiện chưa đạt hoặc thiếu thông tin, thực hiện vòng lặp Loop về Phase 1/Phase 2.
- **Phase 4 — Wrap SpecialistResultContract:** Đóng gói kết quả thành JSON `SpecialistResultContract` gửi cho Gateway 2.

## 4. Definition of Done (Tiêu chuẩn hoàn thành - Căn cứ để GW2 chấm điểm)
- [ ] <Điều kiện đo được 1 - VD: Đã quét đủ các edge cases của luồng thanh toán>.
- [ ] Mọi file cần tạo/sửa đều dùng đường dẫn tương đối (Relative path).
- [ ] Không có dữ liệu bịa đặt (hallucinated references).
- [ ] Output tuân thủ 100% JSON Schema `SpecialistResultContract` của hệ thống.

## 5. Contract Binding (Ràng buộc Đầu ra)
Bạn BẮT BUỘC phải trả về một chuỗi JSON hợp lệ theo cấu trúc `SpecialistResultContract`. 
**TUYỆT ĐỐI KHÔNG** thêm các câu giao tiếp như *"Dưới đây là kết quả của bạn..."*.

```json
{
  "resultId": "<uuid>",
  "taskId": "<từ_input>",
  "specialistRole": "<target_agent_ở_trên>",
  "executionType": "FILE_CREATE", // FILE_CREATE | FILE_MODIFY | CHAT_RESPONSE
  "proposedPayload": [
    {
      "targetPath": "<Đường/dẫn/tương_đối/tới/file>",
      "content": "<Nội_dung_hoàn_chỉnh_khi_executionType_là_FILE_CREATE>",
      "replacementChunk": {
        "startLine": 1,
        "endLine": 10,
        "targetContent": "<Nội_dung_cũ_khi_executionType_là_FILE_MODIFY>",
        "replacementContent": "<Nội_dung_mới>"
      }
    }
  ],
  "chatMessage": "<Điền thông báo ngắn gọn hoặc câu hỏi cho user tại đây>",
  "metadata": {
    "skillUsed": "skill-<role>-<name-kebab>",
    "affectedModules": ["MODULE_NAME"]
  }
}
```