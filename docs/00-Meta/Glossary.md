# 📚 Project Glossary & Path Aliases Registry

> **Nguồn sự thật duy nhất (Single Source of Truth) cho Thuật ngữ Dự án & Ánh xạ Đường dẫn Hệ thống.**  
> **Mục đích:** Tập trung toàn bộ thuật ngữ hệ thống (ví dụ: `primary_language`) và bí danh đường dẫn (Path Aliases / Contract Names) vào một nơi duy nhất. Khi clone hoặc di chuyển dự án, người dùng chỉ cần cập nhật file này (và `config/glossary.yaml`), toàn bộ Sub-Agent và hệ thống sẽ tự động cập nhật mà không cần chỉnh sửa từng file.

---

## 1. System Configuration & Primary Language (Cấu hình Cơ bản)

| Thuật ngữ / Khai báo | Giá trị / Định nghĩa | Giải thích ý nghĩa & Phạm vi sử dụng |
| :--- | :--- | :--- |
| `primary_language` | `TypeScript` / `Python` | Ngôn ngữ lập trình chính được dự án ưu tiên sử dụng để phát triển mã nguồn và các module xử lý. |
| `secondary_language` | `Markdown` / `JSON` | Ngôn ngữ phụ trợ dùng để khai báo hợp đồng dữ liệu, tài liệu quy trình và cấu hình. |
| `default_timezone` | `Asia/Ho_Chi_Minh` (UTC+7) | Múi giờ chuẩn dùng cho thuộc tính `timestamp` trong mọi gói tin hợp đồng và hệ thống log. |
| `environment` | `development` | Môi trường thực thi của hệ thống (`development`, `staging`, `production`). |

---

## 2. Core Terms & Concepts (Từ điển Thuật ngữ Cốt lõi)

| Thuật ngữ | Định nghĩa & Ý nghĩa Hệ thống |
| :--- | :--- |
| **`traceId`** | Mã định danh duy nhất (UUID/Slug) theo dõi toàn bộ vòng đời của một yêu cầu xuyên suốt các Sub-Agent (Distributed Tracing). |
| **`taskId`** | Mã định danh của một nút công việc cụ thể trong Đồ thị Công việc (Task DAG) do Planner Agent tạo ra. |
| **`contractType`** | Loại hợp đồng dữ liệu JSON được trao đổi giữa các Agent (ví dụ: `AGENT_DISPATCH`, `SPECIALIST_RESULT`). |
| **`Blast Radius`** | Bán kính ảnh hưởng — tập hợp tất cả các file, API, và logic bị tác động dây chuyền khi một đoạn code hoặc quy tắc thay đổi. |
| **`Dual Knowledge Graph`** | Đồ thị Tri thức Kép kết hợp **Code Graph** (AST mã nguồn) và **Business Graph** (Luật nghiệp vụ Obsidian) để truy xuất ngữ cảnh chính xác. |
| **`Logical Contract Name`** | Tên đại diện cho hợp đồng dữ liệu (ví dụ: `AgentDispatchContract`), giúp decoupled `AGENT.md` khỏi vị trí file đĩa vật lý. |
| **`Path Alias`** | Bí danh đường dẫn (ví dụ: `PATH_SCHEMAS`), cho phép hệ thống tham chiếu thư mục/file mà không hardcode đường dẫn tuyệt đối. |

---

## 3. Data Contracts Registry (Danh mục Hợp đồng Dữ liệu)

> Các Sub-Agent khai báo Hợp đồng qua **Logical Name** tại đây. Runtime Engine sẽ tra bảng này để tìm file Schema tương ứng.

| Logical Contract Name | File Schema Tương Ứng (Runtime Physical Path) | Mục đích |
| :--- | :--- | :--- |
| `BaseContract` | `schemas/base-contract.schema.json` | Hợp đồng cơ sở chứa `traceId`, `timestamp`, `contractType`. |
| `AgentDispatchContract` | `schemas/agent-dispatch.schema.json` | Lệnh điều phối công việc từ Orchestrator gửi đến Sub-Agent. |
| `SpecialistResultContract` | `schemas/specialist-result.schema.json` | Kết quả thực thi đóng gói từ Sub-Agent gửi về Orchestrator / QA. |

---

## 4. System Path Aliases Map (Bảng Ánh xạ Đường dẫn Hệ thống)

> Toàn bộ các thư mục và file hệ thống được quản lý thông qua tên đại diện (Path Aliases).

| Path Alias (Tên Đại diện) | Relative Path (Đường dẫn Tương đối) | Mô tả Chức năng |
| :--- | :--- | :--- |
| `PATH_ROOT` | `./` | Thư mục gốc của dự án. |
| `PATH_SCHEMAS` | `schemas/` | Thư mục chứa toàn bộ JSON Schemas của các Data Contracts. |
| `PATH_CONFIG` | `config/` | Thư mục chứa các file cấu hình YAML của hệ thống. |
| `PATH_GLOSSARY_CONFIG` | `config/glossary.yaml` | File cấu hình máy đọc chứa Glossary & Path Mapping runtime. |
| `PATH_USER_DOCS` | `docs/` | Thư mục chứa tài liệu sản phẩm dự án của người dùng (`00` đến `06`). |
| `PATH_FRAMEWORK_PROCESS` | `docs/07-Process/` | Thư mục chứa quy trình, tiêu chuẩn vận hành và mẫu của bộ khung AgenticWork. |
| `PATH_FRAMEWORK_STANDARDS` | `docs/07-Process/_standards/` | Thư mục chứa các tiêu chuẩn vận hành chính thức của bộ khung framework. |
| `PATH_AGENT_TEMPLATE` | `docs/07-Process/agent-template/` | Thư mục mẫu chuẩn hóa để khởi tạo Sub-Agent mới. |
| `PATH_BUSINESS_RULES` | `docs/02-Business-Rules/` | Thư mục chứa các quy tắc nghiệp vụ dự án của người dùng. |
| `PATH_SYSTEM_ARCHITECTURE` | `00-System-Architecture.md` | Tài liệu kiến trúc 4 tầng của bộ khung. |
