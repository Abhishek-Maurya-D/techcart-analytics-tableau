# 📊 TechCart Analytics — E-Commerce Business Intelligence Dashboard

**An end-to-end Tableau BI project analyzing 100,000+ real e-commerce transactions — from raw relational data to a 5-dashboard, decision-ready analytics suite.**


<img width="1310" height="610" alt="Screenshot (284)" src="https://github.com/user-attachments/assets/ac43ff27-16c1-4e61-bf94-a3a6aea7cf33" />
<img width="1309" height="624" alt="Screenshot (288)" src="https://github.com/user-attachments/assets/62dd06e4-c375-4adc-aee2-5a4ea4f4e260" />
<img width="1312" height="619" alt="Screenshot (287)" src="https://github.com/user-attachments/assets/17b70b63-4011-441f-8e0d-d790b03c5e37" />
<img width="1312" height="614" alt="Screenshot (286)" src="https://github.com/user-attachments/assets/dff70916-2ee9-4e6f-aa76-42e834cf6e50" />
<img width="1312" height="615" alt="Screenshot (285)" src="https://github.com/user-attachments/assets/2fe5a0ae-c001-430b-b346-4f1a923ad602" />
<img width="1314" height="620" alt="Screenshot (289)" src="https://github.com/user-attachments/assets/d962c8e2-878f-4889-96ea-42df4407229b" />


---

## 📌 Table of Contents
- [Overview](#overview)
- [Business Problem](#business-problem)
- [Dataset](#dataset)
- [Tools & Tech Stack](#tools--tech-stack)
- [Project Architecture](#project-architecture)
- [Data Cleaning & Modeling](#data-cleaning--modeling)
- [Dashboards](#dashboards)
- [Key Calculated Fields](#key-calculated-fields)
- [Skills Demonstrated](#skills-demonstrated)
- [Future Improvements](#future-improvements)
- [Contact](#contact)

---

## Overview

TechCart Analytics simulates a real Data Analyst engagement at a tech-enabled e-commerce marketplace. Using the Olist Brazilian E-Commerce public dataset, I built a complete BI suite covering **sales performance, delivery/logistics efficiency, customer intelligence (RFM segmentation), and seller performance** — the kind of multi-stakeholder dashboard suite a company would actually rely on for decision-making.

This isn't a single-table tutorial dashboard. It's built on **9 relational tables**, uses **LOD expressions, parameter-driven interactivity, and cross-dashboard actions**, and is structured the way a production BI deliverable would be: an Executive summary for leadership, and four deep-dive dashboards for functional teams.

---

## Business Problem

TechCart's leadership had no unified view of business health. Data on orders, payments, deliveries, reviews, and sellers lived in disconnected tables with no way to answer basic operational questions:

- Is revenue growing, and where is it coming from?
- Are we delivering on time — and where are the bottlenecks?
- Which customers are most valuable, and which are at risk of churning?
- Which sellers are underperforming, and is it hurting our reputation?

This project consolidates that data into one analytics suite and answers all four questions with interactive, drillable dashboards.

---

## Dataset

**Source:** [Olist Brazilian E-Commerce Public Dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (Kaggle)

- ~100,000 orders placed between 2016–2018
- 9 relational CSV files: orders, order items, payments, reviews, customers, sellers, products, geolocation, and category name translations
- All monetary values are in **Brazilian Real (R$)** — reflected accurately throughout every dashboard

| Table | Description |
|---|---|
| `olist_orders_dataset.csv` | Order status and key timestamps |
| `olist_order_items_dataset.csv` | Line items, price, freight value |
| `olist_order_payments_dataset.csv` | Payment type, installments, value |
| `olist_order_reviews_dataset.csv` | Review scores and comments |
| `olist_customers_dataset.csv` | Customer ID, city, state |
| `olist_sellers_dataset.csv` | Seller ID, city, state |
| `olist_products_dataset.csv` | Product category, dimensions, weight |
| `olist_geolocation_dataset.csv` | Zip code to lat/long mapping |
| `product_category_name_translation.csv` | Portuguese → English category names |

---

## Tools & Tech Stack

- **Tableau Desktop / Tableau Public** — data modeling, calculations, dashboards
- **Tableau Prep Builder** — initial data cleaning pipeline
- **Excel** — supplementary aggregation and validation checks
- *(Optional: Python/pandas — used for validating duplicate/grain checks before modeling)*

---

## Project Architecture

```
Raw CSVs (9 relational tables)
        ↓
Data Cleaning (Tableau Prep / Excel)
   • Deduplication checks (order_id + order_item_id grain)
   • Payment aggregation (order-level, one row per order)
   • Null handling on delivery dates
   • Category name translation join
        ↓
Data Modeling (Tableau Relationships — star-schema style)
   • Fact: order_items
   • Dimensions: orders, payments, reviews, products, customers, sellers, geolocation
        ↓
Calculated Fields Layer
   • LOD expressions (CLV, RFM, seller/category-level KPIs)
   • Table calculations (RANK, growth %, Top-N filters)
        ↓
23 Worksheets → 5 Interactive Dashboards
        ↓
Published to Tableau Public
```

**Why Relationships instead of Joins:** The data spans multiple grains — order-level payments vs. item-level order lines. Using hard joins would cause row duplication and inflate revenue/order counts. Tableau's relationship model preserves each table's native grain and aggregates correctly at query time.

---

## Data Cleaning & Modeling

- Verified `order_items` grain (no duplicate `order_id` + `order_item_id` combinations)
- Aggregated `order_payments` to one row per order (`SUM(payment_value)`) to prevent fan-out when joined to order-level analysis
- Flagged undelivered orders explicitly as "Not Delivered" rather than excluding them (avoids biasing on-time delivery metrics)
- Joined `product_category_name_translation` to get English category names for readability
- Built a star-schema-style relationship model: `order_items` as the fact table, with `orders`, `payments`, `reviews`, `products`, `customers`, `sellers`, and `geolocation` related at their correct respective grains

---

## Dashboards

### 1️⃣ Executive Overview
High-level KPIs (Revenue, Orders, AOV, % On-Time Delivery), category revenue breakdown, and new-vs-repeat customer mix — built for a leadership audience that needs answers in under 10 seconds.

### 2️⃣ Sales & Revenue Deep-Dive
Revenue trends, MoM growth, category performance, payment type mix, and installment behavior — with a parameter-driven Top-N filter and dynamic metric switcher.

### 3️⃣ Delivery & Logistics Performance
Average delivery time, on-time delivery rate, delay severity, and a seller-level late-delivery leaderboard paired with a scatter plot proving the link between delivery delays and review scores.

### 4️⃣ Customer Intelligence (RFM)
Customer Lifetime Value distribution, a Top 20 customer leaderboard, and the centerpiece **RFM segmentation scatter plot** — classifying every customer into Champions, Loyal, At Risk, or Lost segments, paired with a churn risk breakdown by region.

### 5️⃣ Seller & Product Performance
Seller rating leaderboard (Top/Bottom-N toggle), order volume per seller, and a category-level review-score heatmap flagging quality concerns.

---

## Key Calculated Fields

A sample of the analytical logic behind the dashboards — full list in `/docs/calculated_fields.md`.

```
// Customer Lifetime Value (LOD)
{ FIXED [customer_unique_id] : SUM([payment_value]) }

// Recency (days since last purchase, relative to dataset's last activity)
DATEDIFF('day',
  {FIXED [customer_unique_id] : MAX([order_purchase_timestamp])},
  {FIXED : MAX([order_purchase_timestamp])}
)

// Late Delivery Rate by Seller
{ FIXED [seller_id] :
  SUM(IF [order_delivered_customer_date] > [order_estimated_delivery_date] THEN 1 ELSE 0 END)
} / { FIXED [seller_id] : COUNTD([order_id]) }

// Parameter-driven Top-N filter
RANK(MIN([Avg Seller Rating]), 'asc') <= [Top N Parameter]

// Dynamic metric switcher (Parameter Action)
CASE [Metric Selector]
  WHEN "Revenue" THEN SUM([payment_value])
  WHEN "Orders" THEN COUNTD([order_id])
  WHEN "Avg Review" THEN AVG([review_score])
END
```

---

---

## Skills Demonstrated

`Data Modeling (Relationships/Joins)` `LOD Expressions (FIXED)` `Table Calculations (RANK, LOOKUP)` `Parameters & Parameter Actions` `Calculated Fields` `RFM & Cohort Analysis` `Dashboard Actions (Filter/Highlight)` `Custom Tooltips` `Trend Lines & Reference Lines` `Sets & Groups` `Dashboard Design & UX` `Data Cleaning & Preprocessing`

---

## Future Improvements

- Automate data refresh via Tableau Prep scheduled flows
- Add a predictive churn model (Python integration) to complement the rule-based RFM segmentation
- Migrate the data model to a proper warehouse (BigQuery/Snowflake) for scalability beyond 100K orders
- Add cohort retention analysis as a dedicated dashboard

---

## Contact

**Abhishek Maurya**
📧 abhishekworkit@gmail.com | 💼 https://www.linkedin.com/in/abhishek-maurya-7b7228271/

If you have feedback on this project, I'd love to hear it — feel free to open an issue or connect on LinkedIn.
