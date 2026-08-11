# SQL Bank Warehouse
Hello, my name is Tornike Toradze and this is my second Data Warehousing project. This time bank dataset was used. (Worth noting that the sample dataset was created by Claude AI.)
# Bank Data Warehouse

## 1. Background and Overview

A SQL data warehouse built in PostgreSQL using the medallion architecture (bronze -> silver -> gold). Simulates a bank with three source systems - core banking, card processing, and loan origination - that don't share clean keys, which is the main problem this project solves.

## 2. Data Structure Overview

5 raw CSVs, one per source table:

- `core_customers`, `core_accounts`, `core_branches` - core banking system
- `card_transactions` - card processor feed
- `loan_applications` - loan origination system

Data is messy on purpose: mixed date formats, inconsistent nulls (`N/A`, `NULL`, `-`), currency stored as text, and mismatched keys between systems (e.g. `ACC-100421` vs `ACC100421`).

Pipeline layers:
- **Bronze** - raw load, everything as `varchar`, nothing transformed.
- **Silver** - cleaned and typed: dates, currency, nulls, key formats, duplicates.
- **Gold** - dimensional model (views): `dim_customers`, `dim_branches`, `dim_accounts`, `fact_transactions`, `fact_loans`.

## 3. Executive Summary

The project takes three inconsistent source feeds and turns them into a clean, query-ready star schema. The main work was reconciling data that looks different across systems but represents the same thing - different date formats, different key formats, different null conventions - without silently guessing at values that couldn't be verified. 
Also worth noting that visual schema for this project is available in 'src' folder.

## 4. Insights Deep Dive

Key challenges and how they were handled:

- **Mismatched keys across systems** - card transactions reference accounts with or without a dash; loans reference customers by numeric ID or by email. Both were reconciled during the silver-layer cleaning step so gold-layer joins work cleanly.
- **Currency defaulting** - missing currency values default to `USD`, since the source is confirmed single-currency. This was a documented business rule, not a guess.
- **Contact fields kept as NULL** - unlike currency, missing names/emails/phones were left `NULL` rather than given a placeholder, since these fields need to behave as genuinely missing downstream.
- **No city/state imputation** - city and state are independently randomized in this dataset, so no real relationship exists to fill one from the other. Cleaned for formatting only.
- **Orphaned foreign keys surfaced, not hidden** - transactions referencing accounts that don't exist show up as `NULL`s via `LEFT JOIN`, rather than being silently dropped by an `INNER JOIN`.

