1. Import the data
Power BI Desktop › Get Data › Excel Workbook › select Retail_SupplyChain_Dataset.xlsx.
Check all 7 sheets (Dim_Date, Dim_Product, Dim_Store, Dim_Customer, Dim_Supplier, Fact_Sales, Fact_Inventory) › Transform Data (not Load) so you land in Power Query.
In Power Query, confirm data types: DateKey columns → Date, UnitPrice/UnitCost → Decimal, StockoutFlag → True/False. Fix any that imported as text.
Close & Apply.
2. Build the data model

Go to Model view and create these relationships (drag from the "many" fact table field to the "one" dimension field — Power BI usually auto-detects most of these on load, just verify):

From (many side)	To (one side)	Cardinality	Cross-filter
Fact_Sales[DateKey]	Dim_Date[DateKey]	Many:1	Single
Fact_Sales[ProductKey]	Dim_Product[ProductKey]	Many:1	Single
Fact_Sales[StoreKey]	Dim_Store[StoreKey]	Many:1	Single
Fact_Sales[CustomerKey]	Dim_Customer[CustomerKey]	Many:1	Single
Fact_Inventory[DateKey]	Dim_Date[DateKey]	Many:1	Single
Fact_Inventory[ProductKey]	Dim_Product[ProductKey]	Many:1	Single
Fact_Inventory[StoreKey]	Dim_Store[StoreKey]	Many:1	Single
Dim_Product[SupplierKey]	Dim_Supplier[SupplierKey]	Many:1	Single

This gives a clean star schema — two fact tables sharing the same dimensions.

Mark Dim_Date as the official date table

Select Dim_Date table › Table tools ribbon › Mark as Date Table › pick DateKey as the unique identifier. Required for every time-intelligence measure in DAX_Measures.md (§2) to work.

3. Load the DAX measures

Create a blank measures table for organization: Modeling › New Table:

dax
_Measures = { BLANK() }

Hide its default column. Then paste in every measure from DAX_Measures.md sections 1–3 (you can create them on _Measures instead of the fact tables if you prefer a single home for all of them — just adjust table references in the formulas, which already use fully-qualified names so this works either way).

4. What-If Parameter (price simulation)
Modeling ribbon › New Parameter › Numeric range.
Name Price Increase %, Data type Decimal, Min -0.20, Max 0.30, Increment 0.01, Default 0. Check Add slicer to this page.
Add the two measures from DAX_Measures.md §4 (Simulated Revenue, Revenue Impact from Price Change).
On the "What-If Simulator" page: put the slicer at the top, a card for Total Revenue, a card for Simulated Revenue, and a card for Revenue Impact from Price Change with conditional formatting (green if positive, red if negative).
5. Forecasting (built-in, no extra data needed)
Build a line chart: X-axis = Dim_Date[DateKey] (set to Date, not hierarchy — click the field's dropdown arrow on the axis and pick the plain date, not the auto Year/Quarter/Month/Day hierarchy), Y-axis = Total Revenue.
Filter or aggregate to monthly grain for a cleaner forecast (drag DateKey at Month granularity, or use Dim_Date[YearMonth]).
Click the visual › Analytics pane (magnifying-glass icon) › Forecast › Add. Set Forecast length (e.g., 3 periods), Seasonality (try 12 for monthly data with yearly seasonality, or leave on Auto), Confidence interval 95%. Power BI extends the line with a shaded confidence band.
Optionally overlay the Revenue Trend (Linear) measure (DAX_Measures.md §5) as a second line to show you also understand the underlying math.
6. Row-Level Security (RLS)
Quick version (5 static regional roles)
Modeling ribbon › Manage Roles › Create.
Create 5 roles: North Manager, South Manager, East Manager, West Manager, Central Manager.
For each role, select table Dim_Store and enter a DAX filter, e.g. for North Manager:
dax
   [Region] = "North"

Repeat per region. Because Dim_Store filters Fact_Sales and Fact_Inventory via the relationships from step 2, this restricts both fact tables automatically. 4. Test it: Modeling ribbon › View as › tick a role (e.g. North Manager) › OK. Confirm every visual now shows North-only data. Untick when done.

Enterprise-style version (mapping table)

If you want to demo the more realistic pattern used in production Power BI Service deployments:

Add a small Dim_UserRegion table (email → region) — see DAX_Measures.md §6 for the shape — via Enter Data.
Create one role, e.g. Regional Manager, with the filter on Dim_Store:
dax
   [Region] = LOOKUPVALUE ( Dim_UserRegion[Region], Dim_UserRegion[UserEmail], USERPRINCIPALNAME() )
In the Power BI Service, each user is assigned to the one role and automatically sees only their mapped region — no per-user role needed. Mention this version in interviews to show you know the difference between demo RLS and how it's actually deployed for many users.
7. Report pages to build

Page 1 — Executive Overview

KPI cards: Total Revenue, Gross Margin %, Total Orders, Revenue YoY %
Line chart: Total Revenue by month with Forecast (step 5)
Bar chart: Revenue by Region
Donut: Revenue by Category

Page 2 — Sales & Profitability

Matrix: Category × Sub-Category with Total Revenue, Gross Margin %, Total Units Sold
Scatter: Products plotted Total Units Sold (x) vs Gross Margin % (y), bubble size = Total Revenue — instantly shows high-volume/low-margin vs low-volume/high-margin products
Bar: Total Discount Given by Month, filter for Oct/Nov festive season

Page 3 — Customer & Region

Donut: Revenue by Customer Segment
Bar: Revenue by Region × Store Type
Table: Top 20 customers by Total Revenue

Page 4 — Supply Chain & Inventory

Cards: Stockout Rate %, Fill Rate %, Inventory Turnover (Annualized), Avg Supplier Lead Time (Days)
Bar: Stockout Rate % by Store/Region
Line: Avg Stock On Hand vs Reorder Level by month, per category
Table: Dim_Supplier with Avg Supplier Lead Time and ReliabilityScore, conditional-formatted

Page 5 — What-If Simulator

Built in step 4 above
8. Polish checklist
Consistent color theme (View › Themes) — pick one and apply everywhere
Rename all measures/pages with business-friendly names (already done above)
Add a text box with your name / "Portfolio Project" and a 1-line description on Page 1
Publish to Power BI Service if you want a shareable link (Home › Publish), then assign the RLS roles to yourself under different test accounts if available, or just demo "View as Role" locally during interviews.