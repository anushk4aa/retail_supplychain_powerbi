🛒 Retail & Supply Chain Analytics — Power BI

An end-to-end Power BI analytics project designed to evaluate retail sales performance, profitability, customer behaviour, inventory health, and supply-chain efficiency.

The project uses a star-schema data model combining sales, inventory, customer, product, store, supplier, and date data to transform raw transactional data into an interactive business intelligence dashboard.

📊 Project Overview

The dashboard provides a consolidated view of:

* 💰 Sales & revenue performance
* 📈 Profitability and profit margins
* 👥 Customer behaviour and segmentation
* 📦 Inventory levels and stockout risk
* 🚚 Supplier and supply-chain performance
* 🏪 Store and regional performance
* 📅 Time-based business trends
* 🔮 Forecasting and future performance
* 🎯 What-if pricing analysis

The project is built around a simulated retail business with 18 physical stores and 4 regional online channels, covering 3 fiscal years, 50 SKUs across 5 categories, 5 suppliers, and 2,500 customers.

⸻

🧩 Data Model

The project follows a star schema consisting of five dimension tables and two fact tables.

Dimension Tables

Table	Description
Dim_Customer.csv	Customer information and segmentation attributes
Dim_Date.csv	Calendar and fiscal date information
Dim_Product.csv	Product, category, and SKU information
Dim_Store.csv	Store and regional information
Dim_Supplier.csv	Supplier information and supply-chain attributes

Fact Tables

Table	Description
Fact_Sales.csv	Sales transactions, revenue, quantity, discounts and profit
Fact_Inventory.csv	Inventory levels, stock availability and supply-chain metrics

The model connects the dimension tables to the sales and inventory fact tables to enable cross-functional analysis across products, customers, stores, suppliers and time.

⸻

📌 Key Business KPIs

Sales & Profitability

* Revenue
* Total Orders
* Units Sold
* Profit
* Profit Margin
* Average Order Value (AOV)
* Sales Growth
* YoY / MoM Performance

Customer Analytics

* Customer count
* Customer segmentation
* Customer purchase behaviour
* Revenue contribution by customer segment
* RFM-based customer analysis

Inventory & Supply Chain

* Inventory levels
* Stockout rate
* Fill rate
* Inventory turnover
* Days of supply
* Supplier performance
* Lead-time analysis
* Inventory risk

⸻

⚙️ Power BI Features

⏱️ Time Intelligence

Implemented time-based calculations including:

* Year-over-Year (YoY)
* Month-over-Month (MoM)
* Year-to-Date (YTD)
* Rolling averages
* Fiscal-year analysis

🔐 Row-Level Security

Implemented Row-Level Security (RLS) to control access across regional manager roles and ensure users only see the data relevant to their assigned region.

🎯 What-If Pricing Simulator

An interactive pricing simulation allows users to adjust pricing assumptions and evaluate the potential impact on revenue and profitability.

🔮 Forecasting

Sales forecasting is incorporated to identify expected future performance and support planning and decision-making.

📦 Supply-Chain Monitoring

Inventory and supply-chain metrics are integrated into the same analytical environment to identify potential stockouts, inventory risks and supplier-related issues.

⸻

📁 Repository Structure

retail_supplychain_powerbi/
│
├── Dim_Customer.csv
├── Dim_Date.csv
├── Dim_Product.csv
├── Dim_Store.csv
├── Dim_Supplier.csv
│
├── Fact_Inventory.csv
├── Fact_Sales.csv
│
├── supply_chainproject.pbix
│
├── dax_measures.md
├── buildguide.md
│
└── README.md

⸻

🧮 DAX

The project uses DAX to create calculated measures for:

* Revenue and profit
* Profit margin
* Sales growth
* Time intelligence
* Customer metrics
* Inventory KPIs
* Supply-chain metrics
* Forecasting
* What-if analysis

A complete collection of the project’s DAX measures is available in:

dax_measures.md

⸻

🛠️ Tools & Technologies

* Power BI Desktop
* DAX
* Power Query
* Data Modelling
* Star Schema
* CSV / Tabular Data
* Row-Level Security
* What-If Parameters
* Forecasting & Time-Series Analysis

⸻

🚀 How to Use

1. Clone the repository

git clone https://github.com/anushk4aa/retail_supplychain_powerbi.git

2. Open the Power BI report

Open:

supply_chainproject.pbix

using Power BI Desktop.

3. Check the data sources

If Power BI asks you to locate the source files, connect the queries to the corresponding CSV files in the repository:

Dim_Customer.csv
Dim_Date.csv
Dim_Product.csv
Dim_Store.csv
Dim_Supplier.csv
Fact_Inventory.csv
Fact_Sales.csv

4. Explore the dashboard

Use the interactive filters and visualisations to analyse sales, profitability, customers, inventory and supply-chain performance.

⸻

📖 Build Guide

For a detailed explanation of the modelling process, Power BI setup, RLS, forecasting and other implementation steps, see:

buildguide.md

⸻

🎯 Business Objective

The primary objective of this project is to demonstrate how business intelligence and data analytics can be used to support retail decision-making.

Instead of analysing sales, customers, inventory and suppliers separately, the dashboard brings these areas together to help answer questions such as:

* Which products and categories generate the most revenue and profit?
* Which stores or regions are performing best?
* Which customers contribute the most value?
* Where are inventory shortages occurring?
* Which products have high inventory risk?
* How efficiently are suppliers performing?
* How could pricing changes affect revenue?
* What does future sales performance look like?

⸻

📈 Outcome

The resulting Power BI solution provides a centralized analytical view of the retail business, allowing stakeholders to move from raw transactional data → KPIs → trends → business insights → informed decisions.

⸻

👩‍💻 Author

Anushka Rana

Information Technology | Data Analytics & Business Intelligence

GitHub: @anushk4aa
