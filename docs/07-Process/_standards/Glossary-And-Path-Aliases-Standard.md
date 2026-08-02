# 📜 Tiêu chuẩn Bộ khung: Quy tắc Glossary & Quản lý Đường dẫn (Path Aliases)

> **Mã tiêu chuẩn:** `STD-FW-001`  
> **Phạm vi áp dụng:** Toàn bộ hệ thống Multi-Agent, Sub-Agent Templates, Runtime Engine và MCP Server trong AgenticWork.  
> **Vị trí tài liệu:** `docs/07-Process/_standards/Glossary-And-Path-Aliases-Standard.md` *(Tiêu chuẩn bộ khung framework)*

---

## 1. Lý do & Mục tiêu

Trong các hệ thống Multi-Agent phát triển dự án lớn, việc hardcode đường dẫn đĩa vật lý (như `schemas/agent-dispatch.schema.json` hay `docs/02-Business-Rules/...`) trực tiếp vào file cấu hình của từng Sub-Agent (`AGENT.md`) gây ra hai rủi ro lớn:
1. **Coupling nặng nề:** Sub-Agent bị trói chặt vào cấu trúc repository cụ thể. Khi thay đổi vị trí thư mục, người dùng phải thủ công chỉnh sửa lại hàng loạt file `AGENT.md`.
2. **Khó tái sử dụng & Clone dự án:** Người dùng khi tải bộ khung dự án về phải mất nhiều công sức để tìm và đổi lại các đường dẫn rải rác.

**Tiêu chuẩn `STD-FW-001` ra đời nhằm giải quyết triệt để vấn đề này.**

---

## 2. Các Quy tắc Cốt lõi

### Quy tắc 1: Khai báo Thuật ngữ tập trung (Glossary Centralization)
- Mọi thuật ngữ cốt lõi được sử dụng chung trong hệ thống (như `TECH_STACK`, `traceId`, `taskId`, `contractType`) phải được khai báo và giải thích rõ ràng tại:
  - **Tài liệu cho Người đọc:** [`docs/00-Meta/Glossary.md`](file:///d:/Workspace/Projects/AgenticWork/docs/00-Meta/Glossary.md)
  - **File cấu hình cho Máy đọc:** [`config/glossary.yaml`](file:///d:/Workspace/Projects/AgenticWork/config/glossary.yaml)

### Quy tắc 2: Tách biệt Contract & Đường dẫn trong AGENT.md (Decoupled Specs)
- File định nghĩa Sub-Agent (`AGENT.md`) **KHÔNG ĐƯỢC** chứa đường dẫn vật lý trực tiếp tới các file JSON Schema hay thư mục đĩa.
- `AGENT.md` chỉ sử dụng **Logical Contract Names** (ví dụ: `AgentDispatchContract`, `SpecialistResultContract`).
- Trách nhiệm tự động phân giải tên Logical Contract / Path Alias sang vị trí file đĩa vật lý là của **Runtime Engine** thông qua `config/glossary.yaml`.

```yaml
# Ví dụ khai báo chuẩn trong AGENT.md:
inputContracts:
  - AgentDispatchContract
outputContracts:
  - SpecialistResultContract
```

### Quy tắc 3: Single Source of Change (Một nơi duy nhất để thay đổi)
- Khi clone dự án hoặc di chuyển vị trí thư mục, người dùng **chỉ cần chỉnh sửa file `config/glossary.yaml` (hoặc `Glossary.md`)**.
- Runtime Engine và Orchestrator sẽ tự động cập nhật đường dẫn cho toàn bộ các Sub-Agent đang hoạt động trong hệ thống.

---

## 3. Cấu trúc Ánh xạ Runtime (Runtime Mapping Schema)

Runtime Engine truy xuất `config/glossary.yaml` theo 3 khối cấu trúc:

1. **`terms`**: Từ điển định nghĩa thuật ngữ & biến toàn cục (như `TECH_STACK`).
2. **`contracts`**: Ánh xạ Logical Contract Name $\rightarrow$ Physical Schema Path (`schemas/*.schema.json`).
3. **`paths`**: Ánh xạ Path Aliases $\rightarrow$ Relative System Paths.

---

## 4. Tiêu chí Nghiệm thu & Tuân thủ (Compliance Checklist)

- [x] Không có Sub-Agent nào hardcode đường dẫn `schemas/xyz.json` trong file spec `AGENT.md`.
- [x] File `Glossary.md` có đầy đủ định nghĩa thuật ngữ `TECH_STACK` và Bảng Path Aliases.
- [x] File `config/glossary.yaml` hợp lệ cú pháp YAML và khớp với các thông số trong `Glossary.md`.
- [x] Mọi thay đổi về cấu trúc bộ khung phải được cập nhật tập trung tại `07-Process/_standards/`.
