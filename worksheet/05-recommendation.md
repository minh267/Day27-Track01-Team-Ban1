# 05 - Recommendation + Justification

> **Mục tiêu**: Chọn config nên deploy, justify bằng số liệu, và chuẩn bị present 5 phút.

---

## 4 câu hỏi PM cần trả lời

### Câu 1 - Recommend config nào?

```text
Nhóm recommend Smart Nomad làm config mặc định cho production trong cả Scenario A và Scenario B. Lý do là config này giữ được quality Medium-High, speed Medium-High, nhưng monthly cost vẫn rất thấp: $100.91/tháng ở Scenario A và $503.03/tháng ở Scenario B. Economy Explorer rẻ hơn nhiều nhưng web search OFF làm rủi ro Visa/Weather outdated, còn Luxury Concierge có chất lượng cao nhất nhưng monthly B lên $2,488.66, không cần thiết cho phần lớn traffic.
```

### Câu 2 - So với human baseline $0.50/conv, tiết kiệm bao nhiêu?

```text
Smart Nomad tiết kiệm 97.76% ở Scenario A và 97.21% ở Scenario B so với human baseline. Cụ thể, Scenario A tốn $100.91/tháng so với human $4,500/tháng, tiết kiệm khoảng $4,399.09/tháng. Scenario B tốn $503.03/tháng so với human $18,000/tháng, tiết kiệm khoảng $17,496.97/tháng. Không có config nào đắt hơn human baseline, nhưng AI không thay thế hoàn toàn sales/manager vì Booking và Complaint vẫn cần handoff sang người thật.
```

### Câu 3 - Khi nào nên upgrade / downgrade config?

```text
Nên upgrade từ Smart Nomad lên Luxury Concierge khi nhóm thấy tỉ lệ khách VIP/luxury tăng cao, conversation dài hơn 7 turns thường xuyên, hoặc complaint về chất lượng câu trả lời vượt 5%. Nên upgrade tạm thời trong peak season nếu booking value cao đủ để justify trải nghiệm concierge tốt hơn. Nên downgrade về Economy Explorer khi low season, monthly conversation thấp, câu hỏi chủ yếu là FAQ/Guide đơn giản, và team có thể chấp nhận rủi ro cập nhật Visa/Weather chậm hơn.
```

### Câu 4 - Rủi ro lớn nhất của config được chọn?

```text
Rủi ro lớn nhất của Smart Nomad là Last 5 turns có thể làm bot quên thông tin cũ trong các conversation rất dài hoặc multi-intent phức tạp. Rủi ro thứ hai là web search chỉ bật cho Visa/Weather, nên một số câu Guide cần local update mới nhất có thể chưa đủ chính xác. Mitigation: thêm rule bật web search nếu user hỏi về "current", "this week", "festival", "price now"; fallback sang human nếu confidence thấp; và review log hằng tuần để xem có cần nâng lên Full History cho nhóm khách planning itinerary dài hay không.
```

---

## Final answer - Recommendation in 1 paragraph

```text
Nhóm recommend Smart Nomad làm config mặc định để deploy chatbot du lịch. Config này dùng Gemini 2.5 Flash cho response, Gemini Flash-Lite cho classifier, bật web search selective cho Visa/Weather, và giữ Last 5 turns để cân bằng cost với context. Về chi phí, Smart Nomad chỉ tốn $100.91/tháng ở Scenario A và $503.03/tháng ở Scenario B, tiết kiệm 97.76% và 97.21% so với human baseline. Economy Explorer rẻ hơn nhưng quá rủi ro cho Visa/Weather vì không có real-time data, còn Luxury Concierge có quality cao nhất nhưng cost cao hơn Smart Nomad khoảng 4.9 lần ở Scenario B. Nhóm sẽ upgrade lên Luxury Concierge khi gặp nhiều khách VIP, conversation dài, hoặc complaint về quality vượt 5%. Rủi ro chính của Smart Nomad là có thể quên context cũ hơn 5 turns, nên nhóm sẽ monitor chat log, fallback sang human khi confidence thấp, và bật web search thêm cho các câu hỏi có tính real-time.
```

---

## Chuẩn bị Present 5 phút

### Nhịp 0:00-0:30 - Base flow + 3 knobs đã chọn

Ai trình bày: Chu Minh Quân

```text
Chatbot bắt đầu bằng intent classification, sau đó route sang RAG/Web Search cho Guide, Visa, Weather; Booking và Complaint được handoff sang người thật. Ba knobs nhóm test là model tier, web search strategy, và history management.
```

### Nhịp 0:30-1:00 - Config overview

Ai trình bày: Hoàng Đức Nghĩa

```text
Economy Explorer: Flash-Lite, web OFF, Last 3 - rẻ nhất nhưng rủi ro outdated.
Luxury Concierge: Sonnet 4.6, web broad, Full history - chất lượng cao nhất nhưng đắt nhất.
Smart Nomad: Gemini Flash, web selective cho Visa/Weather, Last 5 - cân bằng cost, quality, speed.
```

### Nhịp 1:00-2:00 - Cost comparison

Ai trình bày: Lê Đức Thanh

```text
Economy Explorer rẻ nhất với monthly B = $43.07, nhưng quality chỉ Low-Medium. Luxury Concierge đắt nhất với monthly B = $2,488.66, nhưng vẫn rẻ hơn human 7.2 lần. Smart Nomad nằm giữa với monthly B = $503.03, rẻ hơn human 35.8 lần và vẫn giữ quality Medium-High.
```

### Nhịp 2:00-3:00 - Key insight

Ai trình bày: Phạm Việt Anh

```text
Knob ảnh hưởng cost lớn nhất là model tier, vì chuyển từ Flash-Lite sang Sonnet làm monthly B tăng từ $43.07 lên $2,488.66, khoảng 57.8 lần. Web search cũng đáng kể, vì mỗi web call thêm $0.005; do đó bật selective cho Visa/Weather tốt hơn bật broad cho mọi intent.
```

### Nhịp 3:00-4:30 - Recommendation + justification

Ai trình bày: Đặng Quang Minh

```text
Nhóm recommend Smart Nomad làm config mặc định để deploy chatbot du lịch. Config này dùng Gemini 2.5 Flash cho response, Gemini Flash-Lite cho classifier, bật web search selective cho Visa/Weather, và giữ Last 5 turns để cân bằng cost với context. Về chi phí, Smart Nomad chỉ tốn $100.91/tháng ở Scenario A và $503.03/tháng ở Scenario B, tiết kiệm 97.76% và 97.21% so với human baseline. Economy Explorer rẻ hơn nhưng quá rủi ro cho Visa/Weather vì không có real-time data, còn Luxury Concierge có quality cao nhất nhưng cost cao hơn Smart Nomad khoảng 4.9 lần ở Scenario B. Nhóm sẽ upgrade lên Luxury Concierge khi gặp nhiều khách VIP, conversation dài, hoặc complaint về quality vượt 5%. Rủi ro chính của Smart Nomad là có thể quên context cũ hơn 5 turns, nên nhóm sẽ monitor chat log, fallback sang human khi confidence thấp, và bật web search thêm cho các câu hỏi có tính real-time.
```

### Nhịp 4:30-5:00 - Hardest question prep

Ai trình bày: Đặng Quang Minh

```text
Câu hỏi khó nhất: Nếu Economy Explorer rẻ hơn Smart Nomad hơn 10 lần, tại sao không chọn Economy Explorer cho high season?
```

```text
Vì high season có nhiều khách và rủi ro sai thông tin cao hơn, đặc biệt với Visa/Weather. Economy Explorer chỉ tốn $43.07/tháng ở Scenario B, nhưng web OFF có thể làm bot trả lời outdated; Smart Nomad vẫn chỉ tốn $503.03/tháng, tiết kiệm 97.21% so với human, nên phần cost tăng thêm là hợp lý để mua độ chính xác và trải nghiệm tốt hơn.
```

---

## Q&A - 2 phút sau khi present

```text
1. Knob ảnh hưởng cost nhiều nhất là model tier. Bảng so sánh cho thấy monthly B tăng từ $43.07 ở Economy Explorer lên $2,488.66 ở Luxury Concierge, chủ yếu do Sonnet 4.6 đắt hơn Flash-Lite rất nhiều.

2. Nếu provider tăng giá API x2, Smart Nomad vẫn sống được vì monthly B ước tính tăng từ $503.03 lên khoảng $1,006.06, vẫn thấp hơn human baseline $18,000/tháng rất nhiều. Tuy nhiên nhóm sẽ monitor cost và có fallback về Economy Explorer cho traffic FAQ đơn giản.

3. Nhóm chọn Smart Nomad thay vì Economy Explorer vì bài toán không chỉ là rẻ nhất, mà là rẻ nhưng vẫn đủ chính xác cho Visa/Weather. Nếu nhóm khác chọn Premium, nhóm mình sẽ argue rằng Luxury Concierge chỉ nên dùng cho VIP segment, không cần làm default cho toàn bộ traffic.
```

---

## Bảng kiểm cuối cùng - trước 12:00 Pens Down

- [x] Đã trả lời 4 câu PM (Recommend / Savings / Threshold / Risk)
- [x] Final answer paragraph viết gọn (5-7 câu)
- [x] Phân công 5 nhịp present cho mỗi thành viên
- [x] Có sẵn câu trả lời cho 3 câu Q&A dự đoán
- [x] Comparison table có sẵn để chiếu / chuyền tay khi present
- [ ] Repo đã commit + push (sẽ nộp link sau buổi học)

---

## Sau buổi học

1. Commit + push repo với tất cả file đã điền.
2. Dán link repo vào Discord `#day27-evidence-boards` trước 23:59.
3. Chuẩn bị cho D28: peer review giữa các nhóm và polish thêm bảng + recommendation nếu cần.
