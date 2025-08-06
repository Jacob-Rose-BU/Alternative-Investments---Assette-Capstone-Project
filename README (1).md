
# Assette ESG Data Pipeline – Alternatives Strategy

## Overview

This project was developed by a student-led team in Boston University’s MSBA Capstone Program in partnership with **Assette**, a leading client reporting platform for asset managers. Our team focused on the **Alternatives** vertical, designing a modular and scalable data pipeline that ingests real and synthetic financial data, processes it through a production-ready Python workflow, and populates a Snowflake database for downstream use in ESG-focused investment fact sheets.

Our solution enables Assette and other asset managers to automate the creation of data-rich fund reporting outputs—even when working with limited or unstructured ESG datasets.

---

## Project Objectives

- Ingest performance, ESG, and security data from public APIs and synthetic generators
- Normalize and transform raw inputs into a Snowflake-ready data schema
- Integrate AI-generated fund commentary using OpenRouter GPT
- Build a data architecture that supports ESG integration across alternative investment portfolios
- Enable automation of institutional-quality factsheets, including performance and sustainability metrics

---

## Data Sources & Pipeline Architecture

| Source            | Type         | Purpose                                       |
|-------------------|--------------|-----------------------------------------------|
| `Yahoo Finance`   | API          | Price history, tickers, and limited ESG scores |
| `World Bank API`  | API          | Country-level ESG indicators and benchmarks    |
| `OpenRouter GPT`  | AI API       | Narrative generation (descriptions, commentary)|

The pipeline is modular, repeatable, and configured using YAML files for flexibility and scaling across additional funds or asset classes.

---

## Core Tables Created & Data Flow

### 1. `PRODUCTMASTER`  
Central registry of investment products.  
Feeds: `PORTFOLIOGENERALINFORMATION`, `PRODUCTATTRIBUTES`

### 2. `PRODUCTATTRIBUTES`  
Fund-specific descriptors including ESG averages and scores.  
Feeds: ESG reporting, narrative enrichment

### 3. `PORTFOLIOGENERALINFORMATION`  
Metadata for each portfolio (e.g., inception, code).  
Feeds: `HOLDINGSDETAILS`, `PORTFOLIOPERFORMANCE`, `PORTFOLIOBENCHMARKASSOCIATION`

### 4. `HOLDINGSDETAILS`  
Security-level holdings with portfolio weights.  
Feeds: `PORTFOLIOPERFORMANCE`, ESG rollups

### 5. `SECURITY_MASTER`  
Master data for all securities (ESG scores, sector, etc.).  
Feeds: ESG scoring, holdings enrichment

### 6. `SECURITY_PERFORMANCE_HISTORY`  
Historical stock price data.  
Feeds: security-level returns and volatility

### 7. `PORTFOLIOPERFORMANCE`  
Time-series performance per portfolio.  
Feeds: benchmark comparison, factsheets

### 8. `PORTFOLIOBENCHMARKASSOCIATION`  
Associates portfolios to benchmarks.  
Feeds: `BENCHMARKGENERALINFORMATION`, `BENCHMARKPERFORMANCE`

### 9. `BENCHMARKGENERALINFORMATION`  
Benchmark metadata (name, type).  
Feeds: `BENCHMARKPERFORMANCE`, `BENCHMARKCHARACTERISTICS`

### 10. `BENCHMARKPERFORMANCE`  
Historical benchmark returns.  
Feeds: comparison to `PORTFOLIOPERFORMANCE`

### 11. `BENCHMARKCHARACTERISTICS`  
Quantitative and qualitative benchmark attributes.  
Feeds: factsheet context

---

## Analysis & Results

- Top 10 securities by weight and performance generated using custom logic
- ESG scores evaluated for each holding; aggregated at the portfolio level
- Comparisons against S&P ESG Index and ESG Leaders Index
- Summary performance and ESG indicators prepped for visual fact sheet integration

---

## Lessons Learned

- Strong schema modeling early on made integration easier across teams
- YAML-based config allowed rapid scaling across fund variations
- Synthetic ESG scoring must balance realism with interpretability
- Documentation and consistent key usage (`PRODUCTCODE`, `PORTFOLIOCODE`) are critical for pipeline transparency
