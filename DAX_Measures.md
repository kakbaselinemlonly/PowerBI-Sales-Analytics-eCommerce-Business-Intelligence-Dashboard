# DAX Measures and Calculated Fields

This document contains the DAX formulas used in the **E-Commerce Sales & Order Fulfillment Performance Dashboard**.

> Some measure names in the Power BI file may use slightly different capitalization or spacing. The calculation logic is the same.

---

## Core Sales Measures

### Total Sales

```DAX
Total Sales =
SUM(df_OrderItems[price])
```

**Business question:** How much total product revenue was generated?

### Total Orders

```DAX
Total Orders =
DISTINCTCOUNT(df_Orders[order_id])
```

**Business question:** How many distinct orders were placed?

### Total Customers

```DAX
Total Customers =
DISTINCTCOUNT(df_Customers[customer_id])
```

**Business question:** How many distinct customers are represented?

### Average Order Value

```DAX
Average Order Value =
DIVIDE(
    [Total Sales],
    [Total Orders],
    0
)
```

**Business question:** How much revenue was generated per order on average?

---

## Order Completion Measures

### Completed Orders

```DAX
Completed Orders =
CALCULATE(
    [Total Orders],
    df_Orders[order_status] = "delivered"
)
```

**Business question:** How many orders were successfully delivered?

### Completed Sales

```DAX
Completed Sales =
CALCULATE(
    [Total Sales],
    df_Orders[order_status] = "delivered"
)
```

**Business question:** How much sales revenue came from delivered orders?

### Order Completion Rate

```DAX
Order Completion Rate =
DIVIDE(
    [Completed Orders],
    [Total Orders],
    0
)
```

**Business question:** What percentage of all orders were completed?

> Format as Percentage.

---

## Product, Shipping, and Payment Measures

### Total Shipping Cost

```DAX
Total Shipping Cost =
SUM(df_OrderItems[shipping_charges])
```

### Total Products

```DAX
Total Products =
DISTINCTCOUNT(df_Products[product_id])
```

### Average Item Price

```DAX
Average Item Price =
AVERAGE(df_OrderItems[price])
```

### Average Shipping Cost per Item

```DAX
Average Shipping Cost per Item =
AVERAGE(df_OrderItems[shipping_charges])
```

### Payment Transactions

```DAX
Payment Transactions =
COUNTROWS(df_Payments)
```

---

## Date Table and Time Fields

### Purchase Date Table

```DAX
Purchase Date Table =
CALENDAR(
    MIN(df_Orders[order_purchase_timestamp]),
    MAX(df_Orders[order_purchase_timestamp])
)
```

### Year

```DAX
Year =
YEAR('Purchase Date Table'[Date])
```

### Month Number

```DAX
Month Number =
MONTH('Purchase Date Table'[Date])
```

### Month Name

```DAX
Month Name =
FORMAT(
    'Purchase Date Table'[Date],
    "MMM"
)
```

> Sort `Month Name` by `Month Number`.

### Purchase Date

```DAX
Purchase Date =
DATE(
    YEAR(df_Orders[order_purchase_timestamp]),
    MONTH(df_Orders[order_purchase_timestamp]),
    DAY(df_Orders[order_purchase_timestamp])
)
```

### Year Month

```DAX
Year Month =
FORMAT(
    'Purchase Date Table'[Date],
    "YYYY-MM"
)
```

### Year Month Sort

```DAX
Year Month Sort =
YEAR('Purchase Date Table'[Date]) * 100
    + MONTH('Purchase Date Table'[Date])
```

> Sort `Year Month` by `Year Month Sort`.

---

## Delivery Analysis Calculated Columns

### Delivery Days

```DAX
Delivery Days =
IF(
    NOT ISBLANK(df_Orders[order_delivered_timestamp]),
    DATEDIFF(
        df_Orders[order_purchase_timestamp],
        df_Orders[order_delivered_timestamp],
        DAY
    ),
    BLANK()
)
```

### Delivery Performance

```DAX
Delivery Performance =
IF(
    ISBLANK(df_Orders[order_delivered_timestamp]),
    "Not Delivered",
    IF(
        df_Orders[order_delivered_timestamp]
            <= df_Orders[order_estimated_delivery_date],
        "On Time",
        "Late"
    )
)
```

---

## Delivery Performance Measures

### Average Delivery Days

```DAX
Average Delivery Days =
AVERAGE(df_Orders[Delivery Days])
```

### Late Orders

```DAX
Late Orders =
CALCULATE(
    [Total Orders],
    df_Orders[Delivery Performance] = "Late"
)
```

### On-Time Orders

```DAX
On-Time Orders =
CALCULATE(
    [Total Orders],
    df_Orders[Delivery Performance] = "On Time"
)
```

### Late Delivery Rate

```DAX
Late Delivery Rate =
DIVIDE(
    [Late Orders],
    [Completed Orders],
    0
)
```

> Format as Percentage.

---

## DAX Functions Used

| Function | Purpose |
|---|---|
| `SUM` | Adds sales and shipping values |
| `AVERAGE` | Calculates average prices, costs, and delivery time |
| `DISTINCTCOUNT` | Counts unique orders, customers, and products |
| `COUNTROWS` | Counts payment records |
| `DIVIDE` | Safely calculates ratios |
| `CALCULATE` | Recalculates a measure under a filter |
| `IF` | Applies conditional logic |
| `ISBLANK` | Checks for missing delivery timestamps |
| `DATEDIFF` | Calculates delivery duration |
| `CALENDAR` | Creates a continuous date table |
| `YEAR` | Extracts a year |
| `MONTH` | Extracts a month number |
| `DATE` | Creates a date-only value |
| `FORMAT` | Creates readable date labels |
| `BLANK` | Returns an empty result when appropriate |

---

## Model Relationships Supporting the Measures

```text
df_Customers (1) -> (*) df_Orders
df_Orders (1) -> (*) df_OrderItems
df_Products (1) -> (*) df_OrderItems
df_Orders (1) -> (*) df_Payments
Purchase Date Table (1) -> (*) df_Orders
```

The date table connects to `df_Orders[Purchase Date]`, not directly to the original timestamp field.
