# HR & Disclosures – Synthetically Generated Data

## Overview & Purpose
This notebook generates synthetic datasets required for ESG equity fund fact sheets—specifically for the **team summary** and **compliance disclosures** sections.

- **HR Data:** Simulates the fields typically provided by HR (as advised by the business stakeholder), including employee **names**, **titles**, **tenure**, and **team affiliations**. The output is saved as CSV and prepared for GPT-based summary generation to describe the team managing each fund.  
- **Compliance Footnotes:** Produces a reusable library of **regulatory disclosure footnotes**. Each footnote is assigned a unique **DisclosureID** and labeled as **mandatory** or **optional**. For each fund, the compliance team provides a list of applicable DisclosureIDs, which are then assembled into the fund’s factsheet.

## Execution Instructions
1. Run the notebook sequentially from top to bottom to generate and export HR and Compliance data.  
2. Outputs are written locally and will be visible in the file panel (or saved to your configured output directory).

## File Roadmap
- **HR Data:** Faker-generated employee names, titles, tenure, and team affiliations.  
- **Compliance Footnotes:** Reusable disclosures labeled with `DisclosureID` and `category` (mandatory/optional).

## Outputs
- `synthetic_employee_hr.csv`  
- `full_compliance_footnotes.csv`

## Next Steps
- Generate a **GPT-based Team Summary** using the HR CSV.  
- Build **Fund-Specific Disclosure** DataFrames by selecting DisclosureIDs per fund.

## Future Improvements: Synthetic Data Enhancements
- Add variability in **job title seniority** and tenure distributions.  
- Group HR data by **team/product alignment** for more realistic org structures.  
