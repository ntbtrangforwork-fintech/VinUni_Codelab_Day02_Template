# Deep-Dive Report — Xanh SM: hỗ trợ xử lý sự cố pin thấp ngoài đường

> **Quyết định scope:** Xây dựng một công cụ hỗ trợ điều phối viên, không phải hệ thống điều phối tự trị. Rule engine ra quyết định an toàn từ dữ liệu thời gian thực; LLM chỉ tạo bản nháp giao tiếp để con người duyệt. Trong prototype của lab, rule bắt buộc là: **pin <5% không được đề xuất trạm cách quá 5 km; phải trả về phương án điều xe sạc pin di động.**

## 1. Bối cảnh và giả định cần xác thực

VinFast công bố mạng lưới trạm sạc trên 63 tỉnh thành và khả năng tra cứu trạm qua ứng dụng. Điều đó làm bài toán tra cứu trạm có cơ sở kỹ thuật, nhưng **không chứng minh** Xanh SM hiện có quy trình hay khối lượng ca sự cố như mô tả dưới đây. [Nguồn VinFast](https://vinfastauto.com/vn_vi/ha-tang-cho-xe-dien-tai-viet-nam)

Các số về khối lượng ca, thời gian xử lý, ngưỡng pin và khoảng cách trong báo cáo là **giả định scoping cho pilot**. Trước khi triển khai, đội vận hành phải xác thực bằng log ticket, GPS, trạng thái trạm, loại xe và kết quả cứu hộ trong tối thiểu 4 tuần.

## 2. Current-State Workflow Mapping

**Tình huống:** Tài xế báo pin thấp/sắp hết pin trong khi đang vận hành. Điều phối viên phải tìm phương án an toàn trước, sau đó mới tối ưu thời gian xe quay lại phục vụ.

```text
Tài xế
  │  (chat/cuộc gọi: biển số, % pin, vị trí, tình trạng xe)
  ▼
┌─────────────────────────────────────────────────────────────┐
│ 1. Tiếp nhận và mở ticket — Dispatcher — ~1 phút            │
│ In: báo cáo tự do của tài xế  | Out: mã ticket               │
└─────────────────────────────────────────────────────────────┘
  │ 🔄 Handoff: tài xế → điều phối viên
  ▼
┌─────────────────────────────────────────────────────────────┐
│ 2. Xác minh xe, % pin và GPS — Dispatcher — ~2 phút         │
│ In: biển số/ticket        | Out: dữ liệu sự cố đã xác minh  │
└─────────────────────────────────────────────────────────────┘
  ▼
┌─────────────────────────────────────────────────────────────┐
│ 3. Mở bản đồ + dashboard trạm — Dispatcher — ~4 phút 🔴     │
│ Kiểm tra khoảng cách, loại xe/cổng sạc, trụ còn chỗ          │
│ Out: danh sách phương án trạm                                │
└─────────────────────────────────────────────────────────────┘
  │ 🔄 Handoff: dashboard trạm/bản đồ → điều phối viên
  ▼
┌─────────────────────────────────────────────────────────────┐
│ 4. Đánh giá an toàn, chọn trạm/cứu hộ, soạn tin — ~3 phút 🔴 │
│ Out: hướng dẫn hoặc yêu cầu cứu hộ                           │
└─────────────────────────────────────────────────────────────┘
  ▼
┌─────────────────────────────────────────────────────────────┐
│ 5. Gửi hướng dẫn, xác nhận và đóng/ghi log — ~2 phút        │
│ Out: tài xế nhận phương án; ticket có audit trail            │
└─────────────────────────────────────────────────────────────┘

Tổng giả định: ~12 phút/ca. 🔴 = bottleneck cần đo bằng log.
```

### Điểm nghẽn và rủi ro

| Điểm | Vấn đề | Hệ quả nếu sai |
|---|---|---|
| Bước 3 | Phải đối chiếu nhiều nguồn; trạng thái trạm có thể thay đổi nhanh. | Chọn trạm không khả dụng hoặc không phù hợp. |
| Bước 4 | Quyết định bị áp lực thời gian và phụ thuộc vào ngưỡng an toàn. | Xe có thể không tới được trạm, tăng thời gian ngừng khai thác/rủi ro trên đường. |
| Bước 5 | Soạn tin tay không nhất quán, có thể thiếu cảnh báo hoặc thông tin xác nhận. | Tài xế hiểu sai; khó audit hậu sự cố. |

## 3. Problem Statement — 6 fields

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Điều phối viên Xanh SM xử lý sự cố; tài xế là người cung cấp thông tin và thực hiện phương án. Trưởng ca chịu trách nhiệm escalation. |
| **2. Current Workflow** | Dispatcher nhận báo cáo, xác minh xe/% pin/GPS, tra bản đồ và dashboard trạm, tự đánh giá khả năng tới trạm hoặc cần cứu hộ, soạn tin và gửi/ghi log. Quy trình giả định gồm 5 bước, khoảng 12 phút/ca; cần baseline bằng log thực tế. |
| **3. Bottleneck** | Tra cứu đồng thời trạm khả dụng, tương thích xe/cổng sạc và khoảng cách; sau đó diễn đạt phương án an toàn cho tài xế. Đây là phần dễ sai nhất vì dữ liệu động và điều kiện an toàn không được bỏ qua. |
| **4. Business Impact** | Mỗi phút xử lý kéo dài làm tăng thời gian tài xế chờ và xe không sẵn sàng nhận cuốc; đồng thời chiếm năng lực điều phối trong giờ cao điểm. Đây là giả thuyết tác động, chưa quy đổi thành VND khi chưa có số ca, downtime và giá trị cuốc thực tế. |
| **5. Success Metric** | Pilot 4 tuần: (a) median từ ticket hợp lệ đến **bản nháp** ≤3 phút; (b) **0** khuyến nghị vi phạm rule an toàn trên dữ liệu pilot; (c) ≥95% draft được dispatcher duyệt không cần sửa phần an toàn; (d) theo dõi thời gian xe trở lại online và tỷ lệ hủy cuốc so với baseline, nhưng không cam kết cải thiện cho đến khi có dữ liệu. |
| **6. Operational Boundary** | AI chỉ đọc dữ liệu được cấp quyền, chạy rule, và tạo **[DRAFT_ONLY]**. Trong prototype: nếu pin **<5%**, AI tuyệt đối không gợi ý trạm **>5 km** mà phải trả về `dispatch_mobile_charger`. AI không tự gửi tin, không tự điều xe/cứu hộ, không sửa trạng thái trạm, không suy đoán % pin/vị trí/trụ trống và không vượt rule an toàn. Dispatcher phải duyệt; ca dữ liệu thiếu, confidence thấp hoặc có dấu hiệu nguy hiểm phải escalation cho trưởng ca/đội cứu hộ. |

## 4. AI Fit: Rule vs. LLM vs. Agent

| Lựa chọn | Phù hợp với phần nào? | Quyết định |
|---|---|---|
| **Rule / state machine** | Kiểm tra dữ liệu bắt buộc, ngưỡng an toàn được Operations phê duyệt, tương thích xe–cổng sạc, quãng đường tối đa, thứ tự escalation. | **Bắt buộc.** Đây là lớp quyết định cốt lõi vì điều kiện có cấu trúc, cần audit và kiểm thử xác định. |
| **LLM feature** | Chuyển phương án rule-approved thành tin nhắn tiếng Việt rõ ràng; tóm tắt ticket và nêu các dữ liệu còn thiếu. | **Có điều kiện.** Chỉ nhận input đã chuẩn hóa, trả JSON schema, không có quyền gọi công cụ hoặc gửi tin. |
| **Agentic loop** | Tự chọn tool, tự thay đổi phương án và thực thi hành động. | **Không chọn.** Không cần thiết trong MVP; rủi ro tự trị cao hơn lợi ích và làm khó audit. |

**Kết luận AI Fit:** Rule-first + LLM drafting + Human-in-the-loop. Nếu template tin nhắn cố định đã đạt chất lượng/speed mục tiêu, LLM có thể được loại khỏi MVP.

## 5. Future-State Flow & Safety Controls

```text
Tài xế báo sự cố
      │
      ▼
[1] Dispatcher tạo/xác minh ticket
      │
      ▼
[2] 🔵 Data adapter lấy GPS, % pin, loại xe, trạng thái trạm
      │
      ├── thiếu/stale dữ liệu ──→ ↩️ Fallback A: dispatcher tra cứu tay + trưởng ca
      ▼
[3] 🔵 Rule engine kiểm tra an toàn, tương thích, khoảng cách và escalation
      │
      ├── pin <5% và trạm >5 km ──→ `dispatch_mobile_charger`; không gợi ý trạm
      ▼
[4] 🔵 LLM tạo JSON bản nháp [DRAFT_ONLY] từ phương án đã duyệt bởi rule
      │
      ├── JSON lỗi / confidence thấp ──→ ↩️ Fallback B: template tin nhắn chuẩn
      ▼
[5] 🟢 Dispatcher xem dữ liệu nguồn + draft, sửa/duyệt hoặc từ chối
      │
      ├── từ chối / nghi ngờ ──→ ↩️ Fallback C: xử lý thủ công và escalation
      ▼
[6] Hệ thống gửi tin SAU KHI DUYỆT, lưu rule result + phiên bản draft + người duyệt
```

### Safety gates trước khi được phép tạo draft

1. Có đủ `vehicle_id`, GPS còn hiệu lực, % pin còn hiệu lực, loại xe và trạng thái trạm có timestamp.
2. Chỉ đề xuất trạm đã qua kiểm tra tương thích và khả dụng. Với prototype của lab: **pin <5% + trạm >5 km ⇒ `dispatch_mobile_charger`**. Khi production, Operations phải rà soát và phê duyệt lại ngưỡng này theo dòng xe/khu vực.
3. Nếu bất kỳ dữ liệu an toàn nào thiếu hoặc stale, output phải là `escalate_to_dispatcher`; không được “đoán” trạm thay thế.
4. LLM output bắt buộc theo JSON schema, mở đầu nội dung tài xế bằng `[DRAFT_ONLY]`, và không có API gửi tin.

## 6. Liên kết với Prompt Prototype

`starter-code/prompt_prototype.py` kiểm tra hai biên an toàn: output luôn có `[DRAFT_ONLY]`, và tấn công yêu cầu đi trạm 8 km khi pin 2% phải kích hoạt `dispatch_mobile_charger`. Vì vậy system prompt và structured output của prototype phải phản ánh đúng Future-State Flow ở trên; kết quả test là bằng chứng kiểm tra **ranh giới prompt**, không phải bằng chứng rằng hiệu quả vận hành đã đạt metric pilot.

## 7. Pilot plan và dữ liệu cần có

| Hạng mục | Thiết kế pilot tối thiểu |
|---|---|
| **Phạm vi** | Một thành phố, một nhóm dispatcher, chỉ các ca pin thấp có dữ liệu tối thiểu; 4 tuần sau khi có baseline. |
| **Dữ liệu** | Ticket/ID đã pseudonymize, timestamp từng bước, % pin/GPS/loại xe, snapshot trạm và cổng sạc, phương án rule, draft, quyết định người duyệt, kết quả cuối. |
| **Đánh giá** | So sánh với baseline/manual theo median/p90; audit 100% ca safety-critical; QA lấy mẫu ngẫu nhiên draft. Không so sánh chỉ bằng trung bình. |
| **Tiêu chí dừng** | Dừng ngay nếu xuất hiện một khuyến nghị vượt rule, dữ liệu nguồn stale gây hướng dẫn sai, hoặc dispatcher không thể giải thích/audit quyết định. |
| **Bảo mật** | Phân quyền tối thiểu; không đưa số điện thoại/định danh thô vào prompt; retention log theo chính sách nội bộ. |

## 8. Evaluate — AI Readiness Checklist

| Tiêu chí | Trạng thái | Bằng chứng / khoảng trống |
|---|---|---|
| Có dữ liệu mẫu/logs sạch để test? | ✅ Đủ cho prototype | Prototype dùng các test case có cấu trúc, gồm ca pin 2% yêu cầu đi trạm 8 km. Trước pilot thật vẫn phải nạp log đã pseudonymize và snapshot trạm. |
| Rủi ro khi AI sai có kiểm soát qua HITL/fallback? | ✅ Có thiết kế và test | Rule-first, dispatcher duyệt, ba nhánh fallback và audit trail. Prototype kiểm tra `[DRAFT_ONLY]` và `dispatch_mobile_charger`. |
| Stakeholder sẵn sàng đổi quy trình? | ✅ Trong phạm vi pilot | Pilot chỉ thêm màn hình gợi ý/draft; dispatcher vẫn giữ quyền quyết định và có thể quay lại luồng thủ công ngay. Trưởng ca cần xác nhận SOP trước khi bật shadow mode. |

### Quyết định: **GO — xây prototype và pilot shadow mode có rào chắn**

**GO không có nghĩa là tự động triển khai production.** Bài toán có workflow rõ, ranh giới kỹ thuật kiểm thử được và phương án fallback ngay lập tức. Nhóm sẽ xây rule router + template/LLM draft, chạy **shadow mode** trong 4 tuần (hệ thống chỉ đề xuất, không gửi), và đo safety gate trước mọi metric năng suất. Chỉ khi đạt 0 vi phạm rule, dữ liệu trạm/pin đủ tươi và dispatcher duyệt SOP thì mới chuyển sang pilot có người duyệt. Nếu không đạt, rollback về quy trình thủ công; không có hành động tự trị nào được bật.
