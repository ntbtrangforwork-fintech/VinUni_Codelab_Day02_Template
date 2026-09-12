# Nhật ký tương tác AI — Lab 02: AI Product Scoping

**Ngày thực hiện:** 12/09/2026  
**Vai trò giả định:** AI Engineer tại Vin Smart Future  
**Mảng lựa chọn:** Vinhomes  
**Công cụ đồng hành:** ChatGPT/Codex

---

## Bắt đầu buổi làm bài — Xác định cách sử dụng AI

Khi bắt đầu Lab 02, tôi xác định AI sẽ đóng vai trò **thought-partner** để mở rộng góc nhìn, đặt giả định và phản biện phương án. Tôi không muốn AI quyết định thay toàn bộ bài toán vì phần quan trọng nhất của product scoping là lựa chọn đúng pain point, kiểm tra số liệu và đặt ranh giới vận hành.

Tôi đọc yêu cầu trong worksheet trước, sau đó đưa cho AI bối cảnh tương đối rõ: tôi là AI Engineer tại Vin Smart Future, đang tìm pain point vận hành cho Vinhomes và cần ít nhất năm quy trình thủ công có thể tối ưu bằng AI. Tôi cũng yêu cầu đầu ra phải có con số tổn thất ước tính thay vì chỉ đưa ra các ý tưởng chung như “dùng chatbot chăm sóc khách hàng”.

Prompt khởi đầu của tôi có dạng:

> “Tôi là AI Engineer tại Vin Smart Future. Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng Vinhomes. Hãy gợi ý năm quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất, kèm con số thống kê ước tính về tổn thất.”

AI giúp tôi mở rộng nhanh phạm vi tìm kiếm sang các nhóm nghiệp vụ như CSKH cư dân, kiểm tra thiết bị tòa nhà, nghiệm thu bàn giao căn hộ và điều phối sửa chữa. Giá trị lớn nhất ở bước này là tôi có thể nhìn thấy nhiều actor và handoff khác nhau thay vì chỉ tập trung vào chatbot.

## Vòng chỉnh sửa đầu tiên — Làm cho Problem Scan cụ thể hơn

Kết quả đầu tiên của AI chưa đạt ngay yêu cầu. Các Lens chưa được sắp xếp theo đúng thứ tự trong worksheet và một số mô tả còn chung chung, chưa cho thấy nhân viên đang làm gì, mất thời gian ở bước nào và vì sao phát sinh rework.

Tôi sửa prompt theo hai ràng buộc:

> “Sắp xếp các bài toán theo đúng thứ tự bốn Lens trong worksheet. Mỗi mô tả phải nêu rõ actor, thao tác thủ công, bottleneck và cách tính tổn thất; không mô tả chung chung.”

Sau vòng này, AI đưa ra năm problem hypotheses cụ thể hơn. Ví dụ, bài toán “tự động hóa CSKH” được thu hẹp thành “đọc, chuẩn hóa, phân loại và chuyển yêu cầu cư dân đến đúng bộ phận”. Quy trình cũng được mô tả rõ từ lúc cư dân gửi nội dung tự do, CSKH kiểm tra thông tin căn hộ, chọn category/SLA cho đến lúc đội vận hành nhận ticket.

Tôi chọn các bài toán sau cho Phase 1:

1. Phân loại và chuyển yêu cầu cư dân.
2. Nhập kết quả kiểm tra thiết bị kỹ thuật tòa nhà.
3. Lập và phân công danh sách lỗi bàn giao căn hộ.
4. Tra cứu tài liệu để trả lời câu hỏi cư dân.
5. Thu thập đủ thông tin cho yêu cầu sửa chữa.

## Điểm AI trả lời chưa đúng và nguy cơ hallucination

Sai lệch quan trọng nhất là AI có thể trình bày các con số ước tính rất tự tin, chẳng hạn 6.000 ticket/tháng, 7 phút/ticket hoặc tỷ lệ chuyển sai 8%. Các con số này hữu ích để xây mô hình tác động ban đầu, nhưng AI không có quyền truy cập dữ liệu vận hành nội bộ của Vinhomes nên không thể coi đó là sự thật.

Tôi xử lý vấn đề này bằng ba cách:

- Đổi cách gọi từ “số liệu thống kê” thành **“giả định scoping cần xác thực”**.
- Yêu cầu AI luôn ghi kèm công thức, ví dụ: `6.000 ticket × 7 phút = 700 giờ/tháng` và `6.000 × 8% × 15 phút = 120 giờ rework/tháng`.
- Thêm kế hoạch kiểm chứng bằng ticket log, time study, lịch sử reassign và chi phí nhân sự đã được phê duyệt.

Một rủi ro khác là AI dễ suy đoán tên hệ thống hoặc quy trình nội bộ cụ thể. Vì tôi chưa có tài liệu xác nhận Vinhomes đang sử dụng chính xác ứng dụng, taxonomy hay phần mềm nào tại từng khu đô thị, tôi giữ cách viết trung tính như “ứng dụng cư dân, hotline hoặc email” và ghi rõ đây là current-state giả định cần xác minh qua phỏng vấn thực địa.

AI cũng có xu hướng đề xuất Agent cho những bài toán chỉ cần một bước phân loại. Tôi đã yêu cầu so sánh rõ Rule, LLM Feature và Agentic Loop. Kết quả là bài toán chính được thu hẹp thành **LLM Feature**, vì model chỉ cần biến văn bản tự do thành structured output. Rule-based code vẫn chịu trách nhiệm kiểm tra schema, SLA, taxonomy và các trường bắt buộc; không cần một agent tự trị gọi nhiều công cụ hoặc tự thực hiện hành động vận hành.

## Phase 2 — Xây dựng ba Quick Problem Cards

Từ năm bài toán ban đầu, tôi yêu cầu AI đánh giá ba phương án có actor rõ, volume đủ lớn, metric đo được và có phần xử lý ngôn ngữ/hình ảnh mà rule thông thường khó giải quyết hoàn toàn.

Ba Quick Problem Cards được chọn là:

- Phân loại và chuyển yêu cầu cư dân.
- Lập và phân công lỗi bàn giao căn hộ.
- Thu thập đủ thông tin cho yêu cầu sửa chữa.

Ở bước này, tôi không chỉ yêu cầu AI viết “AI có thể hỗ trợ”, mà bắt buộc xác định AI nhảy vào **bước số mấy** của workflow và đo thành công bằng chỉ số nào. Ví dụ, với bài toán phân loại ticket, metric chính được đặt là giảm median handling time từ 7 phút xuống không quá 2 phút; guardrail là giảm tỷ lệ chuyển sai từ 8% xuống không quá 3% và đạt urgent-ticket recall tối thiểu 95% trên tập kiểm thử đã gán nhãn.

Việc dùng cả metric hiệu suất và guardrail giúp tôi tránh chọn một giải pháp chỉ nhanh hơn nhưng làm tăng rủi ro vận hành.

## Phase 3 — Deep Dive và phản biện quyết định

Sau Quick Assess, tôi chọn bài toán **phân loại và chuyển yêu cầu cư dân** để Deep Dive. Tôi yêu cầu AI giữ toàn bộ baseline nhất quán giữa các file và xây current-state workflow theo từng actor, input, output, thời gian và handoff.

AI hỗ trợ tôi chuyển mô tả rời rạc thành một workflow có thể kiểm tra:

1. Cư dân gửi yêu cầu.
2. CSKH tiếp nhận và xác minh tòa/căn hộ.
3. CSKH chuẩn hóa mô tả.
4. CSKH chọn category, priority, SLA và đội phụ trách.
5. Ticket được chuyển sang bộ phận vận hành.
6. Bộ phận nhận kiểm tra và trả lại nếu sai hoặc thiếu dữ liệu.

Hai bottleneck được xác định tại bước chuẩn hóa ngôn ngữ và bước ánh xạ nội dung sang taxonomy nghiệp vụ. Đây là lý do LLM có giá trị; trong khi những phần xác định như validation schema và mapping SLA vẫn phù hợp hơn với rule-based code.

AI ban đầu có thể dễ dàng kết luận “GO” dựa trên ROI ước tính. Tôi thấy kết luận đó chưa đủ chặt vì toàn bộ baseline vẫn chưa được xác nhận. Tôi yêu cầu AI đóng vai CFO và Trưởng phòng Vận hành để phản biện lại proposal, tập trung vào ba câu hỏi:

- Dữ liệu ticket có thật sự sạch và có quyền sử dụng hay chưa?
- Nếu AI phân loại sai ticket khẩn cấp thì tác động có được kiểm soát không?
- Nhân viên CSKH và các đội nhận ticket đã sẵn sàng thay đổi workflow chưa?

Sau phản biện, tôi chốt quyết định **NOT YET cho triển khai vận hành, nhưng cho phép làm prototype offline**. Theo tôi, đây là kết luận hợp lý hơn GO vì không biến giả định thành bằng chứng. Báo cáo đồng thời đưa ra các gate để chuyển sang GO: xác nhận baseline, chuẩn bị golden dataset, kiểm thử offline, chạy shadow mode và pilot với HITL 100%.

## Ranh giới vận hành tôi yêu cầu AI tuân thủ

Tôi xác định ranh giới trước khi nghĩ đến prompt prototype. AI chỉ được:

- Trích xuất thông tin từ nội dung ticket.
- Tóm tắt yêu cầu theo cấu trúc chuẩn.
- Gợi ý category, priority và đội tiếp nhận trong taxonomy cho phép.
- Chỉ ra dữ liệu còn thiếu và confidence của đề xuất.

AI tuyệt đối không được:

- Tự gửi phản hồi hoặc cam kết SLA/chi phí với cư dân.
- Tự đóng ticket hoặc sửa hồ sơ cư dân.
- Tự chuyển ticket khẩn cấp mà không có người duyệt.
- Tự tạo category hay tên đội xử lý ngoài taxonomy.
- Bỏ qua cảnh báo an toàn chỉ vì người dùng yêu cầu ưu tiên tốc độ.

Trong prototype và pilot, 100% đề xuất phải qua Human-in-the-loop. Các tình huống liên quan cháy, điện giật, rò gas, ngập nước, kẹt thang máy, an ninh, pháp lý, tranh chấp phí, dữ liệu cá nhân hoặc confidence thấp phải chuyển sang full manual review. Nếu model timeout, trả JSON sai schema hoặc đưa ra nhãn lạ, hệ thống quay về workflow thủ công cũ.

Tôi cũng nhận ra rằng confidence do LLM tự khai báo không mặc nhiên là xác suất đáng tin cậy. Vì vậy, ngưỡng 0,80 trong báo cáo chỉ là ngưỡng khởi đầu để thử nghiệm và phải được hiệu chỉnh trên validation set, không phải một quy tắc an toàn đã được chứng minh.

## Hoàn thiện workflow diagram

Khi tạo sơ đồ current-state, tôi yêu cầu AI không chỉ vẽ các hộp nối tiếp nhau mà phải thể hiện đúng yêu cầu rubric: swimlane theo actor, thời gian từng bước, hai bottleneck, các điểm handoff và vòng rework.

Tôi kiểm tra lại các con số giữa sơ đồ và báo cáo:

- Xử lý lần đầu: 2 + 2 + 2 + 1 = 7 phút/ticket.
- Rework: 8% ticket, thêm khoảng 15 phút/ticket.
- Tổng effort: 700 giờ xử lý lần đầu + 120 giờ rework = 820 giờ/tháng.

Việc kiểm tra chéo này quan trọng vì AI có thể tạo một sơ đồ đẹp nhưng dùng con số khác với Problem Statement nếu không được nhắc giữ consistency.

## Tổng kết cuối buổi

Qua bài lab, AI giúp tôi tăng tốc ở bốn việc: mở rộng danh sách pain point, chuẩn hóa workflow, đề xuất metric và tìm các rủi ro cần đặt boundary. Tuy nhiên, phần có giá trị nhất không phải là nhận câu trả lời đầu tiên, mà là quá trình tôi liên tục thu hẹp prompt và phản biện output.

Các kỹ thuật prompting tôi đã áp dụng gồm:

1. **Role prompting:** đặt bối cảnh AI Engineer tại Vin Smart Future và domain Vinhomes.
2. **Constraint prompting:** yêu cầu đúng bốn Lens, actor, workflow, bottleneck, metric và kiến trúc.
3. **Structured output:** buộc AI trả category và các trường ticket theo schema cố định.
4. **Evidence discipline:** đánh dấu số liệu chưa kiểm chứng là giả định và yêu cầu công thức tính.
5. **Adversarial review:** yêu cầu góc nhìn CFO/Operations để tìm điểm yếu và trường hợp rule-based tốt hơn.
6. **Operational boundaries:** quy định rõ AI được làm gì, không được làm gì, khi nào HITL và fallback được kích hoạt.
7. **Cross-deliverable consistency:** đối chiếu cùng một baseline và workflow giữa Problem Scan, Deep Dive và Workflow Diagram.

Bài học chính của tôi là AI phù hợp nhất khi được sử dụng như một cộng sự có tốc độ cao nhưng cần kiểm soát. Người sử dụng vẫn phải chịu trách nhiệm về giả định, lựa chọn kiến trúc, metric, dữ liệu và ranh giới an toàn. Một prompt tốt không chỉ yêu cầu AI tạo nội dung hay, mà còn phải khiến kết quả **có thể kiểm chứng, đo lường và vận hành an toàn**.
