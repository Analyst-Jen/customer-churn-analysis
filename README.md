# Customer Churn Analysis — Olist E-Commerce Dataset

## Overview

This project analyses customer purchase behaviour using the Olist Brazilian
e-commerce dataset to answer a specific business question: why do 96.9% of
customers never return after their first purchase, and what does the data
tell us about how to address it?

The analysis was built entirely in Microsoft Excel using pivot tables,
VLOOKUP, and charts, following the approach a Business Analyst would take
when delivering findings to a non-technical stakeholder.

## Table of Contents

- [Business Problem](#business-problem)
- [Dataset](#dataset)
- [Key Analytical Decision](#key-analytical-decision)
- [Key Findings](#key-findings)
- [Recommendations](#recommendations)
- [File Structure](#file-structure)
- [Tools Used](#tools-used)

---

## Business Problem

Customer retention is critically low. Only 3.1% of customers made more than
one purchase across the entire dataset period. The analysis investigates
whether this is driven by poor customer satisfaction, or by a failure to
re-engage customers after their first order.

---

## Dataset

- **Source:** Olist Brazilian E-Commerce Public Dataset (Kaggle)
- **Period:** September 2016 – August 2018
- **Total orders:** 99,441
- **Unique customers:** 96,096
- **Files used:** olist_orders_dataset.csv, olist_customers_dataset.csv,
  olist_order_reviews_dataset.csv

The products dataset was excluded as product-level performance is outside
the scope of this analysis.

---

## Key Analytical Decision

`customer_unique_id` was used throughout instead of `customer_id`.

The Olist system assigns a new `customer_id` for every order, meaning the
same physical person can appear multiple times in the orders table with
different IDs. `customer_unique_id` is the only field that correctly
identifies a unique individual. Using `customer_id` would have made every
repeat buyer appear as a new customer, making churn measurement impossible.

---

## Key Findings

| Metric | Value |
|---|---|
| Total unique customers | 96,096 |
| One-time customers | 93,099 (96.9%) |
| Repeat customers | 2,997 (3.1%) |
| Avg review score — one-time buyers | 4.08 / 5 |
| Avg review score — repeat buyers | 4.12 / 5 |
| Orders with no review submitted | 768 |
| Customers giving 1-star rating | 11,424 (11.5%) |

**The critical insight:** repeat and one-time customers have nearly
identical satisfaction scores (4.12 vs 4.08). Poor product quality is
not the primary driver of churn — customers are simply not being
re-engaged after their first purchase.

---

## Recommendations

**1. Launch a post-purchase re-engagement programme**
Satisfaction is high for one-time buyers. A targeted email or push
notification within 30 days of delivery is the highest-priority action
to improve repeat rate.

**2. Address the 11.5% one-star reviewer segment**
11,424 customers gave 1-star ratings. Proactive service recovery —
refund, replacement, or direct outreach — could convert a portion back
into active customers and reduce negative word-of-mouth.

**3. Build a lightweight loyalty mechanism**
Even raising the repeat rate from 3.1% to 5–6% would meaningfully
impact revenue, since repeat buyers average 2.86 orders. A simple
post-first-purchase discount or points programme is a low-cost starting point.

---

## File Structure

```
customer-churn-analysis/
│
├── README.md
│
├── data/
│   └── raw/
│       ├── olist_orders_dataset.csv
│       ├── olist_customers_dataset.csv
│       └── olist_order_reviews_dataset.csv
│
├── notes/
│   └── data_cleaning_log.md
│
└── analysis/
    └── olist_churn_analysis.xlsx
```

The Excel workbook contains 7 sheets in reading order:
`README` → `Dashboard` → `clean_orders` → `clean_customers` →
`clean_order_reviews` → `customer_order_summary` → `Pivot`

---

## Tools Used

- Microsoft Excel (pivot tables, VLOOKUP, charts, conditional formatting)
