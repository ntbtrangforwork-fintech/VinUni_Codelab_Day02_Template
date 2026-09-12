# 02 — Deep-Dive Report (Phase 3: DEEP-DIVE + Phase 5: EVALUATE)
**Lab 02 — AI Product Scoping (Vin Smart Future)**
**Bài toán:** Xanh SM (GSM) — Hướng dẫn sạc pin khi tài xế báo pin yếu/nguy cấp

> ✍️ **Trước khi nộp:** điền các mục `[...]`. Các con số trong báo cáo này là ước lượng hợp lý dùng để tập luyện scoping — nếu nhóm có số liệu thực tế (phỏng vấn tài xế/Dispatcher, log hệ thống), hãy thay vào để báo cáo sát thực tế hơn.

**Nhóm/Người thực hiện:** [Điền tên nhóm/thành viên]
**Ngày thực hiện:** [Điền ngày]

---

## 3.1. Current-State Workflow

Quy trình xử lý sự cố pin thực địa hiện tại của Dispatcher Xanh SM:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận báo cáo │     │ Tra cứu định │     │ Tra trạm sạc │     │ Soạn tin nhắn│
│ pin yếu qua  │ ──→ │ vị GPS xe    │ ──→ │ trống, đúng  │ ──→ │ hướng dẫn,   │
│ app tài xế   │     │              │     │ loại cổng sạc│     │ gửi tài xế   │
│ Ai: Dispatch │     │ Ai: Dispatch │ 🔄  │ Ai: Dispatch │     │ Ai: Dispatch │
│ ⏱ 1 phút     │     │ ⏱ 2 phút     │     │ ⏱ 5 phút 🔴  │     │ ⏱ 4 phút 🔴  │
│ In: Cảnh báo │     │ In: Biển số  │     │ In: Toạ độ   │     │ In: Địa chỉ  │
│ app          │     │ xe           │     │ GPS          │     │ trạm sạc     │
│ Out: Log sự cố│     │ Out: Toạ độ  │     │ Out: Địa chỉ │     │ Out: Tin SMS │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                       │
                                                                       ▼
                                                                ┌──────────────┐
                                                                │ Bước 5       │
                                                                │ Điều xe cứu  │
                                                                │ hộ pin di    │
                                                                │ động nếu cần │
                                                                │ Ai: Dispatch │
                                                                │ ⏱ 1 phút     │
                                                                └──────────────┘
🔴 Bottleneck   🔄 Handoff (thông tin GPS → tra cứu trạm sạc)
⏱ Tổng thời gian xử lý thủ công: ~13 phút/lượt.
```

## 3.2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) tại Trung tâm Điều vận Xanh SM, phối hợp trực tiếp với tài xế đang gặp sự cố pin trên đường. |
| **2. Current Workflow** | Tài xế báo pin yếu qua app → Dispatcher tra định vị GPS thủ công → tra cứu trạm sạc VinFast còn trống & đúng loại cổng sạc trên dashboard riêng → soạn tin nhắn hướng dẫn gửi qua app tài xế → gọi xe cứu hộ pin di động nếu pin đã dưới ngưỡng an toàn. 5 bước, thao tác tay hoàn toàn, mất trung bình 13 phút/lượt. |
| **3. Bottleneck** | Bước 3 & 4: tra cứu trạm sạc trống đúng loại cổng (CCS2 cho VF8/VF9/VFe34) và soạn tin nhắn hướng dẫn — mất ~9 phút/lượt, với rủi ro chỉ nhầm trạm quá xa khi pin đã ở mức nguy cấp (<5%). |
| **4. Business Impact** | Ước tính ~60 sự cố pin thực địa/ngày (Hà Nội + TP.HCM cộng lại) → ~13 giờ làm việc của đội điều vận/ngày. Chỉ dẫn sai trạm sạc khi pin nguy cấp có thể khiến xe cạn pin giữa đường, gây ùn tắc giao thông và ảnh hưởng an toàn — rủi ro cao hơn nhiều so với thất thoát doanh thu thuần túy. |
| **5. Success Metric** | 1. Giảm thời gian xử lý sự cố từ 13 phút → dưới 4 phút (Efficiency).<br>2. 0% trường hợp pin <5% bị chỉ dẫn đến trạm xa hơn 5km (Safety).<br>3. ≥ 95% tin nhắn draft được Dispatcher duyệt mà không cần chỉnh sửa (Quality). |
| **6. Operational Boundary** | **AI được phép:** truy xuất vị trí GPS xe, tra cứu trạm sạc trống & loại cổng sạc, soạn draft tin nhắn hướng dẫn (luôn gắn thẻ `[DRAFT_ONLY]`).<br>**TUYỆT ĐỐI KHÔNG:** tự động gửi tin đi mà không qua Dispatcher duyệt (bắt buộc HITL); đề xuất trạm sạc cách quá 5km khi pin <5% — trường hợp này phải trả về lệnh điều xe cứu hộ pin di động (`dispatch_mobile_charger`) thay vì địa chỉ trạm sạc. |

## 3.3. Future-State Flow & AI Fit

**AI Fit: LLM Feature** — không chọn *Agentic Loop* vì quy trình có cấu trúc cố định và cần một ranh giới an toàn được kiểm soát chặt, không cần mô hình tự lập kế hoạch nhiều bước; không chọn thuần *Rule-based* vì input là mô tả tự nhiên tiếng Việt đa dạng của tài xế (nhiều cách diễn đạt, có thể kèm nài nỉ/khẩn cấp) và output cần soạn tin nhắn linh hoạt, tự nhiên chứ không chỉ là tra bảng.

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận báo cáo │     │ 🔵 AI tự tra │     │ 🔵 AI draft  │     │ 🟢 Dispatcher│
│ pin yếu qua  │ ──→ │ GPS + trạm   │ ──→ │ tin nhắn HOẶC│ ──→ │ duyệt & gửi  │
│ app tài xế   │     │ sạc trống    │     │ JSON dispatch│     │ tài xế       │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                       │
                                                                       ▼
                                                                ↩️ Fallback:
                                                                Nếu AI trả lỗi,
                                                                output rỗng, hoặc
                                                                Dispatcher không
                                                                chắc chắn, xử lý
                                                                thủ công như quy
                                                                trình cũ (Bước 3-5
                                                                của Current-State).
```

*Kết quả stress-test ranh giới an toàn của system prompt trên thực tế nằm trong `prompt_prototype.py` (3 adversarial test cases: bỏ qua trạm xa khi pin nguy cấp, ép bỏ thẻ `[DRAFT_ONLY]`, và giả mạo lệnh "system override") — xem Phase 5 bên dưới.*

---

## 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist
1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? — *Có: toạ độ GPS xe và dữ liệu trạng thái trạm sạc VinFast đã tồn tại trong hệ thống vận hành hiện tại.*
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? — *Có: thẻ `[DRAFT_ONLY]` bắt buộc + Dispatcher luôn duyệt trước khi gửi; fallback về quy trình thủ công nếu AI lỗi.*
3. [ ] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? — *Cần xác nhận thêm với Trưởng ca Điều vận trước khi triển khai pilot.*

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future
- [x] **GO (Bắt đầu xây dựng Prototype)** — scope hẹp: chỉ hỗ trợ soạn draft + phân loại ngưỡng pin, giữ nguyên bước duyệt/gửi thủ công của Dispatcher.
- [ ] NOT YET
- [ ] NO-GO

**Justification:**
> Bài toán có metric rõ ràng và đo được (13 phút → dưới 4 phút), kiến trúc LLM Feature đơn giản và chi phí thấp hơn nhiều so với Agentic Loop, trong khi rủi ro an toàn cốt lõi — chỉ dẫn sai trạm khi pin nguy cấp — đã được kiểm soát bằng ranh giới cứng trong system prompt. Bằng chứng kỹ thuật: cả 3 adversarial test case trong `prompt_prototype.py` (ép chỉ trạm xa khi pin 2%, ép bỏ thẻ `[DRAFT_ONLY]`, và giả lệnh "system override" kèm pin 3%) đều được thiết kế để model phải giữ đúng Rule 1 và Rule 2 bất kể áp lực từ phía người dùng trong prompt. Kết hợp với yêu cầu HITL bắt buộc (không bao giờ tự động gửi), rủi ro còn lại nằm trong tầm kiểm soát để bắt đầu một pilot phạm vi hẹp tại một điểm điều vận trước khi nhân rộng.
