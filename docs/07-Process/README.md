# 📁 07-Process — Tiêu chuẩn Vận hành, Quy trình & Templates 

> ⚠️ **LƯU Ý VỀ PHÂN TÁCH TÀI LIỆU:**  
> Thư mục `07-Process/` là nơi chứa **toàn bộ tiêu chuẩn vận hành, quy trình làm việc và mẫu chuẩn hóa (Templates) của bộ khung AgenticWork**.  
> Tất cả tài liệu nghiệp vụ sản phẩm riêng của người dùng sẽ nằm ở các thư mục từ `00-Meta` đến `06-Change-Log`. Việc phân tách này giúp không làm lẫn lộn tiêu chuẩn của framework với quy tắc nghiệp vụ dự án người dùng.

---

** Standards (Đọc trước) : **
[_standards/](_standards/README.md) — [Luật nền]

## 📜 Danh sách Tiêu chuẩn Vận hành Framework (`docs/07-Process/_standards/`)

- 📐 [STD-FW-000: Documentation Structure & Process Standard](_standards/00-docs-process.md) — Quy chuẩn cấu trúc cây tài liệu hệ thống, phân tách giữa tài liệu nghiệp vụ dự án và tiêu chuẩn framework.
- 📚 [STD-FW-001: Glossary & Path Aliases Standard](_standards/01-glossary-and-path.md) — Tiêu chuẩn quy định quản lý tập trung thuật ngữ (`TECH_STACK`) và tách biệt đường dẫn (Decoupled Contracts & Path Aliases) qua `config/glossary.yaml`.
- 🔀 [STD-FW-002: Workflow Engine Standard](_standards/Workflow-Engine-Standard.md) — Tiêu chuẩn thiết kế Workflow Engine áp dụng LangGraph Patterns (Graph Thinking, Centralized `WorkflowState`, 3-Layer Workflow, Contract-based Execution, Checkpoints) độc lập với framework.

---

## 📋 Danh sách Templates chuẩn cho Sub-Agents & Quy trình (`docs/07-Process/agent-template/` & `Templates/`)

- 🤖 [AGENT.md Spec Template](file:///d:/Workspace/Projects/AgenticWork/docs/07-Process/agent-template/AGENT.md) — Mẫu chuẩn hóa định nghĩa Sub-Agent (System Instructions, Scope, Logical Contracts, SOP, Error Escalation).
- 🛠️ [Template-Skill.md](file:///d:/Workspace/Projects/AgenticWork/docs/07-Process/Templates/Template-Skill.md) — Mẫu chuẩn hóa định nghĩa Skill cho Specialist Agents.
- 📐 [Template-Feature.md](file:///d:/Workspace/Projects/AgenticWork/docs/07-Process/Templates/Template-Feature.md) — Mẫu định nghĩa Feature.
- 📌 [Template-Change-Request.md](file:///d:/Workspace/Projects/AgenticWork/docs/07-Process/Templates/Template-Change-Request.md) — Mẫu Yêu cầu Thay đổi (CR).
- 📜 [Template-Business-Rule.md](file:///d:/Workspace/Projects/AgenticWork/docs/07-Process/Templates/Template-Business-Rule.md) — Mẫu Quy tắc Nghiệp vụ.
