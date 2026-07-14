# PowerBI Sales Analytics — eCommerce Business Intelligence Dashboard

A three-page Power BI dashboard analyzing e-commerce sales, customer and product performance, order fulfillment, delivery efficiency, and shipping costs.

## Project Overview

This project presents an interactive three-page Power BI dashboard built using a multi-table e-commerce dataset. The dashboard analyzes sales performance, customer behavior, product performance, payment trends, and order fulfillment metrics.

The project focuses not only on dashboard development but also on building a proper relational data model and applying DAX measures to answer real business questions.

## Objectives

The dashboard was designed to answer questions such as:

- How much revenue has the business generated?
- How many customers and orders are there?
- Which product categories generate the highest sales?
- Which states contribute the highest revenue?
- How do customers pay?
- How efficiently are orders fulfilled?
- How long does delivery take?
- Which regions experience longer delivery times?

## Dataset

The project uses a multi-table e-commerce dataset from Kaggle containing customer, order, order-item, payment, and product data.

Dataset source: [E-Commerce Order Dataset on Kaggle](https://www.kaggle.com/datasets/bytadit/ecommerce-order-dataset)

The raw data files are not redistributed in this repository. Please download them directly from the original Kaggle source.

**Tables used:**
- Customers
- Orders
- OrderItems
- Products
- Payments

## Data Preparation

Before building the dashboard, the dataset was cleaned and modeled. Key preparation steps included:

- Imported multiple CSV tables
- Removed duplicated Product records
- Created One-to-Many relationships
- Built a relational data model
- Created a dedicated Date Table
- Created calculated columns for delivery analysis
- Built reusable DAX measures

## Data Model

The project uses a relational model connecting:

```
Customers │ Orders │ OrderItems │ Products
Orders │ Payments
```

Relationship design includes:

- One-to-Many relationships
- Primary / Foreign Keys
- Star-schema-inspired modeling
- Single-direction filtering

## DAX Measures

**Sales KPIs**
- Total Sales
- Total Orders
- Total Customers
- Average Order Value

**Fulfillment KPIs**
- Completed Orders
- Completed Sales
- Completion Rate
- Average Delivery Days
- Late Orders
- Late Delivery Rate

**Product & Shipping KPIs**
- Total Products
- Average Item Price
- Total Shipping Cost
- Average Shipping Cost per Item

## Time Intelligence

Created a custom Date Table using DAX:

- Calendar
- Year
- Month Number
- Month Name
- Year-Month hierarchy

This supports chronological trend analysis and provides the foundation for future Time Intelligence calculations.

## Dashboard Pages

### Page 1 — Executive Overview
Provides an executive summary including:
- Sales KPIs
- Customer KPIs
- Monthly Sales Trend
- Order Status
- Product Category Sales

### Page 2 — Customer & Product Insights
Analyzes:
- Sales by State
- Average Order Value by State
- Product Category Performance
- Average Product Price
- Payment Method Distribution

### Page 3 — Order Fulfillment & Shipping Analysis
Focuses on:
- Delivery Performance
- Late Delivery Rate
- Average Delivery Days
- Shipping Cost
- Monthly Completed Orders

## Key Findings

- Sales are heavily concentrated in a single product category: **Toys account for approximately three-quarters of total sales** (~23M of ~30.45M), making it by far the dominant revenue driver.
- Approximately **98% of orders (97.89%)** were successfully completed.
- Roughly **90% of total orders (90.34%)** were delivered on time, based on the "Total Orders by Delivery Performance" breakdown (On Time / Late / Not Delivered).
- Delivery performance varies significantly by state — average delivery days range from **~8.6 days (SP)** to **~28.3 days (RR)**.
- Credit cards account for the large majority of payment transactions (**73.69%**), followed by wallet-type payments (19.37%).
- Sales grew across the 2016–2018 observation period before leveling off in later months.

## Data Notes & Caveats

A few metrics in the current dashboard show minor inconsistencies or would benefit from a source-data check before being cited as final numbers in a report:

- **Late Delivery Rate uses a different denominator than the delivery performance chart — by design, not error.** The KPI card (7.71%) is `Late Orders / Completed Orders`, while the "Total Orders by Delivery Performance" chart (7.54%) shows the same Late Orders count as a share of *Total Orders*. Both are internally consistent (confirmed via the underlying DAX), but since the same "Late" figure yields two different percentages depending on context, it's worth adding a tooltip or footnote clarifying which denominator applies to each visual, so readers don't assume it's a data error.
- **Delivery Performance classification logic (confirmed):** each order is flagged "Not Delivered" (blank delivered timestamp), "On Time" (delivered on/before the estimated delivery date), or "Late" (delivered after), computed at the order level via a DAX calculated column. The "On Time" / "Late" percentages on the delivery chart are therefore correctly read as a share of total orders, including the "Not Delivered" segment in the base.
- **Average Order Value vs. Average Item Price:** both currently show the identical value (340.90). Confirmed via the data model that these are two independently calculated DAX measures — `Average Order Value = DIVIDE([Total Sales], [Total Orders], 0)` (order-level) vs. `Average Item Price = AVERAGE(df_OrderItems[price])` (item-level). The near-identical result likely reflects that most orders in this dataset contain a single item at a similar price point, rather than a calculation error.
- **Total Customers vs. Total Orders:** both currently show 89K. In this dataset, the imported model results in a one-to-one relationship between df_Customers and df_Orders, leading both Total Customers and Total Orders to equal approximately 89K. This behavior is consistent with datasets where a customer identifier represents an order-specific customer record rather than a long-term unique shopper.
- **Payment type labeling:** The payment labels displayed in this dashboard reflect the imported dataset. If the source data originated from the commonly used Brazilian Olist dataset, readers may wish to verify whether the imported "wallet" label corresponds to another payment category (for example, "boleto") after preprocessing.

## Business Recommendations

- Reduce reliance on a single high-performing product category (Toys); diversify category-level revenue exposure.
- Improve logistics in states with longer average delivery times (e.g., RR, AP, AM).
- Monitor late and non-delivered orders to identify operational bottlenecks.
- Continue optimizing the dominant credit card payment experience while encouraging adoption of alternative payment methods.
- Resolve the metric inconsistencies noted above before using this dashboard for external reporting.

## Skills Demonstrated

- Power BI
- Data Modeling
- Data Cleaning
- Data Visualization
- DAX
- Time Intelligence
- KPI Design
- Business Analytics
- Dashboard Design
- Relationship Modeling
