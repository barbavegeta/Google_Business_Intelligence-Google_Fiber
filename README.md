# Google Fiber BI Project - Market Contact Analysis
This repository contains my work for the **Google Business Intelligence certificate - Google Fiber capstone project**.
The goal is to:
- Consolidate customer contact data from three regional markets into a single **target table**.
- Analyse patterns in **contact volume** and **repeat contacts**.
- Build a **Tableau dashboard** that helps stakeholders understand where service issues are concentrated and how they evolve over time.
The original activity is framed as a BI hiring exercise for the Google Fiber customer support team.

**Tech stack**: BigQuery · SQL · Tableau · Google Sheets  

---
## Tableau dashboard
Public dashboard:
- **Google Fiber - Customer Contacts Dashboard**  
  https://public.tableau.com/app/profile/salvatore.barbagallo/viz/GoogleFiber-GoogleBusinessIntelligenceCertificate/Dashboard1
  
The dashboard surfaces:
- Total contacts and repeat contacts over time.
- Comparison of markets and contact types.
- Drill-down views to identify where follow‑up action is needed.
---
## Data
The raw data is provided as three separate CSVs, one per market:
- `market_1.csv`
- `market_2.csv`
- `market_3.csv`
Each file has the same schema, including columns such as:
- `date_created` - date of the contact.
- `contacts_n`, `contacts_n_1` … `contacts_n_7` - contact counts (initial + follow‑ups).
- `new_type` - contact type / channel.
- `new_market` - market identifier.
After cleaning and merging, all rows are stored in a single **target table**:
- `g_fiber.csv` - unified table containing all markets, used as the primary Tableau data source.
> Note: Column names follow the structure used in the course exemplar; no PII is present.
---
## Repository structure
```text
Google_Business_Intelligence---Google-Fiber/
├── data/
│   ├── market_1.csv
│   ├── market_2.csv
│   ├── market_3.csv
│   └── g_fiber.csv          # final combined table
├── sql/
│   └── g_fiber.sql
├── tableau/
│   └── Google_Fiber_Dashboard.twbx   # optional: packaged workbook
└── README.md
```
---
## Creating the target table (BigQuery / SQL)
The target table is built by vertically stacking the three market tables with `UNION ALL` (the schemas are already aligned):
```sql
CREATE TABLE `google_bi.fiber.g_fiber` AS
SELECT
  date_created,
  contacts_n,
  contacts_n_1,
  contacts_n_2,
  contacts_n_3,
  contacts_n_4,
  contacts_n_5,
  contacts_n_6,
  contacts_n_7,
  new_type,
  new_market
FROM `google_bi.fiber.market_1`
UNION ALL
SELECT
  date_created,
  contacts_n,
  contacts_n_1,
  contacts_n_2,
  contacts_n_3,
  contacts_n_4,
  contacts_n_5,
  contacts_n_6,
  contacts_n_7,
  new_type,
  new_market
FROM `google_bi.fiber.market_2`
UNION ALL
SELECT
  date_created,
  contacts_n,
  contacts_n_1,
  contacts_n_2,
  contacts_n_3,
  contacts_n_4,
  contacts_n_5,
  contacts_n_6,
  contacts_n_7,
  new_type,
  new_market
FROM `google_bi.fiber.market_3`;
```
Save this query as:
```text
sql/g_fiber.sql
```  
Run it in BigQuery (or another SQL engine with equivalent tables) to regenerate `g_fiber`.  

---
## How to reproduce the analysis
1. **Load raw CSVs**
   - Upload `market_1.csv`, `market_2.csv`, `market_3.csv` to your warehouse (e.g. BigQuery) into a dataset such as `fiber`.
2. **Create the target table**
   - Execute `sql/g_fiber.sql` to build `google_bi.fiber.g_fiber`.
   - Optionally export `g_fiber` back to CSV for use in Tableau.
3. **Connect Tableau**
   - Open Tableau Desktop / Tableau Public.
   - Connect either directly to `google_bi.fiber.g_fiber` in BigQuery or to the exported `g_fiber.csv`.
   - Rebuild or explore the dashboard following the layout in the public workbook.
---
## BI focus
This project is less about complex modelling and more about **BI workflow**:
- Gathering stakeholder questions (contact volume, repeat contacts, problem areas).
- Designing a simple **target schema** that supports those questions.
- Implementing the data consolidation in SQL.
- Building an **interactive dashboard** that a non‑technical audience can actually use.
It demonstrates the ability to move from fragmented operational data to a clean, documented dataset and a stakeholder‑ready visual product.
