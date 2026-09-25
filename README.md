# Executive Sales & Margin Performance Dashboard (2023–2024)

## Executive Summary
This project delivers an interactive, executive-grade business intelligence dashboard built in Microsoft Excel and Power Pivot, modeling nearly **1,000,000 transaction records** using a formal dimensional **Star Schema** architecture. 

Over the 2023–2024 operating period, total net sales reached **$25.49M** across six international markets, generating **$5.10M** in gross profit in 2024 at a consistent **40.0% gross margin**. While top-line sales grew marginally (+0.12% YoY in 2024), deep exploratory analysis reveals pronounced revenue disparities by geography, key product-level profit concentration, and notable behavioral symmetry across customer loyalty segments that highlight strategic opportunities for margin expansion and promotional redesign.

---

## Business Problem & Strategic Objectives
* **Scale & Performance:** Process and analyze enterprise-scale point-of-sale (POS) data (~1M rows) without workbook instability or formula drag.
* **Data Integrity:** Identify and resolve dimensional anomalies, orphan transaction keys, and uninformative categorical encodings.
* **Executive Visibility:** Provide C-suite decision-makers with dynamic, high-level visibility across four core performance pillars:
  1. Year-over-Year revenue momentum and monthly seasonality.
  2. Product profitability and SKU concentration.
  3. Regional sales distribution.
  4. Customer loyalty contribution and promotional elasticity.

---

## Technical Architecture & Data Modeling

### 1. Star Schema Topology
The data model is implemented in **Power Pivot (xVelocity In-Memory Engine)** using a Star Schema design with explicit $1:\infty$ relationships:

* **Fact Table:** `sales` (990,236 transaction rows post-cleansing)
* **Dimension Tables:**
  * `calendar` ($1:\infty$ on `order_date`) — Continuous date table enabling DAX time-intelligence functions.
  * `products` ($1:\infty$ on `product_id`) — Product hierarchy, base prices, and cost structure.
  * `customers` ($1:\infty$ on `customer_id`) — Customer demographics and loyalty tiering.
  * `stores` ($1:\infty$ on `store_id`) — Store locations, geographic regions, and retail formats.

```text
       [calendar]       [products]
           | 1              | 1
           | ∞              | ∞
     +------------------------------+
     |         sales (Fact)         |
     +------------------------------+
           | ∞              | ∞
           | 1              | 1
       [customers]       [stores]
```

### 2. ETL & Data Quality Audit
* **Orphan Key Resolution:** Auditing the raw dataset identified **9,764 transaction records** (~0.98% of total volume) referencing missing catalog keys (`P0000` and `P0201`).
* **ETL Pruning:** The orphan records were pruned during the Power Query extraction stage, reducing total row count from **1,000,000 to 990,236 rows**. This enforced 100% referential integrity and eliminated invalid `(blank)` dimension artifacts.
* **Categorical Feature Engineering:** The binary flag `loyalty_member` (`0`/`1`) in the customer dimension was transformed into a business-readable categorical attribute, **`Loyalty Status`** (`"Loyalty Member"` vs. `"Standard Customer"`).

### 3. Core DAX Measures
```dax
// Net Revenue
Net Revenue = SUMX(sales, sales[quantity] * sales[unit_price] * (1 - sales[discount_pct]))

// Time Intelligence: Prior Year Net Revenue
Net Revenue PY = CALCULATE([Net Revenue], SAMEPERIODLASTYEAR(calendar[date]))

// Year-over-Year Variance
YoY Revenue Growth = [Net Revenue] - [Net Revenue PY]
YoY Revenue Growth % = DIVIDE([YoY Revenue Growth], [Net Revenue PY], 0)

// Profitability
Gross Profit = [Net Revenue] - SUMX(sales, sales[quantity] * RELATED(products[unit_cost]))
Gross Margin % = DIVIDE([Gross Profit], [Net Revenue], 0)

// Operational Efficiency
Average Order Value = DIVIDE([Net Revenue], DISTINCTCOUNT(sales[order_id]), 0)
Discount Depth % = AVERAGE(sales[discount_pct])
```

---

## Key Performance Indicators (FY 2024)

| Metric | 2024 Actual | vs. Prior Year (2023) | Status / Context |
| :--- | :--- | :--- | :--- |
| **Net Revenue** | **$12.75M** | +$15.9K (+0.12%) | Top-line revenue plateau |
| **Gross Profit** | **$5.10M** | — | Healthy baseline margin |
| **Gross Margin %** | **40.0%** | Stable | Disciplined cost-of-goods management |
| **Units Sold** | **1,501,485** | +0.20% | Stable unit movement |
| **Average Order Value (AOV)** | **$25.48** | -$0.03 | Consistent basket sizing |
| **Average Discount Depth** | **5.64%** | Stable | Controlled markdown policy |

---

## Detailed Analytical Insights

### 1. Revenue Trajectory & Monthly Seasonality
* **Annual Top-Line Stagnation:** Net revenue increased from **$12.74M** (2023) to **$12.75M** (2024), representing a flat **+0.12% YoY growth**.
* **Intra-Year Monthly Patterns:** Monthly revenue averages ~$1.06M with low standard deviation. The volume trough occurs in February ($968K in 2023; $1.01M in 2024), while peak sales occur during holiday and promotional periods in January, March, and August (~$1.08M–$1.09M).

### 2. Geographic Performance & Market Concentration
Across the six operating countries, geographic revenue contribution demonstrates substantial variance:

| Country | Net Revenue (Total) | Contribution Share | Strategic Role |
| :--- | :--- | :--- | :--- |
| **Canada** | **$5.09M** | 19.95% | Core revenue driver; #1 volume market |
| **United Kingdom** | **$4.82M** | 18.93% | High-performing mature market |
| **United States** | **$4.34M** | 17.03% | Stable mid-tier contributor |
| **France** | **$4.34M** | 17.03% | Stable continental anchor |
| **Australia** | **$3.83M** | 15.05% | Emerging tier |
| **Germany** | **$3.06M** | 12.01% | Underpenetrated market (growth opportunity) |

* **Finding:** Canada and the UK collectively generate **38.88%** of total business revenue. Germany represents the smallest geographic footprint, generating 40% less revenue than Canada despite comparable pricing structures.

### 3. SKU Concentration & Profit Drivers
Analysis of the product portfolio reveals significant profit generation among high-cocoa offerings:
* **Top Profit SKU:** `Dark Chocolate 50%` generated **$709.7K** in gross profit, followed by `Truffle Chocolate 80%` (**$656.8K**).
* **Core Profit Cluster:** The Top 10 products generated over **$5.30M** in cumulative gross profit across the dataset, driven primarily by premium dark and truffle varieties.
* **White Chocolate Profile:** Lower individual margins per unit, but consistent mid-tier volume delivery (~$450K–$506K profit contribution per line).

### 4. Customer Segmentation & Promotional Inefficiency
Auditing customer purchasing behavior between loyalty tiers revealed identical metric distributions:
* **Volume Distribution:** 1,505,064 units sold to **Loyalty Members** (50.18%) vs. 1,494,525 units to **Standard Customers** (49.82%).
* **Basket Metrics:** Loyalty Member AOV is **$25.45** vs. **$25.52** for Standard Customers; discount depth is identical at **~5.6%**.
* **Analytical Takeaway:** The current loyalty program shows negligible incremental basket expansion or brand retention premium. It functions as an administrative tier rather than an active driver of customer lifetime value (LTV).

---

## Strategic Recommendations & Action Plan

### 1. Commercial & Marketing Optimization
* **Overhaul Loyalty Incentives:** Transition the loyalty tier from passive enrollment to tiered rewards (e.g., free gift packaging, early access to seasonal truffles, or points-based discounts unlocked only at $35+ order thresholds) to lift member AOV from the current $25.50 baseline.
* **Targeted Geographic Push (Germany):** Conduct store-level basket audits in Germany to identify whether lower revenue ($3.06M) stems from store count footprint limitations or lower regional brand conversion.

### 2. Product & Assortment Strategy
* **Double Down on Premium Dark/Truffle SKUs:** Protect supply chain priorities and optimize shelf placement for the 50%–80% cocoa categories, which account for the highest margins.
* **Product Bundling:** Bundle lower-performing white chocolate items with top-tier dark chocolate sellers as seasonal gift boxes to elevate gross margin dollars per transaction.

### 3. BI Governance & Scalability
* **Automated Data Quality Gates:** Implement automated ETL checks at the ingestion layer to flag unmatched transaction keys before data model ingestion.
* **Power BI Transition:** The existing Star Schema, explicit DAX measures, and metadata can be imported directly into Power BI Desktop (`.pbix`) for deployment on Power BI Service with automated scheduled refreshes.

---

## Dashboard Visual Layout & UX Design
* **Header / Filter Strip:** Dynamic controls (slicers) for `Year`, `Country`, and `Brand` with synchronized slicer report connections across all PivotTables.
* **Executive Scorecards:** Four dynamic KPI cards (`Net Revenue/YoY Revenue Growth`, `Gross Profit`, `Total Units Sold`, `Discount Depth/AOV`) built with floating linked shape containers and automated variance indicators.
* **Visual Canvas:** Built on a strict 12-column grid layout with zero-padding alignment, utilizing an executive light palette (canvas background `#F4F6F9` with `#E2E8F0` bordered card containers), balanced dual-accent series colors (`#5B9BD5` Steel Blue for CY / `#ED7D31` Coral-Amber for PY), suppressed field buttons, and removed gridlines for clean presentation fidelity.