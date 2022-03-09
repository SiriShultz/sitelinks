# Customer Personality Analysis
To start, went to the main billsteeley view and created a new Repository - named shared-links. 

Private, Clicked add a readme file

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

Create Calculated column for Age.

    Age = 
    2014 - Customers[Year_Birth]
    // Data collected in 2014
    
# Children

Create measure to calculate the total number of children (kids and teens at home).

    TotalChildren = 
    SUMX(Customers, Customers[Kidhome] + Customers[Teenhome])
  
 Create Calculated column for Children status.

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

Create Calculated column for customers who has child and no child.

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

Or Average Income

    AvgIncome =
    CALCULATE (
      AVERAGE ( Customers[Income] ),
      Customers[Income] <> BLANK ()
        && Customers[Income] < 200000
        && Customers[Age] < 100
    )
    


Create Calculated column for income group of customers (below average and high income) using 0.5 percentile or average.

    IncomeGroup =
    VAR PctInc =
      CALCULATE ( [PctInc], Customers )
    RETURN
      SWITCH ( TRUE (), Customers[Income] < PctInc, "BelowAVG", "High" )
                
# Spending Group
 
Create measure to calculate the total spending amount for each customer.

    TotalSpending = 
    SUMX('Products Table', 'Products Table'[Amount])

Create Calculated column for Spending amount for each customer.

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

Or Average Spending

    AvgSpnd =
    CALCULATE (
      AVERAGE ( Customers[Spending] ),
      Customers[Income] <> BLANK ()
        && Customers[Income] < 200000
        && Customers[Age] < 100
    )
    

Create Calculated column for Spending group of customers (low and high spending) using 0.5 percentile or average.

    SpendingGroup =
    VAR PctSpnd =
      CALCULATE ( [PctSpnd], Customers )
    RETURN
      SWITCH ( TRUE (), Customers[Spending] < PctSpnd, "Low", "High" )

# Spending Adjusted
Create measure to calculate the total spending amount by filter only the customers that has income less than $200,000 and less than 100 years old..

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

# Senior Group
 
Create measures for a Seniority (time of being customer) by calculate in months and the last date is October 4, 2014 (date when was done the analysis).

    Seniority =
      VAR StartDate =
        MIN ( Customers[Dt_Customer] )
      VAR CurrentDate =
        DATE ( 2014, 10, 4 )
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

Or Average Seniority

    AvgSenior =
    CALCULATE (
      AVERAGE ( Customers[Seniority] ),
      Customers[Income] <> BLANK ()
        && Customers[Income] < 200000
        && Customers[Age] < 100
    )
    

Create Calculated column for Seniority group of customers (New and Old customer) using 0.5 percentile or average.

    SeniorGroup =
    VAR PctSenior =
      CALCULATE ( [PctSenior], Customers )
    RETURN
      SWITCH ( TRUE (), Customers[Seniority] < PctSenior, "New", "Old" )


# Segment
Create segments of customer to 5 groups base on results of calculated columns: Stars, Need Attention, High Potential, Leaky Bucket and Others (for other posibilities)

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

    
End


# Day 3

Some bullet points - create by using a star

* one, hit enter
* two, keep on going
* three, if this is the last one then enter twice and move back to the left indent

Plus can add links under the bullets, just hit the * first and then use the link format with the brackets and parenthesis

* Link to Google.com right here -- [Google.com](https://www.google.com)
* Another link - this one to [Bing](https://www.bing.com/)
* Similar to the <a> and then </> after - to insert [anchor text LINK](https://www.fixclicks.net) within the sentence

End
    

# Day 4

Go back to [Day 2](https://github.com/billsteeley/shared-links/blob/main/README.md#day-2)
    
Trying to combine these things

    Trying to add links within a gray box
    Like putting a [link on this phrase](https://www.fixclicks.net) within this sentence
    Nope, that doesn't work
    How about some bullet points in the gray box? Let's try
    * one
    * two
    * three
    Nope - that doesn't seem to work either
    
Hit enter twice and then backspace to the left margin

End
    
[Back to Top](https://github.com/billsteeley/shared-links/blob/main/README.md#the-top)

