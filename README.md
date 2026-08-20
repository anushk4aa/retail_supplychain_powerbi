# Retail & Supply Chain Analytics — Power BI Project

An end-to-end Power BI project on a simulated retail chain (18 stores + 4 regional
online channels, 3 fiscal years, 50 SKUs across 5 categories, 5 suppliers, 2,500
customers). Covers sales performance, profitability, customer segmentation, and
supply-chain health (inventory, stockouts, supplier lead time) — plus time
intelligence, row-level security, what-if pricing simulation, and forecasting.

## Files
| File | Purpose |
|---|---|
| `supply_chainproject.pbix` | The full Power BI report — open in Power BI Desktop |
| `Retail_SupplyChain_Dataset.xlsx` | Source data — 5 dimension tables + 2 fact tables |
| `DAX_Measures.md` | All DAX measures used, organized by category |
| `Build_Guide.md` | Step-by-step build instructions (model, RLS, what-if, forecast) |

## Data model
Star schema: `Dim_Date`, `Dim_Product`, `Dim_Store`, `Dim_Customer`, `Dim_Supplier`
connected to `Fact_Sales` and `Fact_Inventory`.

## Features
- Time intelligence (YoY, MoM, YTD, rolling averages)
- Row-level security — 5 regional manager roles
- Interactive what-if price simulator
- Revenue forecasting with confidence intervals
- Supply-chain KPIs: stockout rate, fill rate, inventory turnover, days of supply

## How to use
Download `supply_chainproject.pbix` and open it in Power BI Desktop (free). If the
data source needs relinking, point it at `Retail_SupplyChain_Dataset.xlsx` in this repo.
