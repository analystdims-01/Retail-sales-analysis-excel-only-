# 🛍️ Retail Sales Performance & Customer Insights – Excel Dashboard Project

---

## 📌 Project Overview

```text
Data Generation → Data Enrichment → Data Modeling → Interactive Dashboard
```

This is a standalone Excel analytics project — an upgraded version of a previously completed SQL and Power BI retail sales analysis. The same business questions are answered here using **only Excel**, demonstrating that with Power Pivot and proper data modeling, Excel is a fully capable end-to-end analytics tool.

**Dataset**: Feb 2023 – Apr 2025 · 800+ rows · 3 tables · 10 branches · 8 product categories

---

## 🛠️ Tools Used

![Excel](https://img.shields.io/badge/Excel-Data%20Generation-green)
![PowerPivot](https://img.shields.io/badge/Power%20Pivot-Data%20Modeling-orange)
![PowerQuery](https://img.shields.io/badge/Power%20Query-Data%20Transformation-blue)

---

## 📊 1. Tables Created

| Table | Key Columns |
|---|---|
| **Sales** | `sale_id`, `date`, `branch_id`, `customer_id`, `product_id`, `quantity`, `price`, `discount`, `total_amount`, `profit` |
| **Customers** | `customer_id`, `name`, `gender`, `region`, `signup_date`, `loyalty_status`, `store_branch` |
| **Products** | `product_id`, `category`, `product_name`, `cost_price`, `selling_price` |

---

## 🔧 2. Data Generation

All three tables were generated from scratch using Excel functions:

* **Random numeric data:**

```excel
=RANDBETWEEN(1,10)
=DATE(2024, RANDBETWEEN(1,12), RANDBETWEEN(1,28))
```

* **Random text assignment using CHOOSE:**

```excel
=CHOOSE(RANDBETWEEN(1,10),"P.id01","P.id02","P.id03",...)
```

* **Branch ID encoding by region:**

```excel
=INDEX(IF([@region]="North", CHOOSE(RANDBETWEEN(1,2),"N01","N02"),
IF([@region]="South", CHOOSE(RANDBETWEEN(1,2),"S01","S02"),
IF([@region]="East",  CHOOSE(RANDBETWEEN(1,2),"E01","E02"),
IF([@region]="West",  CHOOSE(RANDBETWEEN(1,2),"W01","W02"),
IF([@region]="Central",CHOOSE(RANDBETWEEN(1,2),"C01","C02"),
"Unknown"))))),1,1)
```

* **Discount logic by price tier:**

```excel
=IF([@price]*[@quantity]<=100, 0,
IF([@price]*[@quantity]<=200, 0.10,
IF([@price]*[@quantity]<=400, 0.15,0.20)))
```

* **Total amount after discount:**

```excel
=([@price]*[@quantity])*(1-[@discount])
```

* **Profit calculation:**

```excel
=[@[total_amount]]-([@[cost_price]]*[@[quantity]])
```

---

## 🔗 3. Data Enrichment

Cross-table lookups were used to enrich the Sales table without duplicating the Customers or Products tables:

```excel
-- Selling price from Products
=XLOOKUP([@[product_id]], Products[product_id], Products[selling_price], 0)

-- Cost price from Products
=XLOOKUP([@[product_id]], Products[product_id], Products[cost_price], 0)

-- Category from Products
=XLOOKUP([@[product_id]], Products[product_id], Products[category], "Unknown")
```

> `loyalty_status` was intentionally kept on the Customers table only — accessed via the Data Model relationship rather than duplicated into Sales.

---

## ⭐ 4. Data Modeling (Power Pivot)

Relationships were built in the **Excel Data Model** connecting all three tables:

```text
Customers ──── Sales ──── Products
(customer_id)      (product_key)
```

**Challenge**: `product_id` alone was not unique — the same product appeared at multiple price points. A composite key resolved the many-to-many relationship:

```excel
="P."&[@[product_id]]&"."&TEXT([@[selling_price]],"0.00")
```

This was applied to both tables creating a clean one-to-many join.

---

## 📐 5. Power Pivot DAX Measures

Regular calculated fields are disabled when using the Data Model — all measures were built in Power Pivot:

```dax
Profit Margin % := DIVIDE(SUM(Sales[profit]), SUM(Sales[total_amount]))

Discount Rate % := DIVIDE(SUM(Sales[discount_amount]), SUM(Sales[total_amount]))
```

---

## 📊 6. Interactive Dashboard

### 📸 Dashboard Preview

<img width="1633" height="709" alt="retail sales analysis dashboard(excel)" src="https://github.com/user-attachments/assets/a6785156-725d-4d66-be49-445a3e718de3" />


---

### 🎯 KPI Cards (Dynamic YoY)

| KPI | Value | YoY Change |
|---|---|---|
| YoY Revenue Growth | +23% | 2023 → 2024 |
| Profit Margin % | 17% | ▼ -1% vs 2023 |
| Discount Rate % | 15% | ▲ +1% vs 2023 |

KPI cards are powered by `GETPIVOTDATA` and intentionally **disconnected from slicers** — YoY calculations require both years present simultaneously and break when filtered to a single year.

---

### 📈 Dashboard Visuals

| Chart | Type | Key Finding |
|---|---|---|
| Average Monthly Sales Trend | Line Chart | November peaks consistently across all years |
| Branch Performance Overview | Clustered Bar | N02 leads — C02 needs urgent attention |
| Revenue by Product Category | Clustered Bar | Phones lead at $20k |
| Discount Impact on Profit | Clustered Bar | 10% discount is the sweet spot |
| Revenue vs Discount Breakdown | Donut Chart | $1 in every $5 of revenue given away as discount |

---

### 🎛️ Slicers

* Year · Loyalty Status · Region

---

### 📝 Dashboard Insights (Text Annotations)

Each chart includes a short insight box summarising the key finding:

**Average Monthly Sales Trend**
> November consistently peaks across 2023 and 2024. Revenue remains stable year-round with no extreme slow periods — indicating healthy, predictable demand. Average revenue is stable across other months too.

**Branch Performance Overview**
> N02 leads all locations in revenue. C02 is the weakest performer and requires urgent attention. Regional distribution across all branches shows uneven performance worth investigating.

**Revenue by Product Category**
> Phones lead with $20k followed closely by Toys and Jewelry. The bottom 5 categories are underperforming and require strategic review — whether through targeted promotions, pricing adjustments or stock reallocation.

**Discount Impact on Profit**
> 10% discount is the sweet spot — driving the highest revenue of $61k and profit of $11k. Margins decline steadily beyond 10%. Zero discount customers remain the most margin efficient at 25.3%.

**Revenue vs Discount Breakdown**
> $1 in every $5 of revenue is given away as discount. At 10% this is most efficient. Reviewing 15% and 20% discount tiers could recover significant margin across all branches.

**Where Do We Need Improvement**
> Profit margin declined 1% in 2024 despite 23% revenue growth — indicating growth is being driven by heavier discounting rather than organic demand. Capping discounts at 10% across all branches could recover lost margin without sacrificing customer volume. Bronze tier customers represent the largest segment but generate the lowest revenue per head — a structured loyalty progression programme could unlock significant untapped revenue.

---

## 📈 7. Key Business Insights

* **Phones lead revenue** at $20k — followed closely by Toys ($18.3k) and Jewelry ($18.2k)
* **10% is the optimal discount tier** — highest absolute revenue and profit. Margins erode above 10%
* **N02 leads all branches** in revenue — C02 is the weakest and needs urgent review
* **November peaks consistently** — optimal window for promotions and stock investment
* **Platinum and Gold customers** generate the highest revenue per head despite being the smallest groups
* **Revenue grew +23% YoY** but profit margin fell 1% — growth is discount-driven not demand-driven

---

## 🧠 8. Challenges & Solutions

| Challenge | Solution |
|---|---|
| `RANDBETWEEN` doesn't support text values | Used `CHOOSE(RANDBETWEEN(1,n),...)` with `INDEX` wrapper to prevent `#SPILL` |
| `#SPILL` errors from `[@column]` references | Switched to direct cell references like `C2` outside structured tables |
| Discount formula gap between 300–400 range | Reordered `IF` conditions using `<=` instead of `>` |
| Non-unique `product_id` for Data Model relationship | Created composite `product_key` combining `product_id` + `selling_price` |
| `XLOOKUP` returning "Not Found" | Generated and froze product IDs first before running XLOOKUP |
| Calculated fields inactive when using Data Model | Switched to Power Pivot DAX measures |
| KPI cards showing `#REF` on slicer click | Disconnected KPI Pivot Tables from slicers via Report Connections |
| Months sorting alphabetically in Pivot Table | Added `month_number` column — applied Sort by Column to force chronological order |

---

## 🏆 9. Skills Demonstrated

* **Data Generation**: RANDBETWEEN, CHOOSE, IF, IFS, DATE, TEXT
* **Data Enrichment**: XLOOKUP, VLOOKUP, SUMIFS, COUNTIFS
* **Data Modeling**: Power Pivot relationships, composite key design, one-to-many joins
* **DAX**: DIVIDE, SUM measures via Power Pivot
* **Dashboard Design**: Pivot Tables, Pivot Charts, GETPIVOTDATA, Slicers, Report Connections
* **Business Analysis**: YoY trend analysis, discount optimization, customer segmentation, branch performance

---

## 🚀 10. Conclusion

This project proves that Excel — when treated as a proper data modeling environment rather than just a spreadsheet — can deliver professional grade analytics end to end.

**The standout insight:**

> *Revenue grew +23% YoY but profit margin dropped 1% as discount rate crept from 14% to 15%. Growth bought through discounting is not the same as organic growth.*

**Next step**: Rebuilding this pipeline in Python using Pandas and Plotly 🐍

---
