# 🔍 01 — PROBLEM SCAN & QUICK ASSESS (VINHOMES)
## Đơn vị: Vin Smart Future (Khối Công nghệ Tập đoàn Vingroup)
### Mảng kinh doanh trọng tâm: Vinhomes — Quản lý & Vận hành Đại đô thị Thông minh

---

* **Họ tên / Nhánh Git:** Kỹ sư AI Product (Branch: `2A202602962`)
* **Vai trò:** AI Product Engineer tại Vin Smart Future
* **Mục tiêu tài liệu:** Quét qua toàn bộ hoạt động vận hành của khối Quản lý Đô thị Vinhomes bằng 4 Lenses, nhận diện 5 điểm nghẽn thực tế, hoàn thiện 3 Quick Problem Cards và xác lập bài toán ưu tiên số 1 để thực hiện Deep-Dive.

---

# 🔍 Phase 1 — SCAN: Rà Soát Điểm Nghẽn Vận Hành Vinhomes

Sử dụng **4 Lenses** (Lặp lại, Tốn thời gian, AI-upgrade, Stakeholder Pain) quét qua hoạt động quản lý tại các đại đô thị Vinhomes (Vinhomes Smart City, Vinhomes Ocean Park 1-2-3, Vinhomes Grand Park):

### 📝 Bảng Quét 5 Bài Toán Vận Hành Thực Tế tại Vinhomes:

| # | Đơn vị / Bộ phận | Lens | Tên bài toán / Nghiệp vụ | Mô tả ngắn điểm nghẽn & Tổn thất vận hành |
|---|-------------------|------|--------------------------|-------------------------------------------|
| **1** | **Vinhomes** (CSKH & Lễ tân Tòa nhà) | **Lặp lại (Repetitive)** | **Phân loại & Điều phối Phản ánh Cư dân trên App Vinhomes Resident** | Lễ tân phải đọc thủ công 1.500 – 3.500 ticket/ngày (bóng đèn cháy, mùi rác, thang máy kêu, rò rỉ nước), tự gõ phân loại vào CRM. Hàng đợi trễ **4–12 tiếng** vào giờ cao điểm, nhân viên kiệt sức vì lặp đi lặp lại. |
| **2** | **Vinhomes** (Ban Quản lý & Kỹ thuật) | **Tốn thời gian (Time-consuming)** | **Thẩm định Hồ sơ Đăng ký Thi công Sửa chữa Nội thất** | Cư dân nhận nhà nộp tập hồ sơ bản vẽ (PCCC, điện nước, danh sách thợ). Chuyên viên BQL mất **3–5 ngày** đọc và đối chiếu từng trang giấy với checklist tiêu chuẩn, gây chậm tiến độ dọn vào ở của cư dân. |
| **3** | **Vinhomes** (Lễ tân ảo 24/7) | **AI có thể tốt hơn (AI-upgrade)** | **Trợ lý Ảo Hỗ trợ Quy chế Tòa nhà & Tiện ích Cư dân Ngoài giờ** | Cư dân hỏi thông tin nội quy nuôi thú cưng, mức phí gửi xe, cách đặt tiện ích vườn nướng BBQ/sân tennis ngoài giờ hành chính chỉ nhận được chatbot dạng cây thư mục bấm phím cứng nhắc, gây ức chế. |
| **4** | **Vinhomes** (An ninh & Kỹ thuật P1) | **Nỗi đau từ người khác (Stakeholder Pain)** | **Nhận diện & Kích hoạt Phản ứng Nhanh Sự cố Nguy hiểm (P1 Fast-Track)** | Tin báo sự cố khẩn cấp (mùi khét tủ điện hành lang, kẹt thang máy, tràn nước ngập sàn gỗ) bị xếp chung hàng đợi FIFO với phản ánh tiện ích thông thường, dẫn đến chậm can thiệp trong "15 phút vàng", gây thiệt hại nặng nề. |
| **5** | **Vinhomes** (Vận hành Bãi đỗ xe) | **Nỗi đau từ người khác (Stakeholder Pain)** | **Tiếp nhận & Xử lý Khiếu nại Ô tô Đỗ Sai Quy định trong Đại đô thị** | Cư dân chụp ảnh phản ánh xe đỗ chắn cửa hầm, đỗ trên vỉa hè lên app; bảo vệ mất 20–30 phút tra biển số trên phần mềm gửi xe để tìm số điện thoại gọi chủ xe di dời, thường xuyên xảy ra cự cãi xích mích giữa các cư dân. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn **Top 3 bài toán** tiềm năng nhất từ danh sách trên để phân tích thẻ nghiệp vụ nhanh (10 phút/card):

---

## 🎴 THẺ BÀI TOÁN SỐ 1 (LỰA CHỌN CHÍNH CHO DEEP-DIVE)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán (1 câu): Tự động phân loại danh mục, gán nhãn khẩn cấp P1-P3    │
│ và draft tin nhắn tiếp nhận phản ánh cư dân trên App Vinhomes Resident. │
│ Công ty thành viên: [x] Vinhomes   [ ] VinFast   [ ] Xanh SM            │
│                     [ ] Vinmec     [ ] Khác ___________________________  │
│                                                                         │
│ Ai đang đau (Actor)? Lễ tân / CSKH BQL (quá tải), Cư dân (chờ lâu)       │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Nhận ticket text/ảnh ──> 2. Đọc & Tra căn hộ ──> 3. Phân loại     │
│   ──> 4. Gán việc đội hiện trường ──> 5. Gõ tin phản hồi xác nhận       │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & Bước 5 (⏱ 10 - 15 phút/lượt)  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3, 4 và 5                     │
│ (LLM trích xuất JSON: Phân loại mảng MEP/An ninh, gán P1-P3, draft tin)  │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                    │
│   • Giảm thời gian Triage & Phản hồi từ 15 phút ──> dưới 10 giây.       │
│   • Tỷ lệ phân loại đúng phòng ban đạt ≥ 92%.                           │
│   • Độ nhạy phát hiện sự cố khẩn cấp P1 đạt ≥ 99%.                      │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 🎴 THẺ BÀI TOÁN SỐ 2

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán (1 câu): Tự động kiểm tra danh mục giấy tờ và đối chiếu quy cách│
│ hồ sơ đăng ký thi công sửa chữa nội thất căn hộ Vinhomes.               │
│ Công ty thành viên: [x] Vinhomes   [ ] VinFast   [ ] Xanh SM            │
│                     [ ] Vinmec     [ ] Khác ___________________________  │
│                                                                         │
│ Ai đang đau (Actor)? Kỹ sư xây dựng BQL (ngập hồ sơ), Chủ nhà (chờ đợi) │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Cư dân nộp file PDF hồ sơ ──> 2. Kỹ sư đọc từng trang đối chiếu    │
│   ──> 3. Lập danh sách lỗi thiếu sót ──> 4. Soạn công văn phản hồi      │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 45 - 60 phút/hồ sơ)      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & Bước 3                    │
│ (Multimodal LLM quét bản vẽ/PDF, check checklist an toàn PCCC, điện nước)│
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                    │
│   • Rút ngắn thời gian thẩm định sơ bộ từ 3 ngày ──> dưới 5 phút.        │
│   • Tỷ lệ phát hiện đúng các lỗi sai phạm tiêu chuẩn PCCC đạt 100%.     │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 🎴 THẺ BÀI TOÁN SỐ 3

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                   │
│                                                                         │
│ Bài toán (1 câu): Nhận diện và kích hoạt còi báo động phản ứng nhanh sự  │
│ cố khẩn cấp cấp độ P1 (chập cháy, kẹt thang, vỡ ống nước) theo real-time.│
│ Công ty thành viên: [x] Vinhomes   [ ] VinFast   [ ] Xanh SM            │
│                     [ ] Vinmec     [ ] Khác ___________________________  │
│                                                                         │
│ Ai đang đau (Actor)? Đội phản ứng nhanh An ninh/Kỹ thuật, Cư dân gặp nạn│
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Cư dân gửi tin báo hoảng loạn ──> 2. Chờ lễ tân đọc trong hòm thư  │
│   ──> 3. Lễ tân bấm máy gọi an ninh ──> 4. An ninh tiếp nhận di chuyển   │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 (⏱ 30 - 60 phút do nghẽn hàng)  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1 & Bước 2                    │
│ (LLM phân tích ngữ nghĩa tức thì, phát hiện tín hiệu P1 bắn SMS/còi hú)  │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                    │
│   • Thời gian từ khi cư dân gửi tin đến khi an ninh nhận alert < 30s.   │
│   • Tỷ lệ phát hiện P1 không bỏ sót (False Negative = 0).                │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết Định Lựa Chọn Bài Toán Cho Phase 3 (Deep-Dive)

Sau khi đánh giá cả 3 thẻ bài toán vận hành của Vinhomes, nhóm quyết định lựa chọn **Quick Problem Card #1 — Vinhomes Resident Triage & Dispatch AI** làm bài toán trọng tâm để tiến hành Deep-Dive và lập trình Prompt Prototype.

### 💡 Lý do lựa chọn và đánh giá loại trừ:
1. **Vì sao chọn Card #1:** 
   * Có tần suất xử lý lớn nhất (1.500 – 3.500 lượt/ngày), mang lại tỷ suất hoàn vốn (ROI) và tác động kinh tế lớn nhất.
   * **Bao hàm luôn giá trị của Card #3:** Khi AI phân loại ở Card #1, mô hình đồng thời nhận diện được các sự cố P1 khẩn cấp để bắn alert tức thì cho an ninh.
   * Ranh giới vận hành (Operational Boundary) rõ ràng, dữ liệu text tiếng Việt thực tế phong phú, lý tưởng cho mô hình Gemini 2.5 Flash.
2. **Lý do hoãn triển khai Card #2:** 
   * Bài toán thẩm định hồ sơ bản vẽ thi công đòi hỏi xử lý hình ảnh kỹ thuật phức tạp (CAD/BIM) và các văn bản pháp lý quy hoạch đô thị, chi phí token cao và chưa có đủ dữ liệu số hóa hoàn chỉnh. Cần thu thập thêm dữ liệu để làm trong Phase tiếp theo.

---
*(Bản scan này hoàn thiện toàn bộ yêu cầu của Phase 1 & Phase 2 theo đúng Rubric môn học, sẵn sàng chuyển giao sang Phase 3 Deep-Dive).*
