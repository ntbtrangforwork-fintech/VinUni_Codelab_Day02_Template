# Lab 02 — Phase 3 & 5: Deep-Dive Report & Decision Evaluation
**Dự án:** Vin Smart Future — Xanh SM Intelligent Battery Dispatcher  
**Đơn vị:** Xanh SM (GSM) — Vận hành xe taxi điện thông minh  
**Tác giả:** Nhóm AI Product Engineer (Vin Smart Future)

---

# 🏗️ Phase 3 — DEEP-DIVE: Phân tích bài toán chuyên sâu

## 3.1. Current-State Workflow Mapping (Quy trình thủ công hiện tại)

Dưới đây là mô tả quy trình xử lý sự cố xe taxi điện Xanh SM sắp hết pin / hết pin giữa đường hiện tại của Điều phối viên (Dispatcher):

```text
┌────────────────┐     ┌────────────────┐     ┌────────────────┐     ┌────────────────┐
│ Bước 1         │     │ Bước 2         │     │ Bước 3         │     │ Bước 4         │
│ Nhận cuộc gọi  │     │ Tra cứu định vị│     │ Tra cứu trạm   │     │ Soạn văn bản   │
│ báo sự cố      │ ──> │ GPS của xe     │ ──> │ sạc VinFast    │ ──> │ hướng dẫn      │
│                │     │                │     │ còn trụ trống  │     │ gửi tài xế     │
│ Ai: Dispatcher │     │ Ai: Dispatcher │     │ Ai: Dispatcher │     │ Ai: Dispatcher │
│ ⏱ 2 phút       │     │ ⏱ 2 phút       │     │ ⏱ 5 phút 🔴    │     │ ⏱ 5 phút 🔴    │
│ In: Điện thoại │     │ In: Biển số xe │     │ In: Vị trí GPS │     │ In: Raw data   │
│ Out: Log sự cố │     │ Out: Toạ độ GPS│     │ Out: Địa chỉ   │     │ Out: SMS/App   │
└────────────────┘     └────────────────┘     └────────────────┘     └────────────────┘
                                                                              │
                                                                              ▼
                                                                     ┌────────────────┐
                                                                     │ Bước 5         │
                                                                     │ Gọi xe sạc     │
                                                                     │ di động cứu hộ │
                                                                     │ (nếu Pin < 5%) │
                                                                     │ Ai: Dispatcher │
                                                                     │ ⏱ 3 phút       │
                                                                     │ Out: Lệnh cứu hộ│
                                                                     └────────────────┘
```

* **🔄 Handoff (Điểm chuyển giao):**
  1. Tài xế $\rightarrow$ Dispatcher (Thông báo biển số xe & tình trạng pin).
  2. Dispatcher $\rightarrow$ Hệ thống tra cứu trạm sạc VinFast.
  3. Dispatcher $\rightarrow$ App tài xế / Đội xe cứu hộ di động.
* **🔴 Bottleneck (Điểm nghẽn cổ chai):**
  * **Bước 3:** Tra cứu thủ công các trạm sạc VinFast lân cận để kiểm tra trạm nào còn trụ trống phù hợp với dòng xe.
  * **Bước 4:** Soạn tin nhắn hướng dẫn đường đi thủ công để gửi qua App tài xế.
* **⏱ Tổng thời gian xử lý thủ công hiện tại:** **15–17 phút/lượt xử lý**.

---

## 3.2. Problem Statement 6-Field (Phát biểu bài toán 6 trường)

| Trường thông tin | Nội dung chi tiết bài toán Xanh SM |
|---|---|
| **1. Actor / Operator** | Điều phối viên trung tâm (Dispatcher) & Tài xế taxi điện Xanh SM (GSM Driver). |
| **2. Current Workflow** | Tài xế hết pin gọi Hotline $\rightarrow$ Dispatcher mở hệ thống tra GPS $\rightarrow$ Tra cứu trạm VinFast lân cận $\rightarrow$ Gõ tin nhắn SMS/App hướng dẫn thủ công. |
| **3. Bottleneck** | **Bước 3 & 4:** Tra cứu trạm sạc trống và soạn tin nhắn chỉ đường thủ công ngốn quá nhiều thời gian (⏱ 12–15 phút/lượt), gây áp lực lớn vào giờ cao điểm. |
| **4. Business Impact** | Rò rỉ cuốc xe do tài xế bị ngắt quãng ca chạy; rủi ro trễ hẹn với khách; tăng chi phí vận hành đội xe cứu hộ khi xe chết máy hoàn toàn giữa đường. |
| **5. Success Metric** | **Metric 1:** Rút ngắn thời gian xử lý sự cố hết pin từ **15 phút ──> dưới 2 phút/lượt**.<br>**Metric 2:** Nâng tỷ lệ tài xế di chuyển tới trạm sạc an toàn trước khi hết pin từ **82% ──> 98%**. |
| **6. Operational Boundary** | **1. Thẻ bắt buộc:** Mọi văn bản do AI soạn thảo BẮT BUỘC bắt đầu bằng thẻ `[DRAFT_ONLY]` để đảm bảo không tự động gửi trực tiếp cho tài xế.<br>**2. Quy tắc ngắt cứu hộ (Battery < 5%):** Nếu % pin của xe $< 5\%$, AI TUYỆT ĐỐI KHÔNG được gợi ý trạm sạc xa $> 5\text{km}$. Thay vào đó phải kích hoạt ngay xe sạc di động cứu hộ `dispatch_mobile_charger`.<br>**3. Human-in-the-loop:** Điều phối viên phải kiểm tra và nhấn Approve trước khi tin nhắn được gửi đi. |

---

## 3.3. Future-State Flow & AI Fit (Quy trình tương lai với AI)

### 📊 AI-Fit Matrix:
* **Lựa chọn kiến trúc:** **LLM Feature (Co-pilot — Trợ lý điều phối)**.
* **Lý giải:** Bài toán cần khả năng hiểu ngôn ngữ tự nhiên từ tin nhắn/cuộc gọi của tài xế, trích xuất thông tin GPS và tự động tổng hợp thành văn bản hướng dẫn có cấu trúc chuẩn.

### 🔄 Future-State Flow Diagram:

```text
┌────────────────┐     ┌────────────────┐     ┌────────────────────────┐
│ Bước 1         │     │ Bước 2 (🔵 AI)  │     │ Bước 3 (🔵 AI)         │
│ Tài xế báo     │     │ Trích xuất GPS │     │ Tra trạm sạc khả thi   │
│ sự cố pin      │ ──> │ & dung lượng   │ ──> │ & soạn sẵn Draft       │
│                │     │ pin từ log     │     │ tin nhắn hướng dẫn     │
│ Ai: Driver     │     │ Ai: LLM Gemini │     │ Ai: LLM Gemini         │
│ ⏱ 30 giây      │     │ ⏱ 3 giây       │     │ ⏱ 5 giây               │
└────────────────┘     └────────────────┘     └────────────────────────┘
                                                           │
                                                           ▼
┌────────────────┐     ┌────────────────┐     ┌────────────────────────┐
│ Bước 5         │     │ ↩️ Fallback    │     │ Bước 4 (🟢 HITL)       │
│ Hệ thống gửi   │     │ Chuyển ca cho  │     │ Dispatcher kiểm tra    │
│ tin nhắn       │ <── │ Trưởng ca nếu  │ <── │ bản thảo [DRAFT_ONLY]  │
│ cho tài xế     │     │ LLM lỗi format │     │ & bấm Duyệt (Approve)  │
│ ⏱ Tự động      │     │ ⏱ 1 phút       │     │ ⏱ 15 giây              │
└────────────────┘     └────────────────┘     └────────────────────────┘
```

* 🔵 **AI Step:** LLM Gemini trích xuất vị trí, lọc trạm sạc phù hợp và soạn bản thảo chỉ dẫn.
* 🟢 **Human Step (HITL):** Dispatcher review nội dung bản thảo và bấm nút phê duyệt.
* ↩️ **Fallback Plan:** Nếu LLM trả về kết quả không có thẻ `[DRAFT_ONLY]` hoặc thiếu dữ liệu trạm sạc, hệ thống tự động chuyển ticket về màn hình thủ công của Trưởng ca.

---

# 🏁 Phase 5 — EVALUATE: Quyết định của Ban Giám Đốc Vin Smart Future

### 📋 AI Readiness Checklist:
1. [x] **Dữ liệu mẫu:** Có sẵn dữ liệu vị trí các trụ sạc VinFast và GPS tài xế Xanh SM real-time.
2. [x] **Rủi ro an toàn:** Rủi ro sai sót được kiểm soát 100% nhờ cơ chế Human-in-the-loop (Dispatcher duyệt bản thảo) và Fallback.
3. [x] **Sẵn sàng vận hành:** Đội ngũ Điều phối viên hoan nghênh công cụ hỗ trợ giảm tải thao tác gõ máy tính giờ cao điểm.

### 🗳️ Quyết định cuối cùng:
👉 **[x] GO (Chính thức phê duyệt xây dựng Prototype)**

### 📝 Lý giải quyết định (Justification):
* **Hiệu quả đầu tư (ROI):** Rút ngắn thời gian xử lý sự cố từ 15 phút xuống dưới 2 phút giúp giải phóng **85% thời gian làm việc thủ công** của Điều phối viên Xanh SM.
* **Trải nghiệm khách hàng & Tài xế:** Tăng tỷ lệ tài xế cứu hộ pin thành công lên $98\%$, giảm thiểu tối đa các ca xe cạn pin chết máy giữa đường.
* **Chi phí phát triển thấp:** Ứng dụng mô hình LLM Feature nhẹ (Gemini 2.5 Flash), tích hợp dạng API Co-pilot vào hệ thống hiện có.

