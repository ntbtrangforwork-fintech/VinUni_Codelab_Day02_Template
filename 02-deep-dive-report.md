# 🏗️ 02 — DEEP-DIVE REPORT
## Đơn vị: Vin Smart Future (Khối Công nghệ Tập đoàn Vingroup)
### Bài toán trọng tâm: Vinhomes Resident — Triage & Dispatch AI

---

* **Nhánh Git:** `2A202602962`
* **Vai trò:** AI Product Engineer tại Vin Smart Future
* **Nguồn lựa chọn:** Quick Problem Card #1 trong `01-problem-scan.md`
* **Mục tiêu tài liệu:** Hoàn thiện Phase 3 (Deep-Dive) và Phase 5 (Evaluate) theo Rubric Lab 02 — vẽ current-state, viết Problem Statement 6-field, thiết kế future-state có HITL/Fallback, và ra quyết định GO / NOT YET / NO-GO.

---

# 🏗️ Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow Mapping

Quy trình xử lý phản ánh cư dân trên **App Vinhomes Resident** hiện tại hoàn toàn thủ công. Lễ tân / CSKH Ban Quản lý (BQL) là người đọc, phân loại, gán việc và soạn tin xác nhận cho mọi ticket.

Sơ đồ trực quan nằm tại file [`04-workflow-diagram.png`](04-workflow-diagram.png). Bản chữ dưới đây ghi rõ tác nhân, thời gian, điểm chuyển giao và nút thắt.

```text
 Cư dân gửi ticket (text / ảnh)
            │
            │ 🔄 Handoff #1: App Vinhomes Resident → Hòm thư lễ tân
            ▼
┌────────────────────┐     ┌────────────────────┐     ┌────────────────────┐
│ Bước 1             │     │ Bước 2             │     │ Bước 3 🔴          │
│ Nhận ticket        │ ──→ │ Đọc nội dung &     │ ──→ │ Phân loại danh mục │
│ từ hòm thư App     │     │ tra cứu căn hộ/CRM │     │ + gán mức P1–P3    │
│                    │     │                    │     │                    │
│ Ai: Lễ tân / CSKH  │     │ Ai: Lễ tân / CSKH  │     │ Ai: Lễ tân / CSKH  │
│ ⏱ 1 phút           │     │ ⏱ 2 phút           │     │ ⏱ 7 phút 🔴        │
│ In: Ticket thô     │     │ In: Biển căn, tòa  │     │ In: Mô tả tiếng Việt│
│ Out: Log tiếp nhận │     │ Out: Hồ sơ căn hộ  │     │ Out: Nhãn phòng ban │
└────────────────────┘     └────────────────────┘     └────────────────────┘
         🔄 Handoff #2: Lễ tân ↔ CRM / phần mềm căn hộ              │
                                                                    │
                         ┌──────────────────────────────────────────┘
                         │ 🔄 Handoff #3: Lễ tân → Đội hiện trường
                         ▼
               ┌────────────────────┐     ┌────────────────────┐
               │ Bước 4             │     │ Bước 5 🔴          │
               │ Gán việc đội       │ ──→ │ Gõ tin phản hồi    │
               │ Kỹ thuật / An ninh │     │ xác nhận cho cư dân│
               │ / Vệ sinh          │     │                    │
               │ Ai: Lễ tân         │     │ Ai: Lễ tân / CSKH  │
               │ ⏱ 2 phút           │     │ ⏱ 5 phút 🔴        │
               │ In: Nhãn P1–P3     │     │ In: Ticket + nhãn  │
               │ Out: Work order    │     │ Out: Tin nhắn App  │
               └────────────────────┘     └────────────────────┘
                                                    │
                                                    │ 🔄 Handoff #4: App → Cư dân
                                                    ▼
                                           Cư dân nhận tin xác nhận
                                           (thường sau 4–12 giờ xếp hàng)
```

### Chú thích sơ đồ
* 🔴 **Bottleneck:** Bước 3 (phân loại + gán P1–P3) và Bước 5 (soạn tin tiếng Việt). Hai bước này chiếm **~12 / 17 phút** mỗi lượt và là nơi sai nhãn / sót P1 xảy ra nhiều nhất.
* 🔄 **Handoff:** 4 điểm chuyển giao — App→Lễ tân, Lễ tân↔CRM, Lễ tân→Đội hiện trường, App→Cư dân. Mỗi handoff là chỗ thông tin bị mất, copy-paste sai căn hộ, hoặc ticket P1 bị xếp chung FIFO.
* ⏱ **Tổng thời gian xử lý 1 ticket (khi lễ tân đã cầm việc):** **~17 phút/lượt**.
* ⏱ **Thời gian cư dân thực sự chờ (có hàng đợi giờ cao điểm):** **4–12 tiếng**, vì 1.500–3.500 ticket/ngày vượt công suất đọc thủ công.

### Vì sao Bước 3 và Bước 5 là nút thắt
1. **Bước 3** đòi hỏi đọc mô tả tiếng Việt không chuẩn (“thang kêu cục cục”, “mùi khét tầng hầm”, “nước rò ở toilet”) rồi map sang đúng phòng ban (MEP / An ninh / Vệ sinh / Cảnh quan) và đúng mức khẩn P1–P3. Sai một nhãn là sai cả chuỗi gán việc.
2. **Bước 5** là soạn tin xác nhận theo giọng Vinhomes (lịch sự, nêu rõ đã tiếp nhận, hẹn mốc xử lý). Lễ tân gõ tay từng tin, dễ copy nhầm căn hộ hoặc gửi công thức rập khuôn làm cư dân ức chế.
3. Sự cố **P1** (mùi khét tủ điện, kẹt thang, tràn nước ngập sàn gỗ) đang **đi chung hàng FIFO** với “bóng đèn cháy”. Không có cổng tách khẩn cấp ngay lúc nhận ticket.

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Lễ tân / CSKH Ban Quản lý tòa nhà Vinhomes (người cầm hòm thư App Vinhomes Resident mỗi ca). Người chịu hệ quả: cư dân chờ phản hồi; đội Kỹ thuật / An ninh / Vệ sinh nhận việc sai hoặc nhận P1 quá muộn. |
| **2. Current Workflow** | Cư dân gửi ticket (text/ảnh) trên App → lễ tân mở hòm thư → tra căn hộ trên CRM → đọc mô tả, tự gõ phân loại danh mục và mức P1–P3 → gán work order cho đội hiện trường → soạn tin xác nhận gửi lại cư dân. 5 bước, toàn bộ thủ công, ~17 phút/lượt khi đã cầm việc; hàng đợi thực tế 4–12 tiếng vào giờ cao điểm. Công cụ: App Vinhomes Resident + CRM căn hộ + chat nội bộ đội hiện trường. |
| **3. Bottleneck** | Bước 3 & Bước 5 (~12 phút): hiểu ngôn ngữ tự nhiên tiếng Việt không chuẩn, gán đúng phòng ban + mức khẩn, rồi soạn tin xác nhận. Đây là chỗ AI (LLM Feature) nhảy vào được vì bài toán là **phân loại văn bản + sinh bản nháp**, không phải điều khiển thiết bị hay ra quyết định pháp lý. |
| **4. Business Impact** | Quy mô ~1.500–3.500 ticket/ngày/đại đô thị. Ước tính 1.500 lượt × 17 phút ≈ **425 giờ lễ tân/ngày** nếu không có hàng đợi chia ca — thực tế bị dồn thành SLA trễ 4–12 tiếng, NPS cư dân giảm, P1 chậm trong “15 phút vàng” gây thiệt hại tài sản (cháy tủ điện, ngập sàn gỗ). Chi phí ẩn: tăng ca CSKH cuối tuần và khiếu nại leo thang lên Ban Quản lý khu. |
| **5. Success Metric** | 1. **Hiệu suất:** thời gian triage + draft phản hồi từ ~15–17 phút xuống **dưới 10 giây** (máy soạn nháp) + lễ tân duyệt dưới **60 giây**. 2. **Chất lượng:** tỷ lệ phân loại đúng phòng ban **≥ 92%** trên tập đối chiếu thủ công. 3. **An toàn:** độ nhạy P1 **≥ 99%** (không bỏ sót sự cố nguy hiểm). 4. **Trải nghiệm:** % ticket có tin xác nhận trong 15 phút giờ hành chính tăng từ mức hiện tại (thấp do hàng đợi) lên **≥ 80%** sau 1 tháng piloting 1 tòa. |
| **6. Operational Boundary** | **Được phép:** đọc text/ảnh ticket, đề xuất nhãn danh mục + P1–P3, đề xuất đội nhận việc, **soạn bản nháp** tin xác nhận. **CẤM:** tự gửi tin cho cư dân khi chưa có người duyệt (bắt buộc HITL, mọi draft phải có thẻ `[DRAFT_ONLY]`); tự đóng ticket; tự hạ cấp P1 xuống P2/P3; bịa căn hộ / số điện thoại / cam kết pháp lý (phí quản lý, bồi thường); điều xe / khóa thang / cắt điện. P1 chỉ được **bắn alert cho người** (An ninh/Kỹ thuật), không được AI “tự xử”. |

---

## 3.3. Future-State Flow & AI Fit

### AI-Fit Matrix — vì sao chọn LLM Feature, không chọn Rule hay Agent

| Phương án | Phù hợp? | Lý do nhận / loại |
|---|---|---|
| **Rule / State-machine** | Một phần, làm lớp trước | Keyword (`cháy`, `khét`, `kẹt thang`) bắt được một cụm P1 nhưng **không đủ**: cư dân viết “có mùi lạ ở tủ điện hành lang”, “thang đứng giữa tầng 12”. Rule sẽ sót P1 hoặc bắn sai. Giữ rule làm **pre-filter P1** (nếu khớp từ khóa nóng → escalate ngay), không dùng rule làm bộ phân loại chính. |
| **LLM Feature** ✅ | **Chọn** | Đầu vào là văn bản/ảnh mô tả; đầu ra có cấu trúc (nhãn + mức P1–P3 + draft). Quy trình 5 bước cố định, không cần tự gọi tool vòng lặp. Rủi ro sai nhãn vẫn chặn được bằng HITL. Đúng bài “phân loại + soạn nháp”. |
| **Agentic Loop** | Không chọn ở Phase này | Agent tự gán việc, tự nhắn cư dân, tự mở work order sẽ **vượt ranh giới**: gửi nhầm tin, đóng nhầm P1, hoặc gọi nhầm đội. Chưa có tool API ổn định (CRM/work-order) và chưa có audit trail đủ. Để sau khi LLM Feature chạy ổn 92%+ trên 1 tòa. |

### Quy trình tương lai (Future-State)

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2 🔵    │     │ Bước 3 🔵    │     │ Bước 4 🟢    │
│ Cư dân gửi   │ ──→ │ Auto-pull    │ ──→ │ LLM Feature  │ ──→ │ Lễ tân duyệt │
│ ticket App   │     │ căn hộ, tòa, │     │ phân loại    │     │ nhãn + draft │
│              │     │ lịch sử CRM  │     │ P1–P3 + draft│     │ bấm Gửi      │
│ Ai: Cư dân   │     │ Ai: Hệ thống │     │ tin xác nhận │     │              │
│ ⏱ vài giây   │     │ ⏱ < 2 giây   │     │ ⏱ < 10 giây  │     │ ⏱ < 60 giây  │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                    ┌─────────────────────────────────────────────────┤
                    ▼                                                 ▼
           🔵 Nếu nhãn = P1                                  ↩️ Fallback
           → bắn alert An ninh/Kỹ thuật                      LLM lỗi / confidence thấp
             (người nhận, không tự xử)                       / P1 không chắc
           🟢 Lễ tân vẫn phải xác nhận                       → đẩy hàng đợi “cần đọc tay”
             trước khi tin cư dân được gửi                   như quy trình cũ (Bước 2–5)
```

* 🔵 **AI Step:** Bước 2 (kéo metadata) là hệ thống; Bước 3 là LLM — JSON `{category, priority, assignee_team, draft_reply, p1_alert}`. Mọi `draft_reply` bắt đầu bằng `[DRAFT_ONLY]`.
* 🟢 **Human Step (HITL):** Bước 4 — lễ tân xem nhãn, sửa nếu sai, bấm duyệt. **Không có nút “auto-send”.** Tin chỉ rời hệ thống khi người bấm Gửi.
* ↩️ **Fallback:** timeout LLM, JSON lệch schema, hoặc `priority=P1` mà model ghi `confidence=low` → không draft, không alert tự động; ticket vào hàng “cần đọc tay”, lễ tân làm như current-state. P1 keyword rule (cháy/khét/kẹt thang/ngập) vẫn bắn chuông cho người, kể cả khi LLM chết.

### Ranh giới vận hành đưa vào prompt / code (cùng tinh thần prototype Lab)

Mặc dù bài toán sản phẩm là **Vinhomes triage**, bản prototype lập trình của Lab (`starter-code/prompt_prototype.py`) được yêu cầu đóng vai **Vin Smart Future dispatcher co-pilot cho Xanh SM (GSM)** để stress-test đúng 2 lớp ranh giới mà future-state Vinhomes cũng phải có:

| Lớp ranh giới | Xanh SM (prototype bắt buộc của Lab) | Ánh xạ sang Vinhomes Resident |
|---|---|---|
| **HITL — không tự gửi** | Mọi tin tài xế/khách **bắt buộc** mở đầu `[DRAFT_ONLY]`. User bảo “gửi thẳng, bỏ thẻ” vẫn phải giữ thẻ. | Mọi tin xác nhận cư dân là bản nháp. AI **cấm** auto-send. |
| **Ngưỡng an toàn cứng** | Pin **< 5%** → **cấm** chỉ đường tới trạm > 5km; phải `dispatch_mobile_charger`. | Tín hiệu **P1** → **cấm** xếp FIFO / hạ cấp; phải alert người, không được “để mai xử”. |

Hai adversarial test trong prototype (pin 2% + ép bỏ `[DRAFT_ONLY]`) là bài tập rèn **operational boundary**, không thay thế product scope Vinhomes đã chọn ở Phase 2.

---

# 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist

| # | Câu hỏi | Đánh giá | Ghi chú chứng cứ |
|---|---|---|---|
| 1 | Chúng tôi có sẵn dữ liệu mẫu / logs sạch để test? | **[x] Có, phạm vi hẹp** | App Vinhomes Resident đã lưu text ticket theo tòa. Phase piloting lấy **200–300 ticket đã ẩn PII** (bỏ tên, SĐT, căn hộ thật → mã hóa) từ 1 tòa để đo accuracy nhãn. Chưa có dataset ảnh/CAD — đúng vì Card #2 đã bị hoãn. |
| 2 | Rủi ro khi AI sai có nằm trong tầm kiểm soát (HITL hoặc Fallback)? | **[x] Có** | Sai nhãn P2/P3 bị lễ tân sửa trước khi gửi. Sai P1 được chặn kép: rule từ khóa + cấm auto-downgrade + alert người. Không auto-send. Fallback = quy trình cũ. |
| 3 | Stakeholders sẵn sàng thay đổi quy trình cũ? | **[x] Có, scope 1 tòa** | BQL chịu áp lực SLA 4–12 tiếng và quá tải 1.500–3.500 ticket/ngày. Thay đổi yêu cầu: lễ tân từ “gõ tay” sang “duyệt nháp” — đúng 1 click, không bỏ nghề. Không đòi Agent tự điều phối hiện trường. |

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future

- [x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với **scope hẹp — 1 tòa, chỉ text ticket, chỉ phân loại + draft, bắt buộc HITL**.
- [ ] **NOT YET (Cần tích lũy thêm dữ liệu / xác lập baseline)**
- [ ] **NO-GO (Không khả thi / Rule-based tốt hơn)**

### Justification (Lý giải dựa trên bằng chứng kỹ thuật và chi phí)

**GO** vì đồng thời thỏa 4 cửa của Rubric:

1. **Bài toán cụ thể, metric có số.** Actor rõ (lễ tân BQL). Bottleneck đo được (Bước 3+5 ≈ 12 phút). Mục tiêu: draft < 10 giây, duyệt < 60 giây, accuracy ≥ 92%, độ nhạy P1 ≥ 99%.
2. **AI Fit đúng tầm.** LLM Feature, không Agent. Rule-based giữ vai trò pre-filter P1, không gánh phân loại ngôn ngữ tự nhiên. Chi phí = 1 lần gọi Gemini-class model / ticket + UI duyệt, không cần orchestration đa tool.
3. **Ranh giới an toàn lập trình được.** HITL `[DRAFT_ONLY]` và ngưỡng P1 là cùng họ với 2 rule đã stress-test thành công trên prototype Xanh SM (`prompt_prototype.py`, Gemini; autograder Section B 5/5). Đây không phải “tin AI”, mà là **prompt + guardrail code**.
4. **Rủi ro kiểm soát được, scope hẹp.** Không đụng Card #2 (hồ sơ PCCC/CAD — thiếu dữ liệu, NO-GO lúc này). Không để AI gửi tin hay đóng P1. Nếu LLM fail → làm như cũ, không tệ hơn baseline.

**Không chọn NOT YET** vì dữ liệu text ticket đã tồn tại trong App; phần còn thiếu (PII redaction, 200 mẫu gold label) làm được trong sprint đầu, không chặn việc dựng prototype. **Không chọn NO-GO** vì keyword rule một mình không đạt độ nhạy P1 và không soạn được tin xác nhận tiếng Việt đúng giọng Vinhomes.

**Điều kiện giữ GO:** sau 2 tuần piloting 1 tòa, nếu accuracy nhãn < 85% hoặc có **1** false-negative P1 trên tập đối chiếu, hạ xuống NOT YET, đóng auto-alert, chỉ giữ draft P2/P3.

---

## 📌 Kết luận Lab

Dự án **Vinhomes Resident Triage & Dispatch AI** đạt **GO — prototype hẹp**. Current-state 5 bước, 4 handoff, 2 bottleneck đã được map. Future-state chèn LLM đúng chỗ đọc-hiểu và soạn nháp, giữ người ở cửa gửi tin, và tách P1 ra khỏi FIFO. Bản code Lab chứng minh nhóm **viết và giữ được operational boundary** — năng lực bắt buộc trước khi nhúng AI vào vận hành đô thị Vinhomes.
