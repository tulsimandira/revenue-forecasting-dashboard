# Revenue Forecasting Dashboard

🔗 **Live Dashboard (Tableau Public):**
[Add your Tableau Public link here]

🔗 **Live Interactive Notebook (Hex):**
[Add your Hex link here]

---

## Executive Summary

A subscription-based business generating $4,998,230 in revenue
over 3 years faces critical operational risks that threaten 2025
growth targets. This project performs end-to-end data analysis
across 8 datasets covering revenue, customers, contracts and
payments to identify where revenue is being lost and what actions
will protect the $1,419,549 forecast for 2025.

The analysis shows that 31.2% of payments fail every single month,
32% of revenue cannot be attributed to any region, and 34.5% of
customers have churned — all pointing to systemic operational gaps
that have gone undetected because each team was only looking at
their own slice of the data.

These findings highlight three high-impact opportunities: fixing
payment infrastructure to recover one third of expected monthly
revenue, resolving regional data gaps to enable accurate territory
performance measurement, and implementing a customer retention
programme before churn erodes the 2025 revenue base.

---

## Business Problem

The finance team observed that payments were failing at an
unusually high rate and that the business had no clear visibility
into whether it was on track to hit its 2025 revenue targets.
They partnered with the data team to investigate revenue
performance across regions, products and customer segments —
and identify whether operational issues within payments,
contracts and customer health were negatively impacting the
company's ability to convert and retain paying customers.

To answer these questions the team first needed to resolve
significant data quality issues across all 8 source files —
including 32% of revenue records with no region code, 51% of
contracts with swapped start and end dates, and payment failures
occurring at 31.2% every month without any monitoring in place.

---

## Methodology

- Exploratory Data Analysis (EDA)
- Data Quality Assessment
- Data Cleaning & Preprocessing
- Time Series Analysis
- Customer Cohort Analysis
- Revenue Attribution Analysis
- Dashboard Design & Visualisation

---

## Skills

- SQL (DuckDB Views, CASE statements, UNION ALL, LEFT JOIN,
  COALESCE, STRPTIME, DATE functions)
- Python (pandas, duckdb)
- Data Cleaning & Preprocessing
- Data Visualisation
- Dashboard Design
- Hex Data Science Notebook
- Tableau Public

---

## Data Documentation & Model

### Data Model
```
                        ┌─────────────────┐
                        │    Calendar      │
                        │─────────────────│
                        │ date_id (PK)     │
                        │ cal_date         │
                        │ year             │
                        │ quarter          │
                        │ month_name       │
                        │ week_of_year     │
                        └────────┬────────┘
                                 │
                            date_id = date_id
                                 │
┌─────────────────┐    ┌────────┴────────┐    ┌─────────────────┐
│    Customers     │    │     Revenue      │    │    Products      │
│─────────────────│    │─────────────────│    │─────────────────│
│ customer_id (PK)│    │ revenue_id (PK)  │    │ product_id (PK)  │
│ customer_name   │    │ date_id (FK)     │    │ product_name     │
│ industry        │    │ customer_id (FK) │    │ category         │
│ segment         │    │ product_id (FK)  │    │ base_price       │
│ customer_status │    │ region_code (FK) │    └─────────────────┘
│ onboarding_date │    │ revenue_amount   │
│ region_code     │    └────────┬────────┘
└────────┬────────┘             │
         │                 region_code = region_code
         │                      │
    customer_id            ┌────┴────────────┐
         │                 │  Sales_Regions   │
         │                 │─────────────────│
         │                 │ region_code (PK) │
         │                 │ region_name      │
         │                 │ country          │
         │                 │ sales_director   │
         │                 └─────────────────┘
         │
    ┌────┴──────────────────────┐
    │                           │
┌───┴─────────────┐    ┌────────┴────────┐
│    Contracts     │    │    Payments      │
│─────────────────│    │─────────────────│
│ contract_id (PK)│    │ payment_id (PK)  │
│ customer_id (FK)│    │ customer_id (FK) │
│ contract_start  │    │ payment_date     │
│ contract_end    │    │ payment_amount   │
│ contract_value  │    │ payment_status   │
│ auto_renew_flag │    └─────────────────┘
└─────────────────┘


                        ┌─────────────────┐
                        │ Revenue_Forecast │
                        │─────────────────│
                        │ forecast_month   │
                        │ predicted_revenue│
                        │                  │
                        │ (standalone —    │
                        │  no join key)    │
                        └─────────────────┘
```

---

### Data Dictionary

**Revenue (1,000 rows)**
| Column | Data Type | Description | Issues |
|---|---|---|---|
| revenue_id | VARCHAR | Unique transaction ID | None |
| date_id | INTEGER | Date as integer YYYYMMDD | Needs DATE conversion |
| customer_id | VARCHAR | Foreign key to Customers | None |
| product_id | VARCHAR | Foreign key to Products | None |
| region_code | VARCHAR | Region of transaction | 32.2% NULL |
| revenue_amount | FLOAT | Revenue value in dollars | None |

**Customers (200 rows)**
| Column | Data Type | Description | Issues |
|---|---|---|---|
| customer_id | VARCHAR | Unique customer ID | None |
| customer_name | VARCHAR | Full name of customer | None |
| industry | VARCHAR | Industry sector | Some NULLs |
| segment | VARCHAR | Enterprise, Mid-Market, SMB | None |
| customer_status | VARCHAR | Active, At Risk, Churned | None |
| onboarding_date | DATE | Date customer onboarded | None |
| region_code | VARCHAR | Customer region | Some NULLs |

**Contracts (300 rows)**
| Column | Data Type | Description | Issues |
|---|---|---|---|
| contract_id | VARCHAR | Unique contract ID | None |
| customer_id | VARCHAR | Foreign key to Customers | None |
| contract_start_date | DATE | Contract start date | 51% swapped |
| contract_end_date | DATE | Contract end date | 51% swapped |
| contract_value | FLOAT | Total contract value | None |
| auto_renew_flag | VARCHAR | Y = auto, N = manual | None |

**Payments (1,000 rows)**
| Column | Data Type | Description | Issues |
|---|---|---|---|
| payment_id | VARCHAR | Unique payment ID | None |
| customer_id | VARCHAR | Foreign key to Customers | None |
| payment_date | DATE | Date of payment attempt | None |
| payment_amount | FLOAT | Payment value in dollars | None |
| payment_status | VARCHAR | Completed, Failed, Pending | 31.2% Failed |

**Products (50 rows)**
| Column | Data Type | Description | Issues |
|---|---|---|---|
| product_id | VARCHAR | Unique product ID | None |
| product_name | VARCHAR | Name of product | None |
| category | VARCHAR | Subscription, Add-On, One-Time | None |
| base_price | FLOAT | Standard product price | None |

**Sales_Regions (3 rows)**
| Column | Data Type | Description | Issues |
|---|---|---|---|
| region_code | VARCHAR | APAC, EU, NA | NA has NULL code |
| region_name | VARCHAR | Full region name | None |
| country | VARCHAR | Country name | None |
| sales_director | VARCHAR | Sales director name | None |

**Calendar (1,096 rows)**
| Column | Data Type | Description | Issues |
|---|---|---|---|
| date_id | INTEGER | Date as integer YYYYMMDD | Needs DATE conversion |
| cal_date | DATE | Full calendar date | None |
| year | INTEGER | Year number | None |
| quarter | INTEGER | Quarter number 1-4 | None |
| month | INTEGER | Month number 1-12 | None |
| month_name | VARCHAR | Month name | None |
| week_of_year | INTEGER | Week number | None |

**Revenue_Forecast (12 rows)**
| Column | Data Type | Description | Issues |
|---|---|---|---|
| forecast_month | DATE | Month of forecast | None |
| predicted_revenue | FLOAT | Predicted revenue value | None |

---

### Data Quality Summary

| Table | Rows | Issues |
|---|---|---|
| Revenue | 1,000 | 32.2% NULL region_code |
| Customers | 200 | Some NULL industry values |
| Contracts | 300 | 51% swapped start/end dates |
| Payments | 1,000 | 31.2% Failed payment status |
| Products | 50 | Clean ✅ |
| Sales_Regions | 3 | NULL region_code for NA |
| Calendar | 1,096 | date_id needs DATE conversion |
| Revenue_Forecast | 12 | Clean ✅ |
| **Total** | **3,961** | |

---

## Results & Business Recommendations

### Chart 1 — Monthly Revenue Trend vs 2025 Forecast

![Monthly Revenue Trend](images/chart1_revenue_trend.png)

This chart tracks monthly revenue from 2022 to 2024 and overlays
the 2025 predicted forecast. The solid blue line represents actual
revenue earned historically while the dashed orange line represents
predicted revenue for 2025. This allows the business to compare
past performance against future expectations and identify seasonal
patterns across years.

- **Total Revenue 2022–2024:** $4,998,230
- **Peak Month:** Mid-2023 at ~$210K
- **Weakest Months:** August and October every year
- **2025 Forecast:** $1,419,549

**Recommendation:** Launch targeted promotions 6 weeks before
August and October to smooth seasonal revenue gaps and protect
against predictable dips.

---

### Chart 2 — Revenue by Region

![Revenue by Region](images/chart2_revenue_region.png)

This chart shows total revenue contribution by geographic region.
It immediately highlights that over one third of revenue —
$1,618,542 — has no region attribution at all, labelled as
Unknown. This is a critical data quality issue that makes it
impossible to accurately measure true regional sales performance.

- **Asia Pacific:** $1,742,969 (34.9%)
- **Europe:** $1,636,719 (32.7%)
- **Unknown Region:** $1,618,542 (32.4%) ⚠️
- **North America:** $0 — no revenue transactions recorded

**Recommendation:** Fix the region tagging system at the point
of transaction capture. The $1.62M in unattributed revenue makes
it impossible to measure regional sales performance or make
territory investment decisions.

---

### Chart 3 — Product Category Mix

![Product Category Mix](images/chart3_product_mix.png)

This chart shows how total revenue is split across three product
categories — Subscription, Add-On and One-Time. The near-equal
split of roughly 34% each indicates a healthy diversified product
mix with no single category dominating revenue. The Subscription
segment is particularly important as it represents the most
predictable recurring revenue stream.

- **Add-On:** $1,716,922 (34.4%)
- **Subscription:** $1,701,190 (34.0%)
- **One-Time:** $1,580,118 (31.6%)

**Recommendation:** Prioritise growing the Subscription segment
as it provides the most predictable recurring revenue base. A
shift from One-Time to Subscription revenue would improve
forecast reliability.

---

### Chart 4 — Customer Health by Segment

![Customer Health by Segment](images/chart4_customer_health.png)

This chart breaks down the customer base by health status —
Active, At Risk and Churned — across three segments: Enterprise,
Mid-Market and SMB. The roughly equal distribution of all three
statuses across every segment reveals that churn is a systemic
business-wide problem rather than isolated to one customer group.
Only one third of customers are actively healthy across all
segments.

- **Churn Rate:** 34.5% ⚠️
- **At Risk Customers:** ~33% across all segments
- **Active Customers:** Only ~33% across all segments

**Recommendation:** Implement a customer success programme
immediately. At 34.5% churn the business is losing one third
of its customer base. Converting At Risk customers before they
churn represents a $1.5M+ revenue protection opportunity.

---

### Chart 5 — Payment Status by Month

![Payment Status](images/chart5_payment_status.png)

This chart tracks the volume of Completed, Failed and Pending
payments every month from 2022 to 2024. The consistent presence
of all three statuses in equal proportions every single month
confirms that payment failures are not a seasonal or one-off
issue but a persistent systemic problem that has been silently
costing the business revenue for 3 years.

- **Payment Failure Rate:** 31.2% ⚠️
- **Completed Payments:** ~33% per month
- **Pending Payments:** ~36% per month

**Recommendation:** This is the most urgent fix. A 31.2% monthly
failure rate means the business is failing to collect nearly one
third of expected revenue every month. Investigate payment gateway
reliability, implement automatic retry logic and add real-time
payment failure alerting.

---

### Chart 6 — 2025 Forecast by Month

![2025 Forecast](images/chart6_forecast.png)

This chart shows the predicted revenue for each month of 2025.
January is forecast to be the strongest month at $193,949 while
August is predicted to be the weakest at $60,534. The clear
seasonal pattern mirrors historical trends and gives the business
a month-by-month roadmap to plan campaigns, resources and targets
for the year ahead.

- **Strongest Month:** January at $193,949
- **Weakest Month:** August at $60,534
- **Total 2025 Forecast:** $1,419,549

**Recommendation:** Ensure sufficient sales capacity and marketing
budget is allocated for January. Plan proactive campaigns for
August and October when revenue is predicted to be lowest.

---

### Chart 7 — Auto-Renew vs Contract Value

![Auto-Renew vs Contract Value](images/chart7_auto_renew.png)

This chart compares the total contract value of auto-renewing
contracts against non-auto-renewing contracts. With $4,100,000
— over half of total contract value — sitting in manually renewed
contracts, the business faces significant revenue risk every
renewal cycle. Any contract not actively managed could be lost
without warning.

- **Non-Auto-Renewing:** $4,100,000 (52.6%)
- **Auto-Renewing:** $3,700,000 (47.4%)

**Recommendation:** Assign dedicated account managers to
non-auto-renewing contracts and implement automated renewal
reminder workflows triggered 90 days before contract expiry.

---

## Next Steps

- Build a **churn prediction model** using customer tenure,
  payment history and contract value to identify at-risk
  customers before they churn
- Investigate **Unknown region records** by cross-referencing
  with customer billing addresses to recover attribution for
  $1.62M in revenue
- Categorise **payment failure reasons** to determine whether
  failures are user-side or vendor-side and target the right
  intervention
- Build a **time series forecast model** using ARIMA or Prophet
  for more accurate seasonality-adjusted 2025 predictions
- Add **customer lifetime value analysis** by segment and
  product category to identify highest-value customer profiles

**Limitations:**
- Revenue forecast data appears manually estimated rather
  than model-generated
- 32.2% missing region codes limits the accuracy of
  regional analysis
- No customer acquisition data available to calculate
  CAC or LTV
- Payment failure reasons are not recorded in the source data

---

## About

End-to-end subscription revenue analysis covering data cleaning,
SQL transformation and Tableau dashboard visualisation across
8 datasets and 3 years of business data.
