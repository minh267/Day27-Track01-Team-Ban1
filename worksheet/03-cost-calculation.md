# 03 · Cost Calculation — Tính chi phí từng Config × 2 Scenarios

> **Mục tiêu**: Với mỗi config đã thiết kế ở `02-config-design.md`, tính cost/turn → cost/conversation → monthly cho cả 2 scenarios (low season + high season).
>
> **Thời gian**: 55 phút (phần lớn của Main phase) — checkpoint 11:00 và 11:20

---

## Cách làm

**Đừng tính tay từng turn — đó là cách thừa thời gian.** Dùng AI để tính. Dán prompt template từ `prompts/01-cost-calc.md` vào ChatGPT/Claude/Gemini, thay parameters theo config của nhóm, AI sẽ tính cho.

Tuy nhiên nhóm phải **hiểu** kết quả AI trả về, không phải copy-paste mù. Mỗi lần AI trả số, nhóm phải tự kiểm 1 lần: con số này có hợp lý không? Có vẻ quá đắt hay quá rẻ?

---

## Trước khi gọi AI — Setup chung

**Các tham số cố định cho tất cả configs** (tham khảo `cost-reference-card.md` mục 4):

```text
System prompt:              500 tokens
User message:                80 tokens
Assistant response:         180 tokens (output)
1 prior turn (history):     260 tokens (80 user + 180 assistant)
RAG top-5 chunks:         1,250 tokens (cố định)
Web search results:         800 tokens (khi bật)
Web search API call:       $0.005 / call (Tavily)
LLM classifier:            ~170 tokens (150 in + 20 out) — nếu dùng
```

**Scenario A — mùa thấp điểm**:

```text
Volume:            300 conversations / ngày
Turns/conv:        avg 4 lượt
Intent mix:        Guide 50%, Visa 25%, Weather 10%, Booking 10%, Complaint 5%
AI-served ratio:   85% (15% là Booking + Complaint = handoff)
```

**Scenario B — mùa cao điểm**:

```text
Volume:           1,200 conversations / ngày (×4)
Turns/conv:        avg 7 lượt
Intent mix:        Guide 30%, Visa 15%, Weather 10%, Booking 35%, Complaint 10%
AI-served ratio:   55% (45% là handoff)
```

**Human baseline để so sánh**: $0.50 / conversation cố định.

---

## Quy trình tính cho 1 config (lặp lại cho từng config)

### Bước 1 — Cost per turn (4 mốc: Turn 1, 3, 5, 7)

Với mỗi mốc, tính:

1. **History tokens** = (T − 1) × 260 nếu Full · min(T−1, N) × 260 nếu Last N · ~150 cố định nếu Summarize.
2. **Input total** = 500 (sys) + history + 1,250 (RAG) + (800 nếu web ON cho intent này) + 80 (msg)
3. **Output** = 180 tokens
4. **Cost model** = (input × $/M_input + output × $/M_output) / 1,000,000
5. **Cost web API** = $0.005 nếu web ON cho intent này, ngược lại $0
6. **Cost classifier** = ~$0.000035 nếu dùng LLM classifier, ngược lại $0
7. **Total cost/turn** = cost model + cost web + cost classifier

→ Dùng prompt template, AI sẽ tự tính. Nhập config + intent + turn number, AI ra bảng kết quả.

### Bước 2 — Cost per conversation cho từng intent

Cost 1 conversation = sum(cost từng turn) trong conversation đó.

- Scenario A: cộng cost của Turn 1 → Turn 4 (4 turns)
- Scenario B: cộng cost của Turn 1 → Turn 7 (7 turns)

Tính riêng cho mỗi intent (vì web search có thể khác — Visa/Weather bật web, Guide không bật):

```text
cost_conv_guide   (4 turns) = ____
cost_conv_visa    (4 turns) = ____  (web search bật cho Visa)
cost_conv_weather (4 turns) = ____  (web search bật cho Weather)
cost_1_turn_only             = ____  (Booking + Complaint chỉ 1 turn rồi handoff,
                                       chỉ tốn classifier nếu dùng LLM)
```

### Bước 3 — Weighted average cost per conversation (toàn bộ intent)

Lấy % intent mix × cost từng intent:

**Scenario A** (Guide 50%, Visa 25%, Weather 10%, Booking 10%, Complaint 5%):

```text
avg_cost_A = 50% × cost_conv_guide_4t
          + 25% × cost_conv_visa_4t
          + 10% × cost_conv_weather_4t
          + 10% × cost_1_turn_only
          +  5% × cost_1_turn_only
```

**Scenario B** (Guide 30%, Visa 15%, Weather 10%, Booking 35%, Complaint 10%, 7 turns cho AI-served):

```text
avg_cost_B = 30% × cost_conv_guide_7t
          + 15% × cost_conv_visa_7t
          + 10% × cost_conv_weather_7t
          + 35% × cost_1_turn_only
          + 10% × cost_1_turn_only
```

### Bước 4 — Monthly cost

```text
monthly_A = avg_cost_A × 300 conv/ngày × 30 ngày
monthly_B = avg_cost_B × 1,200 conv/ngày × 30 ngày
```

### Bước 5 — So sánh với human baseline

```text
human_A = $0.50 × 300 × 30 = $4,500 / tháng
human_B = $0.50 × 1,200 × 30 = $18,000 / tháng

savings_A% = (4,500 − monthly_A) / 4,500 × 100
savings_B% = (18,000 − monthly_B) / 18,000 × 100
```

Nếu savings ÂM → AI đắt hơn human → cần justify (24/7? đa ngôn ngữ? scale?).

---

## Điền số cho từng config

Dùng AI tính xong, copy số vào đây. Đừng quên kiểm 1 lần xem số có hợp lý không.

**Giả định tính toán dùng cho phần này**

```text
- Web search API dùng theo worksheet: $0.005 / call.
- LLM classifier dùng Gemini 2.5 Flash-Lite: 150 input + 20 output
  → cost/classification = (150 × $0.10 + 20 × $0.40) / 1,000,000 = $0.000023.
- Booking và Complaint chỉ route/handoff sang người thật, không gọi response model.
- Với web ON selective: chỉ Visa/Policy và Weather/Event dùng web search.
- Với web ON broad: Guide, Visa và Weather đều dùng web search ở mỗi turn.
```

### Config 1 — Economy Explorer

| Item | Scenario A (4 turns) | Scenario B (7 turns) |
|---|---|---|
| Cost / conversation (avg) | $0.001000 | $0.001196 |
| Monthly cost | $9.00 | $43.07 |
| Human baseline | $4,500 | $18,000 |
| **Rẻ hơn human ___×** | 500.2× | 418.0× |
| **Savings %** | 99.80% | 99.76% |

**Sanity check** (trả lời cho nhóm trước khi đi tiếp):

- Cost/conv có nằm trong $0.005–$0.10 không? Nếu quá thấp → có thể quên component (RAG? web? classifier?). Nếu quá cao → có thể tính sai history.
- Monthly có hợp lý không? (cheap config thường $100–$300, premium config có thể đến $3,000+)

```text
Config này rất rẻ vì dùng Gemini Flash-Lite, keyword routing nên classifier = $0,
web search OFF và chỉ giữ Last 3 turns. Cost/conv thấp hơn khoảng sanity range
$0.005-$0.10, nhưng hợp lý vì không có web API và model rất rẻ; rủi ro là chất
lượng/độ mới thông tin thấp hơn, nhất là Visa và Weather.
```

---

### Config 2 — Luxury Concierge

| Item | Scenario A | Scenario B |
|---|---|---|
| Cost / conversation (avg) | $0.057066 | $0.069129 |
| Monthly cost | $513.59 | $2,488.66 |
| **Rẻ hơn human ___×** | 8.8× | 7.2× |
| **Savings %** | 88.59% | 86.17% |

**Sanity check**:

```text
Số này hợp lý cho premium config: Sonnet 4.6 đắt hơn nhiều, web search broad
tốn $0.005 mỗi turn cho Guide/Visa/Weather, và Full History làm input token tăng
đều theo độ dài cuộc hội thoại. Scenario B chỉ đắt hơn A khoảng 4.85× theo monthly,
không phải 7×, vì Scenario B có 45% Booking/Complaint được handoff gần như không
tốn LLM response cost.
```

---

### Config 3 — Smart Nomad

| Item | Scenario A | Scenario B |
|---|---|---|
| Cost / conversation (avg) | $0.011212 | $0.013973 |
| Monthly cost | $100.91 | $503.03 |
| **Rẻ hơn human ___×** | 44.6× | 35.8× |
| **Savings %** | 97.76% | 97.21% |

**Sanity check**:

```text
Đây là mức cost hợp lý cho balanced config: Gemini Flash đắt hơn Flash-Lite nhưng
vẫn rẻ hơn Sonnet rất nhiều. Web search chỉ bật cho Visa/Weather nên cost không
bị đội lên ở Guide, trong khi Last 5 giữ đủ context cho phần lớn conversation.
Monthly B cao hơn A khoảng 5× vì volume tăng 4× và conversation dài hơn, nhưng
intent mix B có nhiều handoff nên không tăng tới 7×.
```

---

### Config 4 (optional)

| Item | Scenario A | Scenario B |
|---|---|---|
| Cost / conversation (avg) | Không tính | Không tính |
| Monthly cost | Không tính | Không tính |
| **Rẻ hơn human ___×** | Không tính | Không tính |
| **Savings %** | Không tính | Không tính |

```text
Nhóm chỉ dùng 3 config chính để so sánh: Economy Explorer, Luxury Concierge,
Smart Nomad. Deep Guide là optional nên không đưa vào bảng cost chính.
```

---

## Quality + Speed estimate (qualitative)

Mỗi config — estimate Low / Medium / High. Không có công cụ đo chính xác trong lab, ước tính dựa trên model tier + web search + history.

| Config | Quality (Low/Med/High) | Speed (Low/Med/High) | Lý do |
|---|---|---|---|
| 1: Economy Explorer | Low-Medium | High | Rẻ và nhanh nhất vì dùng Flash-Lite, không web search, Last 3; phù hợp FAQ/Guide đơn giản nhưng dễ outdated ở Visa/Weather. |
| 2: Luxury Concierge | High | Low-Medium | Chất lượng tốt nhất nhờ Sonnet, web broad và Full History; tốc độ chậm hơn vì model mạnh + web search mỗi turn. |
| 3: Smart Nomad | Medium-High | Medium-High | Cân bằng nhất: Gemini Flash đủ tốt, web selective cho intent cần real-time, Last 5 giữ context vừa đủ. |
| 4: Deep Guide | Không đánh giá | Không đánh giá | Optional, không đưa vào phạm vi tính cost chính của nhóm. |

**Hướng dẫn ước tính**:

- **Quality**: Cheap model → Low (70%). Strong model → High (88%). Web search bật → Quality tăng vì info real-time. History Full → Quality tốt hơn ở conversation dài.
- **Speed**: Cheap model thường nhanh (~200ms). Strong model chậm hơn (~1–3s). Web search bật → +1–2s.

---

## Bảng kiểm trước khi sang file tiếp theo

- [x] Tất cả ≥3 configs đã có cost/conv + monthly cho cả 2 scenarios
- [x] Đã so sánh từng config với human baseline ($0.50/conv)
- [x] Có quality + speed estimate cho mỗi config
- [x] Đã sanity check — không có số "quá lạ" (cost <$0.001 hoặc >$1/conv)

⚑ **Checkpoint 11:00**: ≥1 config đã tính cost xong &nbsp; · &nbsp; ⚑ **Checkpoint 11:20**: tất cả configs đã tính cost xong cho cả 2 scenarios.

Xong → mở `04-comparison-table.md`.
