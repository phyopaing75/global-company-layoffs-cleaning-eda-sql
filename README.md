# Global Company Layoffs: SQL Data Cleaning & Exploratory Analysis

A MySQL project that cleans a raw layoffs dataset (March 2020 to March 2023) and analyzes it to show **when layoffs happened, which companies and funding stages were hit hardest, and which companies shut down completely.**

## Business Problem

A workforce-planning and talent-strategy team wants to understand how the 2020 to 2023 layoff waves unfolded: when they peaked, which companies were involved, and what stage those companies were at. The team needs this to plan hiring, retention and recruiting, and to judge startup risk.

## Business Questions

Each question is answered by a query in `global_company_layoffs_eda.sql`.

1. How did layoff volume change by year and by month, and what is the running total over time?
2. Which companies laid off the most employees overall, and which led in each year?
3. Which company funding stages account for the most layoffs?
4. Which companies laid off 100% of their staff, and how much funding had they raised?

## Key Insights

1. **Layoffs came in two waves, with a quiet period between.** 2020 saw 80,998 layoffs, concentrated in April (26,710) and May (25,804). 2021 dropped to 15,823. Then 2022 reached 160,661, and 2023 had already logged 125,677 by 6 March. January 2023 was the worst single month at 84,714, followed by November 2022 at 53,451. The rolling total reaches 383,159 by March 2023.
2. **A handful of large companies stand out.** Amazon (18,150), Google (12,000), Meta (11,000), Salesforce (10,090) and Microsoft (10,000) lead all companies by total layoffs. The largest single layoff event in the data is 12,000 employees.
3. **Post-IPO companies account for most layoffs by far.** Post-IPO companies laid off 204,132 employees, compared with 40,716 for Unknown stage, 27,576 for Acquired and 20,017 for Series C.
4. **The companies at the top of the list changed by year.** In 2020, travel and gig companies led (Uber 7,525; Booking.com 4,375; Groupon 2,800). By 2022 and 2023, Big Tech led (Meta 11,000 and Amazon 10,150 in 2022; Google 12,000 and Microsoft 10,000 in 2023).
5. **Well-funded companies can still shut down completely.** Among companies that laid off 100% of staff, the most heavily funded were Britishvolt ($2.4B raised), Quibi ($1.8B), Deliveroo Australia ($1.7B), Katerra ($1.6B) and BlockFi ($1.0B).

## Recommendations

1. **Track layoffs with a rolling monthly total.** Sudden jumps (such as November 2022 into January 2023) are an early signal to tighten hiring plans and review retention risk.
2. **Treat layoff spikes at large public companies as a recruiting window.** Post-IPO companies drive most layoff volume, releasing thousands of experienced workers at once.
3. **Do not use funding size alone as a survival signal.** Companies that had raised over $1B each still laid off their entire workforce, so runway and business fundamentals deserve a closer look than total capital raised.

## Project Workflow

### Part 1: Data Cleaning (`global_company_layoffs_cleaning.sql`)

| Step | Technique |
|---|---|
| Protect raw data | Staging tables `layoffs_staging` and `layoffs_staging2` |
| Remove duplicates | `ROW_NUMBER() OVER (PARTITION BY ...)` across all columns |
| Standardize | `TRIM()` on company names, unified Crypto industry variants, trailing punctuation removed from country, dates converted with `STR_TO_DATE()` |
| Handle nulls | Missing industry filled via self-join on company name, then rows with neither a layoff count nor a percentage removed |

### Part 2: Exploratory Data Analysis (`global_company_layoffs_eda.sql`)

| Query | Business question |
|---|---|
| `MAX(total_laid_off)`, `MIN/MAX(date)` | Size of the largest event and the period covered |
| Layoffs by `YEAR(date)` and by month | Q1: how volume changed over time |
| Rolling total with a CTE and `SUM() OVER (ORDER BY ...)` | Q1: running total over time |
| Layoffs by company, and by company and year | Q2: biggest layoffs overall and per year |
| Top 5 companies per year with `DENSE_RANK() OVER (PARTITION BY year ...)` | Q2: who led each year |
| Layoffs by `stage` | Q3: which funding stages were hit hardest |
| Companies with `percentage_laid_off = 1`, ordered by funds raised | Q4: complete shutdowns and their funding |

## Tools & Skills

MySQL · CTEs · Window functions (`ROW_NUMBER`, `DENSE_RANK`, `SUM OVER`) · Self-joins · Data cleaning · Exploratory data analysis

## Data

- File: `global_company_layoffs.csv` (2,361 rows, 9 columns: company, location, industry, total_laid_off, percentage_laid_off, date, stage, country, funds_raised_millions)
- Period covered: 11 March 2020 to 6 March 2023
- Source: *[add the original dataset link here]*

**Limitation:** Cleaning removes only rows where both the layoff count and the percentage are missing. Rows with a missing layoff count but a known percentage remain, so all totals are lower bounds.

## How to Run

1. Create a MySQL schema and import `global_company_layoffs.csv` into a table named `layoffs`.
2. Run `global_company_layoffs_cleaning.sql` to build the cleaned table `layoffs_staging2`.
3. Run `global_company_layoffs_eda.sql` to reproduce the analysis.

## Repository Contents

| File | Purpose |
|---|---|
| `global_company_layoffs.csv` | Raw dataset |
| `global_company_layoffs_cleaning.sql` | Cleaning pipeline |
| `global_company_layoffs_eda.sql` | Exploratory analysis queries |
