# 25 Days of DAX Fridays! Challenge
Here are my DAX solutions for the 25 Days of DAX Fridays! Challenge.
    
This challenge is created by Ruth Pozuelo Martinez (Microsoft MVP). [Learn more about this challenge](https://curbal.com/25-days-of-dax-fridays-challenge)


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

    
[Back to Top](https://github.com/billsteeley/shared-links/blob/main/README.md#the-top)
