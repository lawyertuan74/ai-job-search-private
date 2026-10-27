# Search Queries for Job Scraper

<!-- SETUP: Customize these queries based on your skills, target roles, and location -->

## Installed portal CLIs (primary for `/scrape`)

`/scrape` discovers every portal skill under `.agents/skills/*/SKILL.md` and runs its CLI first. Shipped country-agnostic CLIs include `linkedin-search` and `freehire-search`; the Vietnamese `vietnamworks-search` and `itviec-search` and any skill you add with `/add-portal` are included the same way. You do **not** need a matching `site:` line below for those CLIs to run.

The `site:` query templates in this file are the **WebSearch fallback** — for portals without a CLI, company career pages, or when a CLI fails.

**Language scope:** write every query category in every language listed in your CLAUDE.md Languages table (typically 1-2, sometimes more). A posting requiring a language you have *not* declared, as a job condition, is excluded before scoring; a posting requiring a *higher level* than you declared in a language you *do* work in is flagged for your own judgment, not excluded — see `04-job-evaluation.md`'s Language Gate, the single source of truth for this rule. Translate each category's keywords rather than machine-translating word-for-word (e.g. "Frontend Developer" -> "Desarrollador Frontend", not a literal word-for-word translation) if you work in more than one language.

## Search Sites

Primary (your market's job boards - scaffold one with `/add-portal`):
- **[YOUR_JOB_BOARD]** - your market's largest general job board
- **linkedin.com/jobs** - LinkedIn job listings (filter: [YOUR_COUNTRY] / [YOUR_CITY]); also covered by `linkedin-search` CLI
- **[YOUR_INDUSTRY_JOB_BOARD]** - a niche/industry board for your field (optional)
- **[YOUR_ADDITIONAL_JOB_BOARD]** - another major board for your market (optional)

Vietnam (shipped with this Vietnamese edition):
- **vietnamworks.com** - covered by the `vietnamworks-search` CLI (all sectors, all provinces)
- **itviec.com** - covered by the `itviec-search` CLI (IT roles only; disable it for non-IT candidates)
- **topcv.vn** - no CLI (TopCV's bot protection refuses automated clients, and this repo does not work around that). Covered by **WebFetch** as described in the TopCV block below - fresher than WebSearch. Delete the block if TopCV is not relevant.

### TopCV (WebFetch, no CLI)

TopCV listing pages render for WebFetch. For each query term, WebFetch the listing URL and extract, per posting: title, company, location, salary, the relative posting age ("Đăng 3 ngày trước"), and the posting URL **without its query string** (`https://www.topcv.vn/viec-lam/<slug>/<id>.html`). Treat these results exactly like Step 1c fallback results: tag them with portal `topcv` and source `websearch`, fetch details with WebFetch on the posting URL, and never invent a field the page does not show.

```
https://www.topcv.vn/tim-viec-lam-[YOUR_KEYWORD_SLUG]
```

`[YOUR_KEYWORD_SLUG]` = the Vietnamese or English keyword, lowercase, accents removed, spaces as `-` (e.g. `ke-toan`, `nhan-vien-marketing`, `data-analyst`). The listing is nationwide; filter by location from each result's location field (TopCV labels post-2025 provinces like "Hồ Chí Minh (mới)"). If WebFetch is refused or returns no postings, fall back to WebSearch:

```
site:topcv.vn/viec-lam "[YOUR_PRIMARY_JOB_TITLE_1]" [YOUR_CITY]
```

Secondary (company career pages via Google):
- Direct Google searches with `site:` filters for known target companies

## Query Categories

Queries are grouped by priority. Write **each category in every language from your Languages table** (see Language scope above). Combine each query with your location terms (e.g. your city, region, or metro area) where the site supports it.

**Organize by function, not job title.** The same underlying work carries different titles across companies and markets (a "Data Scientist" role at one employer may be posted as "Insights Analyst" or "Data Consultant" at another). Name each priority category after the function it covers, and list several plausible job titles as query variants within that category rather than betting an entire priority tier on one exact title string.

### Priority 1: Điều hành doanh nghiệp (COO / Giám đốc vận hành)

VietnamWorks CLI (`-l "hcm,binh duong,dong nai,long an,ba ria vung tau"`):
- `-q "COO"`, `-q "Giám đốc vận hành"`, `-q "Chief Operating Officer"`, `-q "Giám đốc điều hành"`, `-q "Operations Director"`, `-q "Phó tổng giám đốc"`

LinkedIn CLI (`-l "Ho Chi Minh City, Vietnam"`):
- `-q "Chief Operating Officer"`, `-q "COO"`, `-q "Operations Director"`, `-q "General Manager"`

TopCV (WebFetch): `giam-doc-van-hanh`, `coo`, `giam-doc-dieu-hanh`

```
site:linkedin.com/jobs "Chief Operating Officer" "Ho Chi Minh"
site:topcv.vn/viec-lam "Giám đốc vận hành" Hồ Chí Minh
```

### Priority 2: Tài chính (CFO / Giám đốc tài chính)

VietnamWorks CLI: `-q "CFO"`, `-q "Giám đốc tài chính"`, `-q "Chief Financial Officer"`, `-q "Finance Director"`, `-q "Kế toán trưởng"` (chỉ khi cấp quản lý cao)

LinkedIn CLI: `-q "Chief Financial Officer"`, `-q "CFO"`, `-q "Finance Director"`

TopCV (WebFetch): `giam-doc-tai-chinh`, `cfo`

```
site:linkedin.com/jobs "Chief Financial Officer" "Ho Chi Minh"
site:topcv.vn/viec-lam "Giám đốc tài chính" Hồ Chí Minh
```

### Priority 3: Pháp chế (Giám đốc Pháp chế / Legal Director)

VietnamWorks CLI: `-q "Giám đốc pháp chế"`, `-q "Trưởng phòng pháp chế"`, `-q "Legal Director"`, `-q "Head of Legal"`, `-q "Legal Counsel"`, `-q "General Counsel"`

LinkedIn CLI: `-q "Legal Director"`, `-q "Head of Legal"`, `-q "General Counsel"`

TopCV (WebFetch): `giam-doc-phap-che`, `truong-phong-phap-che`, `legal-manager`

```
site:linkedin.com/jobs "Head of Legal" "Ho Chi Minh"
site:topcv.vn/viec-lam "Pháp chế" Hồ Chí Minh
```

## Location Filter

Chấp nhận (TP.HCM và vùng lân cận; VietnamWorks vẫn dùng tên tỉnh trước sáp nhập 2025):
- TP. Hồ Chí Minh (gồm Bình Dương và Bà Rịa - Vũng Tàu cũ, sáp nhập vào TP.HCM từ 07/2025)
- Bình Dương, Bà Rịa - Vũng Tàu (tên cũ trên VietnamWorks)
- Đồng Nai (gồm Bình Phước cũ)
- Long An (nay thuộc Tây Ninh mới)
- Tây Ninh (borderline - kiểm tra địa điểm làm việc cụ thể)
- Ngoài khu vực trên: loại, trừ khi tin cho làm việc từ xa / hybrid đặt tại TP.HCM

## Language Filter

Your working languages and levels are in CLAUDE.md's Languages table. When filtering scraped results, apply `04-job-evaluation.md`'s Language Gate: a posting requiring a language you haven't declared at all is excluded; a posting requiring a higher level than you declared in a language you do work in is not excluded, flag it clearly instead (see `job-scraper/SKILL.md`'s Step 3 "Quick Fit Assessment" for how the flag surfaces in `/scrape` output). Postings simply *written* in a language you don't work in, that don't require it on the job, are fine.

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
