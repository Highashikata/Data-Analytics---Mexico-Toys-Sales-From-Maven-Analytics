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

<img width="340" alt="image" src="https://github.com/user-attachments/assets/c76f80d1-1e48-4940-864f-3046459005cb" />
