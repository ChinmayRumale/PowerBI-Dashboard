# 📊 Sales & Revenue Intelligence — Power BI Portfolio Project

> **End-to-end Power BI project** for a Data Analyst portfolio.  
> 5,000 synthetic orders across 5 regions, 5 categories, 4 channels, 2022–2024.

---

## 🗂️ Project Structure

```
sales-powerbi-project/
│
├── data/
│   ├── sales_data.csv       ← 5,000 order rows (main fact table)
│   ├── targets.csv          ← Monthly revenue targets by region & category
│   └── returns.csv          ← 150 return records
│
├── dax/
│   └── all_measures.dax     ← All 30+ DAX measures with comments
│
├── theme/
│   └── SalesIntelligence.json  ← Custom Power BI theme
│
├── docs/
│   └── build_guide.md       ← Step-by-step Power BI build instructions
│
└── preview/
    └── dashboard_preview.html  ← Interactive HTML preview (open in browser)
```

---

## 🚀 Quick Start

1. Open **Power BI Desktop**
2. Load all 3 CSVs from `/data/` folder
3. Follow `/docs/build_guide.md` step by step
4. Apply theme from `/theme/SalesIntelligence.json`
5. Copy DAX measures from `/dax/all_measures.dax`

Preview the layout first: open `/preview/dashboard_preview.html` in your browser.

---

## 📐 Data Model (Star Schema)

```
         ┌─────────┐
         │  Date   │◄──────────────────────┐
         └────┬────┘                       │
              │ 1                          │ Many
              │                            │
   Many ▼     │                   ┌────────┴────────┐
┌─────────────┴──┐                │    Targets      │
│    Sales       │                │ (Region/Month)  │
│  (Fact Table)  │                └─────────────────┘
└────────┬───────┘
         │ 1 : Many
         ▼
    ┌─────────┐
    │ Returns │
    └─────────┘
```

---

## 📊 Dashboard Pages

| Page | Key Visuals |
|------|-------------|
| **Executive Summary** | 5 KPI cards, Revenue trend, Region progress bars, Profit gauge |
| **Sales Breakdown** | Region bar, Channel donut, Rev vs COGS, Segment matrix |
| **Product Analysis** | Top N bar, Scatter, ABC classification table |
| **Time Intelligence** | YTD vs PYTD line, Quarterly comparison, Monthly heatmap |
| **Customer Drill-through** | Per-customer detail (set up as drill-through page) |

---

## 🧮 DAX Highlights

```dax
-- Time Intelligence
YTD Revenue     = TOTALYTD([Total Revenue], 'Date'[Date])
MoM Growth %    = DIVIDE([Total Revenue] - [Revenue PrevMonth], [Revenue PrevMonth])
YoY Growth %    = DIVIDE([Total Revenue] - [Revenue PrevYear], [Revenue PrevYear])

-- Rankings
Product Rank    = RANKX(ALL(Sales[Product]), [Total Revenue],, DESC, DENSE)
Dynamic Top N   = IF([Product Revenue Rank] <= SELECTEDVALUE(TopNSlicer[Value], 10), 1, 0)

-- Classification
ABC Class       = (cumulative RANKX to bucket products into A / B / C)
```

---

## 🎯 Interview-Ready Features

- ✅ Star schema with proper cardinality
- ✅ Calculated Date Table with fiscal year support
- ✅ 30+ DAX measures including time intelligence
- ✅ Dynamic Top N via RANKX + slicer
- ✅ ABC product classification
- ✅ Row Level Security (RLS) setup instructions
- ✅ Drill-through page
- ✅ Bookmarks for toggle views
- ✅ Conditional formatting on margins
- ✅ Custom theme JSON

---

## 💬 Interview Talking Points

**"Walk me through your data model."**
> "I built a star schema with Sales as the fact table, connected to a calculated Date dimension for time intelligence. Returns are linked via OrderID, and Targets connect through Year/Month for achievement tracking."

**"What's your most complex DAX measure?"**
> "The ABC Classification uses cumulative RANKX — it calculates each product's running revenue share and buckets it into A (top 70%), B (70-90%), or C (long tail). It's useful for inventory prioritization."

**"How did you handle time intelligence?"**
> "I created a dedicated Date table using CALENDAR() in DAX, marked it as a Date table, and used functions like TOTALYTD, SAMEPERIODLASTYEAR, and DATEADD to build YTD, MoM, and YoY comparisons."

---

## 📁 Sharing as Portfolio

1. Export `.pbix` file and upload to GitHub
2. Open project in Power BI Service → publish and get shareable link
3. Add `/preview/dashboard_preview.html` as a static portfolio preview
4. Link this README in your resume projects section

---

*Dataset is fully synthetic, generated for portfolio purposes.*
