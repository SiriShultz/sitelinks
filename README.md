# Day 1
#1Count CurrProduct <$20 = 
CALCULATE (
    COUNTROWS ( Products ),
    Products[Discontinued] = FALSE,
    FILTER ( Products, Products[UnitPrice] < 20 )
)
