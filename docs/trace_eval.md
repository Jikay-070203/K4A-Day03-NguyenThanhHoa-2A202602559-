# BÁO CÁO THU HOẠCH NGHIỆM THU BÀI LAB 3 (BƯỚC 3 - SUBMISSION ARTIFACT)

> **Họ và Tên Học viên:** Nguyễn Thanh Hòa  
> **Mã Sinh Viên / Mã Học viên:** 2A202602559  
> **Chủ đề Lựa chọn:** Gợi ý 1.1 - Trợ lý Học vụ VinUni: tra cứu thông tin học vụ sinh viên và đặt lịch tư vấn với cố vấn học tập.

---

## 1. BẢNG CHẤM ĐIỂM AGENTIC FIT SCORING MATRIX (ĐÁNH GIÁ CHỦ ĐỀ)

| Tiêu chí Đánh giá           | Mức độ (1 - 5) | Giải trình chi tiết lý do chọn điểm                                                                                                                                                                                                                                                                                      |
| :-------------------------- | :------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Multi-step Reasoning** |     4 / 5      | Bài toán có các câu hỏi cần xử lý nhiều bước, đặc biệt TC04 yêu cầu tra cứu thông tin sinh viên trước, lấy cố vấn học tập từ kết quả quan sát, sau đó mới đặt lịch tư vấn đúng người và đúng thời gian.                                                                                                                  |
| **2. Tool Interaction**     |     5 / 5      | Hệ thống bắt buộc kết nối MCP Server để gọi hai công cụ: `academic_query` tra cứu dữ liệu học vụ và `schedule_appointment` thực hiện hành động đặt lịch. Ngoài hai tool bắt buộc của lab, hệ thống có mở rộng thêm `cancel_appointment` cho demo giao diện. Chatbot thường không thể tự tạo dữ liệu học vụ đáng tin cậy. |
| **3. Dynamic Decision**     |     4 / 5      | Agent cần quyết định khi nào trả lời trực tiếp, khi nào gọi tool tra cứu, khi nào gọi tool đặt lịch. Với luồng đa bước, hành động đặt lịch phụ thuộc vào kết quả `advisor` từ observation của bước tra cứu.                                                                                                              |
| **4. Long Horizon Goal**    |     3 / 5      | Bài toán có mục tiêu xuyên suốt trong một phiên xử lý ngắn: hỗ trợ sinh viên từ truy vấn thông tin đến hoàn tất đặt lịch. Tuy nhiên chưa có memory dài hạn hoặc lập kế hoạch qua nhiều phiên nên mức độ long-horizon ở mức trung bình.                                                                                   |
| **TỔNG ĐIỂM AGENTIC FIT**   |  **16 / 20**   | Tổng điểm lớn hơn 12/20, chủ đề phù hợp để triển khai ReAct Agent vì cần suy luận, gọi công cụ, quan sát kết quả và chống hallucination.                                                                                                                                                                                 |

---

## 2. TRÍCH XUẤT KẾT QUẢ WATERFALL TRACE LOG (SAU KHI CHẠY TEST SUITE TRÊN API THẬT)

Đoạn trace tiêu biểu dưới đây được lấy từ test case TC04 sau khi chạy `python src/app.py --all` với `LLM Provider: OpenAIProvider`. Luồng này thể hiện rõ ReAct multi-step: tra cứu sinh viên trước, sau đó đặt lịch với cố vấn học tập lấy từ kết quả MCP Server.

```json
[
  {
    "step": 1,
    "query": "Hãy tra cứu thông tin sinh viên SV2026001, sau đó đặt lịch tư vấn với cố vấn học tập của sinh viên này vào lúc 09:00 16/09/2026.",
    "action_type": "TOOL_EXECUTION",
    "thought": "OpenAI quyết định gọi công cụ 'academic_query' với tham số: {\"student_id\": \"SV2026001\"}",
    "tool_name": "academic_query",
    "arguments": {
      "student_id": "SV2026001"
    },
    "observation": {
      "status": "SUCCESS",
      "student_id": "SV2026001",
      "data": {
        "full_name": "Nguyễn Văn An",
        "class": "AI-K4",
        "gpa": 3.85,
        "email": "an.nv@vinuni.edu.vn",
        "status": "Đang học",
        "advisor": "PGS.TS Nguyễn Văn A"
      }
    },
    "latency_ms": 1571.17
  },
  {
    "step": 2,
    "query": "Hãy tra cứu thông tin sinh viên SV2026001, sau đó đặt lịch tư vấn với cố vấn học tập của sinh viên này vào lúc 09:00 16/09/2026.",
    "action_type": "TOOL_EXECUTION",
    "thought": "Đã có thông tin cố vấn học tập từ academic_query, tiếp tục gọi schedule_appointment theo yêu cầu ban đầu.",
    "tool_name": "schedule_appointment",
    "arguments": {
      "student_id": "SV2026001",
      "datetime_str": "09:00 16/09/2026",
      "advisor_name": "PGS.TS Nguyễn Văn A"
    },
    "observation": {
      "status": "SUCCESS",
      "booking_id": "BK-SV2026001-99",
      "student_id": "SV2026001",
      "datetime": "09:00 16/09/2026",
      "advisor": "PGS.TS Nguyễn Văn A",
      "message": "Đặt lịch thành công cho sinh viên SV2026001 với PGS.TS Nguyễn Văn A vào lúc 09:00 16/09/2026."
    },
    "latency_ms": 0.0
  },
  {
    "step": 3,
    "query": "Hãy tra cứu thông tin sinh viên SV2026001, sau đó đặt lịch tư vấn với cố vấn học tập của sinh viên này vào lúc 09:00 16/09/2026.",
    "action_type": "FINAL_ANSWER",
    "thought": "Tổng hợp kết quả từ MCP Server thành công.",
    "output": "Đã tra cứu sinh viên SV2026001 (Nguyễn Văn An) và Đặt lịch thành công cho sinh viên SV2026001 với PGS.TS Nguyễn Văn A vào lúc 09:00 16/09/2026.",
    "latency_ms": 10.0
  }
]
```

---

## 3. TỔNG KẾT KẾT QUẢ NGHIỆM THU & NỘP BÀI

- [x] Đã điền API Key thật trong `.env` và xác nhận Agent chạy mượt mà trên LLM API thật (OpenAI).
- **Tổng số Test Cases đã chạy thành công:** 5 / 5 test cases.
- **Số lượt gọi Tool qua MCP Server chính xác:** 5 lượt.
- **Kết quả đẩy Repo nộp bài:** [x] Đã Commit và Push mã nguồn thành công lên GitHub cá nhân.

---

> **HOÀN TẤT NỘP BÀI:** Sao chép đường link GitHub Repository cá nhân và dán vào ô nộp bài trên hệ thống LMS VLearn để hoàn tất Bài Lab 3.
