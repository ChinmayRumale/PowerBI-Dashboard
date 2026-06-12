# Power BI Build Guide — Sales & Revenue Intelligence

## Step 1: Load Data

1. Open Power BI Desktop → **Get Data → Text/CSV**
2. Load all three files:
   - `data/sales_data.csv`  → rename table to **Sales**
   - `data/targets.csv`     → rename to **Targets**
   - `data/returns.csv`     → rename to **Returns**

---

## Step 2: Power Query Transformations

### Sales Table
- `OrderDate` → change type to **Date**
- `UnitPrice`, `UnitCost`, `Revenue`, `COGS`, `GrossProfit` → **Decimal Number**
- `Quantity` → **Whole Number**
- `Discount` → **Decimal Number**
- Add **Year** column: `= Date.Year([OrderDate])`
- Add **MonthNum** column: `= Date.Month([OrderDate])`

### Targets Table
- `Year`, `Month` → **Whole Number**
- `RevenueTarget` → **Decimal Number**
- Add **YearMonth** column: `= Text.From([Year]) & "-" & Text.PadStart(Text.From([Month]),2,"0")`

### Returns Table
- `ReturnDate` → **Date**
- `RefundAmount` → **Decimal Number**

---

## Step 3: Create Date Table

Go to **Modeling → New Table** and paste the DAX from `dax/all_measures.dax`
(the block inside the comment at the bottom — remove the `/* */` wrapper).

Mark it as a Date Table: right-click the table → **Mark as date table** → select `Date`.

---

## Step 4: Data Model (Star Schema)

Create these relationships in **Model view**:

| From Table    | From Column   | To Table   | To Column    | Cardinality |
|---------------|---------------|------------|--------------|-------------|
| Sales         | OrderDate     | Date       | Date         | Many → One  |
| Sales         | OrderID       | Returns    | OrderID      | One → Many  |
| Targets       | Year          | Date       | Year         | Many → One  |
| Targets       | Month         | Date       | MonthNum     | Many → One  |

> **Important:** Hide all FK/key columns from Report View (right-click → Hide).

---

## Step 5: Create TopN Slicer Table

**Modeling → New Table:**
```
TopNSlicer = GENERATESERIES(1, 20, 1)
```
Rename the column to `Value`.

---

## Step 6: Load All DAX Measures

Open `dax/all_measures.dax`. Create a **Measures Table** first:
- Modeling → Enter Data → name it `_Measures` → Load
- Hide the auto-created column

Create each measure from the DAX file using **Modeling → New Measure**.

---

## Step 7: Apply Theme

View → **Browse for themes** → select `theme/SalesIntelligence.json`

---

## Step 8: Build Report Pages

### Page 1 — Executive Summary
| Visual            | Fields                                      | Position     |
|-------------------|---------------------------------------------|--------------|
| Card (×4)         | Total Revenue / Gross Profit / Total Orders / Profit Margin % | Top row |
| Line Chart        | X: Date[YearMonth], Y: Total Revenue, Secondary: MoM Growth % | Center |
| Clustered Bar     | X: Region, Y: Total Revenue vs Revenue Target | Bottom left |
| Gauge             | Value: Profit Margin %, Min: 0, Max: 0.6, Target: 0.35 | Bottom right |
| Slicer            | Date[Year]                                  | Top-right    |

### Page 2 — Sales Breakdown
| Visual            | Fields                                      |
|-------------------|---------------------------------------------|
| Filled Map        | Location: Region, Size: Total Revenue       |
| Donut Chart       | Legend: Channel, Values: Total Revenue      |
| Stacked Bar       | X: Category, Y: Revenue + COGS              |
| Matrix            | Rows: Segment, Cols: Channel, Values: Revenue, Profit Margin % |
| Slicers           | Category, Segment, Channel                  |

### Page 3 — Product Analysis
| Visual            | Fields                                      |
|-------------------|---------------------------------------------|
| Bar Chart (Top N) | X: Total Revenue, Y: Product (filter: Is Top N Product = 1) |
| Scatter Chart     | X: Total Revenue, Y: Gross Profit Margin %, Size: Total Units Sold, Detail: Product |
| Table             | Product, Category, Revenue, COGS, Gross Profit, Margin %, ABC Class |
| Slicer            | TopNSlicer[Value] (type: numeric range)     |

### Page 4 — Time Intelligence
| Visual            | Fields                                      |
|-------------------|---------------------------------------------|
| Line Chart        | X: Date[MonthShort], Y: YTD Revenue + PYTD Revenue |
| Clustered Column  | X: Date[Quarter], Y: QTD Revenue            |
| KPI Card          | Value: YTD Revenue, Target: PYTD Revenue    |
| Card              | YTD vs PYTD %, Rolling 12M Revenue          |
| Matrix (heatmap)  | Rows: MonthShort, Cols: Year, Values: Total Revenue (use conditional formatting) |

### Page 5 — Drill-Through: Customer Detail
(This page is accessed by right-click → Drill Through from any visual)
- Add a **Drill-through field**: CustomerName
- Table: CustomerID, CustomerName, Region, Segment, Total Revenue, Total Orders, AOV, Return Rate %
- Line chart: Revenue trend by month for that customer

---

## Step 9: Interactivity Polish

1. **Bookmarks** (View → Bookmarks):
   - Create "Revenue View" with revenue metric selected
   - Create "Profit View" with profit metric selected
   - Add two buttons and assign bookmarks

2. **Conditional Formatting** on Matrix/Table:
   - Gross Profit Margin %: Red < 20%, Yellow 20–35%, Green > 35%
   - Revenue vs Target: Red < 90%, Green > 100%

3. **Tooltips**: Add a tooltip page showing sparkline for any product

4. **Row Level Security**:
   - Modeling → Manage Roles → Create role "Region Manager - North"
   - DAX filter on Sales: `[Region] = "North"`
   - Test with View As Roles

---

## Step 10: Publish

1. File → **Publish to Power BI Service**
2. Pin visuals to a **Dashboard**
3. Set **Scheduled Refresh** (if using gateway with live data)
4. Copy the shareable link for your portfolio

---

## Interview Talking Points

- "I built a star schema with a calculated date table to enable time intelligence functions like TOTALYTD and SAMEPERIODLASTYEAR."
- "The DAX RANKX + dynamic TopN slicer lets business users self-serve their own analysis without changing the report."
- "I implemented Row Level Security so regional managers only see their own data."
- "The ABC classification measure uses cumulative RANKX to categorize products — useful for inventory prioritization."
