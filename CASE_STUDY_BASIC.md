# SQL Analytics — SaaS Subscriptions (Basic)

*⭐⭐⭐ Built · SELECT, JOIN, GROUP BY, aggregates*

> Exported from the portfolio page on 2026-10-19. The live page is the canonical version: https://bhawnamanglapm.github.io/travel-tech-more.html#case-sql-saas-analytics-basic

- 900 customers · 3 plans · 24 months
- 8 queries: SELECT, JOIN, GROUP BY, CASE
- Real local SQL Server install, not a browser sandbox
- Fictional company (Aptura), documented churn model

**Tools used:** SQL Server 2022 Express (T-SQL), SSMS (data import), VS Code with the SQL Server (mssql) extension (query writing and execution) — installed and run locally, not a hosted playground.

### Why This Exists

SQL is listed as a skill across this site, but until this project the only place it was actually demonstrated was the PCMS case study — real Bajaj Finance work, which stays exactly as delivered and untouched, permanently a written account rather than something a visitor can independently verify. This project exists to prove the same skill a second way: a fresh, fictional scenario, built and queried end to end on my own machine, not something pre-generated for me to just display.

### My Approach

Built a synthetic B2B SaaS company ("Aptura") from scratch: a Python generator producing 900 customers, 3 pricing plans (Starter $29 / Growth $99 / Scale $299), and 24 months of subscription history, with churn modeled on two well-documented SaaS patterns rather than independent randomness — cheaper plans churn more (weaker product lock-in) and Paid-Search-acquired customers churn more than Referral-acquired ones (referred customers arrive with higher intent). Then installed SQL Server 2022 Express and SSMS on my own Windows machine, connected VS Code's SQL Server (mssql) extension to it, created the database, imported the data, and ran all 8 queries below myself.

### Real Setup, Real Errors

None of this went in cleanly on the first try, which is the actual point of doing it hands-on rather than reading a guide:

- **"Cannot open database … login failed"** the first time VS Code connected to a database name that didn't exist yet — fixed by connecting to the default `master` database first, then running `CREATE DATABASE aptura_saas` directly from a query window before reconnecting.
- **"There is already an object named 'subscriptions'"** from SQL Server's Import Flat File wizard, which always tries to create a brand-new table — it collided with tables I'd already created manually. Fixed by dropping the empty manually-created tables and letting the wizard build them fresh from the CSVs instead.

### Key Findings

- **$63,298 current MRR** across 662 active subscriptions (238 cancelled, 26.4% cumulative churn).
- **Starter churns at 34.0%, more than 3× Scale's 10.1%** (Growth sits in between at 20.8%) — the cheapest plan is also the leakiest.
- **Paid Search churns at 31.6% vs. Referral's 21.5%**, with Organic (26.9%) and Outbound Sales (23.7%) in between — acquisition channel predicts retention almost as strongly as plan tier does.
- Scale carries the fewest active subscriptions (98) but the highest MRR contribution per plan (₹29,302 modeled) — plan mix matters as much as headcount when reading a subscription business.

### What This Means

The churn numbers point to a retention problem that is concentrated in specific parts of Aptura's business rather than being a company-wide issue. Starter customers are churning at 34% versus just 10% on Scale, which suggests Starter customers may not be reaching enough value early enough, or that the plan is attracting customers with weaker product fit. Similarly, Paid Search has 31.6% churn versus 21.5% from Referral, indicating that acquisition source and customer intent may be affecting retention. As PM, I'd first segment these cohorts further by activation, use case, company size, onboarding completion, and time-to-value to identify the real driver rather than immediately changing pricing. I'd then test a stronger Starter onboarding and activation journey, clearer value communication, and targeted in-product nudges, while reviewing Paid Search messaging and landing-page qualification so we attract customers whose expectations match the product.

[image: T-SQL query: SELECT COUNT(*) AS total_customers FROM customers; result: 900] [image: T-SQL query grouping subscriptions by status; result: 662 active, 238 cancelled] [image: T-SQL query joining subscriptions to plans for current MRR; result: 63298] [image: T-SQL query for MRR by plan; result: Scale 29302/98 subs, Growth 24948/252 subs, Starter 9048/312 subs] [image: T-SQL query for new customers by signup month using FORMAT(); result showing a growth ramp from 14 in Jan 2024 to 33 by Jul 2024] [image: T-SQL query for customers by acquisition channel; result: Paid Search 266, Organic 253, Referral 191, Outbound Sales 190] [image: T-SQL query for churn rate by plan using CASE WHEN; result: Starter 34.0%, Growth 20.8%, Scale 10.1%] [image: T-SQL query for churn rate by acquisition channel via a 3-table join; result: Paid Search 31.6%, Organic 26.9%, Outbound Sales 23.7%, Referral 21.5%]

All 8 screenshots are my own SQL Server / VS Code session — query and result grid together, unedited.

### Quick recall

| Section | Summary |
|---|---|
| **Problem** | SQL is listed as a skill across the site, but the only place it was actually demonstrated was the PCMS case study — real Bajaj Finance work that stays untouched as a written account, with nothing a visitor can independently verify. |
| **Research steps** | Built a synthetic B2B SaaS dataset (900 customers, 3 plans, 24 months) with churn modeled on two documented SaaS patterns, not random noise → installed SQL Server 2022 and SSMS on an actual local machine, not a browser sandbox → hit and fixed 2 real setup errors (a login failure from connecting before the database existed, an Import Flat File naming collision) → ran all 8 queries personally and verified the result grids. |
| **Solution** | 8 T-SQL queries (SELECT, JOIN, GROUP BY, CASE, aggregates) surfacing current MRR, churn by plan, and churn by acquisition channel — each with its own unedited query-and-result screenshot. |
| **Conclusion** | Starter plan churns at over 3× Scale's rate, and acquisition channel predicts retention almost as strongly as plan tier does — the real PM move is segmenting further by activation and time-to-value before touching pricing, not reacting to the headline churn number alone. |

Guide[T-SQL practice guide →](https://bhawnamanglapm.github.io/projects/sql-saas-analytics/TSQL_GUIDE.md) Dataset[generator script →](https://bhawnamanglapm.github.io/projects/sql-saas-analytics/generate_dataset.py) NextAdvanced: CTEs & window functions (T31) →
