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
calendrier = 
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

With this method, we have created a Calendar table that takes the Mininum date of all tables and the Maximum date and gives us a range of dates using these two, and with this range, we've created additional columns like Year, MonthNumber, MonthName ... In order to have more options
while manipulating our data.

Here's an updated version of our Data Model, after the addition of the Calendar Table.
<div align="center">
  <img width="857" alt="image" src="https://github.com/user-attachments/assets/dba7c95b-243b-4b56-9831-76af6e970346" />
</div>



### Business Questions to answer and the choice of visuals.
* What is the profit by Product - Bar Graph (Top 10).
* What is the profit by Store - Bar Graph (Top 10).
* Sales by Year / Quarter / Semester - Line Chart.
* Sales by store location over time - Line Chart.
* KPIs / Cards for Sales - Cards
