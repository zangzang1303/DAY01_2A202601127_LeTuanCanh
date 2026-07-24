# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00  
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng,
không để dồn hết về cuối buổi. Thay phần trả lời mẫu bằng nội dung phản ánh
dựa trên kết quả thực hành và quan sát thực tế.

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature

Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt  
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)

> Khi temperature bằng 0.0, câu trả lời thường ổn định, trực tiếp và thiên về những thông tin phổ biến, ít thay đổi nếu chạy lại nhiều lần. Ở mức 0.5, model vẫn giữ được tính chính xác nhưng cách diễn đạt tự nhiên và đa dạng hơn; khi tăng lên 1.0, model có xu hướng lựa chọn những ví dụ thú vị hoặc ít phổ biến hơn. Với temperature 1.5, nội dung sáng tạo và khó đoán hơn, nhưng đồng thời cũng dễ dài dòng, thiếu tập trung hoặc đưa ra thông tin cần kiểm chứng lại.
>
> Qua thử nghiệm, tôi nhận thấy temperature không làm model “thông minh hơn” mà chủ yếu điều chỉnh mức độ ngẫu nhiên trong quá trình lựa chọn token tiếp theo. Vì vậy, temperature thấp phù hợp với các tác vụ yêu cầu tính ổn định và chính xác, còn temperature cao phù hợp hơn với viết sáng tạo, phát triển ý tưởng hoặc tạo nhiều phương án khác nhau.

### Câu 1.2 — Chọn temperature cho sản phẩm

**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**

> Tôi sẽ đặt temperature trong khoảng 0.2–0.3 cho chatbot hỗ trợ khách hàng. Mức này giúp câu trả lời có tính ổn định, nhất quán và ít tự tạo thêm thông tin không có trong dữ liệu, nhưng vẫn đủ linh hoạt để giao tiếp tự nhiên thay vì trả lời quá máy móc.
>
> Chatbot hỗ trợ khách hàng thường xử lý những nội dung có tính nghiệp vụ như chính sách đổi trả, phí vận chuyển, trạng thái đơn hàng, điều kiện bảo hành hoặc hướng dẫn sử dụng sản phẩm. Với các nội dung này, độ chính xác quan trọng hơn tính sáng tạo. Nếu đặt temperature quá cao, model có thể diễn đạt mỗi lần một khác hoặc suy đoán thêm chính sách không tồn tại, từ đó gây hiểu nhầm và ảnh hưởng đến trải nghiệm khách hàng.
>
> Trong hệ thống thực tế, tôi cũng sẽ kết hợp temperature thấp với dữ liệu được truy xuất từ cơ sở dữ liệu hoặc hệ thống RAG. Như vậy, model vừa có nguồn thông tin đáng tin cậy vừa có cách trả lời thống nhất. Riêng những chức năng như gợi ý nội dung marketing hoặc viết lời chào sáng tạo có thể sử dụng temperature cao hơn.

### Câu 1.3 — Đánh đổi chi phí

Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**

> Tổng số lượt gọi API mỗi ngày là:
>
> `10.000 × 3 = 30.000 lượt gọi/ngày`.
>
> Tổng số token đầu ra mỗi ngày là:
>
> `30.000 × 350 = 10.500.000 token đầu ra/ngày`.
>
> Theo bảng giá được sử dụng trong bài, GPT-4o có giá đầu ra là `0,010 USD/1.000 token`, nên chi phí đầu ra ước tính là:
>
> `10.500.000 / 1.000 × 0,010 = 105 USD/ngày`.
>
> GPT-4o-mini có giá đầu ra là `0,0006 USD/1.000 token`, nên chi phí đầu ra ước tính là:
>
> `10.500.000 / 1.000 × 0,0006 = 6,3 USD/ngày`.
>
> Như vậy, nếu chỉ xét chi phí token đầu ra, GPT-4o đắt hơn GPT-4o-mini khoảng:
>
> `105 / 6,3 ≈ 16,67 lần`.
>
> Trong 30 ngày, chi phí đầu ra ước tính của GPT-4o là khoảng `3.150 USD`, trong khi GPT-4o-mini khoảng `189 USD`. Đây là sự khác biệt rất lớn khi hệ thống có nhiều người dùng. Phép tính này mới chỉ xét token đầu ra; chi phí thực tế còn phụ thuộc vào số token đầu vào, system prompt, history và context được gửi kèm mỗi request.
>
> GPT-4o xứng đáng với chi phí trong các tác vụ phức tạp như phân tích tài liệu chuyên môn dài, suy luận nhiều bước, xử lý yêu cầu có độ rủi ro cao hoặc tạo nội dung cần chất lượng ngôn ngữ tốt. GPT-4o-mini phù hợp hơn với chatbot hỏi đáp thông thường, phân loại văn bản, trích xuất thông tin, chuẩn hóa dữ liệu hoặc xử lý lượng request lớn. Một thiết kế hợp lý là dùng mini làm model mặc định và chỉ chuyển sang GPT-4o khi câu hỏi được đánh giá là phức tạp.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona

Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi  
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:

- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)

> Với persona giáo viên tiểu học, model sử dụng câu ngắn, từ vựng đơn giản và hạn chế các thuật ngữ kỹ thuật. Blockchain thường được so sánh với một cuốn sổ chung mà nhiều người cùng giữ bản sao; khi có thông tin mới, mọi người cùng kiểm tra và ghi lại, vì vậy một người khó có thể tự ý thay đổi dữ liệu. Cách giải thích này tập trung vào trực giác và ví dụ gần gũi hơn là mô tả chính xác toàn bộ kiến trúc kỹ thuật.
>
> Với persona chuyên gia tài chính, phản hồi thường dài, có cấu trúc và chuyên sâu hơn. Model sử dụng những thuật ngữ như sổ cái phân tán, hàm băm mật mã, cơ chế đồng thuận, tính bất biến, mạng ngang hàng, smart contract và tài sản số. Ngoài định nghĩa, câu trả lời còn có thể đề cập đến rủi ro, tính thanh khoản, chi phí giao dịch, khả năng mở rộng và ứng dụng blockchain trong tài chính.
>
> Điều này cho thấy system prompt ảnh hưởng mạnh đến vai trò, giọng điệu, độ dài, mức độ chuyên môn, cách lựa chọn từ vựng và loại ví dụ mà model sử dụng. Tuy nhiên, system prompt chủ yếu định hướng cách model trả lời chứ không bảo đảm mọi thông tin đều đúng. Vì vậy, với ứng dụng thực tế, system prompt vẫn cần kết hợp với dữ liệu đáng tin cậy, kiểm tra đầu ra và các quy tắc nghiệp vụ.

### Câu 2.2 — tiktoken vs đếm từ

Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**

> Với một đoạn văn tiếng Việt khoảng 100 từ, công thức ước lượng của Part 1 cho kết quả:
>
> `100 / 0,75 ≈ 133 token`.
>
> Khi sử dụng `count_tokens` với tiktoken, đoạn văn có thể cho kết quả khoảng 200 token. Khi đó, mức chênh lệch so với cách ước lượng theo số từ là:
>
> `(200 - 133) / 133 × 100% ≈ 50,4%`.
>
> Như vậy, trong ví dụ này, số token do tiktoken đếm cao hơn khoảng 50% so với công thức ước lượng thô. Con số cụ thể có thể thay đổi tùy nội dung đoạn văn, model và bộ encoding, nhưng kết quả cho thấy việc quy đổi trực tiếp từ số từ sang token không hoàn toàn chính xác đối với tiếng Việt.
>
> Nguyên nhân là tokenizer không xem mỗi từ tương ứng với đúng một token. Nó chia văn bản thành các đơn vị nhỏ dựa trên những chuỗi ký tự thường xuất hiện trong dữ liệu huấn luyện. Tiếng Việt sử dụng dấu thanh, nhiều ký tự Unicode và các từ ghép có các âm tiết được phân tách bằng khoảng trắng. Một từ theo nghĩa ngôn ngữ có thể gồm nhiều âm tiết, trong khi tokenizer có thể tiếp tục chia từng âm tiết hoặc từng phần ký tự thành nhiều token.
>
> Do đó, để tính chi phí API chính xác hơn, nên dùng tokenizer tương ứng với model thay vì chỉ ước lượng dựa trên số từ. Trong hệ thống thực tế, ngoài nội dung người dùng nhập còn phải tính cả system prompt, lịch sử hội thoại và context được gửi vào model.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming

**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)

> Streaming quan trọng nhất khi model cần tạo câu trả lời dài hoặc thời gian xử lý tương đối lâu, chẳng hạn chatbot hội thoại, trợ lý lập trình, công cụ viết nội dung, tóm tắt tài liệu hoặc giải thích kiến thức. Thay vì chờ toàn bộ phản hồi hoàn thành, người dùng có thể nhìn thấy các phần nội dung xuất hiện ngay khi model sinh ra. Điều này làm giảm thời gian chờ cảm nhận và khiến hệ thống có vẻ phản hồi nhanh hơn, dù tổng thời gian xử lý có thể không thay đổi nhiều. Người dùng cũng có thể đọc phần đầu trong khi phần còn lại đang được tạo và có thể dừng sớm nếu câu trả lời không đúng hướng.
>
> Non-streaming phù hợp hơn khi phản hồi rất ngắn hoặc khi chương trình cần nhận toàn bộ dữ liệu trước khi xử lý tiếp. Ví dụ, hệ thống backend cần model trả về JSON để kiểm tra schema, phân loại một yêu cầu, trích xuất trường dữ liệu, chấm điểm nội dung hoặc lưu kết quả vào cơ sở dữ liệu. Trong các trường hợp đó, việc xử lý từng chunk không đem lại nhiều lợi ích và còn làm code phức tạp hơn. Vì vậy, nên chọn streaming cho trải nghiệm tương tác trực tiếp và non-streaming cho các tác vụ xử lý nội bộ cần kết quả hoàn chỉnh.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?

**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff làm thời gian chờ tăng dần sau mỗi lần thất bại. Ví dụ, với `base_delay = 0.1`, các lần chờ có thể lần lượt là `0.1`, `0.2`, `0.4` giây. Cơ chế này giúp giảm tần suất request khi API đang quá tải, tránh việc client liên tục gửi lại yêu cầu trong thời gian ngắn và tạo thêm áp lực cho hệ thống.
>
> Nếu sử dụng delay cố định, chẳng hạn mọi client đều chờ đúng 1 giây, hàng nghìn client bị lỗi cùng lúc có thể đồng thời retry sau 1 giây. Khi đó, server lại nhận một lượng request rất lớn tại cùng một thời điểm, dẫn đến hiện tượng “thundering herd”. Hệ thống có thể tiếp tục quá tải, khiến các request lại thất bại và lặp lại chu kỳ tương tự.
>
> Exponential backoff phân tán dần các lần retry theo thời gian và tạo cơ hội để server phục hồi. Tuy nhiên, nếu tất cả client vẫn sử dụng cùng một công thức và bắt đầu cùng thời điểm, chúng vẫn có khả năng retry đồng thời. Vì vậy, hệ thống thực tế thường bổ sung `jitter`, tức thêm một khoảng thời gian ngẫu nhiên vào delay. Ngoài ra, chỉ nên retry những lỗi tạm thời như timeout, rate limit hoặc lỗi máy chủ; các lỗi như API key sai hoặc request không hợp lệ thường không nên retry nhiều lần.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona

**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**

> System prompt tôi lựa chọn là:
>
> **"Bạn là trợ giảng thân thiện của khóa học AI dành cho người mới bắt đầu. Hãy trả lời chính xác, rõ ràng và ngắn gọn bằng tiếng Việt. Khi giải thích khái niệm kỹ thuật, hãy dùng ví dụ đơn giản, trình bày từng bước và tránh sử dụng thuật ngữ khó nếu chưa giải thích. Nếu không chắc chắn về thông tin, hãy nói rõ mức độ không chắc chắn thay vì tự tạo câu trả lời."**
>
> Tôi sử dụng cụm “trợ giảng thân thiện” để định hướng model giao tiếp gần gũi, kiên nhẫn và phù hợp với người mới học. Nếu chỉ ghi “chuyên gia AI”, model có thể sử dụng quá nhiều thuật ngữ kỹ thuật hoặc giả định rằng người dùng đã có kiến thức nền.
>
> Yêu cầu “trả lời chính xác, rõ ràng và ngắn gọn bằng tiếng Việt” giúp câu trả lời tập trung vào vấn đề chính, dễ theo dõi và phù hợp với đối tượng người dùng Việt Nam. “Ngắn gọn” không có nghĩa là bỏ qua thông tin quan trọng, mà là hạn chế lặp lại hoặc trình bày lan man.
>
> Cụm “trình bày từng bước” hữu ích với các nội dung như code, thuật toán hoặc quy trình gọi API, vì người học có thể theo dõi logic dễ hơn. Cuối cùng, yêu cầu model nói rõ khi không chắc chắn nhằm giảm nguy cơ model trả lời sai với giọng điệu quá tự tin.

### Câu 4.2 — Hạn chế & cải thiện

**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**

> Hạn chế lớn nhất của trợ lý hiện tại là history chỉ giữ 3 lượt hội thoại gần nhất. Khi cuộc trò chuyện kéo dài, các thông tin quan trọng được cung cấp ở những lượt đầu sẽ bị xóa khỏi history. Vì vậy, model có thể quên tên người dùng, mục tiêu ban đầu, các ràng buộc đã thống nhất hoặc những quyết định đã đưa ra trước đó. Điều này làm giảm khả năng duy trì ngữ cảnh và khiến người dùng phải lặp lại thông tin.
>
> Một cải thiện cụ thể là xây dựng cơ chế bộ nhớ gồm hai tầng. Tầng thứ nhất vẫn giữ một số lượt hội thoại gần nhất để model hiểu ngữ cảnh tức thời. Tầng thứ hai lưu các thông tin dài hạn dưới dạng bản tóm tắt hoặc các “memory item” có cấu trúc, chẳng hạn tên người dùng, sở thích, mục tiêu và các quyết định quan trọng.
>
> Khi history sắp vượt giới hạn, chương trình có thể gọi model để tóm tắt phần hội thoại cũ rồi lưu bản tóm tắt vào cơ sở dữ liệu. Với dữ liệu lớn hơn, có thể tạo embedding cho các đoạn hội thoại và lưu vào vector database. Khi người dùng gửi câu hỏi mới, hệ thống tìm những memory liên quan nhất và đưa chúng vào phần context trước khi gọi model.
>
> Ngoài bộ nhớ, trợ lý cũng cần được cải thiện về kiểm duyệt nội dung, bảo vệ dữ liệu cá nhân, giới hạn token, theo dõi chi phí và xử lý lỗi. Trong phiên bản hoàn chỉnh hơn, tôi sẽ bổ sung logging cho latency, số token, loại lỗi và số lần retry, đồng thời đặt giới hạn chi phí cho từng phiên để tránh sử dụng API ngoài dự kiến.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
