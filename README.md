# Claude Skills & Agents

Tập hợp custom agents và skills cho Claude Code, chuyên về SEO và content strategy.

---

## SEO-CONTENT-STRATEGIST

Agent điều phối chiến lược nội dung SEO. Nhận keyword thô, phân tích và trả về kế hoạch nội dung hoàn chỉnh sẵn sàng giao writer.

**Bao gồm:**
- `seo-content-strategist.md` — Agent chính
- `keyword-research.md` — Sub-agent nghiên cứu từ khóa
- `content-production.md` — Sub-agent sản xuất nội dung

**Cách dùng:**
```
/seo-content-strategist [keyword hoặc domain]
```

---

## search-growth-agent

Agent mẹ tăng trưởng organic traffic. Điều phối 2 sub-agent để phân tích GSC data và tối ưu content.

**Bao gồm:**
- `SKILL.md` — Agent chính (orchestrator)
- `gsc-analyst.md` — Sub-agent phân tích Google Search Console
- `content-writer.md` — Sub-agent tối ưu content

**Cách dùng:**
```
/search-growth-agent example.com
/search-growth-agent example.com traffic
/search-growth-agent example.com ctr
/search-growth-agent example.com content
```

**Yêu cầu:** MCP Google Search Console đã kết nối.

---

## Cài Đặt

Copy các file `.md` vào thư mục `~/.claude/agents/` trên máy của bạn.
