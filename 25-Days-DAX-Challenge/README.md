To start, went to the main billsteeley view and created a new Repository - named shared-links. 

Private, Clicked add a readme file

# The top
How to do stuff

# Day 1: How many current products cost less than $20?

DAX: CALCULATE(), COUNTROWS(), FILTER()

    #1 Count CurrProduct <$20 =
    CALCULATE (
        COUNTROWS ( Products ),
        Products[Discontinued] = FALSE,
        FILTER ( Products, Products[UnitPrice] < 20 )
    )


# Day 2: Which product is the most expensive?

DAX: TOPN(), DISTINCT(), CALCULATE()

    #2 MostExpensiveProduct =
    TOPN (
        1,
        DISTINCT ( Products[ProductName] ),
        CALCULATE (
        Products[Unit Price]
        )
    )


# Day 3: What is the average unit price for our products?

DAX: AVERAGE()

    #3 AvgUnitPrice =
    AVERAGE ( Products[UnitPrice] )
    


# Day 4: How many products are above the average unit price?

DAX: CALCULATE(), ALL(), COUNTROWS(), FILTER()

    #4 CountProductAboveAvg =
    VAR AvgAll = CALCULATE ( [#3AvgUnitPrice], ALL ( Products ) )
    VAR Result = CALCULATE ( COUNTROWS ( Products ), FILTER ( Products, [Unit Price] > AvgAll ) )
    RETURN
    Result


# Day 5: How many products cost between $15 and $25?(inclusive)

DAX: CALCULATE(), COUNTROWS()

    #5 CountProductCost 15-25 = 
    CALCULATE (
        COUNTROWS ( Products ),
        Products[UnitPrice] >= 15 && Products[UnitPrice] <= 25
    )


# Day 6: What is the average number of products (not qty) per order?

DAX: AVERAGEX(), CALCULATE(), DISTINCTCOUNT()

    #6 AvgNumProduct/Order =
    AVERAGEX (
        DISTINCT ( Orders[OrderID] ),
        CALCULATE ( DISTINCTCOUNT ( Orders[ProductID] ) )
    )

# Day 7: What is the order value in $ of open orders? (Not Shipped yet)

DAX: CALCULATE(), SUM(), FILTER(), BLANK()

    #7 OpenOrdersValue =
    CALCULATE (
        SUM ( Orders[_Sales] ),
        FILTER ( Orders, Orders[ShippedDate] = BLANK () )
    )


# Day 8: How many orders are "single item" (only one product ordered)?

DAX: DISTINCT(), COUNTX(), CALCULATE(), FILTER(), DISTINCTCOUNT()

    #8 CountSingleItem =
    VAR DisOrderID =
        DISTINCT ( Orders[OrderID] )
    VAR Result =
        COUNTX (
            DisOrderID,
            CALCULATE (
                FILTER (
                    DISTINCT ( Orders[OrderID] ),
                    CALCULATE ( DISTINCTCOUNT ( Orders[ProductID] ) ) = 1
                )
            )
        )
    RETURN
        Result


# Day 9: Average sales per transaction(orderID) for "Romero y Tomillo"?

DAX: AVERAGEX(), VALUES(), CALCULATE(), DISTINCT()

    #9 AvgSales Romero =
    VAR AvgTotalSale =
        AVERAGEX ( VALUES ( Orders[OrderID] ), [Total sales] )
    VAR Result =
        CALCULATE (
            AVERAGEX ( DISTINCT ( Orders[OrderID] ), [Total sales] ),
            Customers[CompanyName] = "Romero y Tomillo"
        )
    RETURN
        Result    


# Day 10: How many days since "North/South" last purchase?

DAX: CALCULATE(), LASTDATE(), DATEDIFF(), TODAY()

    #10 DaysSinceLastPurchase =
    VAR NS_LastDate =
        CALCULATE (
            LASTDATE ( Orders[OrderDate] ),
            Customers[CompanyName] = "North/South"
        )
    VAR Result =
        DATEDIFF ( NS_LastDate, TODAY (), DAY )
    RETURN
        Result


# Day 11: How many customers have ordered only once?

DAX: SUMMARIZE(), DISTINCTCOUNT(), CALCULATE(), FILTER()

    #11 CountCustomerOrderOnce =
    VAR Temp1 =
        SUMMARIZE (
            Orders,
            Orders[CustomerID],
            "OrderCount", DISTINCTCOUNT ( Orders[OrderID] )
        )
    VAR Result =
        CALCULATE (
            DISTINCTCOUNT ( Orders[CustomerID] ),
            FILTER ( Temp1, [OrderCount] = 1 )
        )
    RETURN
        Result


# Day 12: How many New customers (first purchase in current year) in 2022?

DAX: DISTINCT(), YEAR(), MAX(), CALCULATETABLE(), EXCEPT(), COUNTROWS()

    #12 NewCustomer =
    VAR AllCust =
        DISTINCT ( Orders[CustomerID] )
    VAR MaxYear =
        YEAR ( MAX ( Orders[OrderDate] ) )
    VAR PrevCust =
        CALCULATETABLE (
            DISTINCT ( Orders[CustomerID] ),
            YEAR ( Orders[OrderDate] ) < MaxYear
        )
    VAR NewCust =
        EXCEPT ( AllCust, PrevCust )
    RETURN
        COUNTROWS ( NewCust ) + 0

# Day 13: How many Lost customers (no purchases in current year) in 2022?

DAX: YEAR(), MAX(), CALCULATETABLE(), DISTINCT(), EXCEPT(), COUNTROWS()

    #13 LostCustomer =
    VAR CurrYear =
        YEAR ( MAX ( Orders[OrderDate] ) )
    VAR PrevCust =
        CALCULATETABLE (
            DISTINCT ( Orders[CustomerID] ),
            YEAR ( Orders[OrderDate] ) < CurrYear
        )
    VAR CurrCust =
        CALCULATETABLE (
            DISTINCT ( Orders[CustomerID] ),
            YEAR ( Orders[OrderDate] ) = CurrYear
        )
    VAR LostCust =
        EXCEPT ( PrevCust, CurrCust )
    RETURN
        COUNTROWS ( LostCust )


# Day 14: How many customers have NEVER purchased Queso Cabrales?

DAX: DISTINCT(), CALCULATETABLE(), EXCEPT(), COUNTROWS()

    #14 No Queso Cabrales Customers =
    VAR CustID =
        DISTINCT ( Customers[CustomerID] )
    VAR Queso =
        CALCULATETABLE (
            DISTINCT ( Orders[CustomerID] ),
            Products[ProductName] = "Queso Cabrales"
        )
    VAR No_Queso =
        EXCEPT ( CustID, Queso )
    VAR Result =
        COUNTROWS ( No_Queso )
    RETURN
        Result)


# Day 15: How many customers have purchased only Queso Cabrales (per OderID)?

DAX: FILTER(), SUMMARIZE(), DISTINCTCOUNT(), SELECTEDVALUE(), DISTINCT(), INTERSECT(), COUNTROWS()

    #15Queso Cabrales Customers = 
    VAR T1 = FILTER(
                SUMMARIZE(Orders, Orders[CustomerID], Orders[OrderID], "DistCount", DISTINCTCOUNT(Orders[ProductID])),
            [DistCount] = 1)

    VAR T2 = FILTER(
                SUMMARIZE(Orders, Orders[CustomerID], Orders[OrderID],"ProdID", SELECTEDVALUE(Products[ProductID])),
                [ProdID] = 11)

    VAR T3 = DISTINCT(SELECTCOLUMNS(T1, "Customer", Orders[CustomerID]))
    VAR T4 = DISTINCT(SELECTCOLUMNS(T2, "Customer", Orders[CustomerID]))
    VAR Result1 = INTERSECT(T3, T4)
    VAR Result2 = COUNTROWS(Result1)
    RETURN
    Result2    


# Day 16: How many products are out of stock?

DAX: CALCULATE(), COUNTROWS(), FILTER()

    #16 OutOfStock = 
    CALCULATE(
                COUNTROWS(Products),
                FILTER(Products, Products[UnitsInStock] = 0)
    )


# Day 17: How many products need to be restocked? (based on restock levels)

DAX: FILTER(), COUNTROWS()

    #17 Restock =
    VAR Temp1 =
        FILTER ( Products, Products[UnitsInStock] < Products[Restock level] )
    RETURN
        COUNTROWS ( Temp1 )


# Day 18: How many products on order we need to restock?

DAX: FILTER(), COUNTROWS()

    #18 Restock-InOrder =
    VAR Temp1 =
        FILTER ( Products, Products[Units In Order] > Products[UnitsInStock] )
    RETURN
        COUNTROWS ( Temp1 )

# Day 19: What is the stocked value of the discontinued products?

DAX: SUMX(), FILTER(), CALCULATE()

    #19 StockValue discontinue =
    SUMX (
        FILTER ( Products, Products[Discontinued] = TRUE () ),
        CALCULATE ( Products[Unit Price] * Products[Stocked units] )
    )


# Day 20: Which vender has the highest stock value?

DAX: TOPN(), DISTINCT(), CALCULATE()

    #20 HighestStockValue =
    TOPN ( 1, DISTINCT ( Suppliers[CompanyName] ), CALCULATE ( [StockValue] ) )


# Day 21: How many employees(%) are female?

DAX: DIVIDE(), COUNTROWS(), FILTER()

    #21 FemaleEmployee% =
    DIVIDE (
        COUNTROWS ( FILTER ( Employees, Employees[Gender] = "Female" ) ),
        COUNTROWS ( Employees )
    )   


# Day 22: How many employees are 60 years old or over?

DAX: CALCULATE(), COUNTROWS(), FILTER(), DATEDIFF(), TODAY()

    #22 Employee60Up =
    CALCULATE (
        COUNTROWS ( Employees ),
        FILTER ( Employees, DATEDIFF ( Employees[BirthDate], TODAY (), YEAR ) >= 60 )
    )


# Day 23: Which employee had the highest sales in 2022?

DAX: TOPN(), DISTINCT(), CALCULATE()

    #23 HighestSalesEmp22 =
    TOPN (
        1,
        DISTINCT ( Employees[Full Name] ),
        CALCULATE ( [Total sales], 'Calendar'[Year] = 2022 )
    )


# Day 24: How many employee sold over $100k in 2022?

DAX: DISTINCT(), YEAR(), MAX(), CALCULATETABLE(), EXCEPT(), COUNTROWS()

    #24 Sales100kUp22 =
    COUNTROWS (
        FILTER (
            Employees,
            CALCULATE ( [Total sales], 'Calendar'[Year] = 2022 ) > 100000
        )
    ) + 0

# Day 25: How many employees got hired in 1994?

DAX: CALCULATE(), COUNTROWS(), YEAR()

    #25Employee1994 =
    CALCULATE ( COUNTROWS ( Employees ), YEAR ( Employees[HireDate] ) = 1994 )


















How to create a link on a word - it's about brackets and parenthesis

    Open bracket first [
    Then put what you want the text to say on the link
    Then closed bracket ]
    Right after that, do the open parenthesis (
    And add the entire link in there https:// whatever ever ever .com
    Then closed parenthesis
    
It will look like this
[FixClicks website](https://www.fixclicks.net/seo)

Word is in brackets - and the link is in the parenthesis

    Insert a gray box
    line in gray box
    another line
    another line
    another line
    
Something else

had to go in and replace desktop-tutorial with shared-links on this Skip to link below

Skip to [Day 4](https://github.com/billsteeley/shared-links/blob/main/README.md#day-4)

New Gray box

    {
    "exportFormatVersion": 2,
    "exportTime": "2018-05-23 13:54:39",
    "containerVersion": {
        "path": "accounts/263956808/containers/7030868/versions/0",
        "accountId": "263956808",
        "containerId": "7030868",
        "containerVersionId": "0",
        "container": {
            "path": "accounts/263956808/containers/7030868",
            "accountId": "263956808",
            "containerId": "7030868",
            "name": "UTMZ Cookie Replicator",
            "publicId": "GTM-PLM73NN",
            "usageContext": [
                "WEB"
                
End
# Day 2
This is how to create a gray box
Need to start with 4 spaces, the hit enter

Once done, then hit enter twice - the move the cursor to the left margin
    
Starting a new box


    Enter, then hit the spacebar 4 times to start the box
    Each time you hit enter, it will go to the next row - indented automatically
    Enter again, keep typing
    Enter again, another row. When done, click enter twice
    And then on that next word, hit the backspace so it aligns all the way to the left
    
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


