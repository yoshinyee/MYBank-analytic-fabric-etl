# 📊 Malaysia Banking Stock Analytics — Microsoft Fabric

An end-to-end **data engineering and analytics project** using **Microsoft Fabric, Python, Pandas, Delta Lake, and Power BI** to collect, transform, analyse, and visualise historical stock market data from selected Malaysian banking stocks.

The project implements a **Medallion Architecture (Bronze → Silver → Gold)** to create a structured and scalable data pipeline, followed by a Power BI dashboard for financial analysis and performance monitoring.

---

## 🚀 Project Overview

This project analyses five major Bursa Malaysia banking stocks:

| Stock       | Ticker    |
| ----------- | --------- |
| Maybank     | `1155.KL` |
| Public Bank | `1295.KL` |
| CIMB        | `1023.KL` |
| RHB Bank    | `1066.KL` |
| BIMB        | `5258.KL` |

Historical stock data is extracted from **Yahoo Finance**, processed through Microsoft Fabric's Lakehouse architecture, and transformed into analytical datasets for Power BI.

### Pipeline

```text
Yahoo Finance
      │
      ▼
┌─────────────┐
│   BRONZE    │
│ Raw Data    │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   SILVER    │
│ Clean Data  │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│    GOLD     │
│ Analytics   │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   Power BI  │
│  Dashboard  │
└─────────────┘
```

---

## 🏗️ ETL Architecture

The project follows the **Medallion Architecture** commonly used in modern data engineering.

### 🥉 Bronze Layer (EXTRACT)

The Bronze layer extracts raw historical stock market data using the `yfinance` Python library.

Responsibilities:

* Connect to Yahoo Finance
* Extract historical stock prices
* Process multiple Bursa Malaysia tickers
* Add ingestion metadata
* Store raw data as a Delta table in the Fabric Lakehouse
* Maintain a CSV backup in the Lakehouse Files section

### 🥈 Silver Layer (TRANSFORM)

The Silver layer cleans and standardises the Bronze data.

Data quality processes include:

* Date type conversion
* Numeric type conversion
* Duplicate removal
* Stock ticker-to-name mapping
* Missing price handling
* Volume cleaning
* Chronological sorting
* Silver processing timestamp

The cleaned dataset is stored as the `silver_bursa_banks` Lakehouse table.

### 🥇 Gold Layer (BUILD ANALYTIC LAYER)

The Gold layer transforms the Silver dataset into analytics-ready tables.

| Table | Description |
|---|---|
| `dim_stock` | One row per bank — ticker, name, exchange |
| `dim_date` | Full continuous calendar (not just trading days) for time-intelligence |
| `fact_stock_prices` | Daily OHLCV + all derived metrics (see below) |
| `fact_stock_correlation` | Pairwise correlation of daily returns, long format for heatmap rendering |

Star-schema relationships: `dim_date (1) → fact_stock_prices (*) ← (1) dim_stock`.
`fact_stock_correlation` is intentionally left unjoined — its own `stock_name_a`/`stock_name_b`
columns are used directly in visuals.

---

## 📈 Financial Metrics

The Gold layer calculates several technical and performance indicators.

- **Daily return %** and **rolling volatility** (7-day, 30-day) — based on `adj_close` to
  correctly account for dividends and stock splits
- **SMA 20 & SMA 50** — trend indicators, overlaid on price
- **RSI (14-day)** — momentum/overbought-oversold indicator, with divide-by-zero protection;
  early/insufficient-history days are left blank rather than filled with a fabricated neutral
  value
- **Indexed price (base RM100)** — normalizes all five banks to a common starting point so
  returns are directly comparable regardless of each bank's actual share price
- **Max drawdown** — worst peak-to-trough decline per bank
- **Correlation matrix** — pairwise correlation of daily returns across all five banks

---

## 📊 Power BI Dashboard

The Gold dataset is connected to **Power BI** to create an interactive banking stock analytics dashboard.

The dashboard includes:

### 1. Stock Detail
Filterable by bank and year — price with SMA 20/SMA 50 overlay, and RSI with
overbought/oversold reference lines (30/70).

### 2. Correlation
Heatmap matrix of daily-return correlation across all five banks, paired with a bar chart of
each bank's average correlation to the group, plus narrative insight callouts.

### 3. Overview (Indexed Return)
RM100-indexed price comparison across all five banks, with best/worst performer callouts and
a written summary of the sector's trajectory.

## 💡 Key Insights

Based on the Power BI analysis:

- **All five banks show positive correlation** (0.16–0.59), consistent with shared exposure
  to interest rates, market sentiment, and banking-sector regulation.
- **BIMB stands apart from conventional banks** (~0.30 avg correlation vs ~0.45–0.58 for the
  others) — likely a result of its Islamic banking model (Shariah-compliant, profit-sharing)
  operating under different principles than conventional interest-based banking.
- **Maybank and CIMB are the most sector-representative banks**, both showing the highest
  average correlation (~0.59) with the rest of the group — consistent with their position as
  Malaysia's two largest banking groups by market cap.
- **Diversification takeaway:** pairing BIMB with a conventional bank offers better
  risk-balanced diversification than combining two conventional banks together.
- **RHB Bank leads on period-end indexed value**, reaching a peak indexed price of 188.33 in
  2026, while **CIMB was the most consistent top performer** by year-over-year return in
  2024 and 2025 before RHB overtook it.
- **BIMB was the weakest performer in 2 of the 3 years observed** (2025, 2026) — again
  plausibly tied to its differing business model.
- The sector shows a **strong overall upward trend**, with a recurring pattern of stronger
  price appreciation in Q4 (November/December) across the observed years.

> **Note:** These findings are based on historical data and are intended for analytical and educational purposes only. They should not be interpreted as financial advice.

---

## 🛠️ Technology Stack

| Technology           | Purpose                                   |
| -------------------- | ----------------------------------------- |
| **Python**           | Data extraction and transformation        |
| **Microsoft Fabric** | Data engineering platform                 |
| **Power BI**         | Data visualisation and dashboard          |

---

## 📂 Project Structure

```text
MYBank-analytic-fabric-etl/
│
├── README.md
│
├── LICENSE
│
├── notebooks/
│   ├── Bronze_Fabric.ipynb
│   ├── Silver_Fabric.ipynb
│   └── Gold_Fabric.ipynb
│
├── report/
│   └── MYBank_Report.pdf
│
└── screenshot/
    ├── MYBank_Lakehouse_SQL.png
    ├── MYBank_Report_Correlation.png
    ├── MYBank_Report_Indicator.png
    └── MYBank_Report_Overview.png


```

> Raw market data is not included in this repository. The Bronze notebook retrieves the data directly from Yahoo Finance.

---


## 📌 Why This Project?

This project demonstrates how a raw financial dataset can be transformed into a complete analytics solution using a modern data platform.

It combines:

**Data Engineering + Data Modelling + Financial Analytics + Business Intelligence**

The project was designed to demonstrate practical experience with:

* ETL pipeline development
* Medallion Architecture
* Lakehouse data engineering
* Data quality and transformation
* STAR Schema modelling
* Financial KPI development
* Power BI visualisation
* Business insight generation

---

## 🔮 Future Improvements

Potential improvements include:

* Add more Bursa Malaysia sectors
* Add dividend yield and dividend history
* Add market benchmark comparison such as FBM KLCI
* Implement incremental data loading instead of overwrite
* Add automated pipeline scheduling
* Add data quality monitoring
* Add Power BI forecasting
* Add portfolio optimisation
* Add Sharpe ratio and other risk-adjusted metrics
* Introduce automated alerts for unusual volatility
* Deploy the complete pipeline using Microsoft Fabric Data Factory

---

## ⚠️ Disclaimer

This project is created for **educational and portfolio purposes**.

The analysis is based on historical market data and should not be considered financial advice or a recommendation to buy or sell any security.

---

## 👩‍💻 Author

Built as a portfolio project demonstrating an end-to-end Fabric + Power BI pipeline — from
raw ingestion through cleaning, dimensional modeling, and interactive reporting.

---

## 📄 License

This project is licensed under the [MIT License]

---

⭐ If you find this project useful, feel free to star the repository!
