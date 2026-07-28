---
name: skill-<role>-<name-kebab>
type: skill
intentCategory: {{INTENT_CATEGORY}} # Enum: REQUIREMENTS_REFINEMENT | CODE_GEN | BUG_FIX | ARCHITECTURE_DESIGN | DOCUMENTATION | TESTING
description: >
  Mô tả chi tiết mục tiêu, bài toán giải quyết và hoàn cảnh áp dụng của Skill để Orchestrator match chính xác ý định...
target_agent: {{TARGET_AGENT}} # <CODER_AGENT | BA_AGENT | ARCHITECT_AGENT | TESTER_AGENT>
intent_triggers: 
  - "{{TRIGGER_1}}"
  - "{{TRIGGER_2}}"


# Điều kiện Kích hoạt & Ranh giới Sử dụng (Orchestrator Routing)
when_to_use:
  pre_conditions:
    - "{{PRE_CONDITION_1}}"
  do_not_use_if:
    - "{{DO_NOT_USE_IF_1}}"

# Phân biệt Ranh giới với các Skill khác (Tránh Orchestrator gọi nhầm)
related_skills:
  - name: "{{RELATED_SKILL_NAME}}"
    difference: "{{DIFFERENCE_DESCRIPTION}}"

required_contracts: ["ContextPayloadContract", "SpecialistResultContract"]
tags: ["<tag 1>", "<tag 2>"]
---

# <Tên Skill> — <1 dòng mục đích>

> Skill lo đúng MỘT việc: <...>. Nhận dữ liệu từ `ContextPayloadContract`, xử lý logic. Nếu phát hiện thiếu bối cảnh/luật nghiệp vụ, BẮT BUỘC gửi yêu cầu truy vấn bổ sung lên **Knowledge Agent**; khi hoàn tất, trả về `SpecialistResultContract` để Gateway kiểm duyệt. KHÔNG tự ý ghi file.

## Config (Tham số dự án)
- `{{PROJECT}}` — Tên dự án
- `/docs` — Thư mục tài liệu vault
- `{{LANG_PRIMARY}}` / `{{LANG_SECONDARY}}` — Ngôn ngữ xử lý (VD: VI / EN)

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

## 3. Quy trình Xử lý (Reasoning Phases)
Khi nhận được yêu cầu, hãy tư duy theo các bước sau trong bộ nhớ (RAM) trước khi xuất kết quả:
- **Phase 1 — Gather & Analyze:** Đọc kỹ `userPrompt` và phân tích các `businessRules`, `subgraphs`, `dataDictionarySchemas` do Knowledge Agent cung cấp. Nếu phát hiện thiếu bối cảnh, gửi `KnowledgeQueryRequest` bổ sung trước khi chuyển sang Phase 2.
- **Phase 2 — Synthesize (Tổng hợp):** Thiết kế giải pháp / Viết mã nguồn / Phân tích Yêu cầu.
- **Phase 3 — Self-Audit (Tự kiểm tra):** Đối chiếu giải pháp vừa làm với Definition of Done (bên dưới).
- **Phase 4 — Wrap Contract:** Đóng gói kết quả thành JSON tuyệt đối không dư thừa text.

## 4. Definition of Done (Tiêu chuẩn hoàn thành - Căn cứ để GW2 chấm điểm)
- [ ] <Điều kiện đo được 1 - VD: Đã quét đủ các edge cases của luồng thanh toán>.
- [ ] Mọi file cần tạo/sửa đều dùng đường dẫn tương đối (Relative path).
- [ ] Không có dữ liệu bịa đặt (hallucinated references).
- [ ] Đảm bảo toàn bộ nội dung mã nguồn trong trường `content`, `searchString` và `replaceString` phải được escape JSON hợp lệ (ví dụ: biến xuống dòng thành `\n`, escape dấu quote `\"`).
- [ ] Output tuân thủ 100% JSON Schema `SpecialistResultContract` của hệ thống.

## 5. Contract Binding (Ràng buộc Đầu ra)
Bạn BẮT BUỘC phải trả về một chuỗi JSON hợp lệ theo cấu trúc `SpecialistResultContract`. 
**TUYỆT ĐỐI KHÔNG** thêm các câu giao tiếp như *"Dưới đây là kết quả của bạn..."*.

```json
{
  "traceId": "<trace_id_từ_context_payload>",
  "resultId": "<uuid>",
  "taskId": "<từ_input>",
  "specialistRole": "<target_agent_ở_trên>",
  "executionType": "STRUCTURED_RESULT", // FILE_CREATE | FILE_MODIFY | FILE_DELETE | CHAT_RESPONSE | STRUCTURED_RESULT | DOMAIN_RESULT
  "proposedPayload": [
    // Dạng 1: Dành cho Skill Phân tích / Domain Data (BA, Architect, Security...)
    {
      "payloadType": "<TênDomainPayload - VD: RequirementsInterview / ArchitectureAnalysis>",
      "payload": {
        "field1": "<Dữ_liệu_nghiệp_vụ_cấu_trúc_1>",
        "field2": "<Dữ_liệu_nghiệp_vụ_cấu_trúc_2>"
      }
    }
    // Dạng 2: Dành cho Skill Tạo / Sửa File (Coder, Documentation...)
    /*
    {
      "targetPath": "<Đường/dẫn/tương_đối/tới/file>",
      "content": "<Nội_dung_hoàn_chỉnh_khi_executionType_là_FILE_CREATE_đã_escape_JSON>",
      "replacementChunk": {
        "searchString": "<Nội_dung_cũ_khi_executionType_là_FILE_MODIFY_đã_escape_JSON>",
        "replaceString": "<Nội_dung_mới_đã_escape_JSON>"
      }
    }
    */
  ],
  "chatMessage": "<Điền thông báo ngắn gọn hoặc câu hỏi cho user tại đây>",
  "metadata": {
    "skillUsed": "skill-<role>-<name-kebab>",
    "affectedModules": ["MODULE_NAME"],
    "status": "DRAFT", // DRAFT | READY_FOR_REVIEW | READY_FOR_COMMIT | COMPLETED
    "version": 1,
    "readinessScore": 85,
    "confidence": 0.9,
    "missingFields": []
  }
}
```