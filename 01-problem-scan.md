# 01 — Problem Scan (Phase 1: SCAN + Phase 2: QUICK-ASSESS)
**Lab 02 — AI Product Scoping (Vin Smart Future)**

> ✍️ **Trước khi nộp:** điền các mục trong ngoặc vuông `[...]` bằng thông tin thật của bạn/nhóm. Phần nội dung phân tích bên dưới là bản nháp chất lượng cao — hãy đọc lại, chỉnh sửa theo hiểu biết thực tế của nhóm bạn trước khi merge vào `main`.

**Họ và tên:** [Điền tên bạn]
**Ngày thực hiện:** [Điền ngày]
**Mảng kinh doanh trọng tâm:** Xanh SM (GSM) — Vận hành xe taxi điện thông minh

---

## 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Quét qua vận hành của các công ty thành viên Vingroup bằng 4 Lenses (Lặp lại / Tốn thời gian / AI có thể tốt hơn / Pain từ người khác).

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|----------------------|
| 1 | **Xanh SM** | Pain từ người khác | Tài xế báo pin yếu/nguy cấp giữa ca chạy nhưng Điều phối viên phải tự tra bản đồ trạm sạc và tự soạn tin nhắn hướng dẫn — dễ chỉ nhầm trạm sai loại cổng sạc hoặc quá xa khi pin đã cạn. |
| 2 | **VinFast** | Lặp lại | Đối chiếu hóa đơn sạc điện từ hàng nghìn trụ sạc đối tác ngoài hệ thống với dữ liệu ghi nhận tài chính nội bộ mỗi tuần, hiện làm thủ công trên Excel. |
| 3 | **Vinpearl** | Pain từ người khác | Tổng hợp review tiêu cực khẩn cấp (phòng bẩn, thái độ nhân viên...) từ Booking.com, Agoda, Google Maps để gửi kịp thời cho Quản lý khách sạn. |
| 4 | **Vinhomes** | Lặp lại | Phân loại và định tuyến phản ánh cư dân (mất nước, hỏng thang máy, ồn ào...) gửi qua App Vinhomes Resident đến đúng ban quản lý toà nhà. |
| 5 | **Vinmec** | Tốn thời gian | Bác sĩ soạn tóm tắt hồ sơ xuất viện thủ công từ bệnh án điện tử và ghi chú lâm sàng, mất 20-30 phút/bệnh nhân. |
| 6 | **Xanh SM** | AI có thể tốt hơn | Chatbot CSKH hiện tại trả lời rập khuôn khi khách hỏi về thời gian chờ xe hoặc chính sách huỷ chuyến. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Top 3 được chọn để đánh giá sâu hơn: **#1 (Xanh SM – Sự cố pin)**, **#2 (VinFast – Đối chiếu hoá đơn)**, **#3 (Vinpearl – Review khẩn cấp)**.

### Card #1 — Xanh SM: Hướng dẫn sạc pin khi xe báo pin yếu/nguy cấp

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                        │
│                                                               │
│ Bài toán: Tài xế Xanh SM báo pin yếu/nguy cấp giữa ca chạy,   │
│ cần được hướng dẫn trạm sạc phù hợp hoặc điều xe cứu hộ pin   │
│ di động trước khi xe cạn kiệt hoàn toàn.                      │
│ Công ty thành viên: [x] Xanh SM (GSM)                        │
│                                                               │
│ Ai đang đau (Actor)? Tài xế (lo lắng, mất thời gian chờ);     │
│ Điều phối viên/Dispatcher tại Trung tâm Điều vận (quá tải     │
│ giờ cao điểm, dễ chỉ nhầm trạm không hợp cổng sạc).           │
│                                                               │
│ Workflow thủ công hiện tại (5 bước):                          │
│   1. Tài xế báo pin yếu qua app                               │
│   → 2. Dispatcher tra định vị GPS xe trên bản đồ nội bộ       │
│   → 3. Tra thủ công trạm sạc trống + loại cổng sạc phù hợp    │
│   → 4. Soạn tin nhắn hướng dẫn gửi tài xế qua app             │
│   → 5. Gọi xe cứu hộ pin di động nếu pin đã dưới ngưỡng an toàn│
│                                                               │
│ Bước nào tốn nhất? Bước 3-4 (⏱ ~9 phút/lượt, dễ sai cổng sạc) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4                │
│ (Đối chiếu pin + khoảng cách → chọn trạm hoặc điều xe cứu hộ  │
│  → soạn draft tin nhắn)                                       │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                          │
│ Giảm thời gian xử lý sự cố pin từ ~13 phút → dưới 4 phút;     │
│ 0% trường hợp pin <5% bị chỉ đến trạm xa hơn 5km.             │
│                                                               │
│ Quick Architecture: [x] LLM Feature                          │
└─────────────────────────────────────────────────────────────┘
```

### Card #2 — VinFast: Đối chiếu hóa đơn sạc điện đối tác

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                        │
│                                                               │
│ Bài toán: Đối chiếu hóa đơn sạc điện từ hàng nghìn trụ sạc    │
│ đối tác ngoài hệ thống với dữ liệu ghi nhận tài chính nội bộ  │
│ mỗi tuần, hiện làm thủ công trên Excel.                       │
│ Công ty thành viên: [x] VinFast                              │
│                                                               │
│ Ai đang đau (Actor)? Nhân viên đối soát tài chính.            │
│                                                               │
│ Workflow thủ công hiện tại (4 bước):                          │
│   1. Tải file hóa đơn từ đối tác trạm sạc ngoài hệ thống      │
│   → 2. Tải dữ liệu giao dịch sạc từ hệ thống nội bộ           │
│   → 3. So khớp thủ công từng dòng theo mã trụ + thời điểm sạc │
│   → 4. Đánh dấu và báo cáo các dòng lệch số liệu              │
│                                                               │
│ Bước nào tốn nhất? Bước 3 (⏱ ~6 giờ/tuần cho ~3.000 dòng)     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3                  │
│ (Rule-based fuzzy-matching theo mã trụ, thời gian, kWh)       │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                          │
│ Giảm thời gian đối soát từ 6 giờ → dưới 1 giờ/tuần;           │
│ Độ chính xác khớp dòng đạt ≥ 99%.                              │
│                                                               │
│ Quick Architecture: [x] Rule / State-Machine                 │
└─────────────────────────────────────────────────────────────┘
```

### Card #3 — Vinpearl: Lọc review khẩn cấp đa nền tảng

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                        │
│                                                               │
│ Bài toán: Tổng hợp và lọc các review tiêu cực khẩn cấp        │
│ (phòng bẩn, thái độ nhân viên...) từ Booking.com, Agoda,      │
│ Google Maps để gửi kịp thời cho Quản lý khách sạn.            │
│ Công ty thành viên: [x] Vinpearl                             │
│                                                               │
│ Ai đang đau (Actor)? Bộ phận CSKH / Quản lý chất lượng.       │
│                                                               │
│ Workflow thủ công hiện tại (4 bước):                          │
│   1. Nhân viên vào từng nền tảng đọc review mới mỗi sáng      │
│   → 2. Copy các review nghi ngờ nghiêm trọng vào file chung   │
│   → 3. Phân loại mức độ khẩn cấp bằng cảm tính                │
│   → 4. Gửi email tổng hợp cho Manager từng khách sạn          │
│                                                               │
│ Bước nào tốn nhất? Bước 1-2 (⏱ ~45 phút/ngày, dễ bỏ sót)      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1-3                │
│ (Tự động quét + phân loại mức độ khẩn cấp bằng LLM)           │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                          │
│ Giảm thời gian tổng hợp từ 45 phút → dưới 10 phút/ngày;       │
│ 100% review "khẩn cấp" (an toàn, vệ sinh) được gắn cờ trong    │
│ vòng 1 giờ kể từ khi đăng.                                    │
│                                                               │
│ Quick Architecture: [x] LLM Feature                          │
└─────────────────────────────────────────────────────────────┘
```

---

## 🗳️ Quyết định lựa chọn của nhóm

Nhóm chọn **Card #1 — Xanh SM: Hướng dẫn sạc pin khi xe báo pin yếu/nguy cấp** để thực hiện Deep-Dive (xem `02-deep-dive-report.md`).

**Lý do loại các thẻ khác:**
- **Card #2 (VinFast – Đối chiếu hoá đơn):** là tác vụ đối soát dữ liệu back-office theo lô (batch), không có yếu tố real-time hay rủi ro an toàn vật lý. Fuzzy-matching rule-based đã đủ giải quyết tốt bài toán này — dùng LLM ở đây là "over-engineering" so với gợi ý ở `03-inspiration-kit.md`.
- **Card #3 (Vinpearl – Review khẩn cấp):** giá trị thực nhưng là bài toán phân tích ngoại tuyến (offline), ảnh hưởng gián tiếp đến trải nghiệm khách chứ không có ràng buộc an toàn cứng (hard safety constraint) cần ranh giới nghiêm ngặt.
- **Card #1 (Xanh SM – Sự cố pin)** vừa có tác động vận hành real-time rõ ràng, vừa đòi hỏi một **Operational Boundary an toàn tuyệt đối** (ngưỡng pin <5% / bán kính 5km) — đúng trọng tâm của Lab 02 là tập luyện thiết lập và bảo vệ ranh giới AI bằng system prompt, nên đây là lựa chọn phù hợp nhất để đi sâu.
