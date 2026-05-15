
# Day26-Track01-DangQuangMinh-2A202600022

| Thành viên | MSSV | GitHub username | Phần phụ trách |
|---|---:|---|---|
| Phạm Việt Anh | 2A202600273 | `saig0nvang` | `worksheet/00-user-journey.md` - user journey, câu hỏi tourist, intent mix và insight |
| Chu Minh Quân |  | `quanchu` | `worksheet/01-base-flow.md` - base flow, routing và 3 knobs |
| Hoàng Đức Nghĩa | 2A202600371 | `HoangDucNghia` | `worksheet/02-config-design.md` - thiết kế configs, knobs, lý do chọn và rủi ro |
| Lê Đức Thanh | 2A202600093 | `thanhld2026aik1` | `worksheet/03-cost-calculation.md` và `worksheet/04-comparison-table.md` - tính cost và bảng so sánh |
| Đặng Quang Minh | 2A202600022 | `minh267` | `worksheet/05-recommendation.md` - recommendation, justification, present và Q&A |

---


# Ngày 27 — AI Conversation Cost Simulator

## Mô tả repo

Repo này là bài lab Day 27 của nhóm trong chương trình **VinUni A20 — AI Thực Chiến**. Nội dung chính là mô phỏng bài toán kinh tế khi triển khai chatbot AI cho công ty du lịch: nhóm đóng vai khách du lịch, thiết kế nhiều cấu hình chatbot, tính chi phí theo từng scenario, so sánh với human baseline và đưa ra recommendation cuối cùng.

Các artifact chính của repo gồm:

- `worksheet/00-user-journey.md`: mô phỏng user journey và phân loại intent.
- `worksheet/01-base-flow.md`: mô tả base flow chatbot và 3 knobs cần tối ưu.
- `worksheet/02-config-design.md`: thiết kế các configurations để so sánh.
- `worksheet/03-cost-calculation.md`: tính chi phí cho từng config ở Scenario A và Scenario B.
- `worksheet/04-comparison-table.md`: tổng hợp bảng so sánh cost/quality/speed.
- `worksheet/05-recommendation.md`: recommendation cuối, justification và phần chuẩn bị present.
- `todo.md`: checklist công việc và phân công trách nhiệm.

## Phân công nhóm

| Thành viên | MSSV | GitHub username | Phần phụ trách |
|---|---:|---|---|
| Phạm Việt Anh | 2A202600273 | `saig0nvang` | `worksheet/00-user-journey.md` - user journey, câu hỏi tourist, intent mix và insight |
| Chu Minh Quân |  | `quanchu` | `worksheet/01-base-flow.md` - base flow, routing và 3 knobs |
| Hoàng Đức Nghĩa | 2A202600371 | `HoangDucNghia` | `worksheet/02-config-design.md` - thiết kế configs, knobs, lý do chọn và rủi ro |
| Lê Đức Thanh | 2A202600093 | `thanhld2026aik1` | `worksheet/03-cost-calculation.md` và `worksheet/04-comparison-table.md` - tính cost và bảng so sánh |
| Đặng Quang Minh | 2A202600022 | `minh267` | `worksheet/05-recommendation.md` - recommendation, justification, present và Q&A |

---

> **Lab travel agency chatbot — bài toán kinh tế của AI Product**
>
> *"Với thiết kế này, chi phí là bao nhiêu? Có hợp lý để triển khai không?"*

Đây là repo lab cho Ngày 27 của chương trình **VinUni A20 — AI Thực Chiến**. Mở file này ra là biết phải làm gì.

---

## Bạn sẽ làm gì hôm nay?

Bạn là PM ở một công ty du lịch Việt Nam. Sếp muốn triển khai chatbot AI tiếng Anh trên website để phục vụ khách quốc tế. Câu hỏi sếp đưa ra: **"Với phương án thiết kế nào thì mô hình kinh tế bền vững?"**

Trong 110 phút lab, nhóm 3 người sẽ:

1. **Đóng vai khách hàng** để hiểu chatbot phải phục vụ gì (15 phút)
2. **Thiết kế ≥3 configurations khác nhau** cho chatbot (75 phút)
3. **Tính chi phí từng config** cho 2 kịch bản: mùa thấp điểm vs cao điểm
4. **So sánh, recommend, bảo vệ lựa chọn** (20 phút final + present)

Kết quả nhóm nộp: **1 bảng so sánh đầy đủ + 1 recommendation + 1 justification**.

---

## Thứ tự làm việc

Mở các file theo đúng thứ tự đánh số. Mỗi file viết rõ "Mục tiêu" ở đầu — đọc 1 dòng là biết phần đó làm gì.

### Phần 1 — Setup (10:10 → 10:25, 15 phút)

| File | Bạn cần làm gì |
|---|---|
| `worksheet/00-user-journey.md` | Mỗi người đóng vai 1 tourist — viết 5–7 câu hỏi tiếng Anh thật mình sẽ gửi cho chatbot. Gom nhóm, phân loại intent. |
| `worksheet/01-base-flow.md` | Vẽ flow chatbot (5 intents → routing). Chốt 3 knobs nhóm sẽ tweak. |

### Phần 2 — Main (10:25 → 11:40, 75 phút)

| File | Bạn cần làm gì |
|---|---|
| `worksheet/02-config-design.md` | Đặt tên + chọn 3 knobs cho ≥3 configurations. Gợi ý: 1 Budget, 1 Premium, 1 Smart Mix. |
| `worksheet/03-cost-calculation.md` | Với mỗi config, tính cost/turn → cost/conversation → monthly cho cả 2 scenarios. Dùng AI hỗ trợ (xem `prompts/01-cost-calc.md`). |
| `cost-reference-card.md` | **Tham khảo liên tục** — giá model, token estimates, công thức. |
| `prompts/01-cost-calc.md` | Prompt template để dán vào ChatGPT/Claude/Gemini tính cost giúp nhóm. |

**Checkpoints**:
- ⚑ **11:00** — Đã có ≥3 configs định nghĩa + ≥1 config tính cost xong.
- ⚑ **11:20** — Tất cả configs đã tính cost cho cả 2 scenarios.

### Phần 3 — Final (11:40 → 12:00, 20 phút)

| File | Bạn cần làm gì |
|---|---|
| `worksheet/04-comparison-table.md` | Điền bảng so sánh đầy đủ: 3 configs × 2 scenarios × cost/quality/speed. |
| `worksheet/05-recommendation.md` | Viết 3–5 câu recommendation + justification. Trả lời 4 câu hỏi PM. |
| `prompts/02-config-design-helper.md` | Nếu nhóm bí ý tưởng đặt tên / pick knobs → dùng prompt này. |
| `prompts/03-quality-eval.md` | Nếu có thời gian dư: thử 1 vài test message để cảm nhận quality. |

**Checkpoint cuối**:
- ⚑ **11:50** — Bảng so sánh + recommendation đã xong, sẵn sàng present.
- ⚑ **12:00** — Pens down. Bắt đầu present (5 phút mỗi nhóm + 2 phút Q&A).

---

## Tài liệu tham khảo trong repo

| File | Vai trò |
|---|---|
| `README.md` | File này — bản đồ tổng thể |
| `cost-reference-card.md` | **Reference chính** — flow, pricing 9 models, token estimates, ví dụ tính cost |
| `glossary.md` | Giải thích các thuật ngữ tiếng Anh xuất hiện trong lab |
| `worksheet/` | 6 file lab theo thứ tự — mỗi file là 1 bước |
| `prompts/` | 3 prompt template để dùng AI hỗ trợ |

Không cần đọc tất cả trước khi vào lab. Mở từng file theo thứ tự, mỗi file tự dẫn dắt.

---

## Một số nguyên tắc

- **Không có "đáp án đúng" cho config nào nên chọn**. Mỗi nhóm tự quyết, miễn justify được bằng số.
- **Booking + Khiếu nại = $0 LLM cost** (chuyển con người). Đừng quên trừ phần này ra.
- **Chi phí AI là biến đổi** — mỗi turn, mỗi conversation tốn tiền. Cost tăng theo volume, theo turns, theo intent mix.
- **Dùng AI để tính** — đây là lab AI Product, không phải lab Excel. Dán prompt template vào ChatGPT/Claude, AI sẽ tính từng bước.
- **So với baseline human $0.50/conv** — nếu AI đắt hơn human, justify được không? (24/7? đa ngôn ngữ? scale?)

---

## Nộp bài (cuối ngày)

Repo này là của riêng nhóm. Sau buổi học:

1. Commit tất cả file (worksheet đã điền + comparison table + recommendation) vào repo.
2. Push lên GitHub.
3. Dán link repo vào Discord `#day27-evidence-boards` trước 23:59.

Ngày mai (D28) cả nhóm sẽ trình bày lại bài này trước nhóm khác để peer review — chuẩn bị sẵn câu trả lời cho các câu chất vấn khó nhất.

---

*Lab D27 · AI Conversation Cost Simulator · VinUni A20 AI Thực Chiến*
