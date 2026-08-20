# Agentic Work: Nền tảng Hạ tầng Đa tác tử AI

> **Động cơ trung gian (Middleware Engine) kết nối các AI IDE với Codebase quy mô lớn thông qua Đồ thị Tri thức Kép và chuẩn giao tiếp MCP, đảm bảo mã nguồn sinh ra 100% tuân thủ quy tắc nghiệp vụ.**
>
> 🚀 **Ghi chú Giai đoạn Khởi tạo (Phase 1):** Hiện tại, toàn bộ hệ thống đang được thiết kế và tối ưu riêng để chạy trực tiếp (natively) trên nền tảng **Antigravity IDE**. Các tác tử (Agent) và kỹ năng (Skill) sẽ được ánh xạ thành hệ sinh thái **Antigravity Customizations (Rules & Skills)**, cho phép vận hành luồng làm việc đa tác tử ngay trong IDE mà chưa cần chạy server Node.js độc lập.

---

## 1. Tổng quan & Bài toán giải quyết

Khi ứng dụng các công cụ AI IDE (Cursor, Windsurf, Trae, Antigravity) vào các dự án phần mềm doanh nghiệp phức tạp (như ERP, Odoo), đội ngũ phát triển phải đối mặt với hai rủi ro chí mạng:
*   **Lãng phí Token & Trôi Ngữ cảnh (Context Drift):** Các hệ thống AI thông thường nhồi nhét hàng ngàn dòng code không liên quan vào ngữ cảnh, làm AI bị "nhiễu ảo giác" (hallucination) và tiêu tốn chi phí API đắt đỏ.
*   **Vi phạm Quy tắc Nghiệp vụ (Business Rules Violation):** AI sinh ra code đúng cú pháp nhưng phá vỡ luồng logic kinh doanh ngầm của công ty (Ví dụ: vô tình cho phép khách hàng tiêu chuẩn sử dụng mã giảm giá của khách VIP).

**AutoForge** ra đời để giải quyết triệt để vấn đề này bằng cách thiết lập một "Trạm kiểm duyệt đa tác tử" đứng giữa IDE và Codebase, trang bị khả năng tư duy tự trị, quản lý ngữ cảnh động và phân tích ảnh hưởng thời gian thực.

---

## 2. Giá trị Cốt lõi & Cơ chế Vận hành

*   **Đồ thị Tri thức Kép (Dual Knowledge Graph Engine):**
    *   *Code Graph:* Sử dụng Tree-sitter bóc tách Cây cú pháp trừu tượng (AST) và lưu vào SQLite. Nó chỉ truy xuất đúng Subgraph (đồ thị con) liên quan, giúp tiết kiệm 80-90% token.
    *   *Business Graph:* Quản lý tập luật nghiệp vụ thông qua một Obsidian Vault (tài liệu Markdown), ép chuẩn bằng YAML Frontmatter và tự động theo dõi thay đổi bằng File Watcher.
*   **Cơ chế Kiểm duyệt Kép (Dual-Gateway Verification):**
    *   *Gateway 1 (Policy Engine):* Chốt chặn trước thực thi. Phát hiện và chặn các prompt mâu thuẫn với luật trước khi sinh ra bất kỳ dòng code nào.
    *   *Gateway 2 (Review QA):* Chốt chặn sau thực thi. Ép LLM tự động sửa lỗi (vòng lặp Retry Loop) nếu mã nguồn hoặc tài liệu sinh ra vi phạm tiêu chuẩn.
*   **Tính toán Bán kính Ảnh hưởng (Blast Radius):** Ứng dụng thuật toán duyệt đồ thị để cảnh báo lập tức các file, API, và logic bị ảnh hưởng dây chuyền khi thay đổi một đoạn code hoặc luật nghiệp vụ.
*   **Zero-Config & Trải nghiệm tàng hình:** Tích hợp trực tiếp qua giao thức **MCP (Model Context Protocol)**. Lập trình viên khởi chạy CLI và quan sát trạng thái trên **Local Web Dashboard (localhost:9876)** mà không phải thay đổi thói quen code hàng ngày.
*   **Tiêm Kỹ năng Động (JIT Skill Injection):** Hỗ trợ cắm thêm các kỹ năng chuyên biệt (NPM packages, Obsidian Custom Skills) ngay lúc runtime (thời gian chạy) tùy thuộc vào framework mục tiêu (Odoo, React, NestJS).

---

## 3. Kiến trúc 4 Tầng (4-Layered Architecture)

Hệ thống tuân thủ nguyên tắc thiết kế ranh giới nghiêm ngặt: Tầng dưới không được quyền gọi ngược hoặc phụ thuộc vào tầng trên.

1.  **Tầng Trải nghiệm Người dùng (Client & DX Layer):**
    *   *AI IDEs:* Giao diện chat trực tiếp trên Text Editor.
    *   *CLI Tool:* Công cụ dòng lệnh để khởi tạo dự án (`init` sinh ra thư mục `docs/`) và khởi chạy luồng (`start`).
    *   *Dashboard:* Giao diện Web thời gian thực hiển thị luồng sự kiện (Event Stream), Sơ đồ Node và cung cấp nút bấm Phê duyệt/Từ chối (Approve/Reject).
2.  **Tầng Giao thức (Communication Layer):**
    *   *Native MCP Server:* Cầu nối phiên dịch, biến các năng lực của hệ thống thành MCP Tools/Resources tiêu chuẩn để các IDE gọi xuống.
3.  **Tầng Điều phối Đa tác tử (Multi-Agent Orchestration Layer):**
    *   *Orchestrator & State Manager:* Bộ脑 phân loại ý định, quản lý ngữ cảnh bộ nhớ và đếm vòng lặp sửa lỗi.
    *   *Planner Agent:* Phân rã prompt thành một Đồ thị Công việc (Task DAG).
    *   *Gateways:* Động cơ chính sách (Policy) và QA.
    *   *Specialist Agents (Chỉ tư duy):* Coder, BA, Tester, Architect (Không có quyền ghi file).
    *   *Action Agents (Chỉ Đọc/Ghi):* Knowledge, Vault, Git (Không có quyền tư duy logic).
4.  **Tầng Động cơ Dữ liệu (Dual Graph Layer):**
    *   *SQLite Indexer:* Cơ sở dữ liệu đồ thị mã nguồn tĩnh.
    *   *Obsidian Watcher:* Nguồn sự thật duy nhất (Single Source of Truth) cho tài liệu Markdown.

---

## 4. Luồng xử lý Đa tác tử (Multi-Agent Workflow)

Dưới đây là sơ đồ BPMN chuẩn mô phỏng luồng chảy dữ liệu từ lúc nhận yêu cầu đến lúc sinh ra mã nguồn:

```mermaid
flowchart TB
    subgraph Layer1 [1. Tầng Client & DX]
        IDE([AI IDE])
        Dashboard([Web Dashboard])
    end
    subgraph Layer2 [2. Tầng Giao thức]
        MCP((MCP Server))
    end
    subgraph Layer3 [3. Tầng Điều phối MAS]
        State[(State Manager)]
        Orch[Orchestrator]
        Plan[Planner Agent]
        KA[Knowledge Agent]
        GW1{GW1: Policy Engine}
        Spec[Specialists: Coder/BA]
        GW2{GW2: Review QA}
        IO[Action: Vault/Git]
    end
    subgraph Layer4 [4. Tầng Động cơ Dữ liệu]
        CodeDB[(Code AST)]
        BizDocs[(Obsidian Rules)]
    end

    IDE --> MCP --> Orch
    Orch <--> State
    Orch --> Plan --> KA
    KA -.-> CodeDB & BizDocs
    KA --> GW1
    GW1 -->|Cảnh báo/Xung đột| Dashboard
    Dashboard -->|Ép Phê duyệt| Spec
    GW1 -->|An toàn| Spec
    Spec --> GW2
    GW2 -->|Lỗi| State
    State -->|Vòng lặp Retry| Spec
    GW2 -->|Đạt chuẩn QA| IO
    IO -->|Commit/Lưu file| CodeDB & BizDocs
    IO --> Orch --> MCP --> IDE