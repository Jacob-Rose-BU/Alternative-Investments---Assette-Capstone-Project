# Portfolio Performance Pipeline

This script calculates and compiles portfolio-level performance metrics using holdings data and historical security prices. It also derives average ESG scores for the portfolio based on constituent securities, enabling performance vs ESG analysis for fact sheets. All outputs are structured for Snowflake ingestion and follow the standardized fact sheet schema.

---

## Execution Instructions
1. Ensure holdings details and corresponding security performance data are available in memory or loaded from Snowflake/CSV.  
2. Update Snowflake credentials in `.env` or secure config if writing outputs directly.  
3. Run the script after the holdings and price data have been generated.  
4. Use `load_to_snowflake_merge()` for ingestion to prevent duplication.

---

## File Roadmap
- Load holdings data for the target portfolio(s)  
- Merge holdings with historical price data to compute security-level returns  
- Aggregate returns to portfolio level based on portfolio weights  
- Calculate portfolio average ESG score from constituent ESG scores  
- Align portfolio performance history with benchmark history for comparison  
- Output `PORTFOLIOPERFORMANCE`-ready DataFrame for Snowflake

---

## Output Summary
- **Portfolio Performance Table** – Daily, monthly, and annualized return factors for the portfolio  
- **Average ESG Score** – Weighted ESG score across holdings for each reporting date  
- **Alpha/Excess Return Metrics** – Derived performance relative to assigned benchmark(s)

---

## Next Steps & Future Improvements
- Add multi-benchmark support with automated mapping from `PORTFOLIOBENCHMARKASSOCIATION`  
- Introduce rolling return windows (YTD, 1Y, 3Y)  
- Integrate risk-adjusted performance metrics (Sharpe, Sortino)  
- Extend ESG score tracking to include sector- or country-level attribution
