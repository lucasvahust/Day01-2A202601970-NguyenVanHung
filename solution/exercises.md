# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng mẫu bằng câu trả lời thật
(trong phần câu hỏi và trả lời, không để lại placeholder).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Khi temperature tăng, câu trả lời càng đa dạng và sáng tạo hơn. Temperature 0.0 cho phản hồi ổn định, ít thay đổi và tập trung, còn temperature 1.5 thì câu chữ linh hoạt hơn, thỉnh thoảng có thể mang tính bất ngờ hoặc ít thực tế hơn.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ chọn temperature khoảng 0.2–0.3 để chatbot giữ câu trả lời chắc chắn và ít tạo nội dung sáng tạo không cần thiết. Support cần chính xác, nhất quán và dễ kiểm soát, nên không nên dùng temperature cao.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Với giá output 0.010 USD so với 0.0006 USD, GPT-4o đắt hơn khoảng 16–17 lần cho cùng số token đầu ra. GPT-4o xứng đáng khi cần chất lượng trả lời cao, hiểu ngữ cảnh phức tạp hoặc nội dung chuyên sâu. GPT-4o-mini phù hợp với các câu hỏi thông tin cơ bản, FAQ hoặc trợ lý nội bộ khi ưu tiên tiết kiệm chi phí.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Phản hồi với persona giáo viên tiểu học dùng từ ngữ đơn giản, ngắn gọn và ví dụ dễ hiểu, còn persona chuyên gia tài chính sử dụng thuật ngữ kỹ thuật, giải thích chi tiết hơn và sâu hơn. System prompt định hướng phong cách, mức độ chi tiết và lựa chọn từ vựng của model. Nó khiến cùng một câu hỏi nhận được câu trả lời khác nhau về giọng điệu và mục đích.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với đoạn tiếng Việt 100 từ, tiktoken có thể trả về khoảng 120–140 token, trong khi ước lượng `số từ / 0.75` cho ra khoảng 133 token, nên chênh nhau tầm 10–20%. Tiếng Việt thường tốn nhiều token hơn bởi tokenizer phân chia theo subword và dấu câu, dấu thanh, còn nhiều từ ghép riêng biệt; vì vậy cùng số ký tự tiếng Việt thường bị chia thành nhiều đơn vị token hơn tiếng Anh.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi người dùng tương tác trực tiếp và cần thấy phản hồi sớm, chẳng hạn chatbot hỗ trợ, hỏi đáp hoặc tìm kiếm nhanh. Non-streaming phù hợp hơn cho các tác vụ xử lý batch, khi cần đầu ra hoàn chỉnh và cần kiểm tra toàn bộ văn bản trước khi hiển thị.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff giảm áp lực lên server bằng cách kéo dài thời gian chờ sau mỗi lần thất bại, cho hệ thống thời gian phục hồi. Nếu nhiều client retry với delay cố định giống nhau, chúng sẽ đổ về server cùng lúc sau mỗi lần chờ và có thể gây ra hiệu ứng tải cao hơn, còn backoff giúp phân tán lại các lần retry.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Tôi chọn persona: "Bạn là trợ lý AI thân thiện, trả lời ngắn gọn bằng tiếng Việt, rõ ràng và tập trung vào câu hỏi của người dùng." Cụm từ "trả lời ngắn gọn" giúp tránh phản hồi quá dài, còn yêu cầu "bằng tiếng Việt" đảm bảo assistant luôn dùng đúng ngôn ngữ cho người dùng Việt Nam.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất là history chỉ giữ 3 lượt nên trợ lý không có bộ nhớ dài hạn và dễ quên ngữ cảnh trước đó. Có thể cải thiện bằng cách lưu trữ bản tóm tắt hội thoại hoặc state quan trọng trong một hồ sơ ngắn và thêm vào prompt ở mỗi lượt mới; cách này giúp trợ lý nhớ chủ đề chính mà không làm token input quá dài.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
