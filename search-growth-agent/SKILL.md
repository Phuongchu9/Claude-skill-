---
name: search-growth-agent
description: >
  Agent mẹ chuyên gia tăng trưởng organic traffic. Điều phối 2 sub-agent:
  GSC Analyst (phân tích hiệu suất qua Google Search Console) và Content Writer
  (viết/tối ưu content dựa trên data). Trả về báo cáo tổng thể gồm: hiệu suất
  hiện tại, danh sách trang cần cải thiện, content/title/meta đã tối ưu, và
  action items cụ thể. Dùng khi user nói "tăng traffic", "cải thiện SEO",
  "phân tích website", "tối ưu content", "search growth", "báo cáo SEO tổng thể",
  hoặc cung cấp domain/URL cần tăng trưởng.
user-invokable: true
argument-hint: "<domain-or-url> [focus: traffic|ctr|content|full]"
license: MIT
metadata:
  author: custom
  version: "1.0.0"
  category: seo
---

# Search Growth Agent — Tăng Trưởng Organic Traffic

Agent mẹ điều phối toàn bộ quy trình từ phân tích data đến tối ưu content.

```
User Input
    │
    ▼
Search Growth Agent (orchestrator)
    ├── Sub-agent 1: GSC Analyst
    │       ├── gsc-performance  → traffic, drops, quick wins
    │       └── gsc-ctr          → CTR gap, trang bỏ lỡ clicks
    │
    └── Sub-agent 2: Content Writer
            ├── seo-content-brief → brief cho trang cần rewrite
            ├── copywriting       → title, meta, intro mới
            └── copy-editing      → polish content cuối
    │
    ▼
Báo Cáo Tổng Hợp + Action Items
```

---

## Cách Dùng

```
/search-growth-agent example.com
/search-growth-agent https://example.com/blog/post full
/search-growth-agent sc-domain:example.com traffic
/search-growth-agent example.com ctr
```

**Focus modes:**
- `traffic` — chỉ phân tích traffic drops + quick wins
- `ctr` — chỉ phân tích CTR gap
- `content` — bỏ qua GSC, tập trung tối ưu content từ URL cụ thể
- `full` (mặc định) — chạy toàn bộ pipeline

---

## Bước 0: Tiếp Nhận Input & Chuẩn Bị

**Parse input của user:**
- Nếu là domain (`example.com`) → tìm property trong GSC bằng `list_sites`
- Nếu là URL cụ thể (`example.com/page`) → phân tích trang đó + property cha
- Nếu là `sc-domain:example.com` → dùng trực tiếp làm property

**Kiểm tra MCP:**
- Gọi `list_sites` để xác nhận kết nối GSC MCP
- Nếu lỗi → hướng dẫn user: `claude mcp list` rồi kết nối lại

**Thông báo bắt đầu:**
```
🚀 Search Growth Agent đang phân tích [domain]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Phase 1/3: GSC Analyst đang thu thập data...
```

---

## Phase 1 — Spawn Sub-agent: GSC Analyst

Dùng **Agent tool** để spawn `gsc-analyst`:

```
Agent(
  subagent_type: "gsc-analyst",
  description: "Phân tích GSC cho [property]",
  prompt: "Phân tích property [property] theo đúng quy trình trong file agent của mày.
  Chạy đủ 4 bước: Traffic Overview, Traffic Drops, Quick Wins, CTR Gap.
  Trả về [GSC_PRIORITY_LIST] đầy đủ kèm score và lý do cho từng URL."
)
```

Chờ GSC Analyst hoàn thành và nhận `[GSC_PRIORITY_LIST]`.

**Thông báo chuyển phase:**
```
✅ Phase 1/3 hoàn thành: GSC Analyst tìm thấy X trang cần xử lý
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Phase 2/3: Content Writer đang tối ưu content...
```

---

## Phase 2 — Spawn Sub-agent: Content Writer

Dùng **Agent tool** để spawn `content-writer`:

```
Agent(
  subagent_type: "content-writer",
  description: "Tối ưu content cho [property]",
  prompt: "Tối ưu content cho các trang sau dựa trên GSC data.
  Xử lý tối đa 5 trang theo thứ tự priority.

  [GSC_PRIORITY_LIST]:
  [dán toàn bộ Priority List từ Phase 1 vào đây]

  Với mỗi trang: fetch URL, xác định vấn đề, tạo output theo đúng format
  trong file agent của mày (3 title options + meta + H1/intro/FAQ nếu cần)."
)
```

Chờ Content Writer hoàn thành và nhận output tối ưu cho từng trang.

**Thông báo chuyển phase:**
```
✅ Phase 2/3 hoàn thành: Content Writer đã tối ưu X trang
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Phase 3/3: Tổng hợp báo cáo...
```

---

## Phase 3 — Báo Cáo Tổng Hợp

Search Growth Agent tổng hợp output từ cả 2 sub-agent thành báo cáo cuối.

### Format Output Cuối

```
# 🚀 Search Growth Report — [domain]
Ngày: DD/MM/YYYY | Kỳ phân tích: 28 ngày gần nhất

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 1. HIỆU SUẤT HIỆN TẠI
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

| Metric        | Kỳ hiện tại | Kỳ trước | Thay đổi |
|---------------|------------|---------|---------|
| Clicks        | X,XXX      | X,XXX   | ↑/↓ XX% |
| Impressions   | XXX,XXX    | XXX,XXX | ↑/↓ XX% |
| CTR           | X.X%       | X.X%    | ↑/↓ Xpp |
| Avg Position  | XX.X       | XX.X    | ↑/↓ X.X |

**Nhận xét:** [1–2 câu tổng quan xu hướng]
**Traffic tiềm năng có thể lấy thêm:** +X,XXX clicks/tháng

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 2. DANH SÁCH TRANG CẦN CẢI THIỆN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Sắp xếp theo mức độ ảnh hưởng (impact score cao → thấp):

| # | Trang | Vấn đề chính | Impact | Hành động |
|---|-------|-------------|--------|----------|
| 1 | /...  | CTR gap +X.Xpp | +XXX clicks/tháng | Viết lại title |
| 2 | /...  | Traffic ↓ XX% | Khẩn cấp | Rewrite + update |
| 3 | /...  | Quick win pos X | +XXX clicks/tháng | Thêm FAQ schema |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 3. CONTENT ĐÃ TỐI ƯU
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### Trang 1: [URL]
**Vấn đề:** [mô tả ngắn]
**Primary keyword:** [từ khóa] (Pos: X.X | Imp: X,XXX | CTR: X.X%)

**Title hiện tại:**
> "[title cũ]"

**Title đề xuất (chọn 1 trong 3):**
> 1. "[title mới 1]" — XX ký tự
> 2. "[title mới 2]" — XX ký tự
> 3. "[title mới 3]" — XX ký tự

**Meta description đề xuất:**
> "[meta mới]" — XXX ký tự

**[Nếu cần rewrite] H1 mới:**
> "[H1 mới]"

**[Nếu cần rewrite] Intro mới (150–200 từ):**
> [nội dung intro]

**[Nếu cần FAQ] FAQ Schema gợi ý:**
Q: [câu hỏi 1]
A: [trả lời ngắn 1–2 câu]
...

---
### Trang 2: [URL]
[tương tự]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 4. ACTION ITEMS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### 🔴 Làm ngay (tuần này)
- [ ] [URL 1]: Cập nhật title → "[title đề xuất 1]"
- [ ] [URL 1]: Cập nhật meta description
- [ ] [URL 2]: [action cụ thể]

### 🟠 Tuần tới
- [ ] [URL 3]: Rewrite intro + cập nhật số liệu
- [ ] [URL 4]: Thêm FAQ section + FAQ schema
- [ ] Kiểm tra lại [URL 2] sau khi update

### 🟡 Theo dõi (30 ngày)
- [ ] Monitor CTR của X trang đã tối ưu
- [ ] So sánh clicks tuần sau update vs tuần trước
- [ ] Chạy lại /search-growth-agent để đo kết quả

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
## 5. TÓM TẮT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
- Trang được phân tích: X
- Trang cần xử lý: X
- Traffic tiềm năng tăng thêm: +X,XXX clicks/tháng
- Ưu tiên #1: [action quan trọng nhất]
```

---

## Xử Lý Lỗi & Edge Cases

| Tình huống | Xử lý |
|-----------|-------|
| GSC MCP không kết nối | Dừng Phase 1, hướng dẫn kết nối, chỉ chạy Content Writer nếu user cung cấp URL cụ thể |
| Property không tìm thấy | Gọi `list_sites` → liệt kê các property có sẵn → cho user chọn |
| Không có data drops hoặc CTR gap | Thông báo tích cực, tập trung vào quick wins và content depth |
| URL cụ thể không public | Bỏ qua bước 2.1, dùng GSC data làm basis |
| Focus mode = `content` | Bỏ qua Phase 1, yêu cầu user cung cấp URL + keyword mục tiêu |

---

## Skills Được Dùng

### GSC Analyst dùng:
| Skill | Mục đích |
|-------|---------|
| `gsc-performance` | Traffic overview, drops, quick wins |
| `gsc-ctr` | CTR gap analysis theo trang và từ khóa |

### Content Writer dùng:
| Skill | Mục đích |
|-------|---------|
| `seo-page` | Đọc title/meta/H1 hiện tại của trang |
| `seo-content-brief` | Brief cho trang cần rewrite lớn |
| `copywriting` | Viết title, meta, intro mới |
| `copy-editing` | Polish và kiểm tra chất lượng output |

---

## Ghi Chú Điều Phối

- **Thứ tự bắt buộc:** Phase 1 → Phase 2 → Phase 3 (không song song)
- **Context passing:** GSC Analyst PHẢI hoàn thành và tổng hợp `[GSC_PRIORITY_LIST]` trước khi Content Writer bắt đầu
- **Số trang tối đa:** Content Writer xử lý tối đa 5 trang/lần để giữ chất lượng
- **Độ trễ GSC:** Luôn dùng `endDate = hôm nay - 3 ngày`
- **Branded keywords:** Không tính vào quick wins và CTR gap analysis
