# Quy chuẩn Xây dựng Kỹ năng Hệ thống (Skill Specification Standard)

> **Tài liệu Định nghĩa Cấu trúc Chuẩn, Vị trí Lưu trữ, Cấu hình YAML Frontmatter (Intent Contract) và Giao ước Đầu ra (Artifact Contract) cho mọi Skill trong Hệ thống Agentic Work (AutoForge)**

---

## 1. Nguyên tắc & Vị trí Lưu trữ chuẩn (Skill Hierarchy Standard)

Tất cả các Kỹ năng (Skills) trong hệ thống được quản lý tập trung tại thư mục [`skills/`](file:///d:/Workspace/Projects/AgenticWork/skills) và phân nhóm theo **Domain/Lĩnh vực chuyên môn**:

```text
skills/
├── ba/                        # Kỹ năng phân tích nghiệp vụ (BA)
│   └── requirements-interview/
│       └── SKILL.md           # File hướng dẫn chính (Bắt buộc)
├── backend/                   # Kỹ năng phát triển Backend (API, DB, Clean Arch)
├── frontend/                  # Kỹ năng phát triển Frontend (React, UI Components)
├── odoo/                      # Kỹ năng chuyên biệt framework Odoo
├── shared/                    # Kỹ năng dùng chung cho mọi Agent (Git, Markdown, JSON)
└── 3rdparty/                  # Thư viện Kỹ năng nhập từ bên thứ ba (Chỉ đọc)
```

> [!IMPORTANT]
> **Quy tắc Vàng:**
> 1. **KHÔNG để Skill trong thư mục `agents/`:** Thư mục `agents/` chỉ dùng để định nghĩa vai trò (Role/Identity) của Agent. Thư mục `skills/` là kho tri thức toàn cục (Global Skill Hub).
> 2. **Tên thư mục Skill (`slug`):** Viết thường, không dấu, phân cách bằng dấu gạch ngang (ví dụ: `requirements-interview`, `react-bits`, `database-migration`).
> 3. **File chính bắt buộc:** Mọi folder skill phải chứa đúng file `SKILL.md`.

---

## 2. Cấu trúc YAML Frontmatter — Hợp đồng Ý định (Intent Contract)

Mỗi file `SKILL.md` **BẮT BUỘC** phải chứa khối YAML Frontmatter ở đầu file. Cấu trúc này được kiểm duyệt bằng JSON Schema [`schemas/skill-manifest.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/skill-manifest.schema.json).

```yaml
---
name: requirements-interview
type: skill
intentCategory: REQUIREMENTS_REFINEMENT
description: >
  PHỎNG VẤN YÊU CẦU kiểu Socratic để làm rõ yêu cầu trước khi viết spec...
target_agent: BA_AGENT
intent_triggers:
  - "làm rõ yêu cầu này"
  - "phỏng vấn yêu cầu"
  - "requirements interview"

when_to_use:
  pre_conditions:
    - "Yêu cầu người dùng còn mơ hồ, chưa rõ scope hoặc edge cases"
  do_not_use_if:
    - "Yêu cầu đã cực kỳ đầy đủ thông tin → Chuyển sang skill write-spec"

related_skills:
  - name: "module-documentation"
    difference: "Skill requirements-interview dùng để HỎI ĐÀO SÂU (interview) và tạo CR nháp, còn module-documentation dùng để XUẤT SPEC."

required_contracts: ["ContextPayloadContract", "SpecialistResultContract"]
tags: ["ba", "requirements", "interview"]
---
```

### Chi tiết các trường YAML Bắt buộc & Khuyên dùng:

| Trường | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `name` | `string` | Tên slug định danh duy nhất của Skill (kebab-case). |
| `description` | `string` | Mô tả mục tiêu, bài toán và hoàn cảnh áp dụng Skill (Dùng cho Orchestrator Semantic Matching). |
| `intentCategory` | `Enum` | Nhóm ý định (`CODE_GEN`, `REQUIREMENTS_REFINEMENT`, `BUG_FIX`, `ARCHITECTURE_DESIGN`, `DOCUMENTATION`, `TESTING`...). |
| `intent_triggers` | `string[]` | Danh sách các cụm từ khóa kích hoạt (VI/EN) để Orchestrator match ý định người dùng. |
| `when_to_use` | `object` | Điều kiện tiền đề (`pre_conditions`) và điều kiện từ chối (`do_not_use_if`) khi định tuyến. |
| `related_skills` | `array` | Phân biệt ranh giới cốt lõi với các Skill lân cận để tránh Orchestrator gọi nhầm. |
| `required_contracts` | `string[]` | Danh sách Hợp đồng Dữ liệu sử dụng (`ContextPayloadContract`, `SpecialistResultContract`). |

---

## 3. Cấu trúc Thân bài `SKILL.md` (Reasoning Framework)

Thân bài Markdown của file `SKILL.md` hướng dẫn Specialist Agent thực thi tư duy theo các phần chuẩn hóa sau:

```markdown
# [Tên Kỹ Năng]

> Skill lo đúng MỘT việc: <...>. Nhận dữ liệu từ `ContextPayloadContract`, xử lý logic. Nếu phát hiện thiếu bối cảnh, BẮT BUỘC gửi yêu cầu truy vấn bổ sung lên **Knowledge Agent**; khi hoàn tất, trả về `SpecialistResultContract` để Gateway kiểm duyệt. KHÔNG tự ý ghi file.

## Config (Tham số dự án)
- `{{PROJECT}}` — Tên dự án
- `/docs` — Thư mục tài liệu vault

## 1. Task Mindset & Core Principles (Tư duy & Nguyên tắc Nhiệm vụ)
- **Góc nhìn thực thi:** [Góc nhìn chuyên môn đặc thù]
- **Nguyên tắc cốt lõi:** Zero-hallucination, trung thực tuyệt đối.
- **Ranh giới thực thi:** Nên dùng khi / Không dùng khi.

## 2. Core Execution Rules (Nguyên tắc Thực thi)
1. **Gate:** Chỉ tạo Proposed Payload trong RAM.
2. **Traceability:** Map logic với ID luật nghiệp vụ `[[BR-xxx]]`.
3. **Bi-directional Knowledge Loop:** Gửi `KnowledgeQueryRequest` lên Knowledge Agent nếu thiếu bối cảnh.
4. **Degrade Gracefully:** Ghi chú warning vào `openQuestions` thay vì crash luồng.

## 3. Quy trình Xử lý (Reasoning Phases)
- **Phase 1 — Gather & Analyze:** Phân tích `userPrompt` và `ContextPayloadContract`.
- **Phase 2 — Synthesize (Tổng hợp):** Thiết kế giải pháp / Viết mã nguồn.
- **Phase 3 — Self-Audit (Tự kiểm tra):** Đối chiếu với Definition of Done.
- **Phase 4 — Wrap Contract:** Đóng gói JSON `SpecialistResultContract`.

## 4. Definition of Done (Tiêu chuẩn hoàn thành)
- [ ] Tiêu chí 1
- [ ] Đảm bảo toàn bộ nội dung mã nguồn trong trường `content`, `searchString` và `replaceString` phải được escape JSON hợp lệ (ví dụ: biến xuống dòng thành `\n`, escape dấu quote `\"`).
- [ ] Output tuân thủ 100% JSON Schema `SpecialistResultContract`.

## 5. Contract Binding (Ràng buộc Đầu ra)
```

---

## 4. Giao ước Đầu ra Chuẩn hóa (Artifact Contract / Output Envelope)

Mọi Skill khi thực thi **BẮT BUỘC** phải yêu cầu Specialist Agent đóng gói sản phẩm đầu ra theo định dạng JSON Envelope chuẩn `SpecialistResultContract` để gửi cho **Gateway 2 (Review QA)**:

```json
{
  "traceId": "<trace_id_từ_context_payload>",
  "resultId": "<uuid>",
  "taskId": "<từ_input>",
  "specialistRole": "BA_AGENT", // BA_AGENT | ARCHITECT_AGENT | CODER_AGENT | DATABASE_AGENT | TESTER_AGENT
  "executionType": "FILE_CREATE", // FILE_CREATE | FILE_MODIFY | FILE_DELETE | CHAT_RESPONSE
  "proposedPayload": [
    {
      "targetPath": "docs/06-Change-Log/CR-YYYY-MMDD-[title].md",
      "content": "<Nội_dung_hoàn_chỉnh_đã_escape_JSON: \\n, \\\">",
      "replacementChunk": {
        "searchString": "<Nội_dung_cũ_cần_tìm_đã_escape_JSON: \\n, \\\">",
        "replaceString": "<Nội_dung_mới_đáp_vào_đã_escape_JSON: \\n, \\\">"
      }
    }
  ],
  "chatMessage": "Thông báo ngắn gọn kết quả cho người dùng trên IDE...",
  "metadata": {
    "skillUsed": "[skill-name-slug]",
    "affectedModules": ["MODULE_NAME"]
  }
}
```

---

## 5. Quy trình Xử lý Kỹ năng Bên thứ ba (3rd-Party Skills Pipeline)

Đối với các Skill tải từ bên thứ ba (GitHub repos, Obsidian Hub, Cursor skills, NPM packages):

1. **Ingestion & Adapter Pipeline:** Khi cài đặt, **Skill Engine Parser** quét file thô:
   - Nếu thiếu YAML Frontmatter $\rightarrow$ Tự động sinh file sidecar `.[skill-name].meta.json` để đăng ký vào **Skill Registry**.
   - Nếu repo lớn (ví dụ: `react-bits`) $\rightarrow$ Tạo file `catalog.json` phục vụ **Chỉ mục 2 Tầng (Hierarchical Lazy-Loading)**.
2. **Output Interception:** Đăng ký bộ bọc (wrapper) để tự động bắt và đóng gói kết quả của Skill bên thứ 3 vào **Output Envelope** trước khi chuyển cho Gateway 2.

---

## 6. Mẫu Khởi tạo Quick-Start Template

Khi tạo một Skill mới cho hệ thống, hãy tham khảo và copy mẫu chuẩn tại:
👉 **[`docs/07-Process/Templates/Template-Skill.md`](file:///d:/Workspace/Projects/AgenticWork/docs/07-Process/Templates/Template-Skill.md)**
👉 **[`docs/07-Process/skill-template/SKILL.md`](file:///d:/Workspace/Projects/AgenticWork/docs/07-Process/skill-template/SKILL.md)**

---

*Tài liệu được khởi tạo tự động tại `docs/02-Skill-Specification-Standard.md` và gắn kèm JSON Schema `schemas/skill-manifest.schema.json`.*
