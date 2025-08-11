# PORTFOLIOGENERALINFORMATION Table Synthetic Data Generation
Overview & Purpose
The PORTFOLIOGENERALINFORMATION script creates synthetic portfolio-level metadata aligned to alternatives products. 
It links portfolios to products using PRODUCTCODE and provides key attributes for factsheets (name, investment style, category, currency, inception/termination). 
PORTFOLIOCODE serves as the primary key and is reused across holdings, performance, attribution, and benchmark association tables.
Technical Design & Methodology
• YAML-driven lists for investment_styles, portfolio_categories, currency_map; single asset class focus maintained upstream in PRODUCTMASTER.
• PRODUCTCODEs loaded from product_codes.csv to guarantee downstream joinability.
• Faker-built names follow institutional naming (“<Company> <Style> Strategy”).
• Date logic enforces OPENDATE < PERFORMANCEINCEPTIONDATE ≤ TERMINATIONDATE (or allow NULL termination for active portfolios).
• Output standardized for Snowflake: uppercase columns, ISO dates.
Execution Instructions
1) Ensure portfolio_config.yaml (lists) and product_codes.csv (FK continuity) are present.
2) Execute the script to create NUM_ROWS portfolios; preview DataFrame.
3) Save to CSV (synthetic_alternatives_portfolios.csv) and/or load to Snowflake with MERGE on PORTFOLIOCODE.
4) Optionally, generate qualitative text later using PORTFOLIOCODE + PRODUCTNAME for commentary tables.
Outputs & Schema
Columns: PORTFOLIOCODE, NAME, INVESTMENTSTYLE, PORTFOLIOCATEGORY, OPENDATE, PERFORMANCEINCEPTIONDATE, TERMINATIONDATE, 
BASECURRENCYCODE, BASECURRENCYNAME, ISBEGINOFDAYPERFORMANCE, PRODUCTCODE. 
These fields directly support factsheet profile sections, date banners, and currency displays.
Integration with Other Tables
• PORTFOLIOGENERALINFORMATION → HOLDINGSDETAILS via PORTFOLIOCODE
• PORTFOLIOGENERALINFORMATION → PORTFOLIOPERFORMANCE via PORTFOLIOCODE
• PORTFOLIOGENERALINFORMATION → PORTFOLIOBENCHMARKASSOCIATION via PORTFOLIOCODE
This forms the spine for portfolio-level analytics and presentation.
Testing & QA Use Cases
• Ensure no orphan PRODUCTCODEs and that currency codes/names pair correctly.
• Validate name/formatting constraints (length, special characters) for UI templates.
• Check logical date ordering and edge cases (recent inception, terminated portfolios).
Next Steps & Recommendations
• Add regulatory identifiers (if applicable) and region tags for filtering.
• Implement change capture to audit metadata updates over time.
• Auto-generate sample benchmark associations for new portfolios to enable end-to-end QA quickly.
