# Parallel Execution Policy

Hướng dẫn tối ưu hóa các nhóm Task để hệ thống Workflow Engine có thể thực thi song song (Parallel Execution), nhằm tối đa hóa hiệu suất của Multi-Agent System.

## 1. Nguyên lý Tối ưu hóa (Optimization Principle)
Bất kỳ hai hoặc nhiều task nào **KHÔNG có sự phụ thuộc dữ liệu (dependencies) trực tiếp vào nhau** và **KHÔNG chỉnh sửa chung một tệp tin/tài nguyên (No resource contention)** thì đều CÓ THỂ và NÊN được nhóm lại để thực thi song song.

## 2. Cấu trúc Khai báo (Declaration Structure)
Bên trong `ExecutionPlanContract`, Planner Agent phải xuất ra một mảng hai chiều `parallelGroups`.
Mỗi phần tử trong mảng này là một mảng chứa các `taskId` có thể chạy cùng lúc.
Ví dụ:
```json
"parallelGroups": [
  ["task-02-backend", "task-03-frontend"],
  ["task-04-unit-test", "task-05-linting"]
]
```

## 3. Quy tắc Gom Nhóm (Grouping Rules)
1. **Đồng cấp về độ sâu (Depth Level):** Các task trong cùng một group song song thường có cùng độ sâu trong cây DAG (nghĩa là chúng phụ thuộc vào cùng một tập các task cha).
2. **Không có phụ thuộc chéo (No Cross-Dependencies):** Tuyệt đối không đưa Task A và Task B vào cùng một `parallelGroup` nếu A có khai báo `dependencies: ["B"]` hoặc ngược lại. Điều này sẽ làm Runtime Engine bị Crash hoặc Deadlock.
3. **Giới hạn luồng (Thread Limit):** Mặc dù Engine có thể xử lý nhiều, Planner nên giới hạn tối đa 3-4 tác tử chạy song song trong một nhóm để tránh quá tải Context Window, xung đột bộ nhớ, hoặc vượt giới hạn API Rate Limit của LLM.

## 4. Các Tình Huống Khuyên Dùng (Recommended Scenarios)
- Cập nhật Backend API và thiết kế Frontend UI song song (chỉ sau khi đã có file OpenAPI/Swagger contract chung).
- Chạy nhiều luồng kiểm thử (Unit Test, Security Audit, Code Linting) đồng thời sau khi tính năng đã code xong.
- Tạo các bảng Database hoàn toàn độc lập (không có khóa ngoại - Foreign Keys - trỏ vào nhau).
