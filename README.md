Project Overview

This project is a MySQL-based portfolio that cleans and analyzes global company layoff records using a structured, two-part workflow.

---

Part 1: Data Cleaning (global_company_layoffs_cleaning.sql)

This part focuses on transforming raw, messy data into a reliable format suitable for
analysis:
1. Staging Pipeline: Loads raw records into duplicate-safe staging tables
(layoffs_staging , layoffs_staging2) to protect original data.
2. Deduplication: Uses ROW_NUMBER() OVER(PARTITION BY ...) to locate and safely
remove duplicate rows across all fields.
3. Standardization: Trims company whitespaces, unifies categorical industry names (such
as grouping crypto variants), cleans trailing country punctuation, and converts dates into
SQL DATE format using STR_TO_DATE() .
4. Data Imputation & Cleanup: Fills missing industry values using self-joins on company
names and purges unrecoverable rows where layoff counts and percentages are both
missing.

Part 2: Exploratory Data Analysis (global_company_layoffs_eda.sql)

The cleaned dataset is then queried to extract macro and micro insights into global layoff
patterns:
1. Aggregations & Trends: Evaluates peak layoff events, completely shut down companies (100% laid off), and annual/monthly volume trajectories.
2. Cumulative Tracking: Uses Common Table Expressions (CTEs) and window functions (SUM() OVER(...) ) to calculate rolling monthly totals over time.
3. Segmentation & Rankings: Groups layoff impacts by startup funding stages and applies DENSE_RANK() to rank the top 5 companies by total
layoffs per year.
