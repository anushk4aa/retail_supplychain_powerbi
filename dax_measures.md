1. Core Sales & Profitability
dax
Total Revenue =
SUMX ( Fact_Sales,
    Fact_Sales[Quantity] * Fact_Sales[UnitPrice] * (1 - Fact_Sales[DiscountPercent])
)
dax
Total Cost =
SUMX ( Fact_Sales, Fact_Sales[Quantity] * Fact_Sales[UnitCost] )
dax
Gross Profit = [Total Revenue] - [Total Cost]
dax
Gross Margin % = DIVIDE ( [Gross Profit], [Total Revenue] )
dax
Total Units Sold = SUM ( Fact_Sales[Quantity] )
dax
Total Orders = DISTINCTCOUNT ( Fact_Sales[OrderKey] )
dax
Avg Order Value = DIVIDE ( [Total Revenue], [Total Orders] )
dax
Total Discount Given =
SUMX ( Fact_Sales,
    Fact_Sales[Quantity] * Fact_Sales[UnitPrice] * Fact_Sales[DiscountPercent]
)
dax
Discount % of Revenue =
DIVIDE ( [Total Discount Given], [Total Discount Given] + [Total Revenue] )
2. Time Intelligence

Requires Dim_Date[DateKey] marked as the model's Date Table (Table tools › Mark as date table) and a relationship from Dim_Date[DateKey] to Fact_Sales[DateKey].

dax
Revenue PY = CALCULATE ( [Total Revenue], SAMEPERIODLASTYEAR ( Dim_Date[DateKey] ) )
dax
Revenue YoY % = DIVIDE ( [Total Revenue] - [Revenue PY], [Revenue PY] )
dax
Revenue YTD = TOTALYTD ( [Total Revenue], Dim_Date[DateKey] )
dax
Revenue QTD = TOTALQTD ( [Total Revenue], Dim_Date[DateKey] )
dax
Revenue MTD = TOTALMTD ( [Total Revenue], Dim_Date[DateKey] )
dax
Revenue Prior Month =
CALCULATE ( [Total Revenue], DATEADD ( Dim_Date[DateKey], -1, MONTH ) )
dax
Revenue MoM % = DIVIDE ( [Total Revenue] - [Revenue Prior Month], [Revenue Prior Month] )
dax
Revenue Rolling 3M =
CALCULATE (
    [Total Revenue],
    DATESINPERIOD ( Dim_Date[DateKey], MAX ( Dim_Date[DateKey] ), -3, MONTH )
)
dax
Revenue Fiscal YTD =
TOTALYTD ( [Total Revenue], Dim_Date[DateKey], "3/31" )

(Fiscal year end March 31 — matches the FiscalYear/FiscalQuarter columns in Dim_Date.)

3. Supply Chain & Inventory KPIs

Create these on Fact_Inventory.

dax
Avg Stock On Hand = AVERAGE ( Fact_Inventory[StockOnHandEnd] )
dax
Total Units Received = SUM ( Fact_Inventory[UnitsReceived] )
dax
Total Demand Units = SUM ( Fact_Inventory[DemandUnits] )
dax
Stockout Weeks =
CALCULATE ( COUNTROWS ( Fact_Inventory ), Fact_Inventory[StockoutFlag] = TRUE )
dax
Stockout Rate % = DIVIDE ( [Stockout Weeks], COUNTROWS ( Fact_Inventory ) )
dax
Fill Rate % =
DIVIDE ( SUM ( Fact_Inventory[UnitsSold] ), [Total Demand Units] )
dax
Inventory Turnover (Annualized) =
VAR WeeksInView = DISTINCTCOUNT ( Fact_Inventory[DateKey] )
VAR UnitsSoldInView = SUM ( Fact_Inventory[UnitsSold] )
VAR AvgStock = [Avg Stock On Hand]
RETURN
DIVIDE ( UnitsSoldInView, AvgStock ) * DIVIDE ( 52, WeeksInView )
dax
Days of Supply =
VAR WeeksInView = DISTINCTCOUNT ( Fact_Inventory[DateKey] )
VAR AvgWeeklyDemand = DIVIDE ( [Total Demand Units], WeeksInView )
VAR AvgDailyDemand = DIVIDE ( AvgWeeklyDemand, 7 )
RETURN
DIVIDE ( [Avg Stock On Hand], AvgDailyDemand )
dax
Avg Supplier Lead Time (Days) =
AVERAGEX ( Dim_Product, RELATED ( Dim_Supplier[LeadTimeDays] ) )
4. What-If Parameter — Price Simulation

Create the parameter first: Modeling ribbon › New Parameter › Numeric range

Name: Price Increase %
Data type: Decimal
Min: -0.20 Max: 0.30 Increment: 0.01 Default: 0

Power BI auto-creates a table Price Increase % with a field Price Increase % Value. Add this measure:

dax
Simulated Revenue =
SUMX (
    Fact_Sales,
    Fact_Sales[Quantity]
        * Fact_Sales[UnitPrice]
        * ( 1 + SELECTEDVALUE ( 'Price Increase %'[Price Increase % Value], 0 ) )
        * ( 1 - Fact_Sales[DiscountPercent] )
)
dax
Revenue Impact from Price Change = [Simulated Revenue] - [Total Revenue]
dax
Simulated Gross Margin % =
VAR SimCost = [Total Cost]
RETURN DIVIDE ( [Simulated Revenue] - SimCost, [Simulated Revenue] )

Add the Price Increase % slicer to a page, drop Simulated Revenue and Total Revenue side by side on a card/chart — dragging the slider updates Simulated Revenue live.

5. Advanced — DAX Linear Trend (optional, alongside built-in Forecast)

Power BI's built-in Analytics pane › Forecast on a line chart is the easiest way to forecast (see Build_Guide.md). For an explicit DAX trend-line measure:

dax
Revenue Trend (Linear) =
VAR Summary =
    SUMMARIZE ( Dim_Date, Dim_Date[DateKey], "Rev", [Total Revenue] )
VAR WithIndex =
    ADDCOLUMNS ( Summary, "X", RANKX ( Summary, Dim_Date[DateKey],, ASC ) )
VAR N = COUNTROWS ( WithIndex )
VAR SumX = SUMX ( WithIndex, [X] )
VAR SumY = SUMX ( WithIndex, [Rev] )
VAR SumXY = SUMX ( WithIndex, [X] * [Rev] )
VAR SumX2 = SUMX ( WithIndex, [X] ^ 2 )
VAR Slope = DIVIDE ( N * SumXY - SumX * SumY, N * SumX2 - SumX ^ 2 )
VAR Intercept = DIVIDE ( SumY - Slope * SumX, N )
VAR CurrentX = RANKX ( ALL ( Dim_Date[DateKey] ), MAX ( Dim_Date[DateKey] ),, ASC )
RETURN
Intercept + Slope * CurrentX

Plot Total Revenue and Revenue Trend (Linear) on the same line chart by Dim_Date[DateKey] — the trend line is a straight OLS fit, useful to explain the math behind forecasting if asked in an interview.

6. Row-Level Security helper (used with roles — see Build_Guide.md)

No measure needed for the simple per-region role, but if you want a mapping-table version (closer to real enterprise RLS), add a Dim_UserRegion table:

UserEmail	Region
north.manager@retailco.com	North
south.manager@retailco.com	South
...	...

Then on Dim_Store, create a role filter:

dax
[Region] =
LOOKUPVALUE ( Dim_UserRegion[Region], Dim_UserRegion[UserEm