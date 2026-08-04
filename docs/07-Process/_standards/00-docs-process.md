# 📜 Tiêu chuẩn Bộ khung: Quy chuẩn Cấu trúc Cây Tài liệu Hệ thống & Quy trình Quản lý

> **Mã tiêu chuẩn:** `STD-FW-000`  
> **Phạm vi áp dụng:** Toàn bộ thư mục `docs/`, các Sub-Agent làm việc với tài liệu, và thành viên dự án AgenticWork.  
> **Vị trí tài liệu:** `docs/07-Process/_standards/00-docs-process.md` *(Tiêu chuẩn bộ khung framework)*

---

## 1. Lý do & Mục tiêu

Hệ thống tài liệu AgenticWork được thiết kế theo mô hình phân tách rõ ràng giữa **Tài liệu Nghiệp vụ Dự án** (từ `00-Meta` đến `06-Change-Log`) và **Tiêu chuẩn Vận hành Framework** (`07-Process/`). 

Tiêu chuẩn này quy định cây thư mục chuẩn, nguyên tắc tổ chức tài liệu và điểm vào duy nhất ([`00-INDEX.md`](../../00-INDEX.md)) giúp cả con người và AI Sub-Agent dễ dàng định vị, truy xuất thông tin mà không bị lẫn lộn.

---

## 2. Cấu trúc Cây Tài liệu Hệ thống (Docs Directory Structure)

```text
docs/
├── 00-INDEX.md                        # Điểm vào DUY NHẤT, bản đồ tổng hợp liên kết toàn bộ hệ thống
├── 00-Meta/                           # Từ điển, chỉ mục & các tài liệu Meta
│   ├── Glossary.md                    # Thuật ngữ dự án & thuật ngữ kỹ thuật (CRM, SALE, traceId, ...)
│   ├── Modules-Index.md               # Danh mục các phân hệ / module
│   ├── Business-Goals.md              # Mục tiêu kinh doanh
│   ├── Tags-Index.md                  # Chỉ mục thẻ
│   └── MOC-Home.md                    # Map of Content tổng
├── 01-Agent-Data-Contracts.md         # Quy chuẩn hợp đồng dữ liệu giữa các Sub-Agent
├── 01-Requirements/                   # Yêu cầu hệ thống
│   ├── Epics/                         # Các Epic lớn
│   ├── Features/                      # Tính năng chi tiết
│   └── User-Stories/                  # User Stories
├── 02-Business-Rules/                 # Quy tắc nghiệp vụ
│   ├── Core-Rules/                    # Quy tắc cốt lõi
│   ├── Permissions/                   # Quy định phân quyền
│   └── Validation-Rules/              # Quy tắc kiểm tra dữ liệu (Validation)
├── 02-Skill-Specification-Standard.md # Tiêu chuẩn kỹ thuật định nghĩa Skill cho Specialist Agents
├── 03-Data-Dictionary/                # Từ điển dữ liệu chi tiết theo từng phân hệ
│   ├── CRM/                           # Dữ liệu phân hệ CRM
│   └── SALE/                          # Dữ liệu phân hệ SALE
├── 04-Workflows-Diagrams/             # Sơ đồ luồng & quy trình nghiệp vụ
│   ├── ACCOUNT/                       # Luồng phân hệ ACCOUNT
│   └── SALE/                          # Luồng phân hệ SALE
├── 05-System-Responses/               # Mã phản hồi & thông báo hệ thống
│   ├── Global/                        # Mã lỗi & thông báo dùng chung
│   └── STOCK/                         # Mã lỗi & thông báo phân hệ STOCK
├── 06-Change-Log/                     # Nhật ký thay đổi & Yêu cầu thay đổi (CR)
│   └── 2026/                          # Theo năm/tháng
├── 07-Process/                        # Tiêu chuẩn vận hành Framework, Templates & Agent Tools
│   ├── README.md                      # Hướng dẫn chính về Process & Standards
│   ├── _standards/                    # Framework Standards Kit (Glossary, Workflow Engine, Docs process...)
│   ├── agent-template/               # Mẫu chuẩn hóa định nghĩa Sub-Agent (AGENT.md)
│   ├── Templates/                     # Thư viện mẫu Markdown (CR, Business Rule, Feature, Skill, v.v.)
│   └── tools/                         # Công cụ hỗ trợ vận hành (Python scripts & CLI tools)
└── 08-Workflow-Engine-Specification.md # Đặc tả kiến trúc & cơ chế vận hành Workflow Engine (LangGraph patterns)
```

---

## 3. Quy tắc Quản lý & Cập nhật Tài liệu

1. **Điểm vào Duy nhất (Single Entry Point):**
   - Mọi tài liệu mới tạo ra phải được dẫn link hoặc đăng ký tại [`docs/00-INDEX.md`](../../00-INDEX.md) hoặc file chỉ mục tương ứng trong phân hệ.
2. **Phân tách Rõ ràng:**
   - **Tài liệu Nghiệp vụ (Project/Domain Docs):** Đặt tại `00-Meta` đến `06-Change-Log`.
   - **Tiêu chuẩn Framework (Framework Standards):** Đặt tại `07-Process/_standards/`.
   - **Mẫu chuẩn (Templates):** Đặt tại `07-Process/Templates/` và `07-Process/agent-template/`.
3. **Mẫu Chuẩn hóa (Templates):**
   - Mọi tài liệu tạo mới cần tuân thủ các mẫu chuẩn sẵn có tại [`docs/07-Process/Templates/`](../../07-Process/Templates) (cho Feature, Business Rule, CR, Skill...) và [`docs/07-Process/agent-template/`](../../07-Process/agent-template) (cho `AGENT.md`).
    