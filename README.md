# Day 1
    
#1Count CurrProduct <$20 = 
CALCULATE (
    COUNTROWS ( Products ),
    Products[Discontinued] = FALSE,
    FILTER ( Products, Products[UnitPrice] < 20 )
)

# Day 2
    
#1Count CurrProduct <$20 = 
CALCULATE (
    COUNTROWS ( Products ),
    Products[Discontinued] = FALSE,
    FILTER ( Products, Products[UnitPrice] < 20 )
)
