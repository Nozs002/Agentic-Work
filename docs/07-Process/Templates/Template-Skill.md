---
name: {{SKILL_NAME_SLUG}}
description: >
  MÔ TẢ NGẮN GỌN VỀ MỤC TIÊU VÀ HOÀN CẢNH SỬ DỤNG CỦA SKILL NÀY (Dùng khi nào, giải quyết bài toán gì).
intentCategory: {{INTENT_CATEGORY}} # CODE_GEN | REQUIREMENTS_REFINEMENT | BUG_FIX | ARCHITECTURE_DESIGN | DOCUMENTATION | TESTING
triggers:
  - "{{TỪ_KHÓA_KÍCH_HOẠT_1}}"
  - "{{TỪ_KHÓA_KÍCH_HOẠT_2}}"
  - "{{KEYWORDS_ENGLISH}}"

requires:
  templates:
    - "docs/00-Meta/Templates/{{TEMPLATE_FILE}}.md"
  registries:
    - "docs/00-INDEX.md"
---

# {{SKILL_TITLE}}

## Config (điền khi áp vào dự án)
- `{{PROJECT}}` — tên dự án
- `/docs` — thư mục docs vault
- `{{LANG_PRIMARY}}` / `{{LANG_SECONDARY}}` — ngôn ngữ (mặc định VI + EN)

## Mục tiêu
Mô tả rõ ràng kết quả cụ thể mà Skill này phải đạt được sau khi thực thi.

## Nguyên tắc Cốt lõi
1. **Zero-hallucination:** Không tự bịa thông tin chưa được xác nhận.
2. **Tuân thủ Tiêu chuẩn:** Mọi đầu ra phải tuân thủ Hợp đồng Dữ liệu `SpecialistResultContract`.
3. **Phân rã Rõ ràng:** Thực hiện tư duy theo từng bước (Step-by-Step Reasoning).

## Quy trình Tư duy & Các Bước Thực thi
Mô tả quy trình cụ thể mà Specialist Agent cần tuân theo:

1. **Bước 1: Phân tích bối cảnh** — Đọc và đối chiếu dữ liệu do Knowledge Agent nạp.
2. **Bước 2: Xử lý logic** — Áp dụng các quy tắc chuyên môn để giải quyết bài toán.
3. **Bước 3: Tổng hợp kết quả** — Đóng gói sản phẩm.

## Định dạng Đầu ra (Artifact Contract / Output Envelope)
Agent thực thi skill này **BẮT BUỘC** phải đóng gói kết quả theo chuẩn `SpecialistResultContract` (Output Envelope) gửi tới Gateway 2 (Review QA) kiểm duyệt:

```json
{
  "executionType": "FILE_CREATE", // FILE_CREATE | FILE_MODIFY | FILE_DELETE | CHAT_RESPONSE
  "proposedPayload": [
    {
      "targetPath": "đường/dẫn/file/mục/tiêu.ext",
      "content": "...Nội dung file..."
    }
  ],
  "chatMessage": "Mô tả ngắn gọn kết quả gửi cho người dùng trên IDE...",
  "metadata": {
    "skillUsed": "{{SKILL_NAME_SLUG}}",
    "affectedModules": ["MODULE_NAME"]
  }
}
```

## Tiêu chí Nghiệm thu (Gate Criteria)
- [ ] Đầu ra tuân thủ đúng template.
- [ ] Không có mâu thuẫn với Business Rules.
- [ ] Đã kiểm tra lỗi cú pháp và linter.
