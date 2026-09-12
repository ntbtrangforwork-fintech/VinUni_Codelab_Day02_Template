# Lab 02 — Problem Scan: Vinhomes

## Phase 1 — SCAN (Cá nhân, 20 phút)

### Danh sách bài toán

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---:|---|---|---|
| 1 | Vinhomes | **1. Lặp lại (Repetitive)** | **Phân loại và chuyển yêu cầu cư dân đến đúng bộ phận.** Mỗi yêu cầu từ ứng dụng cư dân, hotline hoặc email phải được nhân viên CSKH đọc, xác định tòa/căn hộ, phân loại như điện, nước, thang máy hoặc an ninh, đánh giá mức ưu tiên rồi chuyển cho đội phụ trách. Giả sử có **6.000 yêu cầu/tháng**, mỗi yêu cầu mất trung bình **7 phút**, công việc này tiêu tốn **700 giờ/tháng**. Nếu khoảng **8% yêu cầu bị chuyển sai** và mất thêm 15 phút để xử lý lại, tổn thất tăng thêm **120 giờ/tháng**. Tổng cộng khoảng **820 giờ**, tương đương **123 triệu đồng/tháng**. |
| 2 | Vinhomes | **1. Lặp lại (Repetitive)** | **Nhập kết quả kiểm tra thiết bị kỹ thuật tại các tòa nhà.** Nhân viên kỹ thuật kiểm tra máy bơm, máy phát điện, tủ điện, hệ thống báo cháy và điều hòa; sau đó chụp ảnh đồng hồ, ghi số liệu vào giấy hoặc nhóm chat rồi nhập lại vào Excel/phần mềm quản lý. Giả sử có **250 lượt kiểm tra/ngày**, mỗi lượt mất thêm **5 phút để nhập lại dữ liệu**, tương đương **625 giờ/tháng**. Nếu 3% bản ghi sai hoặc thiếu khiến kỹ thuật phải kiểm tra lại 20 phút, phát sinh thêm khoảng **38 giờ/tháng**. Tổng rò rỉ khoảng **663 giờ**, tương đương gần **100 triệu đồng/tháng**. |
| 3 | Vinhomes | **2. Tốn thời gian (Time-consuming)** | **Lập và phân công danh sách lỗi khi bàn giao căn hộ.** Kỹ sư nghiệm thu phải chụp ảnh từng lỗi như nứt tường, bong sơn, cửa lệch hoặc thiết bị không hoạt động; nhập mô tả; xác định vị trí; phân loại mức độ; tìm nhà thầu chịu trách nhiệm và theo dõi khắc phục. Với một đợt bàn giao **1.000 căn**, trung bình **12 lỗi/căn**, cần xử lý khoảng **12.000 lỗi**. Nếu mỗi lỗi mất 4 phút để ghi nhận và phân công, tổng thời gian là **800 giờ/đợt**, tương đương khoảng **120 triệu đồng tiền công**. Mô tả không thống nhất hoặc gán sai nhà thầu còn có thể kéo dài thời gian đóng lỗi và bàn giao căn hộ. |
| 4 | Vinhomes | **3. AI có thể tốt hơn (AI-upgrade)** | **Tra cứu tài liệu để trả lời câu hỏi của cư dân.** Khi cư dân hỏi về phí quản lý, sửa chữa căn hộ, đăng ký thi công, sử dụng tiện ích hoặc quy định đỗ xe, nhân viên CSKH phải tìm thông tin trong nội quy khu đô thị, thông báo của ban quản lý và lịch sử căn hộ rồi tự soạn câu trả lời. Giả sử có **4.000 câu hỏi/tháng**, trong đó 30% cần tra cứu tài liệu và mỗi trường hợp mất 10 phút, riêng bước tra cứu tiêu tốn **200 giờ/tháng**. Nếu 5% câu trả lời thiếu hoặc dùng tài liệu cũ khiến cư dân phải liên hệ lại, sẽ phát sinh khoảng **200 lượt xử lý lại/tháng**, làm tăng SLA và giảm mức hài lòng của cư dân. |
| 5 | Vinhomes | **4. Pain từ người khác (Stakeholder Pain)** | **Yêu cầu sửa chữa thiếu thông tin khiến kỹ thuật phải liên hệ hoặc đến kiểm tra nhiều lần.** Cư dân thường chỉ gửi mô tả ngắn như “điều hòa không mát” hoặc “nhà vệ sinh bị rò nước”. CSKH phải gọi lại để hỏi vị trí, mức độ, thời điểm xảy ra và xin ảnh/video trước khi phân công kỹ thuật. Giả sử có **3.000 yêu cầu sửa chữa/tháng**, khoảng 15% thiếu thông tin; mỗi trường hợp cần thêm 15 phút gọi lại, phát sinh **112,5 giờ/tháng**. Nếu một nửa số trường hợp này khiến kỹ thuật phải quay lại lần hai, với 45 phút/lượt, phát sinh thêm khoảng **169 giờ/tháng**. Tổng rò rỉ gần **282 giờ**, tương đương **42 triệu đồng/tháng**, chưa tính thời gian chờ và sự không hài lòng của cư dân. |

---

## Phase 2 — QUICK-ASSESS (Cá nhân, 30 phút)

### Quick Problem Card #1 — Phân loại và chuyển yêu cầu cư dân

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Nhân viên CSKH đang mất nhiều thời gian đọc, chuẩn hóa, phân loại và chuyển từng yêu cầu cư dân đến đúng bộ phận; yêu cầu thiếu thông tin hoặc bị chuyển sai còn gây xử lý lại và trễ SLA. |
| **Công ty thành viên** | [ ] VinFast &nbsp; [ ] Xanh SM &nbsp; [x] **Vinhomes** &nbsp; [ ] Vinmec &nbsp; [ ] Khác |
| **Ai đang đau (Actor)?** | Nhân viên CSKH/ban quản lý tiếp nhận yêu cầu; đội kỹ thuật, an ninh và vệ sinh nhận việc; cư dân chờ kết quả xử lý. |
| **Workflow thủ công hiện tại (3–5 bước)** | 1. CSKH nhận nội dung tự do từ ứng dụng cư dân, hotline hoặc email → 2. Đọc nội dung, xác định tòa/căn hộ và kiểm tra lịch sử liên quan → 3. Chọn nhóm sự cố, mức ưu tiên và SLA → 4. Viết lại nội dung rồi chuyển cho bộ phận phụ trách → 5. Bộ phận nhận việc kiểm tra; nếu sai hoặc thiếu thông tin thì trả lại CSKH. |
| **Bước tốn thời gian/lỗi nhất** | **Bước 2–4:** đọc, chuẩn hóa, phân loại và chọn đơn vị xử lý; trung bình **7 phút/yêu cầu**. Khoảng 8% yêu cầu bị chuyển sai có thể cần thêm 15 phút xử lý lại. |
| **AI có thể hỗ trợ ở đâu?** | Tại bước 2–4, LLM đọc nội dung, trích xuất trường dữ liệu bắt buộc, tạo tóm tắt chuẩn hóa, gợi ý nhóm sự cố, mức ưu tiên và bộ phận phụ trách. Nhân viên CSKH duyệt trước khi chuyển; yêu cầu khẩn cấp hoặc có độ tin cậy thấp phải được chuyển sang xử lý thủ công. |
| **Đo thành công bằng gì?** | **Metric chính:** giảm thời gian phân loại từ 7 phút xuống **không quá 2 phút/yêu cầu**. **Metric chất lượng:** giảm tỷ lệ chuyển sai từ 8% xuống **không quá 3%**; ít nhất **90%** gợi ý AI được tạo trong 10 giây. **Guardrail:** nhận diện ít nhất **95%** yêu cầu khẩn cấp trong tập kiểm thử đã gán nhãn. |
| **Quick Architecture** | [ ] No AI &nbsp; [ ] Rule &nbsp; [x] **LLM Feature** &nbsp; [ ] Agent. LLM xử lý ngôn ngữ tự do; rule-based áp SLA và bắt buộc Human-in-the-loop cho trường hợp khẩn cấp/độ tin cậy thấp. |

### Quick Problem Card #2 — Lập và phân công danh sách lỗi bàn giao căn hộ

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Kỹ sư nghiệm thu phải nhập và chuẩn hóa thủ công hàng nghìn lỗi bàn giao từ ảnh và ghi chú hiện trường, sau đó tự xác định nhóm lỗi và nhà thầu chịu trách nhiệm. |
| **Công ty thành viên** | [ ] VinFast &nbsp; [ ] Xanh SM &nbsp; [x] **Vinhomes** &nbsp; [ ] Vinmec &nbsp; [ ] Khác |
| **Ai đang đau (Actor)?** | Kỹ sư QA/QC và nghiệm thu; ban quản lý dự án; nhà thầu thi công; khách hàng chờ nhận căn hộ. |
| **Workflow thủ công hiện tại (3–5 bước)** | 1. Kỹ sư kiểm tra từng khu vực trong căn hộ → 2. Chụp ảnh và ghi mô tả lỗi → 3. Nhập lại vị trí, loại lỗi và mức độ vào danh sách snag → 4. Xác định hạng mục/nhà thầu rồi phân công khắc phục → 5. Tái kiểm tra, cập nhật trạng thái và đóng lỗi. |
| **Bước tốn thời gian/lỗi nhất** | **Bước 2–4:** chuyển ảnh và ghi chú hiện trường thành bản ghi chuẩn rồi gán đúng hạng mục/nhà thầu; trung bình **4 phút/lỗi**. Với 12.000 lỗi trong một đợt bàn giao, riêng bước này cần khoảng 800 giờ. |
| **AI có thể hỗ trợ ở đâu?** | Tại bước 2–4, mô hình multimodal đọc ảnh và ghi chú, gợi ý loại lỗi, vị trí, mức độ, mô tả chuẩn hóa và nhóm nhà thầu phụ trách. Kỹ sư phải xác nhận trước khi tạo/phân công snag; AI không được tự đóng lỗi hoặc kết luận chất lượng công trình. |
| **Đo thành công bằng gì?** | **Metric chính:** giảm thời gian lập và phân loại snag từ 4 phút xuống **không quá 1,5 phút/lỗi**. **Metric chất lượng:** ít nhất **90%** bản ghi được phân loại đúng ngay lần đầu; tỷ lệ phải chuyển lại nhà thầu **không quá 3%**. **Guardrail:** 100% lỗi được AI đánh dấu nghiêm trọng phải được kỹ sư duyệt. |
| **Quick Architecture** | [ ] No AI &nbsp; [ ] Rule &nbsp; [x] **LLM Feature** &nbsp; [ ] Agent. Sử dụng LLM đa phương thức để hiểu ảnh và ghi chú; workflow và quyền phê duyệt vẫn do hệ thống nghiệp vụ kiểm soát. |

### Quick Problem Card #3 — Thu thập đủ thông tin cho yêu cầu sửa chữa

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Yêu cầu sửa chữa do cư dân gửi thường thiếu vị trí, hiện tượng, mức độ và ảnh minh họa, khiến CSKH phải hỏi lại hoặc kỹ thuật phải đến nhiều lần mới có thể xử lý. |
| **Công ty thành viên** | [ ] VinFast &nbsp; [ ] Xanh SM &nbsp; [x] **Vinhomes** &nbsp; [ ] Vinmec &nbsp; [ ] Khác |
| **Ai đang đau (Actor)?** | Cư dân cần sửa chữa; nhân viên CSKH phải thu thập lại thông tin; điều phối viên và kỹ thuật viên cần chẩn bị đúng chuyên môn, dụng cụ và vật tư. |
| **Workflow thủ công hiện tại (3–5 bước)** | 1. Cư dân gửi mô tả ngắn hoặc gọi hotline → 2. CSKH gọi/nhắn lại để hỏi vị trí, biểu hiện, thời điểm và xin ảnh/video → 3. CSKH tạo work order và chọn đội kỹ thuật → 4. Kỹ thuật đến căn hộ để chẩn đoán → 5. Nếu thiếu thông tin, sai chuyên môn hoặc thiếu vật tư thì phải hẹn và quay lại lần hai. |
| **Bước tốn thời gian/lỗi nhất** | **Bước 2:** bổ sung thông tin mất khoảng **15 phút/yêu cầu thiếu dữ liệu**. **Bước 4–5:** một lượt kỹ thuật phải quay lại do intake thiếu thông tin có thể mất thêm **45 phút**. |
| **AI có thể hỗ trợ ở đâu?** | Tại bước 1–3, trợ lý hội thoại phân tích mô tả, kiểm tra các trường còn thiếu, đặt câu hỏi bổ sung phù hợp với loại sự cố, yêu cầu ảnh/video cần thiết rồi tạo bản tóm tắt có cấu trúc cho CSKH duyệt. Trường hợp có dấu hiệu cháy, điện giật, rò gas hoặc ngập nước phải lập tức chuyển cho người trực khẩn cấp; AI không tự chẩn đoán an toàn hoặc tự điều phối kỹ thuật. |
| **Đo thành công bằng gì?** | **Metric chính:** giảm tỷ lệ work order thiếu thông tin từ 15% xuống **không quá 5%**. **Metric hiệu suất:** giảm thời gian CSKH bổ sung thông tin từ 15 phút xuống **không quá 3 phút/yêu cầu**; giảm số lượt kỹ thuật phải quay lại do thiếu thông tin từ khoảng 225 xuống **không quá 90 lượt/tháng**. **Guardrail:** chuyển đúng 100% tình huống khẩn cấp trong bộ test bắt buộc sang người trực. |
| **Quick Architecture** | [ ] No AI &nbsp; [ ] Rule &nbsp; [ ] LLM &nbsp; [x] **Agentic Loop**. Agent lặp chu trình đọc → kiểm tra độ đầy đủ → hỏi bổ sung → đánh giá lại; chỉ tạo bản nháp work order để nhân viên duyệt, không tự thực hiện hành động vận hành. |
