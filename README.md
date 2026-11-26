# Shadow Mart Ltd. | Analytics Capstone and Root-Cause Analysis

## 1. Project overview
A PostgreSQL analysis of Shadow Mart Ltd., a Bangladeshi e-commerce marketplace that also runs retail, consignment and its own delivery fleet. The repository holds Assignment 1 (27 questions across Commercial, Finance, Operations and Marketing) and Assignment 2 (17 root-cause investigations), with a management presentation, an ER diagram and detective reports.

## 2. Business problem summary
Leadership needs to know what happened across sales, money, logistics and customers (Assignment 1) and why, with what to do about it (Assignment 2). Period analysed: 1 Sep to 31 Dec 2025, plus a January 2026 forecast.

## 3. Dataset description
| Table | Rows | Grain |
|---|---|---|
| fact_order_details (raw, then `fact_clean`) | 191,539 raw, 190,218 clean | one product line inside a package, with 16 lifecycle timestamps |
| dim_customer | 66,591 | one customer |
| dim_product | 54,038 | one product (category, selling price, cost price, stock) |
| dim_shop | 3,234 | one seller shop |
| dim_rider | 116 | one rider and assigned hub |
| campaign_date | 24 | one campaign day with discount rate |
| order_forecast | 31 | one January 2026 day with predicted orders |

After cleaning there are 190,218 lines, 155,292 packages and 124,515 customer orders. Analysis tables: `fact_line` (line), `fact_pkg` (package), `fact_order` (order).

## 4. Technology stack
PostgreSQL 16, pgAdmin 4, Python (matplotlib) for charts, Graphviz for the ER diagram, PowerPoint for the presentation, GitHub.

## 5. Repository structure
```
Shadow-Mart-Analytics-Project/
|-- README.md
|-- Data_CSV/                       (7 source tables as clean CSV, used for import)
|-- SQL/
|   |-- 00_setup_and_cleaning.sql   (tables, fact_clean, analysis layer)
|   |-- Commercial.sql  Finance.sql  Operations.sql  Marketing.sql
|   `-- RCA/  A_Commercial.sql  B_Finance.sql  C_Operations.sql  D_Marketing.sql
|-- Presentation/
|   |-- Shadow_Mart_Analytics_Presentation.pdf
|   `-- Shadow_Mart_Analytics_Presentation.pptx
|-- Documentation/
|   |-- ER_Diagram.png
|   |-- Additional_Notes.pdf
|   `-- Detective_Reports/          (17 reports as .md, plus Detective_Reports_All.pdf)
`-- Images/
    `-- Dashboard_Screenshots/      (all charts)
```

## 6. Instructions to run / review
1. Create a database `shadow_mart` in pgAdmin 4 and open the Query Tool.
2. Open `SQL/00_setup_and_cleaning.sql`. Select and run **Part 1** (down to the STOP banner). It creates schema `sm` and the 7 tables.
3. Import each CSV from `Data_CSV/` (right-click the table, Import/Export Data, csv, header on). `fact_order_details.csv` goes into `sm.fact_order_details_raw`; every other file goes into the table with the same name. Check that the raw fact table has 191,539 rows.
4. Select and run **Part 2** of `00_setup_and_cleaning.sql`. It builds `fact_clean` and the analysis tables and ends with 190,218 / 155,292 / 124,515 (lines / packages / orders).
5. Run `Commercial.sql`, `Finance.sql`, `Operations.sql`, `Marketing.sql`, then the four files in `SQL/RCA/`. Run one query at a time (select, F5).

## 7. Key findings
1. Campaign weeks overwhelm in-house fulfilment: Retail and Consignment 24h breach is 56-61% on campaign days vs 3.5-4.6% otherwise (High).
2. COD failures outside Dhaka are the cash leak: OSD + COD holds 72.8% of failed-delivery COGS; BDT 2.7-5.4M protectable (High).
3. Big November Hunt lost money: ROAS 2.59 vs 4.1-5.0 and BDT 3.1M negative after discount (High).
4. Dead stock and stockouts coexist: 42.3% of products hold 97.8% of stock capital while 511 best-sellers are nearly out (Medium).
5. Two of three buyers never return: 65% bought once; a 5-20% reorder rate is worth BDT 2.5-9.9M GMV (Long-Term).

## 8. Assumptions and limitations
- 1,321 exact duplicate rows are removed (`fact_clean`); orders and packages are unchanged.
- GMV = SellingPrice x Quantity; COGS = CostPrice x Quantity; 24h SLA breach = RTS minus Pending over 24 hours.
- ISD = Dhaka North/South; 3PL = destination hub 3PL, CB Hub or Parabot.
- OrderDate is used for time reporting (11,040 lines are one day earlier than the Pending date).
- Return-trip courier cost is not in the data: BDT 60 (ISD) / 120 (OSD) is an editable assumption in Finance Q3.
- Four months of history only; margins are gross margins (no delivery or marketing cost); statistical tests show association, not proof of cause.
- Full list and the data-quality log: `Documentation/Additional_Notes.pdf`.
