# 04 · Comparison Table — Bảng so sánh đầy đủ

> **Mục tiêu**: Tổng hợp tất cả số đã tính ở `03-cost-calculation.md` thành 1 bảng so sánh duy nhất — đây là artifact chính nhóm sẽ present.
>
> **Thời gian**: 10 phút (đầu phần Final)

---

## Vì sao có bảng so sánh?

Khi sếp hỏi "Nên deploy config nào?", bạn cần đặt lên bàn **1 bảng** thay vì đọc 3 báo cáo riêng. Bảng so sánh đầy đủ cho phép so sánh thẳng từng dòng, dễ nhìn ra tradeoff.

---

## Bảng chính

Điền số đã tính. Số nào chưa có → quay lại `03-cost-calculation.md` tính cho xong.

| | Config 1 | Config 2 | Config 3 | (Config 4) |
|---|---|---|---|---|
| **Tên** | Economy Explorer | Luxury Concierge | Smart Nomad | Deep Guide |
| **① Model** | Gemini 2.5 Flash-Lite ($0.10/$0.40 per 1M input/output) | Claude Sonnet 4.6 ($3/$15 per 1M) | Gemini 2.5 Flash ($0.30/$2.50 per 1M) | Optional: Gemini 2.5 Flash |
| **② Web search** | OFF | ON broad cho Guide/Visa/Weather | ON selective cho Visa/Weather | Optional: ON selective |
| **③ History** | Last 3 turns | Full history | Last 5 turns | Optional: Full history |
| **Intent classifier** | Keyword routing ($0) | LLM: Gemini Flash-Lite (~$0.000023/turn) | LLM: Gemini Flash-Lite (~$0.000023/turn) | Không tính |
| **Cost / conv (Scenario A — 4 turns)** | $0.001000 | $0.057066 | $0.011212 | Không tính |
| **Cost / conv (Scenario B — 7 turns)** | $0.001196 | $0.069129 | $0.013973 | Không tính |
| **Monthly A** (300 conv/day × 30) | $9.00 | $513.59 | $100.91 | Không tính |
| **Monthly B** (1,200 conv/day × 30) | $43.07 | $2,488.66 | $503.03 | Không tính |
| **vs human $4,500/mo (A)** | rẻ 500.2× | rẻ 8.8× | rẻ 44.6× | Không tính |
| **vs human $18,000/mo (B)** | rẻ 418.0× | rẻ 7.2× | rẻ 35.8× | Không tính |
| **Savings % (A)** | 99.80% | 88.59% | 97.76% | Không tính |
| **Savings % (B)** | 99.76% | 86.17% | 97.21% | Không tính |
| **Quality estimate** | Low-Medium | High | Medium-High | Không đánh giá |
| **Speed estimate** | High | Low-Medium | Medium-High | Không đánh giá |
| **Điểm yếu chính** | Dễ outdated ở Visa/Weather vì không dùng web search, context ngắn. | Cost cao nhất, tốc độ chậm hơn do Sonnet + web broad + full history. | Có thể quên thông tin cũ hơn 5 turns trong conversation rất dài. | Optional, chưa đưa vào cost chính nên chưa đủ dữ liệu so sánh. |
| **Best for** (khi nào nên dùng) | Low season, FAQ/Guide đơn giản, website mới launch hoặc cần tối ưu cost tối đa. | Khách VIP/luxury, conversation dài, yêu cầu chất lượng và thông tin real-time cao. | Production default: cân bằng giữa cost, quality, speed và độ chính xác cho Visa/Weather. | Có thể cân nhắc sau nếu muốn test một bản sâu hơn Smart Nomad. |

---

## Quan sát nhanh từ bảng

Trước khi sang file recommendation, trả lời 4 câu — đây là material để present:

### Câu 1 — Config rẻ nhất là gì? Đắt nhất là gì?

```text
Rẻ nhất: Economy Explorer — monthly B = $43.07
Đắt nhất: Luxury Concierge — monthly B = $2,488.66
Chênh: khoảng 57.8× lần
```

### Câu 2 — Knob nào ảnh hưởng cost nhiều nhất?

So sánh các config khác nhau ở knob nào, chênh bao nhiêu. Thường: model tier > history > web search.

```text
Knob ảnh hưởng cost nhiều nhất là model tier, sau đó đến web search, rồi mới đến
history. Khi đi từ Economy Explorer sang Luxury Concierge, monthly B tăng từ
$43.07 lên $2,488.66, tức khoảng 57.8×; phần lớn đến từ Sonnet 4.6 đắt hơn
Flash-Lite rất nhiều. Web search cũng rất đáng kể: ở Smart Nomad, Visa/Weather
7 turns tốn $0.045394/conv, trong khi Guide không web chỉ $0.008714/conv;
chênh khoảng $0.03668/conv, gần bằng 7 lần phí web search $0.005/call.
```

### Câu 3 — Tại sao Scenario B không đắt ×4 lần Scenario A?

Volume Scenario B = ×4 lần Scenario A. Turns dài hơn (7 vs 4 = ×1.75). Mong đợi monthly B ≈ A × 7. Thực tế có thể thấp hơn vì sao?

Trước khi viết, nghĩ: intent mix Scenario B có gì khác? Booking + Complaint = $0 LLM ở scenario B là bao nhiêu %?

```text
Scenario B có volume gấp 4 lần và số turn dài hơn, nhưng intent mix có 35% Booking
+ 10% Complaint = 45% conversation được handoff, gần như không tốn response model.
Vì vậy monthly B chỉ tăng khoảng 4.8-5.0× so với A, thấp hơn mức kỳ vọng gần 7×.
```

### Câu 4 — Có config nào AI đắt hơn human không?

So sánh monthly từng config với human baseline ($4,500 cho A, $18,000 cho B). Nếu AI rẻ hơn → savings %. Nếu đắt hơn → cần justify.

```text
Không có config nào đắt hơn human baseline. Ngay cả Luxury Concierge, config đắt
nhất, vẫn chỉ tốn $513.59/tháng ở Scenario A so với human $4,500 và $2,488.66/tháng
ở Scenario B so với human $18,000. Tuy nhiên Economy Explorer tiết kiệm nhiều nhất
nhưng rủi ro quality cao hơn, nên không nên chỉ nhìn savings mà bỏ qua độ chính xác
cho Visa/Weather và trải nghiệm khách VIP.
```

---

## Bảng kiểm trước khi sang file tiếp theo

- [x] Bảng đầy đủ — không còn ô trống
- [x] Đã có 4 câu trả lời cho 4 quan sát ở trên
- [x] Nhóm đồng thuận về số trong bảng (đã sanity check)

Xong → mở `05-recommendation.md` để viết recommendation cuối + chuẩn bị present.
