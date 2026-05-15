# 02 · Configuration Design — Đặt tên + Chốt knobs cho ≥3 Configs

> **Mục tiêu**: Biến phác thảo ở `01-base-flow.md` thành ≥3 configurations chi tiết, mỗi config có tên + 3 knobs đã chốt + lý do chọn.
>
> **Thời gian**: 15 phút (đầu phần Main, trước khi tính cost)

---

## Tại sao đặt tên + viết lý do?

Khi present, nhóm sẽ nói "Config 1, Config 2, Config 3" → người nghe sẽ chán ngay. Đặt tên gợi mở (Budget Bot, Premium Concierge, Smart Mix...) giúp memorable + cho thấy nhóm hiểu rõ tradeoff. Viết lý do giúp nhóm tự kiểm tra: "Mình chọn config này vì lý do gì? Có justify được không?"

---

## Cách điền

Với mỗi config: đặt tên + chốt 3 knobs + viết 2–3 câu lý do chọn. Mỗi câu lý do phải gắn với 1 tình huống thực tế (volume thấp / khách hỏi visa nhiều / budget bị siết...).

Tham khảo bảng pricing chi tiết tại `cost-reference-card.md` mục **3. Decision Points**.

---

## Config 1

**Tên config** (gợi mở: "Budget Bot", "Bare Minimum", "Lean Mode", "Night Mode" — đặt tên có cá tính):

```text
Economy Explorer
```

### 3 Knobs

**① Model tier**:

```text
Response model: Gemini 2.5 Flash-Lite → giá $0.10 / $0.40 per 1M tokens (input/output)
Classifier model: Keyword routing → giá $0
```

**② Web search**:

```text
☑ OFF
□ ON selective — bật cho intent: __________________
□ ON broad
```

**③ History management**:

```text
☑ Last 3
□ Last 5
□ Full
□ Summarize every ___ turns
```

### Lý do nhóm chọn config này

Trước khi viết, tự hỏi:

- Config này phục vụ tình huống nào tốt nhất? (mùa thấp điểm? night-time? volume cao đột biến?)
- Trade-off chính là gì? (Rẻ nhưng kém chất lượng? Đắt nhưng chính xác?)
- Khách hàng nào sẽ hài lòng nhất với config này? Khách nào sẽ thất vọng?

```text
- Config này phù hợp cho mùa thấp điểm hoặc website mới launch khi volume chat chưa cao và công ty muốn giảm chi phí tối đa. 
Phần lớn câu hỏi Guide/Destination có thể trả lời bằng knowledge base có sẵn mà không cần web search real-time.

- Việc dùng Flash-Lite + Last 3 turns giúp response rất nhanh và cost cực thấp, phù hợp cho khách hỏi nhanh 
những câu đơn giản như địa điểm du lịch, món ăn, hoặc cách di chuyển.

- Config này phù hợp với budget travelers hoặc khách chỉ cần FAQ cơ bản. 
Trade-off là chatbot có thể quên context ở conversation dài và thông tin visa/thời tiết có thể outdated.
```

### Rủi ro lớn nhất của config này

```text
Thông tin Visa/Weather có thể không còn chính xác vì không dùng web search, đặc biệt khi policy thay đổi đột ngột.
```

---

## Config 2

**Tên config**:

```text
Luxury Concierge
```

### 3 Knobs

**① Model tier**:

```text
Response model: Claude Sonnet 4.6 → giá $3.00 / $15.00 per 1M tokens
Classifier model: Gemini 2.5 Flash-Lite → giá $0.10 / $0.40 per 1M tokens
```

**② Web search**:

```text
□ OFF
□ ON selective — bật cho intent: __________________
☑ ON broad
```

**③ History management**:

```text
□ Last 3
□ Last 5
☑ Full
□ Summarize every ___ turns
```

### Lý do nhóm chọn config này

```text
- Config này hướng tới khách high-value như luxury travelers hoặc khách VIP cần trải nghiệm tư vấn gần giống concierge thật. 
Claude Sonnet có khả năng reasoning và conversational quality tốt hơn, phù hợp với các câu hỏi phức tạp về itinerary, 
luxury experience, hoặc multi-intent conversation.

- Web search ON broad giúp chatbot luôn có thông tin mới nhất về visa, thời tiết, sự kiện, tỷ giá và local updates. 
Full history giúp bot nhớ toàn bộ sở thích, budget và yêu cầu trước đó trong các cuộc trò chuyện dài 7–10 turns.

- Config này phù hợp khi công ty muốn tối đa customer satisfaction hơn là tối ưu cost.
```

### Rủi ro lớn nhất của config này

```text
Chi phí có thể tăng mạnh trong mùa cao điểm hoặc khi khách chat dài nhiều lượt vì Full History + Web Search broad làm token input phình lớn.
```

---

## Config 3

**Tên config**:

```text
Smart Nomad
```

### 3 Knobs

**① Model tier**:

```text
Response model: Gemini 2.5 Flash → giá $0.30 / $2.50 per 1M tokens
Classifier model: Gemini 2.5 Flash-Lite → giá $0.10 / $0.40 per 1M tokens
```

**② Web search**:

```text
□ OFF
☑ ON selective — bật cho intent: Visa/Policy, Weather/Event
□ ON broad
```

**③ History management**:

```text
□ Last 3
☑ Last 5
□ Full
□ Summarize every ___ turns
```

### Lý do nhóm chọn config này

```text
- Đây là config balanced mà nhóm đánh giá thực tế nhất cho production. Gemini Flash đủ mạnh để trả lời các câu Guide/Destination chiếm 45% intent của nhóm, 
nhưng vẫn rẻ hơn nhiều so với Sonnet hoặc GPT-5.5.

- Selective web search chỉ bật cho Visa và Weather giúp đảm bảo độ chính xác ở các intent cần real-time 
mà không lãng phí search fee cho các câu hỏi Guide thông thường.

- Last 5 turns giữ đủ context cho phần lớn conversations thực tế (~2.5 lượt/chủ đề) 
mà không làm token cost tăng quá nhanh ở các lượt cuối.
```

### Rủi ro lớn nhất của config này

```text
Conversation rất dài hoặc nhiều intent liên tục có thể khiến bot quên thông tin cũ hơn 5 turns, đặc biệt với khách planning itinerary phức tạp.
```

---

## Config 4 (optional — nếu thời gian dư)

Nhóm có thể thiết kế thêm config thứ 4 để có thêm điểm so sánh. Không bắt buộc.

**Tên config**:

```text
Deep Guide
```

### 3 Knobs

```text
Model: Gemini 2.5 Flash
Web: ON selective
History: Full
```

### Lý do

```text
- Config này dành cho khách research sâu trước khi booking, ví dụ hỏi itinerary nhiều thành phố hoặc planning nhóm lớn. 

- Full history giúp chatbot nhớ toàn bộ preference của khách nhưng vẫn giữ model cost ở mức thấp hơn Luxury Concierge.
```

---

## Bảng kiểm trước khi tính cost

- [ ] ≥3 configs đã đặt tên (không chỉ "Config 1/2/3")
- [ ] Mỗi config đã chốt rõ 3 knobs (không còn ô trống)
- [ ] Mỗi config có ≥2 câu lý do
- [ ] 3 configs đủ khác biệt — không phải chỉ đổi mỗi 1 knob nhỏ
- [ ] Nhóm đồng thuận đây là 3 configs đáng so sánh

**Nếu 3 configs quá giống nhau** (chỉ đổi model, knobs khác giống hệt) → quay lại tweak. Mục đích là thấy tradeoff — configs giống nhau quá → không thấy tradeoff.

Xong → mở `03-cost-calculation.md` để bắt đầu tính cost.
