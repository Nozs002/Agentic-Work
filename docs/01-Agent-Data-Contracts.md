# Quy chuẩn Hợp đồng Dữ liệu giữa các Agent (Agent Data Contracts Specification)

> **Tài liệu Định nghĩa Chi tiết các Interface TypeScript & JSON Schemas Giao tiếp giữa các Agent trong Kiến trúc 4 Tầng của Agentic Work (AutoForge)**

---

## 1. Tổng quan về Hệ thống Hợp đồng Dữ liệu (Data Contracts Architecture)

Để đảm bảo nguyên tắc **Phân định Ranh giới Nhiệm vụ**, **Định vết Xuyên suốt (Distributed Tracing)** và **An toàn Mã nguồn tuyệt đối**, mọi dữ liệu truyền giữa các Agent trong hệ thống **Agentic Work** đều phải tuân thủ nghiêm ngặt các **Data Contracts (Hợp đồng Dữ liệu)** được định nghĩa bằng JSON Schema tại thư mục [`schemas/`](file:///d:/Workspace/Projects/AgenticWork/schemas).

```mermaid
flowchart LR
    A[Planner Agent] -->|1. TaskDAGContract| B[Knowledge Agent]
    B -->|2. ContextPayloadContract| C{GW1: Policy Engine}
    C -->|3. PolicyVerificationContract| D[Specialist Agents]
    D -->|4. SpecialistResultContract| E{GW2: Review QA}
    E -->|5. ReviewQAContract| F[State Manager / Action Agent]
    F -->|6. DiskWriteContract| G[Vault / File System Agent]
```

> [!IMPORTANT]
> **Nguyên tắc Vàng:**
> 1. **Distributed Tracing (`traceId`):** Tất cả Contract đều kế thừa từ `BaseContract` chứa `traceId`. Một `traceId` duy nhất được khởi tạo từ lúc người dùng nhấn Enter trên IDE và được mang theo xuyên suốt toàn bộ vòng đời xử lý đến khi xuất file xuống ổ đĩa, giúp Dashboard kết nối Event Stream mà không cần join nhiều ID thủ công.
> 2. **No Untyped Data:** Không có bất kỳ dữ liệu văn bản tự nhiên (unstructured text) nào được truyền trực tiếp giữa các Agent mà không có Data Contract bao bọc.
> 3. **Action Agent Execution Barrier:** Action Agent (File System Agent) chỉ chấp nhận duy nhất [`DiskWriteContract`](file:///d:/Workspace/Projects/AgenticWork/schemas/disk-write.schema.json) đã được ký duyệt `PASSED` từ Gateway 2 (Review QA).

---

## 2. Hợp đồng Cơ sở (Base Contract)

*   **JSON Schema:** [`schemas/base-contract.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/base-contract.schema.json)
*   **Mục đích:** Khai báo các thuộc tính hệ thống cốt lõi bắt buộc (`traceId`, `timestamp`, `fromAgent`, `contractType`) mà tất cả 7 Hợp đồng Dữ liệu đều kế thừa thông qua `allOf`.

Mọi Data Contract giữa các Agent đều mở rộng (extend) từ `BaseContract` sau:

```typescript
export interface BaseContract {
  traceId: string;      // Unique ID xuyên suốt 1 vòng đời request từ IDE đến Disk
  workflowId?: string;  // Unique ID của luồng quy trình công việc (nếu có)
  timestamp: string;    // Thời điểm khởi tạo gói tin (ISO 8601)
  fromAgent: 'USER_IDE' | 'PLANNER_AGENT' | 'KNOWLEDGE_AGENT' | 'GW1_POLICY_ENGINE' | 'BA_AGENT' | 'ARCHITECT_AGENT' | 'CODER_AGENT' | 'DATABASE_AGENT' | 'TESTER_AGENT' | 'GW2_REVIEW_QA' | 'FILE_SYSTEM_AGENT' | 'GIT_AGENT' | 'ORCHESTRATOR'; // Nguồn phát tạo
  toAgent?: 'USER_IDE' | 'PLANNER_AGENT' | 'KNOWLEDGE_AGENT' | 'GW1_POLICY_ENGINE' | 'BA_AGENT' | 'ARCHITECT_AGENT' | 'CODER_AGENT' | 'DATABASE_AGENT' | 'TESTER_AGENT' | 'GW2_REVIEW_QA' | 'FILE_SYSTEM_AGENT' | 'GIT_AGENT' | 'ORCHESTRATOR';   // Đích nhận (mặc định ORCHESTRATOR)
  contractType: 'AGENT_DISPATCH' | 'TASK_DAG' | 'CONTEXT_PAYLOAD' | 'POLICY_VERIFICATION' | 'SPECIALIST_RESULT' | 'REVIEW_QA' | 'DISK_WRITE'; // Phân loại gói tin để Orchestrator chuyển giao State Machine
}
```

---

## 3. Hợp đồng DSL & Quản lý Workflow State (`WorkflowState`)

### 📌 `WorkflowDefinitionContract` (Custom YAML DSL Config $\rightarrow$ Orchestrator)
*   **JSON Schema:** [`schemas/workflow-definition.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/workflow-definition.schema.json)
*   **Logical Contract Name:** `WorkflowDefinitionContract` (tra cứu qua `config/glossary.yaml`)
*   **Mục đích:** Định nghĩa cấu hình quy trình Đồ thị (Node, Edge, Condition, Loop, Parallel DAG, Checkpoint) cho Workflow Engine mà không phụ thuộc LangGraph SDK.

### 📌 Quy tắc Vàng về `WorkflowState`:
1. **Orchestrator Managed Only:** `WorkflowState` do Orchestrator lưu giữ và cập nhật. Các Agent hoàn toàn Stateless và tuyệt đối **không được đột biến trực tiếp** `WorkflowState`.
2. **Contract Isolation:** Agent tiếp nhận `AgentDispatchContract` (Input Contract) và trả kết quả qua `SpecialistResultContract` (Output Contract). Orchestrator chịu trách nhiệm hợp nhất dữ liệu vào `WorkflowState`.

---

## 4. Chi tiết các Hợp đồng Dữ liệu Cốt lõi

### 📌 4.0 `AgentDispatchContract` (Orchestrator $\rightarrow$ Agents / Gateways)
*   **JSON Schema:** [`schemas/agent-dispatch.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/agent-dispatch.schema.json)
*   **Mục đích:** Lệnh Phát Thực thi do **Orchestrator** đóng gói và giao nhiệm vụ hai chiều cho các Agent/Gateway.

```typescript
export interface AgentDispatchContract extends BaseContract {
  dispatchId: string;
  taskId: string;
  assignedRole: string;
  instruction: string;
  requiredSkills?: string[];
  contextData?: Record<string, any>;
  isRetry?: boolean;
}
```

### 📌 3.1 `TaskDAGContract` (Planner Agent $\rightarrow$ Knowledge Agent & GW1)
*   **JSON Schema:** [`schemas/task-dag.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/task-dag.schema.json)
*   **Mục đích:** Đóng gói Đồ thị Công việc (Task DAG) do **Planner Agent** rã từ prompt của người dùng.

```typescript
export interface TaskDAGContract extends BaseContract {
  dagId: string;
  userPrompt: string;
  intentCategory: 'CODE_GEN' | 'REQUIREMENTS_REFINEMENT' | 'BUG_FIX' | 'ARCHITECTURE_DESIGN' | 'DOCUMENTATION';
  tasks: Array<{
    taskId: string;
    stepNumber: number;
    description: string;
    assignedRole: 'BA_AGENT' | 'ARCHITECT_AGENT' | 'CODER_AGENT' | 'DATABASE_AGENT' | 'TESTER_AGENT' | 'FILE_SYSTEM_AGENT' | 'GIT_AGENT';
    dependencies?: string[];
    targetModule?: string;
    requiredSkills?: string[];
  }>;
}
```

---

### 📌 3.2 `ContextPayloadContract` (Knowledge Agent $\rightarrow$ GW1 & Specialist Agents)
*   **JSON Schema:** [`schemas/context-payload.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/context-payload.schema.json)
*   **Mục đích:** Gói toàn bộ bối cảnh tối thiểu (Code AST Subgraphs, Business Rules, Data Dictionary) đã qua cắt lọc (**Context Pruning**).

```typescript
export interface ContextPayloadContract extends BaseContract {
  contextId: string;
  dagId: string;
  tokenMetrics: {
    estimatedTokenCount: number;
    pruningRatioPercentage: number;
  };
  subgraphs: Array<{
    filePath: string;
    symbolType: 'CLASS' | 'INTERFACE' | 'FUNCTION' | 'METHOD' | 'TYPE_ALIAS';
    symbolName: string;
    snippet: string;
  }>;
  businessRules: Array<{
    ruleId: string;
    ruleTitle: string;
    vaultPath?: string;
    ruleContent: string;
  }>;
  dataDictionarySchemas?: Array<{
    moduleName: string;
    tableName: string;
    fields: Array<{
      fieldName: string;
      dataType: string;
      isRequired: boolean;
      description?: string;
    }>;
  }>;
  resolvedDependencies?: Array<{
    dependencyName: string;
    status: 'PRESENT' | 'MISSING_ACTION_REQUIRED';
  }>;
}
```

---

### 📌 3.3 `PolicyVerificationContract` (Gateway 1 Policy Engine $\rightarrow$ Dashboard / Orchestrator)
*   **JSON Schema:** [`schemas/policy-verification.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/policy-verification.schema.json)
*   **Mục đích:** Lưu trữ kết quả kiểm tra xung đột với Business Rules trước khi cho phép Specialist Agent sinh code/spec.

```typescript
export interface PolicyVerificationContract extends BaseContract {
  verificationId: string;
  dagId: string;
  status: 'APPROVED' | 'CONFLICT_DETECTED' | 'REJECTED';
  requireHumanApproval?: boolean;
  conflicts?: Array<{
    ruleId: string;
    ruleTitle: string;
    severity: 'CRITICAL' | 'HIGH' | 'MEDIUM' | 'INFO';
    reason: string;
    suggestedResolution?: string;
  }>;
}
```

---

### 📌 3.4 `SpecialistResultContract` (Agent Output Adapter $\rightarrow$ Gateway 2 Review QA)
*   **JSON Schema:** [`schemas/specialist-result.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/specialist-result.schema.json)
*   **Mục đích:** Phong bì Vận chuyển Hạ tầng (**Transport Envelope**) được **Agent Output Adapter** đóng gói từ **Domain Model** gốc của Skill/Agent để gửi tới Gateway 2 và Action Agent.

```typescript
export interface SpecialistResultContract extends BaseContract {
  resultId: string;
  taskId: string;
  specialistRole: 'BA_AGENT' | 'ARCHITECT_AGENT' | 'CODER_AGENT' | 'DATABASE_AGENT' | 'TESTER_AGENT';
  executionType: 'FILE_CREATE' | 'FILE_MODIFY' | 'FILE_DELETE' | 'CHAT_RESPONSE';
  
  // Dữ liệu Domain Model gốc thuần túy do Skill/Agent sinh ra (RequirementAnalysis, GeneratedCode, TestPlan...)
  domainPayload?: Record<string, any>;
  
  // Dữ liệu thao tác file/đĩa đã qua Agent Output Adapter chuyển đổi từ domainPayload
  proposedPayload?: Array<{
    targetPath: string;
    content?: string;
    replacementChunk?: {
      startLine: number;
      endLine: number;
      targetContent: string;
      replacementContent: string;
    };
  }>;
  chatMessage?: string;
  metadata?: {
    skillUsed?: string;
    affectedModules?: string[];
  };
}
```

---

### 📌 3.5 `ReviewQAContract` (Gateway 2 Review QA $\rightarrow$ State Manager / Action Agent)
*   **JSON Schema:** [`schemas/review-qa.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/review-qa.schema.json)
*   **Mục đích:** Đóng dấu xác nhận chất lượng (Linter, Compiler, Business Rules). Cung cấp chữ ký `qaSignature` để cấp phép cho Action Agent ghi đĩa.

```typescript
export interface ReviewQAContract extends BaseContract {
  reviewId: string;
  resultId: string;
  status: 'PASSED' | 'FAILED' | 'FAILED_MAX_RETRIES';
  retryMetrics?: {
    currentAttempt: number; // Số lần đã thử hiện tại (1, 2, 3...)
    maxAllowed: number;     // Giới hạn số lần thử lại tối đa (ví dụ: 3)
  };
  qaSignature?: string; // Bắt buộc khi PASSED để cấp phép ghi đĩa
  feedback?: {
    linterErrors?: string[];
    syntaxErrors?: string[];
    businessRuleViolations?: string[];
    suggestedFix?: string;
  };
}
```

---

### 📌 3.6 `DiskWriteContract` (Action Agents Input: Vault / File System Agent & Git Agent)
*   **JSON Schema:** [`schemas/disk-write.schema.json`](file:///d:/Workspace/Projects/AgenticWork/schemas/disk-write.schema.json)
*   **Mục đích:** Hợp đồng thực thi ghi đĩa đơn nguyên (**Atomic Disk Writes**) dành cho File System Agent.

```typescript
export interface DiskWriteContract extends BaseContract {
  operationId: string;
  qaSignature: string; // Chữ ký phê duyệt từ Gateway 2
  operations: Array<{
    type: 'CREATE_FILE' | 'MODIFY_FILE' | 'DELETE_FILE';
    targetPath: string;
    content?: string;
    replacementChunk?: {
      startLine: number;
      endLine: number;
      targetContent: string;
      replacementContent: string;
    };
  }>;
  rollbackSnapshot?: Array<{
    targetPath: string;
    originalContent?: string;
  }>;
}
```

---

## 4. Vòng đời Xử lý Dữ liệu qua các Hợp đồng (Contract Processing Lifecycle)

```mermaid
sequenceDiagram
    autonumber
    participant IDE as AI IDE / User
    participant Plan as Planner Agent
    participant KA as Knowledge Agent
    participant GW1 as GW1 Policy Engine
    participant Spec as Specialist Agent
    participant GW2 as GW2 Review QA
    participant FS as File System Agent (Action)

    Note over IDE, FS: [traceId = "tr-2026-0726-8899a"] xuyên suốt toàn bộ luồng
    IDE->>Plan: Prompt: "Tạo module Payment..."
    Plan->>KA: 1. TaskDAGContract (traceId)
    KA->>GW1: 2. ContextPayloadContract (traceId)
    GW1->>Spec: 3. PolicyVerificationContract (traceId, APPROVED)
    Spec->>GW2: 4. SpecialistResultContract (traceId, Output Envelope)
    GW2->>FS: 5. ReviewQAContract (traceId, PASSED + Signature)
    FS->>FS: 6. DiskWriteContract (traceId, Thực thi Ghi File)
```

---
*Tài liệu được cập nhật tự động tại `docs/01-Agent-Data-Contracts.md` và bổ sung `traceId` tại bộ JSON Schemas `schemas/`.*
