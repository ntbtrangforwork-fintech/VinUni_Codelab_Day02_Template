# 03 — Nhật Ký Tương Tác AI (Cá nhân)

**Học viên:** _[Điền tên bạn]_
**Công cụ AI sử dụng:** Claude (thought-partner chính trong buổi lab), Gemini 2.5 Flash (model được stress-test trong `prompt_prototype.py`)

> Ghi chú: Đây là bản nháp phản ánh dựa trên đúng quá trình làm việc thực tế của buổi lab này. Hãy đọc lại, chỉnh sửa bằng giọng văn của chính bạn, và bổ sung/bớt đi những chi tiết đúng với trải nghiệm cá nhân bạn trước khi nộp — bài phản ánh chỉ có giá trị khi nó trung thực.

---

## 1. AI đã giúp tôi những gì?

Trong buổi lab, tôi dùng AI như một **thought-partner** ở ba giai đoạn:

- **Scoping bài toán:** AI giúp tôi hệ thống hóa 5 bài toán từ Inspiration Kit thành các Quick Problem Cards có cấu trúc nhất quán (Actor, Bottleneck, Metric, Architecture), thay vì tôi tự viết rời rạc từng ý.
- **Viết Operational Boundary / System Prompt:** AI giúp tôi diễn đạt hai ranh giới an toàn (`[DRAFT_ONLY]` và ngưỡng pin <5%/5km) thành các chỉ thị system-prompt rõ ràng, có cấu trúc `IF/THEN`, thay vì một đoạn mô tả mơ hồ dễ bị model diễn giải sai.
- **Viết code tích hợp SDK:** AI hoàn thiện hàm `evaluate_prompt()` bằng `google-genai` SDK đúng cú pháp hiện hành (`client.models.generate_content(..., config=types.GenerateContentConfig(...))`), giúp tôi tiết kiệm thời gian tra docs.
- **Tổng hợp tài liệu:** AI giúp tôi dựng khung `01-problem-scan.md` và `02-deep-dive-report.md` bám sát rubric, để tôi tập trung thời gian vào việc kiểm tra tính đúng đắn thay vì định dạng.

## 2. AI đã sai / hallucinate ở đâu, và tôi phát hiện ra sao?

Tôi không xem output của AI là đúng mặc định — tôi chủ động kiểm chứng ở các điểm sau:

1. **Nguy cơ dùng sai/lỗi thời cú pháp SDK:** Các thư viện như `google-genai` thay đổi API khá nhanh (ví dụ SDK cũ `google-generativeai` dùng cách gọi khác hẳn SDK mới). Thay vì tin vào trí nhớ huấn luyện của AI, tôi yêu cầu AI **tra cứu trực tiếp tài liệu chính thức** trước khi viết code, để tránh việc AI "nhớ nhầm" sang một phiên bản SDK cũ đã lỗi thời.
2. **Ranh giới an toàn ban đầu có lỗ hổng:** Bản nháp system prompt đầu tiên chỉ nói "không được gửi tin khi chưa duyệt" chung chung, chưa xử lý rõ trường hợp **pin ở mức chưa xác định (unknown)** — một attacker có thể khai thác khoảng xám này bằng cách không khai báo % pin. Tôi yêu cầu bổ sung rõ nhánh xử lý khi battery level không được cung cấp.
3. **Adversarial test ban đầu chưa đủ (chỉ 2 test case):** Trong khi `01-worksheet.md` yêu cầu tối thiểu 3 test tấn công (bao gồm cả tấn công dạng **prompt injection / rút trích system prompt**, không chỉ vi phạm luật nghiệp vụ). Tôi phải chủ động yêu cầu bổ sung Test Case 3 để kiểm tra khả năng model từ chối tiết lộ system prompt khi bị giả danh admin/"DAN mode".
4. **Rủi ro AI tự tin quá mức về hành vi thật của Gemini:** AI có thể viết ra logic kiểm tra (assertion) rất gọn gàng, nhưng **không có gì đảm bảo Gemini 2.5 sẽ tuân thủ 100%** cho đến khi tôi thực sự chạy script với API key thật — đây là điểm tôi tự nhắc mình không được "tin trên giấy", phải kiểm chứng bằng cách chạy thật và đọc log output.

## 3. Tôi đã sửa prompt / ranh giới như thế nào để đạt kết quả chuẩn?

- Viết lại `SYSTEM_PROMPT` theo dạng **hai định dạng đầu ra loại trừ lẫn nhau** (plain-text `[DRAFT_ONLY]` HOẶC JSON `dispatch_mobile_charger`), thay vì mô tả bằng văn xuôi — giúp giảm khả năng model trộn lẫn hai định dạng hoặc thêm lời giải thích thừa.
- Thêm điều khoản **chống prompt-injection tường minh**: liệt kê rõ các chiêu thức tấn công phổ biến (giả danh admin, roleplay "DAN mode", viện lý do khẩn cấp) và khẳng định các ranh giới "có hiệu lực bất kể cách diễn đạt của người dùng".
- Thêm cấm chỉ **tiết lộ system prompt**, vì đây là một lớp phòng thủ độc lập với Rule 1/Rule 2 nhưng quan trọng không kém với một dispatcher co-pilot vận hành thật.
- Bổ sung **Test Case 3** (prompt injection) và logic verification tương ứng để không chỉ kiểm tra tuân thủ nghiệp vụ mà còn kiểm tra khả năng chống rò rỉ hướng dẫn nội bộ.

## 4. Bài học rút ra

Bài học lớn nhất của tôi là: AI là một **thought-partner tốc độ cao nhưng không phải nguồn sự thật cuối cùng**. Giá trị lớn nhất tôi nhận được không phải là code/prompt AI viết ra ngay lần đầu, mà là ở **những câu hỏi phản biện tôi buộc phải đặt ra** cho chính output đó: "Ranh giới này có lỗ hổng nào chưa được nghĩ tới?", "SDK này có còn đúng với phiên bản mới nhất không?", "Nếu tôi là người cố tình phá luật, tôi sẽ tấn công ở đâu?". Kỹ năng scoping AI Product, vì vậy, nằm nhiều ở khả năng **đặt câu hỏi và kiểm chứng**, hơn là khả năng "ra lệnh" cho AI viết đúng ngay từ đầu.
