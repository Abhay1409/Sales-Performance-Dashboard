# ═══════════════════════════════════════════════════════════════════════════
#  STANDARD OPERATING PROCEDURE (SOP)
#  Sales Performance Dashboard — Maintenance & Reporting
#  Version: 1.0  |  Author: Abhay Pratap Singh  |  Date: March 2026
# ═══════════════════════════════════════════════════════════════════════════

---

## 1. PURPOSE

This SOP defines the monthly workflow for refreshing, validating, and
distributing the Sales Performance Analysis Dashboard built in Power BI.
Following this procedure ensures consistent, accurate reporting with
minimal manual effort (target: < 30 minutes/month).

---

## 2. FOLDER STRUCTURE

    C:\SalesDashboard\
    ├── raw_data\              ← Drop new monthly CSV exports here
    │   └── raw_sales_data.csv
    ├── processed\
    │   └── cleaned_sales_data.csv   ← Overwrite this file each month
    ├── scripts\
    │   ├── 01_generate_data.py      ← (initial setup only)
    │   └── 02_clean_data.py         ← Run every month
    ├── powerbi\
    │   └── SalesPerformance.pbix    ← Main dashboard file
    ├── reports\
    │   └── cleaning_report.txt      ← Auto-generated each run
    └── SOP.md                       ← This file

---

## 3. PROJECT NUMBERS (Baseline — March 2026)

| Metric | Value |
|---|---|
| Total Transactions | 52,000+ rows |
| Date Range | Jan 2022 – Dec 2024 (3 years) |
| Total Revenue | ₹113.27 Cr |
| Total Profit | ₹20.01 Cr |
| Total Cost | ₹93.26 Cr |
| Profit Margin | 17.67% |
| Total Orders | 52,000 |
| Avg Order Value | ₹21,780 |
| Avg Discount | 9% |
| Missing Values Resolved | 2,500+ |
| Duplicates Removed | 200 |
| Product Categories | 5 |
| Regions | 4 |
| Unique Products | 25 |
| Salespersons | 10 |

---

## 4. MONTHLY REFRESH CHECKLIST

### Step 1 — Receive New Data (Day 1 of month)
- [ ] Download raw transaction export from ERP/CRM
- [ ] Save to: `C:\SalesDashboard\raw_data\raw_sales_data.csv`
- [ ] Confirm row count in source system matches file row count

### Step 2 — Run Cleaning Script (Day 1, ~5 min)
```
cd C:\SalesDashboard\scripts
python 02_clean_data.py
```
- [ ] Verify `cleaning_report.txt` shows 0 remaining nulls
- [ ] Confirm output row count is within ±5% of expected
- [ ] Review "TOP 3 UNDERPERFORMERS" section for changes

### Step 3 — Refresh Power BI (Day 1, ~2 min)
- [ ] Open `SalesPerformance.pbix`
- [ ] Click **Home → Refresh**
- [ ] Confirm all 5 queries load without error (bottom status bar)
- [ ] Verify KPI cards show updated month/year

### Step 4 — KPI Validation (Day 1, ~10 min)
Run these spot-checks before distributing:

| Check | Method | Expected |
|---|---|---|
| Total Revenue matches | Compare card vs SUM in Excel | ±0.1% of ₹113.27 Cr |
| Profit Margin correct | Check card value | ~17.67% |
| Avg Order Value correct | Check card value | ~₹21,780 |
| Total Orders correct | Check card value | ~52,000 |
| No negative profits | Filter Profit_Flag = "Loss" | < 0.5% rows |
| Date range current | Check date slicer max date | Current month |
| Top region consistent | Cross-check with CRM | Same top region |

### Step 5 — Export & Distribute (Day 2)
- [ ] File → Export → PDF → Save to `reports\Dashboard_MMYYYY.pdf`
- [ ] Email to stakeholders (use template in Section 7)
- [ ] Update SharePoint/Teams with new .pbix file

---

## 5. DATA DICTIONARY

| Column | Type | Description |
|---|---|---|
| Order_ID | Text | Unique order identifier (ORDxxxxxx) |
| Order_Date | Date | Transaction date (YYYY-MM-DD) |
| Region | Text | North / South / East / West |
| Category | Text | Electronics / Furniture / Clothing / Food & Beverage / Sports |
| Product_Name | Text | Product description (25 unique products) |
| Quantity | Integer | Units sold (always > 0) |
| Unit_Price | Decimal | Price per unit in INR |
| Discount_Pct | Decimal | Discount applied (0.00 – 0.25), avg 9% |
| Sale_Amount | Decimal | Net revenue = Unit_Price × Qty × (1 – Discount_Pct) |
| Cost_Amount | Decimal | Cost of goods sold |
| Profit_Amount | Decimal | Sale_Amount – Cost_Amount |
| Profit_Margin | Decimal | Profit_Amount / Sale_Amount (avg 17.67%) |
| Salesperson | Text | Name of sales representative (10 reps) |
| Customer_ID | Text | Customer identifier (CUSTxxxx) |
| Channel | Text | Online / Retail / Wholesale |
| Year | Integer | Calendar year (2022, 2023, 2024) |
| Month | Integer | Calendar month (1–12) |
| Quarter | Text | Q1–Q4 |
| Month_Name | Text | Jan–Dec |

---

## 6. KPI DEFINITIONS

| KPI | Formula | Baseline Value |
|---|---|---|
| Total Revenue | SUM(Sale_Amount) | ₹113.27 Cr |
| Total Profit | SUM(Profit_Amount) | ₹20.01 Cr |
| Total Cost | SUM(Cost_Amount) | ₹93.26 Cr |
| Profit Margin % | Profit / Revenue | 17.67% |
| Total Orders | COUNTROWS(SalesData) | 52,000 |
| Avg Order Value | Revenue / Orders | ₹21,780 |
| Avg Discount % | AVERAGE(Discount_Pct) | 9% |
| Unique Customers | DISTINCTCOUNT(Customer_ID) | ~9,000 |

---

## 7. DASHBOARD PAGES SUMMARY

| Page | Name | Key Visuals |
|---|---|---|
| 1 | Overview | 5 KPI Cards, Revenue Trend, Category Donut, Region Bar, Top 10 Products |
| 2 | Profitability | Profit Trend, Underperformer Table, Discount vs Margin Scatter |
| 3 | Regional Analysis | Salesperson Bar, Channel Mix, Region Donut |
| 4 | Product Analysis | Category Margin Bar, Product Scatter, Full Product Table, Channel Pie |

---

## 8. POWER BI MODEL STRUCTURE

| Table | Type | Rows | Purpose |
|---|---|---|---|
| SalesData | Fact Table | 52,000+ | Main transaction data |
| DimDate | Dimension | 1,096 | Date intelligence (YoY, MTD, QTD) |
| DimProduct | Dimension | 25 | Product & category lookup |
| MonthlySummary | Aggregated | 36 | Pre-aggregated monthly KPIs |
| RegionSummary | Aggregated | 4 | Pre-aggregated regional KPIs |

---

## 9. DATA CLEANING SUMMARY

| Issue | Count | Resolution |
|---|---|---|
| Missing Discount_Pct | 700 | Filled with region median |
| Missing Profit_Margin | 350 | Filled with category median |
| Missing Salesperson | 600 | Filled with "Unknown" |
| Missing Channel | 400 | Filled with mode value |
| Missing Customer_ID | 500 | Filled with "CUST0000" |
| Duplicate rows | 200 | Dropped exact duplicates |
| Negative quantities | 5 | Removed invalid rows |
| Mixed date formats | 50 | Standardised to YYYY-MM-DD |
| **Total issues resolved** | **2,805** | |

---

## 10. STAKEHOLDER EMAIL TEMPLATE

Subject: [Monthly] Sales Dashboard Updated — [Month Year]

Hi Team,

The Sales Performance Dashboard has been refreshed with [Month Year] data.

KEY HIGHLIGHTS:
• Total Revenue  : ₹113.27 Cr (▲/▼ X% vs last month)
• Total Profit   : ₹20.01 Cr
• Profit Margin  : 17.67%
• Total Orders   : 52,000
• Avg Order Value: ₹21,780
• Top Region     : [Region]
• Top Category   : Electronics (61.4% revenue share)

⚠ ATTENTION ITEMS:
• [List any underperformers or anomalies found]

Dashboard link: [SharePoint/Teams link]
PDF Report    : See attached

Regards,
[Your Name]

---

## 11. TROUBLESHOOTING

| Problem | Likely Cause | Fix |
|---|---|---|
| Power BI refresh fails | File path changed | Update Source path in Power Query |
| Null values remain after clean | New column added to source | Add column to cleaning script |
| Date slicer shows wrong range | New data outside DimDate range | Update DimDate Start/End in Power Query |
| Revenue mismatch | Duplicate rows in raw file | Re-run cleaning script; check dedup step |
| Margin shows 0 | Missing Profit_Margin column | Re-derive using Step 9 in clean script |
| Underperformer Flag all On Track | Threshold too low | Adjust VAR threshold in DAX measure |
| Canvas not responding | Graphics acceleration issue | File → Options → Disable hardware acceleration |

---

## 12. VERSION HISTORY

| Version | Date | Change |
|---|---|---|
| 1.0 | Nov 2025 | Initial version — 52,000 rows, 3 years, 5 categories, 4 regions |
| 1.1 | Nov 2025 | Updated with actual project numbers — ₹113.27 Cr revenue, 17.67% margin |

---

*End of SOP*
