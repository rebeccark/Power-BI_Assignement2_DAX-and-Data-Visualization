# Power-BI_Assignement2_DAX-and-Data-Visualization
DAX and Data Visualization
Part 1: Load and clean the data
Home → Get Data → Text/CSV and load all three files. Click Transform Data, not Load.

Part 2: Data model

Create a Calendar table.

DAX
Calendar = CALENDAR(DATE(2018,4,1), DATE(2019,3,31))

DAX
Month = FORMAT('Calendar'[Date], "MMM-YY")
MonthSort = YEAR('Calendar'[Date]) * 100 + MONTH('Calendar'[Date])
Select Month → Column tools → Sort by column → MonthSort. This keeps months in date order instead of alphabetical.
Right-click Calendar → Mark as date table → pick Date.

Create a Category table (New Table):

DAX
Category = DISTINCT('Order Details'[Category])

Relationships. Go to Model view and drag fields between tables:

From (one side)	To (many side)
Orders[Order ID]	Order Details[Order ID]
Calendar[Date]	Orders[Order Date]
Calendar[Date]	Sales Target[Month Date]
Category[Category]	Order Details[Category]
Category[Category]	Sales Target[Category]

Part 3: Calculated columns (in Order Details)


DAX
Category Type = 'Order Details'[Category] & " - " & 'Order Details'[Sub-Category]
DAX
Revenue = 'Order Details'[Amount] * 'Order Details'[Quantity]
DAX
Sales Category =
IF('Order Details'[Amount] > AVERAGE('Order Details'[Amount]), "Above Average", "Below Average")

DAX
Profit Margin = DIVIDE('Order Details'[Profit], 'Order Details'[Amount])

Format Profit Margin as Percentage.

Part 4: Measures

DAX
Total Sales = SUM('Order Details'[Amount])
DAX
Total Target = SUM('Sales Target'[Target])
DAX
Order Count = DISTINCTCOUNT('Order Details'[Order ID])

DAX
Avg Profit Delhi = CALCULATE(AVERAGE('Order Details'[Profit]), Orders[City] = "Delhi")

DAX
YTD Sales =
CALCULATE(
    [Total Sales],
    FILTER(ALL('Calendar'[Date]), 'Calendar'[Date] <= MAX('Calendar'[Date]))
)
Part 5: Visuals

#	Visual	Setup
1	Clustered column: Target vs Actual	X-axis: Category[Category] · Y-axis: Total Sales, Total Target
2	Donut: Max Profit Margin	Legend: Sub-Category · Values: Profit Margin, then click the dropdown on it → Maximum
3	Line: Monthly trend	X-axis: Calendar[Month] · Y-axis: Total Sales
4	Scatter: Profit vs Quantity	Values: Sub-Category · X: Quantity (Sum) · Y: Profit (Sum)
5a	Card ×2	One with Total Sales, one with Total Target
5b	Multi-row card: Min target	Fields: Sales Target[Category] + Target, then dropdown → Minimum
6	Matrix	Rows: Category[Category] · Columns: Calendar[Month] · Values: Total Sales, Total Target
7	Map: Sales by city	Location: Orders[City] · Bubble size: Total Sales
8	Treemap	Category: Sub-Category · Values: Total Sales
9	Funnel: Orders by state	Category: Orders[State] · Values: Order Count
