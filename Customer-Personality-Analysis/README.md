# Customer Personality Analysis with Power BI

Customer Personality Analysis is a detailed analysis of a company’s ideal customers. It helps a business to better understand its customers and makes it easier for them to modify products according to the specific needs, behaviors and concerns of different types of customers.

This project was inspired by the Data Science Project on Customer Personality Analysis with Python that was created by Aman Kharwal. I used a dataset that provided on Kaggle. My goal was to create a similar project, but use Power BI instead of Python. I used Power BI and Power Query to prepare/clean data, created fact and dimension tables for data modeling, and connected them with relationships. I also created measures and calculated columns for grouping and the calculation process. Finally, I create the visualizations below to show the results of the analysis. [See the full project here.](https://sirishultz.com/portfolio/customer-personality-analysis)

Acknowledgement: The dataset for this project is provided by Dr. Omar Romero-Hernandez.

Inspiration: https://www.kaggle.com/imakash3011/customer-personality-analysis

Solution with Python by Aman Kharwal: https://thecleverprogrammer.com/2021/02/08/customer-personality-analysis-with-python/


Challenge: Perform the Customer Personality Analysis using Power BI.

 - [Measures for Analysis](https://github.com/SiriShultz/sitelinks/blob/main/Customer-Personality-Analysis/README.md#measures-for-analysis)
 - [Data Distribution Measures](https://github.com/SiriShultz/sitelinks/tree/main/Customer-Personality-Analysis#create-data-distribution-measures)

# Date Table

Start with January 1st of minmimum year and ends wtih December 31st of maximum year.

    Calendar =
    VAR MinYear =
      YEAR ( MIN ( Customers[Dt_Customer] ) )
    VAR MaxYear =
      YEAR ( MAX ( Customers[Dt_Customer] ) )
    VAR StartDate =
      DATE ( MinYear, 1, 1 )
    VAR EndtDate =
      DATE ( MaxYear, 12, 31 )
    RETURN
    ADDCOLUMNS (
        CALENDAR ( StartDate, EndtDate ),
        "Year", YEAR ( [Date] ),
        "Quarter", "Q" & QUARTER ( [Date] ),
        "MonthNumSort", MONTH ( [Date] ),
        "Month", FORMAT ( [Date], "mmmm" ),
        "YearMonthSort", FORMAT ( [Date], "yyyy,mm" ),
        "MonthYear", FORMAT ( [Date], "mmm,yyyy" ),
        "Weekday/Weekend", IF ( WEEKDAY ( [Date], 2 ) > 5, "Weekend", "Weekday" ),
        "Day", FORMAT ( WEEKDAY ( [Date] ), "dddd" ),
        "DayNumSort", WEEKDAY ( [Date], 2 )
    )

# Age

Create a calculated column for Age.

    Age = 
    2014 - Customers[Year_Birth]
    // Data collected in 2014
    
# Children

Create a measure to calculate the total number of children (kids and teens at home).

    TotalChildren = 
    SUMX(Customers, Customers[Kidhome] + Customers[Teenhome])
  
 Create a calculated column for Children status.

    Children = 
    CALCULATE(
      SWITCH([TotalChildren],
      0, "No Child",
      1, "1 Child",
      2, "2 Children",
      3, "3 Children",
      "More"), 
      ALLEXCEPT(Customers,Customers[ID])
    )
 
 # Has_Child

Create a calculated column for customers who has child and no child.

    Has_Child =
    CALCULATE (
      SWITCH ( [Children], 0, "No Child", "Has Child" ),
      ALLEXCEPT ( Customers, Customers[ID] )
      )

 # Income Group
 
Create measures for a 0.5 percentile or an average of income by filter only the customers that has income less than $200,000 and less than 100 years old.

    PctInc =
    CALCULATE (
      PERCENTILE.INC ( Customers[Income], 0.5 ),
      Customers[Income] <> BLANK ()
        && Customers[Income] < 200000
        && Customers[Age] < 100
     )

Or Average Income.

    AvgIncome =
    CALCULATE (
      AVERAGE ( Customers[Income] ),
      Customers[Income] <> BLANK ()
        && Customers[Income] < 200000
        && Customers[Age] < 100
    )
    


Create a calculated column for income group of customers (below average and high income) using 0.5 percentile or average.

    IncomeGroup =
    VAR PctInc =
      CALCULATE ( [PctInc], Customers )
    RETURN
      SWITCH ( TRUE (), Customers[Income] < PctInc, "BelowAVG", "High" )
                
# Spending Group
 
Create a measure to calculate the total spending amount for each customer.

    TotalSpending = 
    SUMX('Products Table', 'Products Table'[Amount])

Create a calculated column for Spending amount for each customer.

    Spending = 
    [TotalSpending]


Create measures for a 0.5 percentile or an average of Spending by filter only the customers that has income less than $200,000 and less than 100 years old.

    PctSpnd =
    CALCULATE (
      PERCENTILE.INC ( Customers[Spending], 0.5 ),
      Customers[Income] <> BLANK ()
        && Customers[Income] < 200000
        && Customers[Age] < 100
     )

Or Average Spending.

    AvgSpnd =
    CALCULATE (
      AVERAGE ( Customers[Spending] ),
      Customers[Income] <> BLANK ()
        && Customers[Income] < 200000
        && Customers[Age] < 100
    )
    

Create a calculated column for Spending group of customers (low and high spending) using 0.5 percentile or average.

    SpendingGroup =
    VAR PctSpnd =
      CALCULATE ( [PctSpnd], Customers )
    RETURN
      SWITCH ( TRUE (), Customers[Spending] < PctSpnd, "Low", "High" )

# Senior Group
 
Create measures for a Seniority (time of being customer) by calculate in months and the last date is Decemberr 14, 2014 (date when was done the analysis).

    Seniority =
      VAR StartDate =
        MIN ( Customers[Dt_Customer] )
      VAR CurrentDate =
        DATE ( 2014, 12, 07 )
      VAR DayNum =
        DATEDIFF ( StartDate, CurrentDate, DAY )
      RETURN
        DIVIDE ( DayNum, 30 )


Create measures for a 0.5 percentile or an average of Seniority by filter only the customers that has income less than $200,000 and less than 100 years old.

    PctSenior =
    CALCULATE (
      PERCENTILE.INC ( Customers[Seniority], 0.5 ),
      Customers[Income] <> BLANK ()
        && Customers[Income] < 200000
        && Customers[Age] < 100
     )

Or Average Seniority.

    AvgSenior =
    CALCULATE (
      AVERAGE ( Customers[Seniority] ),
      Customers[Income] <> BLANK ()
        && Customers[Income] < 200000
        && Customers[Age] < 100
    )
    

Create a calculated column for Seniority group of customers (New and Old customer) using 0.5 percentile or average.

    SeniorGroup =
    VAR PctSenior =
      CALCULATE ( [PctSenior], Customers )
    RETURN
      SWITCH ( TRUE (), Customers[Seniority] < PctSenior, "New", "Old" )


# Segment
Create segments of customer to 5 groups base on results of calculated columns: Stars, Need Attention, High Potential, Leaky Bucket and Others (for other posibilities).

    Segment =
      SWITCH (
        TRUE (),
        Customers[SeniorGroup] = "Old"
          && Customers[IncomeGroup] = "High"
          && Customers[SpendingGroup] = "High", "Stars",
        Customers[SeniorGroup] = "New"
          && Customers[IncomeGroup] = "BelowAVG"
          && Customers[SpendingGroup] = "Low", "Need Attention",
        Customers[SeniorGroup] = "New"
          && Customers[IncomeGroup] = "High"
          && Customers[SpendingGroup] = "High", "High Potential",
        Customers[SeniorGroup] = "Old"
          && Customers[IncomeGroup] = "BelowAVG"
          && Customers[SpendingGroup] = "Low", "Leaky Bucket",
      --Customers[SeniorGroup] = "Old" && Customers[IncomeGroup] = "BelowAVG" && Customers[SpendingGroup] = "High", "OverSpendOld",
      --Customers[SeniorGroup] = "New" && Customers[IncomeGroup] = "BelowAVG" && Customers[SpendingGroup] = "High", "OverSpendNew",
      --Customers[SeniorGroup] = "Old" && Customers[IncomeGroup] = "High" && Customers[SpendingGroup] = "Low", "CheapOld",
      --Customers[SeniorGroup] = "New" && Customers[IncomeGroup] = "High" && Customers[SpendingGroup] = "Low", "CheapNew",
        "Others"
      )

# Measures for Analysis

[Back to Top](https://github.com/SiriShultz/sitelinks/blob/main/Customer-Personality-Analysis/README.md#customer-personality-analysis-with-power-bi)

# Spending Adjusted
Create a measure to calculate the total spending amount by filter only the customers that has income less than $200,000 and less than 100 years old.

    Spending Adj =
    CALCULATE (
      [TotalSpending],
      FILTER (
          Customers,
          Customers[ID]
              && Customers[Income] <> BLANK ()
              && Customers[Income] < 200000
              && Customers[Age] < 100
      )
    )

# Average Income
Create a measure to calculate the average income by filter only the customers that has income less than $200,000 and less than 100 years old.

    AvgIncome =
    CALCULATE (
        AVERAGE ( Customers[Income] ),
        Customers[Income] <> BLANK ()
            && Customers[Income] < 200000
            && Customers[Age] < 100
     )
# Average Seniority
Create a measure to calculate the average seniority by filter only the customers that has income less than $200,000 and less than 100 years old.

    AvgSeniority =
    CALCULATE (
        AVERAGE ( Customers[Seniority] ),
        Customers[Income] <> BLANK ()
            && Customers[Income] < 200000
            && Customers[Age] < 100
     )
# Average Spending
Create a measure to calculate the average spending by filter only the customers that has income less than $200,000 and less than 100 years old.

    AvgSpending =
    CALCULATE (
        AVERAGE ( Customers[Spending] ),
        Customers[Income] <> BLANK ()
            && Customers[Income] < 200000
            && Customers[Age] < 100
     )


# Create Data Distribution Measures
[Back to Top](https://github.com/SiriShultz/sitelinks/blob/main/Customer-Personality-Analysis/README.md#customer-personality-analysis-with-power-bi)

# Count Age
Create a measure to calculate the count of customer in age range by filter only the customers that has income less than $200,000 and less than 100 years old.

    CountAge = 
    COUNT(Customers[Age]))


# Count Income
Create a measure to calculate the count of customer in income range by filter only the customers that has income less than $200,000 and less than 100 years old.

    CountIncome = 
    COUNT(Customers[Income]))

# Count Senior
Create a measure to calculate the total spending amount by filter only the customers that has income less than $200,000 and less than 100 years old.

    CountSenior = 
    COUNT(Customers[Senior]))

# Count Spending
Create a measure to calculate the total spending amount by filter only the customers that has income less than $200,000 and less than 100 years old.

    CountSpending = 
    COUNT(Customers[Spending]))


# Count Purchase
Create a measure to calculate the total spending amount by filter only the customers that has income less than $200,000 and less than 100 years old.

    Num Purchase =
    CALCULATE (
        SUM ( PurchaseTypes[No. of Purchase] ),
        FILTER ( Customers, Customers[ID] && Customers[Income] <> BLANK () )
    )

    
[Back to Top](https://github.com/SiriShultz/sitelinks/blob/main/Customer-Personality-Analysis/README.md#customer-personality-analysis-with-power-bi)

