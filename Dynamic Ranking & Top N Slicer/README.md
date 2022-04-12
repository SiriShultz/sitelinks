# Dynamic Ranking & Top N Slicer (What If Parameter)

This project was created by using a flatefile dataset (CSV) exported from [Zillow.com](https://www.zillow.com/research/data/). The original file contains 271 columns and more than 30,000 rows. The data shows monthly Home Value Index populated by Zillow from January, 2000 to December, 2021 across the regions (zipcode, city, metro, state). I use Power Query to clean and extract data then create 1 fact table and 2 dimension tables (Calendar and Geographic) and also imported/merged an extra file for a full state name which is not exist in the original file. After created data modeling between tables I use DAX to perform calculation of needed metrics and dynamic ranking in each geographic level. I also create a dynamic title/head line that will change based on filters selection. I added the What If Parameters to the reports as slicers to let the user choose the number of Top N of the metrics. [See the full project here.](https://sirishultz.com/portfolio/dynamic-ranking-top-n-slicer)


# Date Table

Start with January 1st of minmimum year and ends wtih December 31st of maximum year.

    Calendar = 
    VAR MyCal = CALENDAR(MIN('fHomeValue'[Date]), MAX('fHomeValue'[Date]))
    Return
    ADDCOLUMNS(MyCal,
    "Year", YEAR([Date]),
    "Quarter", FORMAT([Date],"\Qq"),
    "MonthNum", MONTH([Date]),
    "Month", FORMAT([Date],"mmmm"),
    "Month/Year", FORMAT([Date],"mmm yyyy"),
    "Year-Month sort", FORMAT([Date], "yyyy-mm")
    )


# Measures for Analysis


# Average Home Value
Create a measure to calculate the average home value.

    AvgHomeValue = AVERAGE(fHomeValue[HomeValue])

# Average Income
Create a measure to calculate the average income by filter only the customers that has income less than $200,000 and less than 100 years old..

    AvgIncome =
    CALCULATE (
        AVERAGE ( Customers[Income] ),
        Customers[Income] <> BLANK ()
            && Customers[Income] < 200000
            && Customers[Age] < 100
     )
# Average Seniority
Create a measure to calculate the average seniority by filter only the customers that has income less than $200,000 and less than 100 years old..

    AvgSeniority =
    CALCULATE (
        AVERAGE ( Customers[Seniority] ),
        Customers[Income] <> BLANK ()
            && Customers[Income] < 200000
            && Customers[Age] < 100
     )
# Average Spending
Create a measure to calculate the average spending by filter only the customers that has income less than $200,000 and less than 100 years old..

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
Create a measure to calculate the count of customer in age range by filter only the customers that has income less than $200,000 and less than 100 years old..

    CountAge = 
    COUNT(Customers[Age]))


# Count Income
Create a measure to calculate the count of customer in income range by filter only the customers that has income less than $200,000 and less than 100 years old..

    CountIncome = 
    COUNT(Customers[Income]))

# Count Senior
Create a measure to calculate the total spending amount by filter only the customers that has income less than $200,000 and less than 100 years old..

    CountSenior = 
    COUNT(Customers[Senior]))

# Count Spending
Create a measure to calculate the total spending amount by filter only the customers that has income less than $200,000 and less than 100 years old..

    CountSpending = 
    COUNT(Customers[Spending]))


# Count Purchase
Create a measure to calculate the total spending amount by filter only the customers that has income less than $200,000 and less than 100 years old..

    Num Purchase =
    CALCULATE (
        SUM ( PurchaseTypes[No. of Purchase] ),
        FILTER ( Customers, Customers[ID] && Customers[Income] <> BLANK () )
    )

    
[Back to Top](https://github.com/SiriShultz/sitelinks/blob/main/Customer-Personality-Analysis/README.md#customer-personality-analysis-with-power-bi)

