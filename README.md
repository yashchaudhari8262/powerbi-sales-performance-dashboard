# Sales Performance & Target Achievement Dashboard (Power BI)

A 3-page interactive Power BI dashboard that tracks sales against targets across regions, product categories and customer segments, built on a star-schema data model with DAX measures.

> **Note:** The dataset is synthetic (generated for learning and portfolio purposes). It is modelled on a typical Indian retail/office-supplies business, with amounts in INR.

## Objectives

- Measure actual sales against monthly targets by Region and Category
- Find which regions and categories are under-performing
- Understand which products, sub-categories, customer segments and states drive revenue
- Track year-over-year growth and profit margin

## Dataset

| File | Rows | Description |
|---|---|---|
| `sales.csv` | 4,500 | Order-level transactions (Jan 2024 - Dec 2025): quantity, price, discount, sales amount, cost, profit |
| `customers.csv` | 250 | Customer name, segment (Consumer / Corporate / Small Business), city, state, region |
| `products.csv` | 18 | Product, category, sub-category, unit price, unit cost |
| `targets.csv` | 288 | Monthly sales target by Region x Category |

The data is clean: no nulls, no duplicate rows, and every `CustomerID` / `ProductID` in `sales` matches its dimension table.

## Data Model

Star schema with `sales` and `targets` as fact tables:

- `customers` -> `sales` (CustomerID)
- `products` -> `sales` (ProductID)
- `Region` -> `customers`, `targets` (Region)
- `Category` -> `products`, `targets` (Category)
- `Calendar` -> `sales[OrderDate]`, `targets[MonthStart]`

`Region`, `Category` and `Calendar` are small dimension tables created in DAX, which lets one slicer filter both sales and targets.

## Key DAX Measures

```DAX
Total Sales = SUM(sales[SalesAmount])
Total Targets = SUM(targets[SalesTarget])
Achievement % = DIVIDE([Total Sales], [Total Targets])
Variance = [Total Sales] - [Total Targets]
Profit Margin % = DIVIDE([Total Profit], [Total Sales])
Avg Order Value = DIVIDE([Total Sales], [Total Orders])

Sales YoY % =
VAR CY = MAX('Calendar'[Year])
VAR CurrSales = CALCULATE([Total Sales], 'Calendar'[Year] = CY)
VAR PrevSales = CALCULATE([Total Sales], 'Calendar'[Year] = CY - 1)
RETURN DIVIDE(CurrSales - PrevSales, PrevSales)

Status Color =
SWITCH(TRUE(),
    [Achievement %] >= 1, "#2E7D32",
    [Achievement %] >= 0.95, "#F9A825",
    "#C62828")
```

## Dashboard Pages

> The screenshots below are filtered to **Year = 2025**. With no year selected, the dashboard shows both years (2024-2025).

### Page 1: Executive Overview

![Executive Overview](screenshots/1_executive_overview.png)

A one-glance summary of how the business is performing against its targets.

- **KPI cards:** Total Sales, Total Targets, Achievement % and Profit Margin %. For 2025: INR 39.49M sales vs INR 41.40M target, **95.39%** achievement, **22.20%** profit margin.
- **Monthly Sales vs Target (line chart):** shows how sales track the target month by month on a single shared axis. In 2025 sales stayed just below target in every month. The gap was smallest in June (99.3%), April and December (98.4%), and largest in May (90.7%), March (92.9%) and January (93.1%). Both lines peak in September-December and dip in February and June.
- **Sales by Region (bar chart):** West (INR 14.4M) and South (INR 10.9M) are the largest markets; North and East are smaller.
- **Slicers:** Year, Region and Category filter every visual on the page. Page navigation buttons at the top switch between the three pages.

### Page 2: Target Achievement Analysis

![Target Achievement Analysis](screenshots/2_target_analysis.png)

Shows where targets are being missed, by region and by category.

- **Sales vs Target by Region:** compares actual sales to target for each region. West and South have the biggest gaps.
- **Achievement % by Category:** Office Supplies (98.09%) and Furniture (97.58%) are close to target; Technology (94.12%) is the weakest.
- **Overall Achievement (gauge):** overall achievement of 95.39% against the 100% target.
- **Region & Category Performance (matrix):** Total Sales, Total Targets, Variance and Achievement % for each region, expandable to category level. Status colours show green for 100% or more, amber for 95-100%, and red below 95%. The total shortfall for 2025 is about INR 1.91M, mostly from West (-INR 998K) and South (-INR 749K).

### Page 3: Product & Customer Insights

![Product & Customer Insights](screenshots/3_product_customer.png)

Explains what is being sold and who is buying it.

- **Cards:** 2,314 orders in 2025, average order value of about INR 17,067, and year-over-year sales growth of 7.1%.
- **Top 10 Products by Sales:** Laptop (INR 10.7M), Smartphone (INR 6.5M) and Tablet (INR 5.1M) lead.
- **Sales by Sub-Category (treemap):** Computers is by far the largest sub-category, followed by Mobiles and Tables.
- **Sales by Customer Segment (donut):** Consumer 54.06%, Corporate 29.92%, Small Business 16.02%.
- **Sales by State (map):** each state has its own colour, so the geographic spread of sales is visible at a glance; hovering shows the sales value.
- **Slicers:** Year and Customer Segment filter all visuals on the page.

## Key Insights

- **Overall:** Total sales of about INR 76.4M against a target of INR 79.3M, an achievement of **96.25%**, with a **22.4%** profit margin.
- **Growth:** Sales grew **7.1%** from 2024 (INR 36.9M) to 2025 (INR 39.5M), but achievement fell from **97.2%** to **95.4%** because targets grew faster (+9.1%) than sales.
- **Regions:** In 2025, **West (93.5%)** and **South (93.6%)** missed targets the most, while **East (99.8%)** and **North (98.0%)** came close to target.
- **Categories:** Technology generates about 65% of revenue but has the lowest margin (**17.5%**) and the lowest 2025 achievement (**94.1%**). Office Supplies has the best margin (**36.3%**).
- **Products:** Laptops (INR 21.6M), Smartphones (INR 12.3M) and Tablets (INR 9.5M) are the top 3 products.
- **Customers:** Consumers contribute about 53% of sales, Corporate about 30%, and Small Business about 17%.
- **Discounts:** Profit margin drops from **26.7%** with no discount to **8.5%** at a 20% discount, so heavy discounting erodes profit.
- **Seasonality:** December, November and September are the strongest months; February and June are the weakest.

## Tools & Skills

Power BI Desktop, Power Query, Data Modeling (star schema), DAX, data visualization, dashboard design.

## Files in this Repository

```
├── data/
│   ├── sales.csv
│   ├── customers.csv
│   ├── products.csv
│   └── targets.csv
├── Sales_Performance_Dashboard.pbix
├── screenshots/
│   ├── 1_executive_overview.png
│   ├── 2_target_analysis.png
│   └── 3_product_customer.png
└── README.md
```

## Author

**Yash Chaudhari**
[GitHub](https://github.com/yashchaudhari8262) | [LinkedIn](https://www.linkedin.com/in/yash-chaudhari-078b41375)
