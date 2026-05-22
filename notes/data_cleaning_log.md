# Data Cleaning Log
## Project: Customer Churn Analysis — Olist E-Commerce Dataset
## Tool Used: Microsoft Excel

---

## Source Files

| File | Rows | Columns | Notes |
|---|---|---|---|
| olist_orders_dataset.csv | 99,441 | 8 | Primary analysis table |
| olist_customers_dataset.csv | 99,441 | 5 | Used for customer lookup |
| olist_order_reviews_dataset.csv | 99,224 | 7 | Used for satisfaction analysis |
| olist_products_dataset.csv | 32,951 | 9 | Not used — outside project scope |

---

## Why olist_products_dataset.csv Was Excluded

The products file contains product-level attributes (weight, dimensions, category name in Portuguese). This project is focused on customer purchase behaviour and churn — not product performance. Including it would have required a separate order_items join table which was not part of the source data provided. It was intentionally excluded as out of scope.

---

## Orders Table (olist_orders_dataset.csv)

### Raw state
- 99,441 rows, 8 columns
- Columns: order_id, customer_id, order_status, order_purchase_timestamp, order_approved_at, order_delivered_carrier_date, order_delivered_customer_date, order_estimated_delivery_date

### What was changed

**No rows were removed.**
All 99,441 orders were retained regardless of order_status. This was a deliberate decision — filtering to only "delivered" orders would have undercounted customers and distorted the churn rate. A customer who placed a cancelled or invoiced order still placed an order and counts as a customer interaction.

**Five columns were added:**

1. `customer_unique_id` — joined in from olist_customers_dataset.csv using customer_id as the lookup key (Excel VLOOKUP). This column was necessary because the orders table only contains customer_id, which is a transaction-level ID. The same physical customer can have multiple customer_ids across different orders. customer_unique_id is the true identifier of a unique person, and is the correct column to use for counting distinct customers and measuring repeat purchase behaviour. All 99,441 rows matched successfully — zero nulls after join.

2. `order_Date` — extracted from order_purchase_timestamp (which includes time). A date-only column was created to simplify grouping and trend analysis.

3. `order_month` — extracted from order_purchase_timestamp using Excel MONTH() function. Used for monthly trend analysis in pivot tables.

4. `order_year` — extracted from order_purchase_timestamp using Excel YEAR() function. Used for year-level grouping.

5. `review_score` — joined in from olist_order_reviews_dataset.csv using order_id as the lookup key (Excel VLOOKUP). 768 orders returned no match and show as blank. This is expected — not every order receives a review. These blanks are not errors; they represent orders where the customer did not submit a review.

### Nulls retained (not errors)
- order_approved_at: 160 nulls — orders not yet approved at time of data extraction
- order_delivered_carrier_date: 1,783 nulls — orders not yet picked up by carrier
- order_delivered_customer_date: 2,965 nulls — orders not yet delivered to customer
- review_score: 768 nulls — orders with no customer review submitted

These nulls are structurally expected for non-delivered or unreviewed orders and were left in place intentionally.

---

## Customers Table (olist_customers_dataset.csv)

### Raw state
- 99,441 rows, 5 columns
- Columns: customer_id, customer_unique_id, customer_zip_code_prefix, customer_city, customer_state
- Zero nulls across all columns

### What was changed

**No rows were removed. No columns were removed. No columns were added.**

The table was imported as-is into the clean_customers sheet.

### Important note on customer_id vs customer_unique_id

The raw customers table contains 99,441 rows but only 96,096 unique customer_unique_ids. This means 3,345 rows share a customer_unique_id with another row — the same physical customer was assigned a different customer_id for each separate order they placed. This is how the Olist system works: customer_id is order-scoped, not person-scoped.

For this analysis, customer_unique_id was used throughout as the identifier for a distinct person. Using customer_id would have artificially inflated the customer count and made every customer appear as a one-time buyer even if they ordered multiple times.

---

## Reviews Table (olist_order_reviews_dataset.csv)

### Raw state
- 99,224 rows, 7 columns
- Columns: review_id, order_id, review_score, review_comment_title, review_comment_message, review_creation_date, review_answer_timestamp

### What was changed

**No rows were removed. No columns were removed. No columns were added.**

The table was imported as-is into the clean_order_reviews sheet.

### Nulls retained (not errors)
- review_comment_title: 87,656 nulls — the majority of customers did not write a title. This is normal for review datasets.
- review_comment_message: 58,247 nulls — many customers submitted a star rating without a written comment. Also normal.

These nulls were not cleaned out because the text comment columns were not used in the analysis. Only review_score was used, which has zero nulls in the reviews table itself.

### Note on the #N/A values in the dashboard
The dashboard shows 768 orders under #N/A in the review score distribution. These are the 768 orders in clean_orders that had no matching review when the VLOOKUP was performed. They are not corrupt data — they are orders that exist in the orders table but not in the reviews table. Excel displays the unmatched VLOOKUP result as #N/A rather than blank in pivot tables.

---

## Summary of All Decisions

| Decision | What Was Done | Why |
|---|---|---|
| Row filtering | No rows removed from any table | All order statuses represent real customer interactions |
| customer_id vs customer_unique_id | Used customer_unique_id throughout | Correctly identifies unique people, not unique transactions |
| Date columns | Extracted date, month, year from timestamp | Enables time-based grouping without losing original timestamp |
| Review score join | Left join — 768 unmatched orders kept as blank | Unreviewed orders are real orders; removing them would bias the data |
| Comment columns | Retained but not used in analysis | Out of scope for churn analysis; retained for completeness |
| Products dataset | Excluded entirely | Outside the scope of customer churn analysis |
| Nulls in delivery columns | Retained as-is | Structurally expected for non-delivered orders |
