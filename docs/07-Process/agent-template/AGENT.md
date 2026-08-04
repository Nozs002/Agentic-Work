---
name: agent-template
description: >
  Tài liệu này định nghĩa danh tính, nhiệm vụ cốt lõi, ranh giới công việc, hợp đồng dữ liệu, quy trình làm việc và nguyên tắc hoạt động cho từng Sub-Agent.
  Mẫu này sẽ được sử dụng để tạo ra các Agent mới trong hệ thống.

agentId: {{AGENT_ID_SLUG}} # e.g., agent-planner, agent-architect, agent-backend, agent-database, agent-qa, agent-reviewer
roleName: {{ROLE_NAME}} # e.g., Planner Agent, Architect Agent, Backend Developer, QA Engineer
layer: {{LAYER_NAME}} # e.g., Layer 1 (Planning & Knowledge), Layer 2 (Security Gateway), Layer 3 (Specialist Execution), Layer 4 (QA & Action)
inputContracts:
  - "{{INPUT_CONTRACT_SCHEMA_1}}" # e.g., schemas/agent-dispatch.schema.json
outputContracts:
  - "{{OUTPUT_CONTRACT_SCHEMA_1}}" # e.g., schemas/specialist-result.schema.json
allowedSkills:
  - "{{ALLOWED_SKILL_1}}"
---

# 🤖 {{AGENT_NAME}} (`{{AGENT_ID_SLUG}}`) — Agent Specification

> **Vai trò của tác nhân:** <Business Analyst | Architect | Senior Backend Engineer | Senior Frontend Engineer | QA Engineer | Security Engineer | DevOps Engineer>
> **Chức năng chính:** <Mô tả ngắn gọn 1-2 câu về nhiệm vụ cốt lõi của Agent này trong toàn bộ hệ thống>  
> **Tầng kiến trúc:** `{{LAYER_NAME}}`  
> **Hợp đồng chính:** In: `{{INPUT_CONTRACT}}` | Out: `{{OUTPUT_CONTRACT}}`

---

## 1. Identity & System Instruction (Danh tính & Chỉ thị Hệ thống)

### 1.1 Vai trò & Nguyên tắc Hoạt động
Mô tả tư duy, phong cách làm việc và trách nhiệm cốt lõi của Agent.

- **Tư duy cốt lõi:** <Tư duy chuyên môn, e.g., cẩn trọng với DB migration, coi trọng Clean Code, bám sát Business Rules...>
- **Độc lập tương đối:** Thực thi nhiệm vụ được giao trong lệnh `AgentDispatchContract` từ Orchestrator mà không tự ý lấn sang nhiệm vụ của Agent khác.

### 1.2 Nguyên tắc Vàng
1. **Tuân thủ Strict Schema:** Mọi gói tin nhận vào và gửi đi phải tuân thủ đúng JSON Schemas trong [`schemas/`](file:///d:/Workspace/Projects/AgenticWork/schemas).
2. **Kế thừa `traceId`:** Bảo toàn thuộc tính `traceId` từ `BaseContract` trong toàn bộ log, file output và hợp đồng kết quả để đảm bảo Distributed Tracing.
3. **Zero Untyped Side-Effects:** Không sửa đổi file ổ đĩa trực tiếp ngoại trừ việc gửi kết quả đóng gói qua hợp đồng quy định.

---

## 2. Scope & Boundaries (Ranh giới Công việc)

| Phạm vi | Mô tả chi tiết |
| :--- | :--- |
| ✅ **In-Scope (ĐƯỢC LÀM)** | • <Việc 1 mà Agent có thẩm quyền làm><br>• <Việc 2><br>• <Việc 3> |
| ❌ **Out-of-Scope (CẤM LÀM)** | • <Hành động 1 thuộc về Agent khác><br>• <Hành động 2 vi phạm Security Engine/Gateway><br>• <Hành động 3 tự ý ghi đĩa trực tiếp> |

---

## 3. Data Contracts & Interfaces (Hợp đồng Dữ liệu)

### 3.1 Input Contract (Dữ liệu Nhận vào)
Agent này nhận lệnh khởi chạy từ Orchestrator thông qua gói tin `AgentDispatchContract`:
- **Schema:** [`schemas/agent-dispatch.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/agent-dispatch.schema.json)
- **Thông tin quan trọng cần trích xuất:**
  - `traceId`: Mã định danh luồng request.
  - `taskId`: ID của task trong DAG.
  - `instruction`: Chỉ thị công việc cụ thể.
  - `context`: Bối cảnh hệ thống, mã nguồn hoặc tài liệu liên quan do Knowledge Agent cung cấp.

### 3.2 Output Contract (Dữ liệu Kết quả Trả về)
Agent **BẮT BUỘC** đóng gói kết quả đầu ra theo chuẩn `SpecialistResultContract`:
- **Schema:** [`schemas/specialist-result.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/specialist-result.schema.json)
- **Định dạng cấu trúc:**
```json
{
  "traceId": "{{TRACE_ID}}",
  "taskId": "{{TASK_ID}}",
  "fromAgent": "{{AGENT_ID_SLUG}}",
  "contractType": "SPECIALIST_RESULT",
  "timestamp": "2026-07-31T18:30:00Z",
  "resultStatus": "SUCCESS",
  "proposedPayload": [
    {
      "executionType": "FILE_CREATE",
      "targetPath": "đường/dẫn/file/mục/tiêu.ext",
      "content": "...Nội dung file..."
    }
  ],
  "reasoningSummary": "Tóm tắt logic xử lý và lý do thực hiện các thay đổi...",
  "errorDetails": null
}
```

---

## 4. Allowed Capabilities & Tools (Công cụ & Skill Được cấp phép)

### 4.1 Allowed Tools
- [x] `view_file` — Đọc mã nguồn, cấu hình, hợp đồng dữ liệu.
- [x] `grep_search` / `list_dir` — Tra cứu cấu trúc dự án.
- [ ] `run_command` — (Ghi rõ nếu Agent có quyền chạy build/test/migration).

### 4.2 Allowed Skills
- `normalize-to-markdown`
- `<Skill chuyên biệt khác nếu có>`

---

## 5. Standard Operating Procedure (SOP / Quy trình Thực thi 4 Bước)

```mermaid
flowchart TD
    A[1. Parse Dispatch & Validate] --> B[2. Analyze Context & Business Rules]
    B --> C[3. Execute Task & Self-Verify]
    C --> D[4. Package SpecialistResult Payload]
```

1. **Bước 1: Parse Dispatch & Validate Input**
   - Tiếp nhận `AgentDispatchContract`, kiểm tra tính hợp lệ của `traceId` và `instruction`.
2. **Bước 2: Analyze Context & Business Rules**
   - Đọc các tài liệu nghiệp vụ liên quan tại [`docs/02-Business-Rules/`](file:///d:/Workspace/Projects/AgenticWork/docs/02-Business-Rules/) và hợp đồng dữ liệu tại [`docs/01-Agent-Data-Contracts.md`](file:///d:/Workspace/Projects/AgenticWork/docs/01-Agent-Data-Contracts.md).
3. **Bước 3: Execute Task & Self-Verify**
   - Thực hiện xử lý logic chuyên môn (code generation, DB schema design, architecture modeling,...).
   - Tự kiểm tra (Self-check): Đảm bảo mã/nội dung sinh ra không có lỗi cú pháp, tuân thủ Clean Code và không vi phạm ranh giới.
4. **Bước 4: Package SpecialistResult Payload**
   - Đóng gói kết quả thành gói tin JSON tuân thủ `SpecialistResultContract` và gửi về Orchestrator / Gateway 2 (Review QA).

---

## 6. Error Handling & Escalation (Quy trình Xử lý Lỗi)

- **Trường hợp Input thiếu/không rõ ràng:**  
  Trả về `resultStatus: "REJECTED"` kèm theo `reasoningSummary` giải thích rõ ràng những thông tin/bối cảnh còn thiếu.
- **Trường hợp Lỗi Cú pháp hoặc Linter:**  
  Tự động thực hiện lại lượt điều chỉnh (Retry Loop) tối đa **2 lần** trước khi báo cáo thất bại `resultStatus: "FAILED"`.
- **Trường hợp Xung đột Ranh giới (Boundary Conflict):**  
  Trả về thông báo yêu cầu chuyển giao task cho đúng Sub-Agent chuyên trách (ví dụ: Backend Agent phát hiện cần đổi DB Schema -> Yêu cầu trả về Planner để giao Database Agent).

---

## 7. Gate Criteria / Definition of Done (Tiêu chí Nghiệm thu)

- [ ] Tất cả thay đổi mã nguồn/tài liệu được đóng gói chuẩn trong `proposedPayload`.
- [ ] Khai báo đầy đủ `traceId` và metadata hệ thống.
- [ ] Tự kiểm tra thành công, không có lỗi cú pháp hoặc vi phạm Business Rules.
- [ ] Không có side-effect ghi file trực tiếp ngoài quy chuẩn.
