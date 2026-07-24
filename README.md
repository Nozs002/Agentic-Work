# Agentic Work: Nền tảng Hạ tầng Đa tác tử AI

> **Động cơ trung gian (Middleware Engine) kết nối các AI IDE với Codebase quy mô lớn thông qua Đồ thị Tri thức Kép và chuẩn giao tiếp MCP, đảm bảo mã nguồn sinh ra 100% tuân thủ quy tắc nghiệp vụ.**

> 📌 **Định hướng phát triển:** Trong **Giai đoạn 1 (Phase 1)**, dự án tập trung chủ yếu vào việc vận hành ổn định, tối ưu hóa và tương thích sâu trên **Antigravity IDE**.

---

## 1. Tổng quan & Bài toán giải quyết

Khi ứng dụng các công cụ AI IDE vào các dự án phần mềm doanh nghiệp phức tạp (như ERP, Odoo), đội ngũ phát triển phải đối mặt với hai rủi ro chí mạng:
* **Lãng phí Token & Trôi Ngữ cảnh (Context Drift):** Các hệ thống AI thông thường nhồi nhét hàng ngàn dòng code không liên quan vào ngữ cảnh, làm AI bị "nhiễu ảo giác" (hallucination) và tiêu tốn chi phí API đắt đỏ.
* **Vi phạm Quy tắc Nghiệp vụ (Business Rules Violation):** AI sinh ra code đúng cú pháp nhưng phá vỡ luồng logic kinh doanh ngầm của công ty.

**Agentic Work** ra đời để giải quyết triệt để vấn đề này bằng cách thiết lập một "Trạm kiểm duyệt đa tác tử" đứng giữa IDE và Codebase, trang bị khả năng tư duy tự trị, quản lý ngữ cảnh động và phân tích ảnh hưởng thời gian thực.

---

## 2. Lộ trình Phát triển (Roadmap Focus)

* 🚀 **Giai đoạn 1 (Hiện tại):** Tập trung phát triển hạ tầng core, bộ kiểm duyệt Gatekeeper, Dual Knowledge Graph và adapter hỗ trợ ưu tiên hoạt động tốt nhất trên **Antigravity IDE** (`packages/adapters/antigravity`).
* 🔮 **Giai đoạn 2:** Mở rộng và tối ưu hỗ trợ toàn diện cho các IDE khác (Cursor, Windsurf, Trae, VSCode).

---

## 3. Giá trị Cốt lõi & Cơ chế Vận hành

* **Đồ thị Tri thức Kép (Dual Knowledge Graph Engine):**
  * *Code Graph:* Sử dụng Tree-sitter bóc tách Cây cú pháp trừu tượng (AST) và lưu vào SQLite (`packages/ast-indexer`).
  * *Business Graph:* Quản lý tập luật nghiệp vụ thông qua một Obsidian Vault (`docs/`), ép chuẩn bằng YAML Frontmatter.
* **Cơ chế Kiểm duyệt Kép (Dual-Gateway Verification):**
  * *Gateway 1 (Policy Engine):* Phát hiện và chặn các prompt mâu thuẫn với luật trước khi sinh code.
  * *Gateway 2 (Review QA):* Ép LLM tự động sửa lỗi (Retry Loop) nếu mã nguồn vi phạm tiêu chuẩn.
* **Tính toán Bán kính Ảnh hưởng (Blast Radius):** Cảnh báo lập tức các file, API, và logic bị ảnh hưởng dây chuyền khi thay đổi code hoặc luật nghiệp vụ.
* **Tiêm Kỹ năng Động (JIT Skill Injection):** Hỗ trợ cắm thêm các kỹ năng chuyên biệt (`skills/ba/`, `skills/backend/`...) lúc runtime.

---

## 4. Cấu trúc Dự án (Project Structure)

```
├── apps/                                   # Các ứng dụng (cli, dashboard, mcp-server, playground)
├── packages/                               # Core libraries (agent-core, gatekeeper, graph-engine, adapters...)
│   └── adapters/
│       ├── antigravity/                    # Antigravity IDE Adapter (Tập trung Giai đoạn 1)
│       └── ...
├── docs/                                   # Obsidian Vault — Business Knowledge Graph
├── agents/                                 # Định nghĩa Agents (planner, document, architect, qa...)
├── skills/                                 # Prompt Skills (ba, backend, frontend...)
├── config/                                 # Cấu hình hệ thống & Agents
├── schemas/                                # YAML/JSON Schemas
└── tests/                                  # Integration & Unit Tests
```
