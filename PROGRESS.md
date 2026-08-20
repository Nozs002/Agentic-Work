# Nhật ký Tiến độ & Bối cảnh Kiến trúc (Architecture Context) - Khởi tạo Document Agent

**Ngày cập nhật:** 2026-08-20
**Thực hiện bởi:** AI Assistant (Antigravity)

> **Lưu ý:** File này được thiết kế để cung cấp đầy đủ bối cảnh (Context) về Document Agent cho các Agent khác đọc lại trong tương lai, đề phòng trường hợp lịch sử hội thoại bị xóa.

---

## 1. Document Agent là gì? (Executive Summary)

Trong hệ thống Multi-Agent, **Document Agent** đóng vai trò là **"Thư ký & Người gác cổng tri thức" (Technical Writer & Knowledge Manager)**.
Nhiệm vụ cốt lõi:
- Đọc hiểu mã nguồn (Code AST), Cấu trúc Database, và các đặc tả đã duyệt (như BRD).
- Dịch "Ngôn ngữ máy" hoặc tài liệu nguyên khối khổng lồ sang "Ngôn ngữ người" dễ đọc (API Docs, User Manuals, Release Notes, Mermaid Diagrams).
- Sắp xếp, đánh chỉ mục (Indexing) và quy hoạch thư mục tri thức `docs/`.

---

## 2. Ranh giới Trách nhiệm: BA Agent vs Document Agent

Rất dễ nhầm lẫn giữa BA Agent và Document Agent vì cả hai đều "viết tài liệu". Sự khác biệt nằm ở **Source of Truth** và **Loại tài liệu**:

| Tiêu chí | 👨‍💼 BA Agent (`ba`) | ✍️ Document Agent (`document`) |
| :--- | :--- | :--- |
| **Giai đoạn** | **Trước khi Code:** Định hình hệ thống CẦN LÀM GÌ. | **Trong/Sau Code:** Ghi chép hệ thống ĐÃ LÀM GÌ và HƯỚNG DẪN. |
| **Input (Nguồn)** | Phỏng vấn, ý tưởng, mục tiêu kinh doanh. | Source Code, DB Schema, file BRD đã chốt. |
| **Output (Đầu ra)** | BRD, Change Request, User Story. | API Specs, User Manual, Release Notes, Sơ đồ Mermaid, Bóc tách quy tắc. |
| **Bản chất** | **Sáng tác bộ luật** (Nhà lập pháp). | **Xuất bản & Sắp xếp** (Nhà xuất bản / Thủ thư). |

---

## 3. Kiến trúc Bảo mật (Security & Execution Architecture)

**Document Agent KHÔNG PHẢI là Action Agent.**
Theo chuẩn `STD-FW-001` và `STD-FW-002`, Document Agent nằm ở **Layer 3 (Specialist Execution)**. Nó bị cấm tuyệt đối việc ghi file trực tiếp hoặc gọi API mạng (Zero Untyped Side-Effects). 

**Cơ chế Ủy quyền (Delegation):**
1. **File System:** Để tạo/sửa file, nó sinh ra `SpecialistResultContract` với `executionType: "FILE_CREATE"` hoặc `"FILE_MODIFY"`. Lệnh này đi qua Gateway kiểm duyệt trước khi Action Agent lưu vào đĩa.
2. **External API (VD: Google Drive):** Để đồng bộ lên Drive, nó không tự gọi HTTP Request. Thay vào đó, nó xuất ra `executionType: "STRUCTURED_RESULT"` chứa payload `GoogleDriveSyncCommand`. Action Engine (Hệ thống Runtime) sẽ bắt payload này và thực hiện gọi API an toàn.

---

## 4. Tầm nhìn Knowledge Graph (Kiểu Obsidian)

Một tính năng xuất sắc của Document Agent là biến toàn bộ vault `docs/` thành một mạng lưới tri thức liên kết (Knowledge Graph).
- **Phân rã (Split):** Tự động bóc tách các file khổng lồ (BRD) thành các quy tắc nhỏ (`BR-001.md`, `BR-002.md`).
- **Liên kết chéo (Cross-linking):** Tự động chèn các Wiki-links (`[[BR-001]]`) vào các tài liệu tính năng để dễ tra cứu.
- **Tính toàn vẹn:** Đảm nhiệm việc quét và cảnh báo các liên kết hỏng (Broken Links / Dead-link Checker) nếu có file bị thay đổi tên hoặc xóa.

---

## 5. Danh sách File Đã Triển Khai (Implementation Logs)

| Loại | Đường dẫn File (Path) | Trạng thái | Mô tả chi tiết |
| :--- | :--- | :--- | :--- |
| **Agent Spec** | `agents/document/AGENT.md` | ✅ Mới | Bản đặc tả danh tính, ranh giới và chuẩn đầu ra/đầu vào. |
| **Skill** | `skills/document/split-brd/SKILL.md` | ✅ Mới | Kỹ năng đọc BRD, bóc tách ra `docs/02-Business-Rules/` và tạo link `[[BR-XXX]]`. Bắn lệnh `FILE_CREATE`. |
| **Skill** | `skills/document/sync-drive/SKILL.md` | ✅ Mới | Kỹ năng chuẩn hóa nội dung, phát lệnh `STRUCTURED_RESULT` (`GoogleDriveSyncCommand`) để uỷ quyền cho Action Engine upload lên Drive. |
| **Tracking** | `PROGRESS.md` | ✅ Đã cập nhật | File báo cáo tiến độ và lưu trữ Context Kiến trúc. |

## 🚀 Bước tiếp theo (Next Steps)
- Kích hoạt Orchestrator để test thử luồng cấp phát (Dispatch) một file `BRD.md` nháp cho `DOCUMENT_AGENT`.
- Xây dựng thêm Skill `audit-links` (tìm link hỏng) và `generate-api-docs` (sinh tài liệu API từ code).
