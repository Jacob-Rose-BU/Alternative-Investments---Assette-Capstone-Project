# PORTFOLIOBENCHMARKASSOCIATION Table Creation
Overview & Purpose
The PORTFOLIOBENCHMARKASSOCIATION script builds the mapping between portfolios and benchmarks (e.g., ESGD, EFIV). 
It supports relative performance reporting, benchmark-aware narratives, and compliance-ready factsheets. 
Each record pairs a PORTFOLIOCODE with a BENCHMARKCODE and assigns a RANK to represent primary/secondary associations.
Technical Design & Methodology
• Inputs: list of portfolio codes (from PORTFOLIOGENERALINFORMATION) and a curated list of benchmark tickers (from YAML or inline config).
• Logic: assign 1–3 benchmarks per portfolio with unique RANK ordering; optional deterministic seeding for reproducibility.
• Output: tidy, Snowflake-ready mapping table keyed by (PORTFOLIOCODE, BENCHMARKCODE) with RANK constraint.
Execution Instructions
1) Load portfolio codes from synthetic_alternatives_portfolios.csv.
2) Define benchmark tickers (e.g., ENRG, SHE, VOTE, ESGD, EFIV) or read from benchmark_config.yaml.
3) Run the association script; preview mapping.
4) Export to CSV or load to Snowflake and enforce uniqueness on (PORTFOLIOCODE, BENCHMARKCODE, RANK).
Outputs & Schema
Columns: PORTFOLIOCODE (PK in portfolio table), BENCHMARKCODE (FK to benchmark tables), RANK (1 = primary). 
This table is designed to be joined to benchmark performance for relative and excess return analytics.
Integration with Other Tables
• PORTFOLIOBENCHMARKASSOCIATION → BENCHMARKPERFORMANCE via BENCHMARKCODE + HISTORYDATE
• PORTFOLIOBENCHMARKASSOCIATION → PORTFOLIOPERFORMANCE via PORTFOLIOCODE (for relative metrics)
• Supports factsheet sections that display one or more benchmarks with clear precedence.
Testing & QA Use Cases
• Verify each portfolio has ≥1 benchmark and unique RANK sequence.
• Ensure ESG-themed portfolios map to ESG-relevant benchmarks where applicable.
• Test downstream joins to compute excess returns and benchmark-relative visuals.
Next Steps & Recommendations
• Add effective date ranges (EFFECTIVE_FROM/TO) for benchmark changes over time.
• Support blended benchmarks via weights and validate totals = 100% for each portfolio.
• Automate mapping rules based on portfolio style, region, or ESG profile to minimize manual curation.
