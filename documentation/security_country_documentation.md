# Security Master – Country Mapping Pipeline

This script enriches the Security Master table by adding country and region metadata for each security, enabling geographic breakdowns in fact sheets. It joins security identifiers to country codes and region mappings, ensuring compatibility with Assette’s geographic allocation tables.

---

## Execution Instructions
1. Load Security Master data from yfinance or other upstream source.  
2. Ensure country code mappings (ISO or custom) are available in CSV or Snowflake.  
3. Run the script prior to geographic breakdown or allocation calculations.  
4. Use `load_to_snowflake_merge()` for ingestion to preserve existing records.

---

## File Roadmap
- Extract security list from Security Master DataFrame  
- Map each security’s country code to full country name and region classification  
- Handle missing or unmapped countries via fallback logic (e.g., “Unknown”)  
- Output enriched Security Master table to Snowflake with updated country/region columns

---

## Output Summary
- **Security Master Table (Enriched)** – Includes `ISSUECOUNTRY`, `REGION`, and original identifiers  
- **Country Lookup Reference** – Mapping table between security IDs and assigned country/region  
- **Geographic Breakdown Inputs** – Ready for use in holdings allocation charts

---

## Next Steps & Future Improvements
- Automate country mapping updates from ISO registry or World Bank API  
- Add sub-region classification for finer granularity (e.g., Western Europe vs Eastern Europe)  
- Validate mappings against benchmark holdings to ensure consistency in fact sheet visuals
