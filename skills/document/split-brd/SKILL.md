---
name: skill-document-split-brd
type: skill
description: >
  Bóc tách một file Business Requirement Document (BRD) hoàn chỉnh thành các file Markdown nhỏ lẻ (Business Rules, Features, NFRs) và thiết lập liên kết chéo (Knowledge Graph).
target_agent: DOCUMENT_AGENT
intent_triggers: ["bóc tách brd", "phân rã brd", "chia nhỏ tài liệu yêu cầu", "split brd"]

when_to_use:
  pre_conditions:
    - "Đã có một file BRD hoàn chỉnh, đã được kiểm duyệt."
    - "Cần chia nhỏ BRD để quản lý thành các Business Rules (BR) và Features riêng biệt."
  do_not_use_if:
    - "BRD chưa hoàn thiện hoặc còn đang nháp."

related_skills:
  - name: "skill-document-sync-drive"
    difference: "split-brd dùng để chia nhỏ file nội bộ; sync-drive dùng để đẩy file ra hệ thống ngoài."
tags: ["document", "split", "brd", "knowledge-graph"]
requires:
  templates:
    - "docs/07-Process/Templates/Template-Skill.md"
---

# Bóc tách & Phân rã BRD (split-brd)

> Skill lo đúng MỘT việc: Đọc file BRD gốc, nhận diện các mảng nghiệp vụ (Business Rules, NFRs, Features), bóc tách chúng thành các file riêng lẻ trong `docs/02-Business-Rules/` hoặc `docs/01-Requirements/Features/` và gắn link liên kết chéo.

## Config
- `{{DOC_VAULT}}` — Thư mục tài liệu tri thức (VD: `docs/`)

## 1. Task Mindset & Core Principles
- **Librarian Mindset:** Bạn là thủ thư. Mục tiêu của việc bóc tách là để dễ tra cứu.
- **Knowledge Graph Integrity:** Khi tách rule ra file riêng (ví dụ `BR-001.md`), phải đảm bảo ở các file khác nếu có nhắc đến rule đó thì phải biến nó thành link `[[BR-001]]`.
- **Bảo toàn ngữ nghĩa:** Cắt nhỏ nhưng không được làm thay đổi hoặc mất chữ của bản gốc.

## 2. Core Execution Rules
1. **Gate:** Trả về `SpecialistResultContract` với `executionType: "FILE_CREATE"` chứa danh sách các file bóc tách. KHÔNG tự ghi đĩa.
2. **Cấu trúc lưu trữ mục tiêu:** 
   - Luật kinh doanh $\rightarrow$ `docs/02-Business-Rules/Core-Rules/BR-XXX.md`
   - Tính năng $\rightarrow$ `docs/01-Requirements/Features/Feature-XXX.md`
   - Phi chức năng (NFR) $\rightarrow$ `docs/01-Requirements/NFRs/NFR-XXX.md`

## 3. Quy trình Xử lý (Capability Workflow)

```mermaid
flowchart TD
    S1[1. Đọc file BRD] --> S2[2. Nhận diện các khối (Chunks)]
    S2 --> S3[3. Sinh nội dung file con]
    S3 --> S4[4. Quét & Tạo liên kết chéo Wiki-links]
    S4 --> S5[5. Đóng gói FILE_CREATE]
```

- **Bước 1:** Đọc toàn bộ nội dung file BRD.
- **Bước 2 & 3:** Trích xuất các Business Rules (Mục 8 của BRD) và NFRs (Mục 9 của BRD). Tạo thành các nội dung file độc lập.
- **Bước 4:** Gắn link.
- **Bước 5:** Đóng gói JSON. Đảm bảo nội dung file (content) đã được escape JSON hợp lệ (chuẩn mới).

## 4. Definition of Done
- [ ] Các file tách ra độc lập nhưng vẫn giữ được logic của bản gốc.
- [ ] Cấu trúc Wiki-links (`[[...]]`) được thiết lập.
- [ ] Nội dung trong mảng `proposedPayload` phải được escape JSON hợp lệ.
- [ ] Output tuân thủ 100% `SpecialistResultContract`.

## 5. Contract Binding
BẮT BUỘC trả về JSON hợp lệ:
```json
{
  "traceId": "<kế thừa>",
  "resultId": "<uuid>",
  "taskId": "<kế thừa>",
  "specialistRole": "DOCUMENT_AGENT",
  "contractType": "SPECIALIST_RESULT",
  "timestamp": "<current_iso_time>",
  "fromAgent": "DOCUMENT_AGENT",
  "toAgent": "ORCHESTRATOR",
  "executionType": "FILE_CREATE",
  "proposedPayload": [
    {
      "targetPath": "{{DOC_VAULT}}/02-Business-Rules/Core-Rules/BR-001.md",
      "content": "...nội dung đã escape JSON..."
    },
    {
      "targetPath": "{{DOC_VAULT}}/02-Business-Rules/Core-Rules/BR-002.md",
      "content": "...nội dung đã escape JSON..."
    }
  ],
  "chatMessage": "Đã bóc tách BRD thành các quy tắc nghiệp vụ độc lập.",
  "metadata": {
    "skillUsed": "skill-document-split-brd",
    "affectedModules": ["REQUIREMENTS", "BUSINESS_RULES"],
    "status": "READY_FOR_REVIEW",
    "version": 1
  }
}
```
