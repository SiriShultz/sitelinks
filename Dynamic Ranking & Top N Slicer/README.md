# Dynamic Ranking & Top N Slicer (What If Parameter)

This project was created by using an Open Source flatefile dataset (CSV) exported from [Zillow.com](https://www.zillow.com/research/data/). The original file contains 271 columns and more than 30,000 rows. The data shows monthly Home Value Index populated by Zillow from January, 2000 to December, 2021 across the regions (zipcode, city, metro, state). I used Power Query to clean and extract data, created one fact table and two dimension tables (Calendar and Geographic), and imported/merged an extra file for a full state name which did not exist in the original file. After created data modeling between tables, I use DAX to perform calculations for needed metrics and dynamic rankings for each geographic level. I also created a dynamic title/head line that changes based on filters selection. I added the What If Parameters to the reports as slicers to let the user choose the Top N ranking. [See the full project here.](https://sirishultz.com/portfolio/dynamic-ranking-top-n-slicer)


# Date Table

Start with January 1st of minimum year and ends with December 31st of maximum year.

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

# Average Year over Year % of Home Value
Create a measure to calculate the average year over year % of home value.

    HomeValue YoY% = IF(
                    ISFILTERED('Calendar'[Date]),
                    ERROR("Time intelligence quick measures can only be grouped or filtered by the Power BI-provided date hierarchy or primary date column."),
                    VAR __PREV_YEAR =
                        CALCULATE(
                            fHomeValue[AvgHomeValue],
                            DATEADD('Calendar'[Date], -1, YEAR)
                        )
                    RETURN
                        DIVIDE(fHomeValue[AvgHomeValue] - __PREV_YEAR, __PREV_YEAR))
                        
# Top Rankings for City, Metro, and State
Create measures to rank the average home value in all level of regions (city, metro, state).

    City Rank =
    RANKX(ALL(dGeo[City-State]),[AvgHomeValue])
    
    Metro Rank =
    RANKX(ALL(dGeo[Metro]),[AvgHomeValue])
    
    State Rank =
    RANKX(ALL(dGeo[StateName]),[AvgHomeValue])
    
 # Year over Year Top Rankings for City, Metro, and State
Create measures to rank the average year over year % of home value in all level of regions (city, metro, state).

    City RankYoY =
    RANKX(ALL(dGeo[City-State]),[HomeValue YoY%])
    
    Metro RankYoY =
    RANKX(ALL(dGeo[Metro]),[HomeValue YoY%])
    
    State RankYoY =
    RANKX(ALL(dGeo[StateName]),[HomeValue YoY%])

 # Dynamic Rankings
Create measures to rank the average home value and Year over Year % in all level of regions (city, metro, state).

    Rank =
        SWITCH(TRUE(),
              ISINSCOPE(dGeo[City-State]), [City Rank],
              ISINSCOPE(dGeo[Metro]),[Metro Rank],
              ISINSCOPE(dGeo[StateName]),[State Rank],
           BLANK())                
    
    RankYoY =
        SWITCH(TRUE(),
              ISINSCOPE(dGeo[City-State]), [City RankYoY],
              ISINSCOPE(dGeo[Metro]),[Metro RankYoY],
              ISINSCOPE(dGeo[StateName]),[State RankYoY],
           BLANK())                
   
   # What If Parameter
Generate slicers with What If Parameter function with the default value at 5.

    Parameter Value =
    SELECTEDVALUE('TopN'[Parameter], 5)
    

# Dynamic Top N 
Create Mearsures to filter the Top N results base on the selected value on the slicer.

    TopN HomeValue =
                      VAR Ranking = [Rank]
                      VAR _TopN = [Parameter Value]
                      VAR Result = INT(Ranking <= _TopN)
                      RETURN Result        

# Dynamic Headline 
Create Mearsures for the Card Visuals to display the results base on the selected value.

    Headline1 =
                VAR _Year = SELECTEDVALUE('Calendar'[Year])
                VAR _TopNum = SELECTEDVALUE('TopN'[Parameter])
                VAR _State = SELECTEDVALUE(dGeo[StateName])
                RETURN
                _Year & " Average Year over Year by State in USA"

    Headline2 =
                    VAR _Year = SELECTEDVALUE('Calendar'[Year])
                    VAR _TopNum = SELECTEDVALUE('TopN'[Parameter])
                    VAR _State = SELECTEDVALUE(dGeo[StateName])
                    RETURN
                    IF(HASONEVALUE(dGeo[StateName]), _Year & " Top " & _TopNum & " Cities in " & _State,
                    _Year & " Top " & _TopNum & " Cities in USA")

[Back to Top](https://github.com/SiriShultz/sitelinks/blob/main/Customer-Personality-Analysis/README.md#customer-personality-analysis-with-power-bi)

