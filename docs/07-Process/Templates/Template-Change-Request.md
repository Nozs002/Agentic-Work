---
type: change-request
id: CR-XXXX
title: <tiêu đề ngắn>
status: requested          # requested | approved | in-progress | testing | done | rejected | deferred
request_type: change       # feature | change | bug | question
priority: medium           # low | medium | high | urgent
requested_date: YYYY-MM-DD
modules: []                # bị tác động, vd ["[[09-reports]]"]
screens: []                # màn hình cụ thể (nếu biết)
api: []                    # endpoint liên quan (nếu biết)
related_crs: []
done_date:
lang: vi
tags: [change-request, client/<slug>, status/requested]
---

# CR-XXXX — <Tiêu đề> 

> Yêu cầu thay đổi. Module: [[09-reports]]
> 📋 [[_moc-change-requests]] · 🇬🇧 [[CR-XXXX-....en|EN]]

## 1. Bối cảnh & vấn đề

Yêu cầu của người dùng đang gặp / muốn gì, vì sao.

## 2. Kết quả mong đợi (Expected Behavior)

Mô tả luồng bước-một hoặc kết quả mong đợi sau thay đổi.

## 3. Phạm vi thay đổi (sửa gì, ở đâu)

> Phần này là cốt lõi truy vết — ghi càng cụ thể càng tốt.

- **Module tác động:** [[09-reports]]
- **Màn hình:** …
- **Dữ liệu & Fields (thêm/sửa/bỏ):** …  <!-- [MỚI] -->
- **API/Backend:** …
- **DB/Entity:** …
- **Ảnh hưởng lan tỏa (Blast Radius):** (module/báo cáo khác bị tác động)

## 4. Tiêu chí nghiệm thu (Acceptance Criteria) <!-- [MỚI] -->

Checklist để kiểm chứng tính năng đã XONG ĐÚNG:
- [ ] Scenario 1: Given... When... Then...
- [ ] Scenario 2: ...

## 5. Danh sách cần xác nhận (Open Questions) <!-- [MỚI - CỰC KỲ QUAN TRỌNG] -->

- [ ] ⚠️ `[Cần xác nhận với {{CLIENT}}]`: ...
- [ ] ⚠️ ...

## 6. Quyết định & ghi chú

- Đồng ý làm? phương án? đánh đổi?

## 7. Trạng thái & lịch sử

| Ngày | Trạng thái | Ghi chú |
|---|---|---|
| YYYY-MM-DD | requested | Khởi tạo từ phỏng vấn BA |

## 8. Liên kết

- CR liên quan: …
