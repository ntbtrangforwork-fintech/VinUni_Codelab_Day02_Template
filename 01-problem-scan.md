# Lab 02 — Worksheet: AI Product Scoping (Vin Smart Future)

---

## 🏛️ 1. Bối cảnh thực tế: Vin Smart Future (Vingroup)

**Vingroup** — Tập đoàn tư nhân lớn nhất Việt Nam — vừa sáp nhập toàn bộ các phòng ban công nghệ thuộc các công ty thành viên thành một đơn vị công nghệ thống nhất mang tên **Vin Smart Future**.

Nhiệm vụ của **Vin Smart Future** là xây dựng các giải pháp AI, số hóa, và tự động hóa cốt lõi để nâng cao hiệu suất vận hành và trải nghiệm khách hàng xuyên suốt các công ty thành viên:
* 🚗 **VinFast:** Hệ thống xe điện thông minh (EV), trợ lý AI ảo trong xe, dự đoán bảo trì pin, và quản lý chuỗi cung ứng sản xuất.
* 🚕 **Xanh SM (GSM):** Vận hành đội xe taxi/xe máy điện thông minh, điều vận thông minh (Smart Dispatching), tối ưu hóa lộ trình di chuyển.
* 🏢 **Vinhomes:** Quản lý đô thị thông minh (Smart Cities), trợ lý cư dân thông minh, tối ưu hóa mức tiêu thụ năng lượng.
* 🏥 **Vinmec:** Y tế thông minh, chẩn đoán hình ảnh bằng AI, tối ưu hóa quản lý hồ sơ bệnh án.
* 🎢 **Vinpearl / VinWonders:** Trải nghiệm du lịch số hóa, quản lý phòng và luồng khách thông minh tại các khu vui chơi.

Trong buổi Lab hôm nay, nhóm của bạn sẽ đóng vai trò là **AI Product Engineer** tại **Vin Smart Future**, tiến hành tìm kiếm, scoping, phân tích độ khả thi, thiết lập ranh giới vận hành, và xây dựng một **bản mẫu kỹ thuật (prompt prototype)** cho một bài toán cụ thể thuộc một trong những mảng kinh doanh trên.

---

## 📊 2. Cơ cấu tính điểm bài lab

### 👥 Điểm nhóm (60 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **G1. Workflow Mapping** | 20 | Problem Deep-Dive | Vẽ chi tiết quy trình hiện tại: các bước, handoff, thời gian, bottleneck |
| **G2. Problem Statement** | 20 | Problem Deep-Dive | Problem Statement 6-field bám sát thực tế, metric có số và ranh giới rõ ràng |
| **G3. AI Fit & Future Flow** | 10 | Problem Deep-Dive | So sánh Rule vs LLM vs Agent, future flow có bước AI, ranh giới và Fallback |
| **G4. Decision Quality** | 10 | Problem Deep-Dive | Quyết định Go/Not Yet/No-Go trung thực và có chứng cứ rõ ràng |

### 👤 Điểm cá nhân (40 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **I1. Scan & Cards** | 15 | Quick Cards | Liệt kê 5 problems sử dụng 3 lenses, hoàn thiện 3 quick cards chất lượng |
| **I2. Prototyping** | 10 | 02-lab/ | Chạy thử nghiệm programmatic prompt prototype thành công |
| **I3. AI Log & Reflection** | 15 | 03-ai-log.md | Phản ánh trung thực về việc dùng AI làm thought-partner (giúp gì, sai gì, sửa gì) |

---

# 🚀 Phase 0 — worked Example: Xanh SM Intelligent Dispatcher (15 min)

*Giảng viên walk-through ví dụ thực tế từ Vin Smart Future để bạn hiểu rõ cách scoping một bài toán AI.*
Đọc chi tiết worked example tại file [02-deliverable-example.md](02-deliverable-example.md).

---

# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

> [!TIP]
> **🤖 AI Prompts — Partner brainstorm:**
> Hãy sử dụng prompt sau để brainstorm các bài toán thực tế nếu bạn chưa có ý tưởng:
> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng [Chọn một: VinFast / Xanh SM / Vinhomes / Vinmec]. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên xử lý thủ công báo cáo xe sắp cạn pin: tra GPS, kiểm tra trạm sạc tương thích/còn chỗ, rồi soạn hướng dẫn hoặc gọi cứu hộ. **Ước tính để scoping:** 60–80 ca/ngày × 12 phút/ca tương đương 12–16 giờ điều phối/ngày; xe chờ hỗ trợ cũng giảm thời gian khai thác. |
| 2 | Xanh SM | Lặp lại | Nhân viên CSKH đọc ghi chú tài xế, chat và ghi âm cuộc gọi hủy chuyến để gán lý do hủy, sau đó nhập lại vào CRM. **Ước tính:** 300 lượt hủy/ngày × 3 phút/lượt = khoảng 15 giờ nhập liệu/ngày; dữ liệu phân loại chậm làm đội vận hành khó phát hiện điểm nóng. |
| 3 | Xanh SM | Pain từ người khác | Tài xế gửi mô tả tự do về điểm đón khó tìm, đường cấm hoặc khách không liên lạc được; điều phối viên phải đọc và chuyển thủ công sang đội bản đồ/vận hành. **Ước tính:** 100 phản ánh/ngày × 5 phút/lượt = hơn 8 giờ xử lý/ngày, đồng thời tăng nguy cơ khách chờ lâu và hủy cuốc. |
| 4 | Xanh SM | AI có thể tốt hơn | Nhân viên tổng đài trả lời các câu hỏi lặp lại của tài xế về chính sách thưởng, quy trình nhận chuyến, sạc xe và xử lý sự cố. **Ước tính:** 500 câu hỏi/ngày × 2 phút/câu = gần 17 giờ tổng đài/ngày; câu trả lời không nhất quán có thể tạo khiếu nại lặp lại. |
| 5 | Xanh SM | Tốn thời gian | Điều hành ca mở nhiều dashboard để tổng hợp số xe online, cuốc chưa nhận, tỷ lệ hủy và khu vực thiếu xe rồi viết báo cáo bàn giao. **Ước tính:** 3 ca/ngày × 2 nhân sự × 45 phút = 4,5 giờ/ngày; báo cáo chậm khiến quyết định điều xe kém kịp thời. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

> **Cơ sở scoping:** Các lượng việc/thời gian ở Phase 1 là giả định để thiết kế thí nghiệm, phải được đối chiếu bằng log nội bộ trước khi đầu tư. Bối cảnh khả thi là VinFast công bố mạng lưới sạc trải rộng 63 tỉnh thành và người dùng có thể tra cứu trạm trên ứng dụng; tuy nhiên trạng thái trụ trống, loại xe và dữ liệu pin phải lấy từ API thời gian thực, không được suy đoán từ LLM. [VinFast: hạ tầng sạc](https://vinfastauto.com/vn_vi/ha-tang-cho-xe-dien-tai-viet-nam)

### QUICK PROBLEM CARD #1 — Xử lý sự cố pin thấp ngoài đường (ưu tiên Deep-Dive)

| Hạng mục | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Hỗ trợ điều phối viên Xanh SM ra quyết định an toàn và tạo bản nháp hướng dẫn khi tài xế báo pin thấp hoặc sắp hết pin ngoài đường. |
| **Công ty thành viên** | [x] Xanh SM |
| **Ai đang đau (Actor)?** | Tài xế chờ phương án; điều phối viên phải chuyển giữa bản đồ, dữ liệu xe, trạm sạc và kênh nhắn tin; khách có thể bị chậm/hủy chuyến. |
| **Workflow thủ công hiện tại (5 bước)** | 1. Tài xế gọi/chat báo pin và vị trí → 2. Điều phối viên xác minh biển số, % pin, GPS → 3. Tra trạm sạc, cổng tương thích và số chỗ trống → 4. Ước tính khả năng tới trạm hoặc gọi cứu hộ → 5. Tự soạn tin, gửi hướng dẫn và ghi log sự cố. |
| **Bước tốn thời gian/lỗi nhất** | Bước 3–4, **giả định 7/12 phút mỗi ca**: dễ chọn trạm không còn chỗ, không phù hợp hoặc vượt quãng đường an toàn khi pin quá thấp. |
| **AI hỗ trợ ở đâu?** | Rule engine lấy và kiểm tra dữ liệu cấu trúc (ngưỡng pin, quãng đường, tương thích, trạng thái trạm); LLM chỉ tạo **[DRAFT_ONLY]** bằng tiếng Việt từ phương án hợp lệ. Điều phối viên duyệt trước khi gửi. |
| **Metric có số** | Pilot 4 tuần: median thời gian từ tạo ticket đến bản nháp **≤ 3 phút**; **100%** ca pin dưới ngưỡng phải được rule chặn khỏi khuyến nghị trạm vượt ngưỡng; ≥ **95%** bản nháp được dispatcher duyệt không cần sửa nội dung an toàn. Baseline và ngưỡng pin/khoảng cách cần Operations phê duyệt. |
| **Quick Architecture** | [ ] No AI [x] Rule [x] LLM [ ] Agent — không dùng agent tự trị. |

### QUICK PROBLEM CARD #2 — Phân loại nguyên nhân hủy chuyến để cải thiện vận hành

| Hạng mục | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Chuẩn hóa lý do hủy chuyến từ mã hệ thống, ghi chú tài xế và hội thoại CSKH để đội vận hành thấy điểm nóng theo khu vực/khung giờ. |
| **Công ty thành viên** | [x] Xanh SM |
| **Ai đang đau (Actor)?** | Nhân viên CSKH/QA nhập lại lý do; quản lý vận hành phải tổng hợp báo cáo muộn; tài xế và khách chịu hệ quả khi lỗi lặp lại không được phát hiện sớm. |
| **Workflow thủ công hiện tại (4 bước)** | 1. Xuất danh sách cuốc hủy → 2. Mở mã hủy, chat/ghi chú và bản chép lời nếu có → 3. Đọc rồi gán nhãn thủ công → 4. Tổng hợp Excel theo ngày/khu vực và gửi báo cáo. |
| **Bước tốn thời gian/lỗi nhất** | Bước 2–3, **giả định 3 phút/lượt hủy**: mô tả không thống nhất, một ca có nhiều nguyên nhân và người gán nhãn có thể hiểu khác nhau. |
| **AI hỗ trợ ở đâu?** | Rule map các mã hủy chuẩn; LLM chỉ chuẩn hóa văn bản tự do vào taxonomy có sẵn, kèm confidence và trích dẫn câu bằng chứng. Confidence thấp/chủ đề mới chuyển QA. |
| **Metric có số** | Trên tập test đã gán nhãn: macro-F1 ≥ **0,85** cho taxonomy đã thống nhất; ≥ **90%** ca confidence thấp được route QA; báo cáo ngày sẵn trước **09:00** hôm sau. Không dùng chỉ số “độ chính xác” chung chung. |
| **Quick Architecture** | [ ] No AI [x] Rule [x] LLM [ ] Agent — trước hết thử rule-only với mã hủy; chỉ thêm LLM nếu phần văn bản tự do tạo giá trị. |

### QUICK PROBLEM CARD #3 — Triaging phản ánh điểm đón/điều kiện đường từ tài xế

| Hạng mục | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Phân loại và ưu tiên phản ánh tự do của tài xế về điểm đón khó tìm, đường cấm hoặc khách không liên lạc được để chuyển đúng đội xử lý. |
| **Công ty thành viên** | [x] Xanh SM |
| **Ai đang đau (Actor)?** | Tài xế phải mô tả lại vấn đề; điều phối viên/CSKH đọc và chuyển ticket; đội bản đồ/vận hành nhận thông tin thiếu hoặc sai ưu tiên. |
| **Workflow thủ công hiện tại (5 bước)** | 1. Tài xế gửi chat/ghi chú → 2. CSKH đọc và hỏi lại nếu thiếu vị trí → 3. Tự gán loại sự cố, mức khẩn và khu vực → 4. Chuyển ticket cho Map Ops/Dispatch/CSKH → 5. Theo dõi phản hồi và đóng ticket. |
| **Bước tốn thời gian/lỗi nhất** | Bước 2–4, **giả định 5 phút/ticket**: cùng một câu có thể vừa là điểm đón sai vừa là đường cấm; thiếu tọa độ làm chuyển sai đội. |
| **AI hỗ trợ ở đâu?** | LLM trích xuất vấn đề, địa điểm, mức khẩn và câu hỏi cần làm rõ; rule quyết định tuyến xử lý, SLA và các trường hợp an toàn. Nhân viên xác nhận ticket trước khi tạo/chuyển. |
| **Metric có số** | ≥ **85%** ticket được gợi ý đúng nhóm xử lý trên tập test; giảm median thời gian tạo ticket từ baseline xuống **≤ 2 phút**; **100%** phản ánh có từ khóa an toàn/va chạm được gắn cờ khẩn và chuyển người thật. |
| **Quick Architecture** | [ ] No AI [x] Rule [x] LLM [ ] Agent — không tự động thay đổi bản đồ hay gửi chỉ dẫn cho tài xế. |

### Phản biện mô phỏng — góc nhìn CFO và Trưởng phòng Vận hành

**Thẻ được stress-test: Card #1 (sự cố pin thấp).** Đây là bài toán có giá trị rõ nhất nhưng cũng có rủi ro vận hành cao nhất.

1. **Logic nhân quả chưa được chứng minh.** “Giảm thời gian tạo bản nháp” không đồng nghĩa giảm thời gian xe ngừng khai thác hay giảm hủy cuốc; ca khó có thể bị chi phối bởi ùn tắc, đội cứu hộ hoặc trạm hết chỗ. Trước pilot cần log timestamp từng bước và nhóm đối chứng/manual để đo riêng: thời gian xử lý, thời gian xe trở lại online, tỷ lệ ca cần cứu hộ và tỷ lệ hủy cuốc.
2. **Metric có nguy cơ khuyến khích hành vi sai.** Mục tiêu ≤3 phút có thể khiến người dùng chọn trạm gần thay vì trạm an toàn/khả dụng; “95% duyệt không sửa” cũng không đo được an toàn thật. CFO yêu cầu đặt **safety gate không được đánh đổi** (0 khuyến nghị vi phạm rule) và báo cáo khoảng tin cậy/số mẫu; metric năng suất chỉ được xem sau safety gate.
3. **AI có thể không phải khoản đầu tư đầu tiên tốt nhất.** Các quyết định cốt lõi — ngưỡng pin, khoảng cách, loại xe/cổng sạc, trạng thái trụ, điều kiện gọi cứu hộ — đều là dữ liệu có cấu trúc và có thể giải bằng rule-based router + API + template tin nhắn. LLM chỉ có lý do tồn tại ở bước diễn đạt bản nháp và xử lý mô tả tự do. Vì vậy MVP nên triển khai rule/template trước, đo tỷ lệ sửa tin; chỉ A/B test LLM nếu chất lượng giao tiếp hoặc thời gian soạn còn là bottleneck đáng kể.

> [!TIP]
> **🤖 AI Prompts — Stress-Test thẻ bài toán:**
> Hãy dán nội dung thẻ bài toán của bạn vào LLM để nhận phản biện:
> *"Đây là một thẻ bài toán vận hành tôi đề xuất cho Vin Smart Future: [Dán nội dung]. Hãy đóng vai trò là một CFO và Trưởng phòng Vận hành cực kỳ khắt khe, chỉ ra cho tôi 3 điểm yếu về logic, metric, và giải thích vì sao rule-based code thông thường có thể giải quyết bài toán này tốt hơn là dùng AI."*

---
