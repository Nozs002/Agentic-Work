# 📚 Project Glossary & Path Aliases Registry

> **Nguồn sự thật duy nhất (Single Source of Truth) cho Thuật ngữ Dự án & Ánh xạ Đường dẫn Hệ thống.**  
> **Mục đích:** Tập tập trung toàn bộ thuật ngữ hệ thống (ví dụ: `PRIMARY_DOC_LANGUAGE`) và bí danh đường dẫn (Path Aliases / Contract Names) vào một nơi duy nhất. Khi clone hoặc di chuyển dự án, người dùng chỉ cần cập nhật file này (và `config/glossary.yaml`), toàn bộ Sub-Agent và hệ thống sẽ tự động cập nhật mà không cần chỉnh sửa từng file.

---

## 1. System Configuration

| Thuật ngữ / Khai báo | Giá trị / Định nghĩa | Giải thích ý nghĩa & Phạm vi sử dụng |
| :--- | :--- | :--- |
| `PROJECT_NAME` | `<TÊN_DỰ_ÁN>` | Tên dự án sản phẩm nghiệp vụ của người dùng (quản lý từ thư mục `00-Meta` đến `06-Change-Log`). |
| `FRAMEWORK_NAME` | `AgenticWork` | Tên bộ khung (framework) quản lý quy trình, tiêu chuẩn vận hành và Sub-Agents (`docs/07-Process/`). |
| `DOC_VAULT` | `docs/` | Thư mục tài liệu của dự án (cơ sở tri thức dành cho AI). |
| `REPOSITORY_URL` | `<LINK_GITHUB_REPO>` | Đường dẫn kho lưu trữ mã nguồn (GitHub Repository) của dự án. |
| `GOOGLE_DRIVE_URL` | `<LINK_GOOGLE_DRIVE>` | Thư mục lưu trữ tài liệu kỹ thuật dành cho con người (Dev, Khách hàng) đọc. |
| `TECH_STACK` | `<ĐIỀN_STACK_CÔNG_NGHỆ>` | Tập hợp công nghệ, ngôn ngữ lập trình và định dạng dữ liệu được dự án sử dụng. |
| `PRIMARY_DOC_LANGUAGE` | `Vietnamese` | Ngôn ngữ chính được dự án sử dụng để viết tài liệu hệ thống và giao tiếp với Agent. |
| `SECONDARY_DOC_LANGUAGE` | `English` | Ngôn ngữ phụ trợ dùng cho tài liệu kỹ thuật, mã nguồn và chuẩn giao tiếp. |
| `DEFAULT_TIMEZONE` | `Asia/Ho_Chi_Minh` (UTC+7) | Múi giờ chuẩn dùng cho thuộc tính `timestamp` trong mọi gói tin hợp đồng và hệ thống log. |
| `ENVIRONMENT` | `development` | Môi trường thực thi của hệ thống (`development`, `staging`, `production`). |

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
| **`WorkflowState`** | Trạng thái toàn cục duy nhất của quy trình công việc do Orchestrator quản lý độc quyền (không chia sẻ trực tiếp cho Agent sửa). |
| **`CapabilityWorkflow`** | Quy trình thực thi nhiều bước (Graph) bên trong một Skill (Layer 3 Workflow), thay thế cho prompt tĩnh đơn lẻ. |
| **`WorkflowDSL`** | Ngôn ngữ khai báo cấu hình quy trình dạng YAML (Node, Edge, Condition, Loop, Parallel DAG, Checkpoint). |
| **`Checkpoint`** | Điểm lưu ảnh chụp trạng thái quy trình (State Snapshot) để phục vụ Human-in-the-Loop hoặc phục hồi sau sự cố. |

---

## 3. Data Contracts Registry

> Các Sub-Agent khai báo Hợp đồng qua **Logical Name** tại đây. Runtime Engine sẽ tra bảng này để tìm file Schema tương ứng.

| Logical Contract Name | File Schema Tương Ứng (Runtime Physical Path) |
| :--- | :--- |
| `BaseContract` | `schemas/base-contract.schema.json` |
| `AgentDispatchContract` | `schemas/agent-dispatch.schema.json` |
| `SpecialistResultContract` | `schemas/specialist-result.schema.json` |
| `WorkflowDefinitionContract` | `schemas/workflow-definition.schema.json` |

---

## 4. System Path Aliases Map (Bảng Ánh xạ Đường dẫn Hệ thống)

> Toàn bộ các thư mục và file hệ thống được quản lý thông qua tên đại diện (Path Aliases).

| Path Alias (Tên Đại diện) | Relative Path (Đường dẫn Tương đối) |
| :--- | :--- |
| `PATH_ROOT` | `./` |
| `PATH_SCHEMAS` | `schemas/` |
| `PATH_CONFIG` | `config/` |
| `PATH_GLOSSARY_CONFIG` | `config/glossary.yaml` |
| `PATH_USER_DOCS` | `docs/` |
| `PATH_FRAMEWORK_PROCESS` | `docs/07-Process/` |
| `PATH_FRAMEWORK_STANDARDS` | `docs/07-Process/_standards/` |
| `PATH_AGENT_TEMPLATE` | `docs/07-Process/agent-template/` |
| `PATH_BUSINESS_RULES` | `docs/02-Business-Rules/` |
| `PATH_SYSTEM_ARCHITECTURE` | `00-System-Architecture.md` |
| `PATH_WORKFLOW_SCHEMA` | `schemas/workflow-definition.schema.json` |
| `PATH_WORKFLOW_SPEC` | `docs/08-Workflow-Engine-Specification.md` |
| `PATH_WORKFLOW_STANDARD` | `docs/07-Process/_standards/Workflow-Engine-Standard.md` |
