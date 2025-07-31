# Alternative-Investments---Assette-Capstone-Project

**Purpose of the project**
Assette is a SaaS provider to the institutional investment management industry. Our software is a
“unified content factory” to produce all sales and client service content.
This project aims to accelerate and finalize the creation of a functional “model client” simulating a
large, diversified asset manager such as BlackRock. The outcome will serve as a foundational
demonstration asset for Assette’s sales, quality assurance (QA), and research and development
(R&D) functions. Each model must support full-cycle content workflows encompassing ingestion,
processing, generation, and visualization.

**Objectives**
• Populate qualitative, quantitative, and disclosure content using AI and automation.
• Simulate multi-source data ingestion and display within Assette’s no-code platform and
Snowflake.

• Demonstrate real-world scenarios using synthetic Salesforce contacts and periodic
updates.
• Promote modular development, minimizing inter-team dependencies while enabling
collaboration.

**1. Data Format**
Each team must produce synthetic but functional data across multiple formats and
endpoints for input to Assette:
o One Excel source
o One CSV source
o One Snowflake data source (structured tables)
o One API-based data source (e.g., public REST endpoint)
o OpenAI-generated content (e.g., qualitative sections)
**2. Automation Requirements**
o All sources must be automatically refreshed or ingestible without manual steps.
o One quantitative data source must simulate daily updates (e.g., prices,
performance).
o Another must simulate weekly updates (e.g., benchmarks, flows).
o All remaining sources must be at least monthly.
o All data refresh logic must be fully automated and documented.
**3. Code Requirements**
All code must adhere to best practices and include:
o In-line comments, docstrings, and supporting documentation
o Meaningful unit and integration tests
o Basic performance optimization (e.g., indexing, caching)
o Structured error handling and logging
**4. Fact Sheet Artifacts**
Each team will produce a one-page fact sheet using Assette. Target Fact Sheets will be
provided, but in general, each team will be expected to produce:
o Quantitative Content

▪ Performance Table
▪ Top 10 Holdings or Exposures
▪ Statistics/Characteristics
▪ AUM and Key Fund Facts
▪ Sector breakdowns
▪ Contribution to return tables
▪ Risk/return scatterplots
o Qualitative Content
▪ Manager commentary
▪ Strategy descriptions
▪ Investment philosophy and objectives
o Disclosures and Footnotes
▪ Regulatory disclosures
▪ Risk and performance notices
▪ Custom footnotes for synthetic strategies
