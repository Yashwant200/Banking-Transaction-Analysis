# 🏦 Banking Transaction Analysis

A comprehensive end-to-end data analytics project covering **Exploratory Data Analysis (EDA)** and an **interactive 3-page Power BI dashboard** built on personal banking transaction data spanning 3 years (2021–2024).

---

## 📌 Project Overview

| Parameter | Detail |
|---|---|
| **Domain** | Banking & Financial Analytics |
| **Dataset** | Personal Bank Transactions |
| **Period** | October 2021 – May 2024 |
| **Total Transactions** | 297 |
| **Tools Used** | Python, Power BI, DAX, Power Query |
| **Data Quality** | Zero null values |

---

## 📊 Dashboard Pages

### Page 1 — Account Overview
- **KPI Cards** — Total Deposit (₹27.35K), Total Withdrawal (₹26.82K), Net Flow (₹521.53), Closing Balance (₹444.53)
- **Stacked Bar Chart** — Year-wise Deposit vs Withdrawal comparison
- **Line Chart** — Yearly Closing Balance trend using `LASTNONBLANK` DAX

### Page 2 — Spending Patterns
- **Horizontal Bar Chart** — Total Withdrawal by Month Name
- **Transaction Receiver Table** — Top receivers ranked by withdrawal amount
- **Bar Chart** — Closing Balance by Month Name

### Page 3 — Deposit & Balance Analysis
- **Year Slicer** — Filter all visuals by year (2021–2024)
- **Horizontal Bar Chart** — Total Deposit by Month Name
- **Line Chart** — Balance trend across months
- **Scatter Plot** — Withdrawal vs Balance relationship with outlier analysis

---

## 🔍 Key Insights

1. **Net flow is only ₹521 positive over 3 years** — extremely thin financial margin
2. **2022 was the highest financial risk year** — despite the largest transaction volume, the year-end balance dropped to just ₹141 (88% decline from 2021)
3. **Self-transfers dominate at ₹19,808** — account was used primarily as a transit account
4. **September is peak month** for both deposits (₹8,500) and withdrawals (₹10,700) — indicating seasonal financial obligations
5. **Two outlier transactions (₹5,000 and ₹7,000)** nearly emptied the account — flagged as liquidity risk indicators
6. **2024 shows recovery** — lower withdrawals and stabilising balance, indicating improving financial discipline

---

## ⚙️ Technical Implementation

### Data Modelling
- Built a custom **Date Table** using DAX `CALENDAR(MIN(), MAX())` — dynamic and auto-adjusting
- Established **Many-to-One relationship** (`bank[DATE]` → `DateTable[Date]`)
- **Single cross-filter direction** following Power BI best practices
- Month Name sorted by Month Number for correct chronological ordering

### DAX Measures

```dax
-- Total Deposit
Total Deposit = SUM('bank'[DEPOSIT])

-- Total Withdrawal
Total Withdrawal = SUM('bank'[WITHDRWAL])

-- Net Flow
Net Flow = [Total Deposit] - [Total Withdrawal]

-- Closing Balance (most recent transaction)
Closing Balance = 
CALCULATE(
    LASTNONBLANK('bank'[BALANCE], 1),
    FILTER('bank', 'bank'[DATE] = MAX('bank'[DATE]))
)

-- Yearly Closing Balance
Yearly Closing Balance = 
CALCULATE(
    LASTNONBLANK('bank'[BALANCE], 1),
    LASTDATE('DateTable'[Date])
)

-- Outlier Flag
Outlier Flag = IF('bank'[WITHDRWAL] > 2000, "Outlier", "Normal")
```

### Date Table

```dax
DateTable = 
ADDCOLUMNS(
    CALENDAR(MIN('bank'[DATE]), MAX('bank'[DATE])),
    "Year",          YEAR([Date]),
    "Month Number",  MONTH([Date]),
    "Month Name",    FORMAT([Date], "MMMM"),
    "Short Month",   FORMAT([Date], "MMM"),
    "Quarter",       "Q" & QUARTER([Date]),
    "Week Number",   WEEKNUM([Date]),
    "Day Name",      FORMAT([Date], "DDDD"),
    "Short Day",     FORMAT([Date], "DDD"),
    "Year-Month",    FORMAT([Date], "YYYY-MMM"),
    "Is Weekend",    IF(WEEKDAY([Date], 2) >= 6, "Weekend", "Weekday")
)
```

---

## 📈 Scatter Plot — Outlier Analysis

The Withdrawal vs Balance scatter plot revealed a **clear negative correlation** across all years:

| Finding | Detail |
|---|---|
| Dense cluster (left) | Most transactions are small UPI payments under ₹26 |
| 2023 (highest density) | 128 transactions — most active financial year |
| 2024 (controlled) | Low withdrawals, stable mid-range balance |
| Outlier — ₹7,000 | Largest single withdrawal — near-zero balance after |
| Outlier — ₹5,000 | Second major stress transaction — risk flagged |

> Outliers were **not removed** — they were flagged using a DAX calculated column and investigated separately. In a real banking scenario, these would trigger a **liquidity risk alert or fraud review**.

---

## 🗂️ Project Structure

```
banking-transaction-analysis/
│
├── data/
│   └── bank.csv                        # Raw transaction dataset
│
├── dashboard/
│   └── Banking_Transaction_Analysis.pbix  # Power BI Dashboard file
│
├── report/
│   └── Banking_Transaction_Analysis_Report.docx  # Full analytical report
│
└── README.md
```

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| Python (Pandas, NumPy) | Data loading, EDA, transaction categorization |
| Power BI Desktop | Interactive 3-page dashboard |
| DAX | Custom measures, time intelligence, outlier flagging |
| Power Query | Data type correction (DateTime → Date) |
| CSV | Source data format |
