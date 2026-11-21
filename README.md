# Pizza Sales & Order Operations Analytics (SQL & Excel)

An end-to-end sales, order-volume, and menu-performance analytics project analyzing **48,620 order line items** across **21,350 unique orders** and **$817,860.05** in total revenue for a pizza retail business.

---

## 📋 Table of Contents
1. [Business Problem & Analytical Scope](#-business-problem--analytical-scope)
2. [Tech Stack & Tools](#-tech-stack--tools)
3. [Data Breakdown: Dimension Attributes & Fact Metrics](#-data-breakdown-dimension-attributes--fact-metrics)
4. [SQL Query Logic & KPI Definitions](#-sql-query-logic--kpi-definitions)
5. [Key Performance Indicators (KPIs) & Analytical Findings](#-key-performance-indicators-kpis--analytical-findings)
6. [Strategic Recommendations & Action Plan](#-strategic-recommendations--action-plan)
7. [Repository Structure](#-repository-structure)

---

## 🎯 Business Problem & Analytical Scope

To help restaurant operations and menu leadership optimize staffing, inventory prep, and menu mix, this project answers core operational and revenue questions across order line items, time windows, categories, sizes, and individual pizza SKUs:

* **Headline Sales & Basket Efficiency:** What are total revenue, total pizzas sold, total orders, Average Order Value (AOV), and average pizzas per order?
* **Peak Demand Windows:** Which days of the week and hours of the day drive the highest order volume (including filterable month and quarter views)?
* **Category, Size & SKU Performance:** How do revenue share and unit sales distribute across `Classic`, `Chicken`, `Veggie`, and `Supreme` categories, sizes (`S`, `M`, `L`, `XL`, `XXL`), and best- vs. bottom-selling pizzas?

---

## 🛠️ Tech Stack & Tools

* **SQL:** Aggregations (`SUM`, `COUNT(DISTINCT)`, `AVG`), decimal casting (`CAST(... AS DECIMAL(10,2))`), date/time functions (`DATENAME(DW, order_date)`, `DATEPART(HOUR, order_time)`, `MONTH(order_date)`, `DATEPART(QUARTER, order_date)`), subqueries for revenue percentage share, and `TOP 5` rankings.
* **Excel & CSV (`pizza_sales excel file.xlsx`, `pizza_sales.csv`):** Source order dataset (`48,620` rows × `12` columns, zero missing values) and spreadsheet analysis/reporting workbook.

---

## 🗂️ Data Breakdown: Dimension Attributes & Fact Metrics

The source dataset (`pizza_sales` / `pizza_sales.csv`, **48,620 records**) is stored as a single order-line table with **12 columns** and no null/missing values. For analytical modeling and reporting, its columns break down into **Dimension attributes** (descriptive slicing, filtering, and grouping context) and **Fact metrics/identifiers** (line-item quantities, prices, and order keys):

### 1. Dimension Attributes (Descriptive & Grouping Dimensions)
| Analytical Dimension | Source Columns in `pizza_sales` | Role in Analysis & Grouping |
| :--- | :--- | :--- |
| **Pizza Menu & Recipe** | `pizza_name_id`, `pizza_name`, `pizza_category`, `pizza_size`, `pizza_ingredients` | Segments menu performance across 4 categories (`Classic`, `Chicken`, `Veggie`, `Supreme`), 5 sizes (`L`, `M`, `S`, `XL`, `XXL`), individual pizzas, and ingredients. |
| **Order Date & Time** | `order_date`, `order_time` | Supports day-of-week trends (`DATENAME(DW, ...)`), hourly demand (`DATEPART(HOUR, ...)`), and monthly/quarterly filtering (`MONTH`, `DATEPART(QUARTER, ...)`). |

### 2. Fact Metrics & Identifiers (Line-Item & Order Measures)
| Metric / Field Type | Source Columns in `pizza_sales` | Aggregation & Analytical Use |
| :--- | :--- | :--- |
| **Line & Order Keys** | `pizza_id`, `order_id` | `COUNT(DISTINCT order_id)` yields total unique orders (`21,350`) across `48,620` line items (`pizza_id`). |
| **Pricing & Quantity Facts** | `quantity`, `unit_price`, `total_price` | `SUM(total_price)` yields total revenue (`$817,860.05`); `SUM(quantity)` yields total pizzas sold (`49,574`); `total_price` reflects `quantity * unit_price` per line item. |

---

## 💻 SQL Query Logic & KPI Definitions

Core SQL patterns documented in `PIZZA SALES SQL QUERIES.pdf` against `pizza_sales`:

* **Core KPIs:**
  * **Total Revenue:** `SUM(total_price)` (`$817,860.05`)
  * **Average Order Value (AOV):** `SUM(total_price) / COUNT(DISTINCT order_id)` (`$38.31`)
  * **Total Pizzas Sold:** `SUM(quantity)` (`49,574`)
  * **Total Orders:** `COUNT(DISTINCT order_id)` (`21,350`)
  * **Average Pizzas per Order:** `CAST(CAST(SUM(quantity) AS DECIMAL(10,2)) / CAST(COUNT(DISTINCT order_id) AS DECIMAL(10,2)) AS DECIMAL(10,2))` (`2.32`)
* **Temporal Trends:** `GROUP BY DATENAME(DW, order_date)` for daily orders and `GROUP BY DATEPART(HOUR, order_time)` ordered by hour (`9` to `23`) for intraday demand, plus `WHERE MONTH(order_date) = ...` and `WHERE DATEPART(QUARTER, order_date) = ...` filter examples.
* **Share of Total Revenue & Rankings:** Category and size revenue percentages calculated via scalar subquery `(SELECT SUM(total_price) FROM pizza_sales)`, paired with `TOP 5 ... ORDER BY SUM(quantity) DESC/ASC` for best and bottom sellers.

---

## 📊 Key Performance Indicators (KPIs) & Analytical Findings

* **Headline Performance:** **$817,860.05** total revenue across **21,350** orders and **49,574** pizzas sold, with a **$38.31** AOV and **2.32** average pizzas per order.
* **Peak Days & Hours:** Orders peak on **Friday (3,538 orders)**, **Thursday (3,239)**, and **Saturday (3,158)**, with lowest volume on **Sunday (2,624)**. Hourly demand concentrates around **lunch (12:00 PM: 2,520 orders; 1:00 PM: 2,455)** and **dinner (6:00 PM: 2,399; 5:00 PM: 2,336; 7:00 PM: 2,009)**, tapering sharply after **9:00 PM (1,198)** and **10:00 PM (663)**.
* **Category & Size Mix:**
  * **By Category:** `Classic` leads revenue (**$220,053.10**, **26.91%**) and volume (**14,888** pizzas), followed by `Supreme` (**$208,197.00**, **25.46%**; **11,987** pizzas), `Chicken` (**$195,919.50**, **23.96%**; **11,050** pizzas), and `Veggie` (**$193,690.45**, **23.68%**; **11,649** pizzas).
  * **By Size:** `L` drives **45.89%** of revenue (**$375,318.70**; **18,956** pizzas), `M` drives **30.49%** (**$249,382.25**; **15,635** pizzas), and `S` drives **21.77%** (**$178,076.50**; **14,403** pizzas), while `XL` (**$14,076.00**, **1.72%**) and `XXL` (**$1,006.60**, **0.12%**) make up less than **2%** combined.
* **Top vs. Bottom 5 Pizzas (by Quantity Sold):**
  * **Top 5:** *The Classic Deluxe Pizza* (`2,453`), *The Barbecue Chicken Pizza* (`2,432`), *The Hawaiian Pizza* (`2,422`), *The Pepperoni Pizza* (`2,418`), and *The Thai Chicken Pizza* (`2,371`).
  * **Bottom 5:** *The Brie Carre Pizza* (`490`), *The Mediterranean Pizza* (`934`), *The Calabrese Pizza* (`937`), *The Spinach Supreme Pizza* (`950`), and *The Soppressata Pizza* (`961`).
 
  1. Dashboard Screenshot
     <img width="1252" height="711" alt="Screenshot 2026-11-22 012133" src="https://github.com/user-attachments/assets/351ffec9-1837-43b6-acf3-4e4cde3a6be3" />


---

## 🚀 Strategic Recommendations & Action Plan

1. **Staff & Prep for Lunch and Evening Rush:** Align kitchen prep, dough/topping inventory, and delivery/counter staffing ahead of **12:00 PM–1:00 PM** and **5:00 PM–7:00 PM**, especially on **Thursday–Saturday** peaks.
2. **Protect Core Bestsellers & Large/Medium Formats:** Maintain stock availability and combo/bundle promotions around top-moving `Classic` and `Chicken` pizzas and high-revenue `L` and `M` sizes.
3. **Evaluate Underperforming SKUs & Niche Sizes:** Audit low-velocity items—especially *The Brie Carre Pizza* (`490` units) and `XL`/`XXL` sizes—for menu rationalization, recipe/pricing review, or limited-time promotion.

---

## 📁 Repository Structure

```text
pizza-sales-analytics/
├── README.md
├── data/
│   ├── pizza_sales.csv
│   └── pizza_sales excel file.xlsx
└── sql/
    └── PIZZA SALES SQL QUERIES.pdf
