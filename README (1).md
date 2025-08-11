
# Alternatives Capstone Project – Data Pipeline Documentation

## Overview
This project builds an automated data pipeline for generating fact sheet components in the Assette schema using multiple data sources (Yahoo Finance, World Bank, Synthetic Data, OpenRouter GPT API). The pipeline ingests, cleans, transforms, and outputs data into Snowflake tables and auxiliary formats (Excel/CSV) for downstream reporting.

Our primary goal: create a modular, reproducible process that covers **all major fact sheet areas**:
- Strategy & Fund Info
- Portfolio Performance
- Benchmark Data & Association
- Holdings & Allocation
- ESG & Country Data
- People & Disclosures

---

## Data Flow Diagram
Below is the high-level pipeline diagram showing ingestion sources, processing scripts (grouped by fact sheet area), and output targets:

![Data Flow Diagram](diagram.png)

---

## Code-to-Table Mapping

| Script | Tables Populated |
| --- | --- |
| productmaster.py | PRODUCTMASTER |
| portfoliogeneralinformation.py | PORTFOLIOGENERALINFORMATION |
| portfolioperformance.py | PORTFOLIOPERFORMANCE |
| yfinance_core_tables.py | HOLDINGSDETAILS, SECURITY_PERFORMANCE_HISTORY, BENCHMARKPERFORMANCE, BENCHMARKCHARACTERISTICS |
| security_country.py | SECURITY_MASTER |
| portfoliobenchmarkassociation.py | PORTFOLIOBENCHMARKASSOCIATION |
| hr_&_disclosure_files.py | HR_INFORMATION, DISCLOSURE_INFORMATION |

---

## Calculation Methodologies

### 1. Strategy & Fund Info
**Scripts:** `productmaster.py`, `portfoliogeneralinformation.py`  
Populates PRODUCTMASTER and PORTFOLIOGENERALINFORMATION using synthetic input and GPT API commentary where available. No numeric calculations; direct ingestion, validation, and mapping to Snowflake schema.

---

### 2. Portfolio Performance Calculation
**Script:** `portfolioperformance.py`  
1. **Data Source:** Adjusted close prices from Yahoo Finance for all portfolio holdings and benchmark tickers.
2. **Daily Returns:** Calculated as percentage change in adjusted close.
3. **Weighting:** Equal-weighted unless otherwise specified (no dependency on dated holdings snapshots).
4. **Cumulative Returns:** Daily returns chain-linked over reporting periods (1M, 3M, YTD, 1Y).
5. **Benchmark Comparison:** Same methodology applied to benchmark tickers for identical date ranges.
6. **Alpha:** Calculated as `Portfolio Return – Benchmark Return`.
7. **Output:** Returns, benchmark returns, alpha stored in `PORTFOLIOPERFORMANCE`.

---

### 3. Benchmark Performance Calculation
**Scripts:** `yfinance_core_tables.py` (benchmark data), `portfoliobenchmarkassociation.py` (mapping)  
- Ingests benchmark tickers from BENCHMARKGENERALINFORMATION.  
- Retrieves adjusted close prices from Yahoo Finance.  
- Computes daily and cumulative returns with the same methodology as portfolio performance.  
- Stores results in `BENCHMARKPERFORMANCE` and `BENCHMARKCHARACTERISTICS`.

---

### 4. Holdings & Allocation Calculation
**Script:** `yfinance_core_tables.py`  
- Ingests security-level market values from Yahoo Finance.  
- Calculates portfolio weights (Market Value ÷ Total Portfolio Value).  
- Aggregates by sector, asset class, and region (via join with SECURITY_MASTER).  
- Outputs `HOLDINGSDETAILS` and allocation breakdowns for fact sheet charts.

---

### 5. ESG & Country Data Calculation
**Scripts:** `security_country.py`, `yfinance_core_tables.py`  
1. **Primary ESG Scores:** Direct from Yahoo Finance at the security level.
2. **Fallback Logic:**  
   - Country-level scores from World Bank when security ESG is missing.  
   - Peer group averages or synthetic multipliers when neither is available.  
3. **Country Data:** GDP, CO₂ emissions per capita, other macro indicators from World Bank.
4. **Output Tables:** SECURITY_MASTER, SECURITY_PERFORMANCE_HISTORY.

---

### 6. People & Disclosures
**Script:** `hr_&_disclosure_files.py`  
- Direct ingestion from structured input files for HR_INFORMATION (team members, tenure, experience) and DISCLOSURE_INFORMATION (regulatory/legal notes).  
- No calculations — validation and formatting only.

---

## ESG Scoring Logic (Detailed)
When ESG scores are missing at the security level:
1. Pull country-level ESG from World Bank.
2. If country score missing, compute peer group averages by sector & region.
3. If still unavailable, apply synthetic multiplier based on similar assets.

Final ESG scores are stored in SECURITY_MASTER and linked to portfolio holdings for aggregation.

---

## Outputs
- **Snowflake:** Final fact sheet-ready tables in Assette schema.
- **Excel/CSV:** Certain disclosure and HR datasets for non-database consumers.
- **Python DataFrames:** Intermediate steps for validation/debugging.

---

## Visualization Examples

### Code-to-Table Mapping Diagram
![Code-to-Table Mapping](code_to_table_mapping.png)

### ESG Scoring Flow
![ESG Scoring Flow](esg_scoring_flow.png)

---

## Key Technical Notes
- All scripts modularized for independent execution & testing.
- Configurable API keys and Snowflake credentials via `.env`.
- Data transformations handled in pandas before Snowflake write.
- Pipeline designed for incremental updates and re-runs without overwriting historical data.

---

## Authors
Alternatives Capstone Project Team – Boston University MSBA 2025
