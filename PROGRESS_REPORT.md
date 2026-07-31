# 📝 Báo cáo Tiến độ Công việc (Progress Report) — 2026-08-01

> **Trạng thái:** Đang thực hiện (In-Progress)  
> **Chủ đề chính:** Chuẩn hóa Quy tắc Glossary & Quản lý Đường dẫn (Path Aliases) cho Bộ khung AgenticWork.

---

## 1. ✅ Những việc ĐÃ HOÀN THÀNH (Accomplished)

1. **Phân định rõ ràng Kiến trúc Thư mục Framework:**
   - Xác định thư mục `docs/07-Process/` là nơi lưu trữ duy nhất cho **Tiêu chuẩn vận hành, Quy trình & Templates của Bộ khung (Framework)**.
   - Giữ nguyên các thư mục tài liệu từ `00-Meta` đến `06-Change-Log` làm không gian chứa tài liệu nghiệp vụ sản phẩm riêng của Người dùng.

2. **Ban hành Tiêu chuẩn Bộ khung `STD-FW-001`:**
   - Đã tạo file tiêu chuẩn: [`docs/07-Process/_standards/Glossary-And-Path-Aliases-Standard.md`](file:///d:/Workspace/Projects/AgenticWork/docs/07-Process/_standards/Glossary-And-Path-Aliases-Standard.md).
   - Quy định việc quản lý thuật ngữ tập trung (như `primary_language`) và nguyên tắc tách biệt đường dẫn (Decoupling paths).

3. **Tạo Cấu hình Máy đọc `config/glossary.yaml`:**
   - Tạo file [`config/glossary.yaml`](file:///d:/Workspace/Projects/AgenticWork/config/glossary.yaml) chứa khai xạ `terms`, `contracts`, và `paths` để Runtime Engine / Orchestrator đọc tự động lúc runtime.

4. **Khởi tạo Khung Mẫu `Glossary.md`:**
   - Cập nhật file [`docs/00-Meta/Glossary.md`](file:///d:/Workspace/Projects/AgenticWork/docs/00-Meta/Glossary.md) làm Single Source of Truth cho thuật ngữ và Bảng ánh xạ Path Aliases.

5. **Cập nhật Mẫu Sub-Agent Template:**
   - Tinh chỉnh file mẫu [`docs/07-Process/agent-template/AGENT.md`](file:///d:/Workspace/Projects/AgenticWork/docs/07-Process/agent-template/AGENT.md) và cập nhật `07-Process/README.md`.

---

## 2. ⏳ Những việc ĐANG DỞ & CHƯA HOÀN THÀNH (In Progress / Pending)

1. **Chưa chuẩn hóa xong hoàn toàn file Glossary (`Glossary.md` & `config/glossary.yaml`):**
   - Hiện tại mới chỉ hoàn thiện khung mẫu và định nghĩa một số thuật ngữ cơ bản (Mục 1 & Mục 2 cơ bản).
   - 📌 **CHÚ THÍCH CẦN THỰC HIỆN:** **"Tham khảo AI chuẩn hóa từ mục 2"**  
     *(Ngày mai khi mở dự án lên, yêu cầu AI tiếp tục rà soát, tham khảo và hoàn thiện chuẩn hóa toàn bộ nội dung chi tiết từ Mục 2 trở đi trong `docs/00-Meta/Glossary.md` và `config/glossary.yaml`).*

2. **Chưa cập nhật Path Aliases cho các file cấu hình khác trong `config/`:**
   - Các file như `config/agents.yaml`, `config/graph.yaml`, `config/mcp.yaml` chưa áp dụng ánh xạ từ `glossary.yaml`.

3. **Chưa rà soát toàn bộ các Sub-Agent specs hiện có trong `agents/`:**
   - Cần đối chiếu các Agent trong `agents/` với tiêu chuẩn `STD-FW-001`.

---

## 3. 🎯 Việc CẦN LÀM TIẾP THEO (Next Steps for Tomorrow)

1. **[ƯU TIÊN 1] Complete Glossary Standardization:**
   - Nhắc AI: *"Tham khảo AI chuẩn hóa từ mục 2 trong file Glossary.md và config/glossary.yaml"*.
   - Hoàn thiện đầy đủ các thuật ngữ chuyên môn, Data Contracts mapping và danh sách đầy đủ tất cả Path Aliases hệ thống.

2. **[ƯU TIÊN 2] Integrate with Config Files:**
   - Áp dụng `config/glossary.yaml` vào các file cấu hình chính của hệ thống (`agents.yaml`, `dashboard.yaml`, `graph.yaml`, `mcp.yaml`).

3. **[ƯU TIÊN 3] Validate Sub-Agent Specs:**
   - Rà soát các Sub-Agent specs trong `agents/` để đảm bảo việc phân giải đường dẫn và thuật ngữ hoàn toàn tuân thủ `STD-FW-001`.
