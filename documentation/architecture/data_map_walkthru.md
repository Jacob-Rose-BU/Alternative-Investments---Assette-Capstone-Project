**Data Flow Overview: ESG Fund Fact Sheet Schema**

This document outlines the structure and relationships of the ESG fund database for Alternatives strategy fact sheets, reflecting the final pipeline implementation. It explains the role of each table and how they connect to support performance reporting, ESG scoring, and benchmark analysis.

# ESG Data Map – Flow Diagram

```mermaid
flowchart TD
    %% Product & Portfolio Spine
    PRODUCTMASTER["PRODUCTMASTER\nPK: PRODUCTCODE"] -->|PRODUCTCODE| PORTFOLIOGENERALINFORMATION["PORTFOLIOGENERALINFORMATION\nPK: PRODUCTCODE\nFK: PORTFOLIOCODE"]

    %% Portfolio → Benchmark Path
    PORTFOLIOGENERALINFORMATION -->|PORTFOLIOCODE| PORTFOLIOBENCHMARKASSOCIATION["PORTFOLIOBENCHMARKASSOCIATION\nPK: PORTFOLIOCODE\nFK: BENCHMARKCODE"]
    PORTFOLIOBENCHMARKASSOCIATION -->|BENCHMARKCODE| BENCHMARKGENERALINFORMATION["BENCHMARKGENERALINFORMATION\nPK: BENCHMARKCODE\nFK: SYMBOL"]
    BENCHMARKGENERALINFORMATION --> BENCHMARKCHARACTERISTICS["BENCHMARKCHARACTERISTICS\nPK: BENCHMARKCODE"]
    BENCHMARKGENERALINFORMATION --> BENCHMARKPERFORMANCE["BENCHMARKPERFORMANCE\nPK: BENCHMARKCODE"]

    %% Portfolio → Holdings → Security Path
    PORTFOLIOGENERALINFORMATION -->|PORTFOLIOCODE| HOLDINGSDETAILS["HOLDINGSDETAILS\nPK: PORTFOLIOCODE\nFK: TICKER"]
    HOLDINGSDETAILS -->|TICKER| SECURITY_MASTER["SECURITY_MASTER\nPK: TICKER"]
    SECURITY_MASTER --> SECURITY_PERFORMANCE_HISTORY["SECURITY_PERFORMANCE_HISTORY\nPK: TICKER, DATE"]

    %% Portfolio → Performance
    PORTFOLIOGENERALINFORMATION -->|PORTFOLIOCODE| PORTFOLIOPERFORMANCE["PORTFOLIOPERFORMANCE\nPK: PORTFOLIOCODE\nFK: BENCHMARKCODE"]
    PORTFOLIOPERFORMANCE -->|BENCHMARKCODE| BENCHMARKPERFORMANCE

    %% Independent Tables
    HR_INFORMATION["HR_INFORMATION\nPK: EMPLOYEEID"]
    DISCLOSURE_INFORMATION["DISCLOSURE_INFORMATION\nPK: DISCLOSUREID"]



### **PRODUCTMASTER**

* **Why it exists / function:** Entry point for products/funds; defines the strategy “container” that downstream tables hang off.

* **Primary key:** `PRODUCTCODE`.

* **Selected columns:** `PRODUCTCODE, PRODUCTNAME, STRATEGY`. esg\_pipeline\_table\_mapp…

* **Connections:** One‑to‑one with `PORTFOLIOGENERALINFORMATION` via `PRODUCTCODE`. Upstream of all portfolio‑level content.

---

### **PORTFOLIOGENERALINFORMATION**

* **Why:** Core portfolio metadata tied to the product; supplies category and links to a specific portfolio code used everywhere else.

* **Primary key:** `PRODUCTCODE`.

* **Foreign keys:** `PORTFOLIOCODE`.

* **Selected columns:** `PRODUCTCODE, PORTFOLIOCODE (FK), PORTFOLIOCATEGORY`. esg\_pipeline\_table\_mapp…

* **Connections:**

  * Upstream from `PORTFOLIOBENCHMARKASSOCIATION`, `HOLDINGSDETAILS`, and `PORTFOLIOPERFORMANCE` through `PORTFOLIOCODE`.

  * Joins to `PRODUCTMASTER` on `PRODUCTCODE`.

---

### **PORTFOLIOBENCHMARKASSOCIATION**

* **Why:** Associates a portfolio with one or more benchmarks (and rank if multiple).

* **Primary key:** `PORTFOLIOCODE`.

* **Foreign keys:** `BENCHMARKCODE`.

* **Selected columns:** `PORTFOLIOCODE (PK), BENCHMARKCODE (FK), RANK`. esg\_pipeline\_table\_mapp…

* **Connections:**

  * Links portfolios to `BENCHMARKGENERALINFORMATION` (and via that to benchmark stats/perf).

---

### **BENCHMARKGENERALINFORMATION**

* **Why:** Canonical benchmark master (names/codes); supports perf and characteristics used in factsheets.

* **Primary key:** `BENCHMARKCODE`.

* **Foreign keys:** `SYMBOL`.

* **Selected columns:** `BENCHMARKCODE (PK), SYMBOL (FK), NAME, ISBEGINOFDAYPERFORMANCE`. esg\_pipeline\_table\_mapp…

* **Connections:**

  * Parent to `BENCHMARKCHARACTERISTICS` and `BENCHMARKPERFORMANCE`.

  * Reached from a portfolio via `PORTFOLIOBENCHMARKASSOCIATION`.

---

### **BENCHMARKCHARACTERISTICS**

* **Why:** Footnote/metadata stats for the benchmark (category, PE/beta‑like stats, history date).

* **Primary key:** `BENCHMARKCODE`.

* **Selected columns:** `BENCHMARKCODE, CURRENCYCODE, CURRENCY, LANGUAGECODE, CATEGORY, CATEGORYNAME, CHARACTERISTICNAME, CHARACTERISTICDISPLAYNAME, STATISTICTYPE, CHARACTERISTICVALUE, ABBREVIATEDTEXT, HISTORYDATE`. esg\_pipeline\_table\_mapp…

* **Connections:** Child of `BENCHMARKGENERALINFORMATION` on `BENCHMARKCODE`.

---

### **BENCHMARKPERFORMANCE**

* **Why:** Time‑series values for the chosen benchmark(s) to compare against fund performance.

* **Primary key:** `BENCHMARKCODE`.

* **Selected columns:** `BENCHMARKCODE, PERFORMANCEDATATYPE, CURRENCYCODE, CURRENCY, PERFORMANCEFREQUENCY, VALUE, HISTORYDATE1, HISTORYDATE2`. esg\_pipeline\_table\_mapp…

* **Connections:** Child of `BENCHMARKGENERALINFORMATION`. Joins to portfolio series when plotting Fund vs Benchmark.

---

### **HOLDINGSDETAILS**

* **Why:** Position‑level holdings snapshot (weights, issue info) for allocations, top‑10, and geo/sector breakdowns.

* **Primary key:** `PORTFOLIOCODE`.

* **Foreign keys:** `TICKER` (to security).

* **Selected columns:** `PORTFOLIOCODE (PK), TICKER (FK), CURRENCYCODE, CURRENCY, ISSUENAME, QUANTITY, MARKETVALUE, PORTFOLIOWEIGHT, PRICE, ASSETCLASSNAME, ISSUETYPE, ISSUECOUNTRYCODE, ISSUECOUNTRY, HISTORYDATE`. esg\_pipeline\_table\_mapp…

* **Connections:**

  * Child of portfolio (`PORTFOLIOCODE`).

  * Joins to `SECURITY_MASTER` via `TICKER` for sector/region enrichment.

---

### **SECURITY\_MASTER**

* **Why:** Canonical security attributes (classification, geography, macro add‑ons like LCU and CO2 per capita).

* **Primary key:** `TICKER`.

* **Selected columns:** `TICKER, SHORTNAME, SECTOR, INDUSTRY, EXCHANGE, CURRENCY, MARKET_CAP, COUNTRY, COUNTRY_CODE, REGION, INCOME_GROUP, LCU_PER_USD, CO2_EMISSIONS_PER_CAPITA`. esg\_pipeline\_table\_mapp…

* **Connections:**

  * Parent of `SECURITY_PERFORMANCE_HISTORY`.

  * Joins from `HOLDINGSDETAILS` on `TICKER` (and country codes for geo charts).

---

### **SECURITY\_PERFORMANCE\_HISTORY**

* **Why:** Price/returns and ESG fields at the security‑date grain for rollups and distributions.

* **Primary key (composite):** `TICKER, DATE`.

* **Selected columns:** `TICKER, DATE, OPEN, HIGH, LOW, CLOSE, VOLUME, DIVIDENDS, STOCK_SPLITS, DATA_TYPE, ESGPERFORMANCE, TOTALESG, ENVIRONMENTSCORE, SOCIALSCORE, GOVERNANCESCORE, HIGHESTCONTROVERSY`. esg\_pipeline\_table\_mapp…

* **Connections:**

  * Child of `SECURITY_MASTER` via `TICKER`.

  * Indirectly supports portfolio‑level charts/tables (e.g., holdings ESG distribution).

---

### **PORTFOLIOPERFORMANCE**

* **Why:** Portfolio‑level returns, alpha, and an AVERAGE\_ESG\_SCORE time series for factsheet performance & ESG visuals.

* **Primary key:** `PORTFOLIOCODE` (functions as series identifier).

* **Selected columns:** `PORTFOLIOCODE, PORTFOLIOPERFORMANCE, PORTFOLIOFOCUS, AVERAGE_ESG_SCORE, BENCHMARKCODE, ALPHA, HISTORYDATE, CURRENCYCODE, CURRENCY, PERFORMANCECATEGORY, PERFORMANCEFREQUENCY, PERFORMANCETYPE, PERFORMANCEFACTOR`. esg\_pipeline\_table\_mapp…

* **Connections:**

  * Tied to `PORTFOLIOGENERALINFORMATION` via `PORTFOLIOCODE`.

  * Joins to `BENCHMARKPERFORMANCE` on aligned dates/`BENCHMARKCODE` for excess/alpha.

---

### **HR\_INFORMATION**

* **Why:** People data for “Team” sections (key personnel, titles, experience).

* **Selected columns:** `EMPLOYEEID, NAME, START_DATE, INFUSTRY_EXPERIENCE_YEARS, JOB_FUNCTION, TITLE, TEAM, EDUCATION, EMPLOYMENTTYPE, LOCATION, ISKEYPERSONNEL, STATUS`. esg\_pipeline\_table\_mapp…

* **Connections:** Independent; filtered for “Key Personnel,” then rendered in factsheets.

---

### **DISCLOSURE\_INFORMATION**

* **Why:** Central store for mandatory/non‑mandatory disclosures and their governance (effective/review dates, who reviewed).

* **Selected columns:** `DISCLOSUREID, TOPIC, EFFECTIVEDATE, DISCLOSURETYPE, APPLIESTO, LASTREVIEWDATE, REVIEWEDBY, DISCLOSUREREQUIREMENT, DISCLOSURESOURCE`. esg\_pipeline\_table\_mapp…

* **Connections:** Independent; filtered by type/context for the factsheet footers.

