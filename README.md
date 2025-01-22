# Data-Analytics---Mexico-Toys-Sales-From-Maven-Analytics
This is a project to apply some of Power BI knowledge to a Maven Analytics problem

### Data Source.
In this project, we will download data from Maven Analytics website, this is the link to explore all their Datasets: [Maven Analytics Datasets](https://www.mavenanalytics.io/data-playground?page=2).

### Loading & transforming the Data
In this step, since we have a csv files, we will begin by importing the 4 csv files of our tables, and we will analyse the different data and we will try to apply the different transformations, like changing data types, setting the first row as header if needed,
replacing Null values either by 0 or N/A, creating custom columns if needed for our analysis, columns by example and so on.

By the way in a more real world scenario, it is more than recommanded to go deeper in the term of data cardinality and data granularity in order to optimize our data transformation at our ELT part (Power Query), and to do that we need to have more business knowledge that will permit us
to deeply understand all that is at stake with our data.

### Data Modeling
After the step of data transformation, the next step is Data Modeling, we begin by identifying the diffrent Fact and Dim tables and we need to also verify the different relationships between Facts and Dims in order to see the consistency of these relations.
This is the first look of our Data Model.

<div align="center">
  <img width="895" alt="image" src="https://github.com/user-attachments/assets/89a5ccc7-cd0c-4029-8800-1b33507de3f0" />
</div>

### Data Model optimization
In every Power BI project, when we try to analyze our data and track the eveolution in time, we need to create a Calendar table, it's one of the best practices.

### **Method 1 :**

```
Calendar = 
VAR MinDate = 
    MINX(
        UNION(
            SELECTCOLUMNS('Dim_Stores', "Date", 'Dim_Stores'[Store_Open_Date]),
            SELECTCOLUMNS('Fact_Sales', "Date", 'Fact_Sales'[Date])
        ),
        [Date]
    )
VAR MaxDate = 
    MAXX(
        UNION(
            SELECTCOLUMNS('Dim_Stores', "Date", 'Dim_Stores'[Store_Open_Date]),
            SELECTCOLUMNS('Fact_Sales', "Date", 'Fact_Sales'[Date])
        ),
        [Date]
    )
RETURN
ADDCOLUMNS (
    CALENDAR (DATE(YEAR(MinDate), MONTH(MinDate), DAY(MinDate)), DATE(YEAR(MaxDate), MONTH(MaxDate), DAY(MaxDate))),
    "Year", YEAR([Date]),
    "MonthNumber", MONTH([Date]),
    "MonthName", FORMAT([Date], "MMMM"),
    "Quarter", "Q" & QUARTER([Date]),
    "Weekday", WEEKDAY([Date]),
    "WeekdayName", FORMAT([Date], "dddd"),
    "YearMonth", FORMAT([Date], "YYYY-MM")
)
```
### **Method 2 : a simple way of creating the Date Table**
```
Calendrier = 
VAR StartDate =
    MIN ( Fact_Sales[Date] )
VAR EndDate =
    MAX ( Fact_Sales[Date] )
VAR DateTable =
    ADDCOLUMNS (
        CALENDAR ( StartDate, EndDate ),
        "QuarterNo", QUARTER ( [Date] ),
        "QuarterName", FORMAT ( [Date], "\QQ" ),
        "MonthNo", MONTH ( [Date] ),
        "MonthName", FORMAT ( [Date], "MMM" )
    )
RETURN
    DateTable
```

**NB:** Amongest the best practices in Power BI, is that after we create a Date Table or a Calendrier table, we select in our case *QuarterName* and *MonthName*, and we sort them using the columns *QuarterNo* and *MonthNo*, so that we will have the Months and quarters in 
the correct order while putting them in a line Chart.


With this method, we have created a Calendar table that takes the Mininum date of all tables and the Maximum date and gives us a range of dates using these two, and with this range, we've created additional columns like Year, MonthNumber, MonthName ... In order to have more options
while manipulating our data.

Here's an updated version of our Data Model, after the addition of the Calendar Table.
<div align="center">
  <img width="867" alt="image" src="https://github.com/user-attachments/assets/70a1562c-a161-4142-b2e0-d81b23605c6b" />
</div>



### Business Questions to answer and the choice of visuals.
* What is the profit by Product - Bar Graph (Top 10).
* What is the profit by Store - Bar Graph (Top 10).
* Sales by Year / Quarter / Semester - Line Chart.
* Sales by store location over time - Line Chart.
* KPIs / Cards for Sales - Cards


### Measures and calculated columns, using DAX.
Amongst the best practices in Power BI, is putting all the measures in a dedicated measures tables (called mesures in our case), and to organize your measures, you ought to put them into different folders, in order to facilitate the navigation.

<div align="center">
  <img width="173" alt="image" src="https://github.com/user-attachments/assets/cb5c97bd-ace9-492b-8c1d-989300fd5181" />
</div>

```
// Comparisons
YoY Sales = 
[Total Sales Units] - [LY Sales]

YoY Sales % = 
DIVIDE([YoY Sales], [LY Sales], 0)

// Current Year Metrics
YTD Sales = 
CALCULATE(
    [Total Sales Units], 
    DATESYTD(Dim_Date[Date])
)

// Inventory
Days to Stock Out = 
DIVIDE(
    [Stock Remaining by Product], 
    AVERAGEX(Fact_Sales, Fact_Sales[Units])
)

Out of Stock = 
IF(SUM(Fact_Inventory[Stock_On_Hand]) = 0, "Yes", "No")

Stock Remaining by Product = 
CALCULATE(SUM(Fact_Inventory[Stock_On_Hand]), Dim_Product[Product_ID])

Stock Remaining by Store = 
CALCULATE(SUM(Fact_Inventory[Stock_On_Hand]), Dim_Stores[Store_Name])

Stock Utilization % = 
DIVIDE([Total Sales Units], [Total Stock on Hand], 0)

Total Inventory Value = 
SUMX(
    Fact_Inventory, 
    Fact_Inventory[Stock_On_Hand] * RELATED(Dim_Product[Product_Price])
)

Total Stock on Hand = 
SUM(Fact_Inventory[Stock_On_Hand])

// Last Year Metrics
LY Sales = 
CALCULATE(
    [Total Sales Units], 
    SAMEPERIODLASTYEAR(Dim_Date[Date])
)

LY YTD Sales = 
CALCULATE(
    [Total Sales Units], 
    DATESYTD(SAMEPERIODLASTYEAR(Dim_Date[Date]))
)

// Performance Metrics
Inventory Turnover Rate = 
DIVIDE([Total Sales Units], [Total Stock on Hand], 0)

Sales to Stock Ratio by Store = 
DIVIDE([Total Sales by Store], [Stock Remaining by Store], 0)

// Sales
Sales by City = 
CALCULATE(SUM(Fact_Sales[Units]), Dim_Stores[Store_City])

Total Sales by Store = 
CALCULATE(SUM(Fact_Sales[Units]), Dim_Stores[Store_Name])

Total Sales per Product = 
CALCULATE(SUM(Fact_Sales[Units]), Dim_Product)

Total Sales Units = 
SUM(Fact_Sales[Units])

Total Transactions = 
COUNT(Fact_Sales[Sale_ID])

// Time Analysis
First Sale Date = 
MIN(Fact_Sales[Date])

Stores Opened Before 2023 = 
CALCULATE(
    COUNT(Dim_Stores[Store_ID]),
    Dim_Stores[Store_Open_Date] < DATE(2023, 1, 1)
)

```


### Report Development
Canvas settings : Custom 1100 x 1500 px.
Colors:
- BG: #F2F2F2.
- Bar: #325959.
<div align="center">
  <img width="528" alt="Visual1" src="https://github.com/user-attachments/assets/ddcb84ee-e99d-42a7-914b-c544b70bd2a2" />
</div>



<div align="center">
  <img width="529" alt="Visual2" src="https://github.com/user-attachments/assets/cc8ef74a-081a-4cf3-ab9a-df53b730e9a1" />
</div>


  











