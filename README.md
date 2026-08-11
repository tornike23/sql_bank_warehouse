# sql_bank_warehouse
Bank Data Warehouse
Hello, my name is Tornike Toradze and this is my second Data Warehousing project. This time bank dataset was used. (Worth noting that the sample dataset was created by Claude AI.)
A SQL data warehouse built in PostgreSQL using the medallion architecture (bronze -> silver -> gold). Simulates a bank with three source systems - core banking, card processing, and loan origination - that don't share clean keys, which is the main problem this project solves.

Data

5 CSVs: mixed date formats, inconsistent nulls (N/A, NULL, -), currency stored as text, and mismatched keys between systems (e.g. ACC-100421 vs ACC100421).

Layers
Bronze - raw load, everything as varchar, nothing transformed.
Silver - cleaned and typed: dates, currency, nulls, key formats, duplicates.
Gold - dimensional model (views): dim_customers, dim_branches, dim_accounts, fact_transactions, fact_loans.
A few decisions worth noting
Missing currency defaults to USD (confirmed single-currency source), but missing names/emails stay NULL rather than a placeholder.
city/state aren't cross-filled - no real relationship between them in this data.
Orphaned foreign keys (e.g. transactions on accounts that don't exist) are kept visible as NULLs, not dropped.
Tech

PostgreSQL - CTEs, window functions, regex-based cleaning, CASE logic.
