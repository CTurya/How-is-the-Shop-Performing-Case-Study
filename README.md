# How-is-the-Shop-Performing-Case-Study
A beginner data analysis case study: cleaning, joining, and analyzing four raw tables from an online shop (electronics, accessories, wearables, home office, stationery, and gaming) to answer real business questions for a non-technical stakeholder — the shop's Head of Operations.

## Scenario

The shop wants a clear picture of how it performed from **January 2024 to
June 2026**. The brief: turn four raw CSV files into a short, plain-language
report with headline numbers, clean charts, and three actionable
recommendations — not a technical write-up for another analyst.

## The Data

| File | Rows | One row represents | Key columns |
|---|---|---|---|
| `customers.csv` | 10,000 | one customer | CustomerID, Age, City, SignupDate, CustomerSegment |
| `orders.csv` | 50,120 | one order line | OrderID, CustomerID, OrderDate, ProductID, Quantity, Discount, PaymentMethod, Status |
| `payments.csv` | 50,000 | one payment attempt | PaymentID, OrderID, PaymentDate, PaymentStatus |
| `products.csv` | 20 | one product | ProductID, ProductName, Category, UnitPrice |

**How they connect:** `orders` → `customers` (CustomerID), `orders` →
`products` (ProductID), `orders` → `payments` (OrderID).

## Approach

The analysis follows a standard 7-step framework:

1. **Get to know the data** — sized up each file, checked column types, wrote one question per file.
2. **Clean the data** — checked for missing values, duplicates, invalid numbers, inconsistent text, and broken links.
3. **Combine the tables** — joined orders → products → customers → payments, verifying the row count at each step.
4. **Create new columns** — `Revenue`, `Year`/`Month`/`YearMonth`, and a revenue-recognition rule.
5. **Answer the business questions** — six questions on revenue, growth, products, customers, order/payment health, and discounting.
6. **Visualize the findings** — one KPI card or chart per question, matched to what it's showing (line = trend, bar = comparison, KPI card = headline number).
7. **Tell the story** — an executive summary, a "so what" per chart, and three recommendations.

### Data cleaning decisions

| Table | Issue found | Decision |
|---|---|---|
| orders | 120 exact duplicate rows | Dropped |
| orders | 30 orders tied to a placeholder `CustomerID` (999999) | Dropped — not a real customer |
| orders | 35 rows missing `OrderDate` | Dropped — can't be placed on a monthly trend |
| orders | 105 rows with quantity ≤ 0 or missing | Dropped — invalid or unusable for revenue |
| orders | 221 rows missing `Discount` | Filled with 0 (assume no discount) |
| orders | 452 rows missing `PaymentMethod` | Filled with `"Unknown"`, row kept |
| customers | City spelled two ways (`tehran`/`Tehran`, `Mashad`/`Mashhad`) | Standardized to one spelling |
| customers | 119 rows missing `City` | Filled with `"Unknown"` |
| customers | 180 rows missing `Age` | Left as-is — not used by any business question |
| payments | Checked for duplicate `OrderID`s and orphaned records | None found — no cleaning needed |
| products | Checked for duplicates and unusual prices | None found — no cleaning needed |

**Revenue recognition rule:** `Revenue = Quantity × UnitPrice × (1 − Discount)`,
but only counted when `Status = "Completed"` **and** `PaymentStatus = "Paid"`.
Cancelled, returned, failed, and refunded orders are excluded from every
revenue figure — the shop never actually kept that money — but they're still
counted in order-volume and failure-rate metrics, since those questions are
about operational health, not revenue.

## Key Findings

1. **Revenue dropped ~32% in February 2026 and hasn't recovered.** Flat and
   steady through 2024–2025 (~$105K/month), then a sudden, sustained drop to
   ~$71K/month for 5 straight months — spread evenly across every category,
   city, and payment method, which points to a demand/traffic problem rather
   than a fulfillment one.
2. **VIP customers aren't outspending anyone.** New, Regular, and VIP
   customers each generate about $300 in revenue per customer — the VIP
   label isn't yet a growth lever.
3. **Bigger discounts don't grow the basket, they just cut revenue.** Units
   per order stay flat (~1.9) regardless of discount level, while a 30%
   discount cuts revenue per order by up to 26%.

| Metric | Value |
|---|---|
| Total revenue (Completed & Paid orders) | $2,988,379.80 |
| Revenue-generating orders | 42,696 |
| Average order value | $69.99 |
| Cancelled + Returned orders | 8.0% |
| Payment failure rate | 3.9% |
| Top category by revenue | Electronics ($1.51M) |
| Top city by revenue | Tehran ($822,878) |

## Recommendations

1. **Find and fix the Feb 2026 demand drop** — check marketing spend, site
   traffic, pricing changes, and competitor activity from that period first.
2. **Make VIP status pay off** — build real perks (early access, free
   shipping thresholds, bundles) and track revenue-per-customer by segment
   monthly.
3. **Replace blanket discounts with targeted offers** — shift spend to
   bundles and reserve deep discounts for genuine excess stock.

## Tools Used

- **Python / pandas** — data cleaning, joining, and analysis
- **SQL** — equivalent cleaning/analysis pipeline for a Databricks SQL warehouse
- **Databricks** — notebook environment, Delta table storage
- **PowerPoint (python-pptx)** — 8-slide stakeholder presentation
- **HTML / Chart.js** — interactive one-page dashboard
- **Excel (openpyxl)** — cleaned working file with formula-driven summary tables

## Repository Structure

```
├── README.md
├── data/
│   ├── customers.csv
│   ├── orders.csv
│   ├── payments.csv
│   └── products.csv
├── notebooks/
│   ├── shop_simple_databricks.py     # pandas pipeline (Databricks notebook)
│   └── shop_simple_databricks.sql    # SQL pipeline (Databricks SQL notebook)
├── deliverables/
│   ├── Shop_Performance_Review.pptx        # 8-slide presentation
│   ├── Shop_Performance_Working_File.xlsx  # cleaned data + formula summary
│   └── shop_dashboard.html                 # interactive dashboard
```

## How to Reproduce

1. Upload the four CSVs to a Databricks Volume (or local folder).
2. Open `notebooks/shop_simple_databricks.py` (or the `.sql` version) in
   Databricks and update the `folder` / file path at the top.
3. **Run All** from the first cell — cleaning, joining, and all six business
   questions run top to bottom, ending in one summary table.
4. Open `deliverables/shop_dashboard.html` in a browser, or
   `Shop_Performance_Review.pptx` for the slide version.

## Data Note

This is a synthetic dataset built for a data analysis case study (BrightLearn).
All customers, orders, and figures are fictional.

---
*Case study completed as part of a beginner data analysis exercise.*
