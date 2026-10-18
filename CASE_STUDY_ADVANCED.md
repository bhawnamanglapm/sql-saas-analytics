# SQL Analytics — SaaS Subscriptions (Advanced)

*⭐⭐⭐⭐ Built · CTEs, window functions, cohort retention*

> Exported from the portfolio page on 2026-10-19. The live page is the canonical version: https://bhawnamanglapm.github.io/travel-tech-more.html#case-sql-saas-analytics-advanced

- 4 queries: CTEs, LAG, SUM() OVER, RANK() PARTITION BY
- Monthly cohort retention analysis
- Real T-SQL dialect bug hit and fixed
- Same Aptura dataset as the Basic case study

**Tools used:** SQL Server 2022 Express (T-SQL), VS Code with the SQL Server (mssql) extension.

### Why Advanced, Not Just Basic

Aggregates and joins prove I can retrieve data; this half proves I can shape it the way a PM actually needs to — cohort retention curves, month-over-month growth, and running totals, the exact SQL patterns behind most subscription-business dashboards. Same Aptura dataset as the Basic case study (T30), four new query patterns instead of eight simpler ones.

### A Real T-SQL Dialect Bug, and the Fix

These 4 queries were first written and verified against SQLite for a browser-based version of this project, then ported to T-SQL for SQL Server (`FORMAT()` instead of `strftime()`, `DATEDIFF()` instead of `julianday()`). Three of the four ported cleanly on the first run. The running-cumulative-signups query didn't:

| Msg 8729, Level 16: ORDER BY list of RANGE window frame has total size of 8000 bytes. Largest size supported is 900 bytes. |
|---|

The cause: `FORMAT()` returns `nvarchar(4000)` regardless of how short the actual string is, and SQL Server's default window-frame type (RANGE) caps its ORDER BY column at 900 bytes for that reason. The fix was one clause, not a rewrite — swapping the implicit RANGE frame for an explicit `ROWS UNBOUNDED PRECEDING` frame (the semantically correct frame for a running total anyway):

| SUM(new_customers) OVER (ORDER BY month ROWS UNBOUNDED PRECEDING) AS cumulative_customers |
|---|

### Key Findings

- **3- and 6-month cohort retention bounces between roughly 62% and 96%** month to month, because monthly cohorts are small (14–33 signups each) — reading that as expected noise rather than over-reacting to any single month's number is itself part of the skill.
- **Month-over-month new-MRR growth swings widely for the same reason** (e.g. −40.5% in March, +183.6% in May) — a small-N, growing company's month-over-month metrics are volatile by construction, not necessarily a signal.
- **Cumulative signups climb cleanly** (14 → 37 → 55 → 71 → 94 across the first five months) once the running-total fix above was applied, confirming the window function now computes correctly.
- **Ranking active customers by tenure within each plan** surfaces exactly who's been on a plan longest — useful for e.g. identifying who to interview for retention research, or who to invite to a beta first.

### What This Means, Applied

The cohort-retention query above is what turns the Basic case study's churn-by-plan and churn-by-channel diagnosis into something actionable rather than a one-time snapshot: I'd track 30/60/90-day retention, activation rate, and churn by cohort to validate whether interventions — a stronger Starter onboarding journey, tighter Paid Search targeting — actually improve retention and lifetime value, instead of just assuming they worked.

[image: T-SQL CTE query for monthly cohort retention; result showing 3-month and 6-month retention percentages per signup-month cohort] [image: T-SQL query using LAG() OVER for month-over-month new-MRR growth; result showing new MRR per month and percent change vs. the prior month] [image: T-SQL query using SUM() OVER with an explicit ROWS UNBOUNDED PRECEDING frame for running cumulative signups; result climbing 14, 37, 55, 71, 94] [image: T-SQL query using RANK() OVER PARTITION BY to rank active customers by tenure within each plan; result showing the longest-tenured Growth-plan customers ranked 1 through 7]

All 4 screenshots are my own SQL Server / VS Code session — query and result grid together, unedited, including the frame-fix retry on the cumulative-signups query.

### Quick recall

| Section | Summary |
|---|---|
| **Problem** | The Basic case study proves I can retrieve data (SELECT, JOIN, GROUP BY) but not shape it the way a PM actually needs to — cohort retention curves, month-over-month growth, running totals — the SQL patterns behind most real subscription-business dashboards. |
| **Research steps** | Ported 4 queries from SQLite (a browser-based prototype) to T-SQL for SQL Server → 3 of 4 ported cleanly, but the running-cumulative-signups query hit a real dialect error (RANGE window frame's 900-byte ORDER BY cap, triggered by FORMAT()'s nvarchar(4000) return type) → diagnosed the root cause rather than guessing, and fixed it with one clause: an explicit ROWS UNBOUNDED PRECEDING frame, which is also the semantically correct frame for a running total anyway. |
| **Solution** | 4 T-SQL queries using CTEs, LAG(), SUM() OVER, and RANK() PARTITION BY — monthly cohort retention, month-over-month MRR growth, cumulative signups, and tenure ranking within each plan — on the same Aptura dataset as the Basic study. |
| **Conclusion** | Small-N monthly cohorts (14–33 signups each) produce genuinely volatile retention and growth numbers month to month — reading that as expected noise rather than over-reacting to any single month is itself part of the skill. This is what turns the Basic study's churn diagnosis into something trackable over time, instead of a one-time snapshot. |

Guide[T-SQL practice guide →](https://bhawnamanglapm.github.io/projects/sql-saas-analytics/TSQL_GUIDE.md) BackBasic: SELECT, JOIN, GROUP BY (T30) →
