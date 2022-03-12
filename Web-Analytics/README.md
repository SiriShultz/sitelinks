Web Analytics Time Series with Calculation Group and custom conditional formatting.


# Date Table
Create a date table with the first date and last date that exists in the fact table.

    dDate =
    VAR Mycal =
    CALENDAR (
        FIRSTDATE ( 'Web Traffic'[Day Index] ),
        LASTDATE ( 'Web Traffic'[Day Index] )
    )
    RETURN
    ADDCOLUMNS (
        Mycal,
        "Day", FORMAT ( [Date], "dddd" ),
        "DayID", WEEKDAY ( [Date], 2 ),
        "Month", FORMAT ( [Date], "mmmm" ),
        "MonthShort", FORMAT ( [Date], "mmm" ),
        "MIndex", MONTH ( [Date] ),
        "Month-Year", FORMAT ( [Date], "mmm, yyyy" ),
        "YearMonthID", FORMAT ( [Date], "yyyy,mm" ),
        "Year", YEAR ( [Date] ),
        "Quarter", FORMAT ( [Date], "\Qq" ),
        "Weekend/Working", IF ( WEEKDAY ( [Date], 2 ) > 5, "Weekend", "Weekday" )
    )
    
# Desktop Users

Total number of desktop users.

    Desktop = 
    SUM('Web Traffic'[Desktop Users])
    
# Mobile Users

Total number of mobile users.

    Mobile = 
    SUM('Web Traffic'[Mobile Users])

# Tablet Users

Total number of tablet users.

    Tablet = 
    SUM('Web Traffic'[Tablet Users])
    
# All Devices Users

Total number of users for all devices.

    AllDevices =
    SUMX (
    'Web Traffic',
    'Web Traffic'[Desktop Users] + 'Web Traffic'[Mobile Users] + 'Web Traffic'[Tablet Users]
    )
# Device % by Calculation Group

Use SELECTEDMEASURE() in Calculation Group.

    Device% =
    DIVIDE ( SELECTEDMEASURE(), [AllDevices] )

# Year over Year by Calculation Group

Use SELECTEDMEASURE() in Calculation Group.

    DeviceYOY =
    VAR PrevYear =
    CALCULATE ( SELECTEDMEASURE(), DATEADD ( dDate[Date], -1, YEAR ) )
    RETURN
    IF ( PrevYear > 0, SELECTEDMEASURE() - PrevYear, BLANK () )

# Year over Year % by Calculation Group

Use SELECTEDMEASURE() in Calculation Group.

    DeviceYOY% =
    VAR PrevYear =
    CALCULATE ( 'Web Traffic'[Desktop], DATEADD ( dDate[Date], -1, YEAR ) )
    RETURN
    DIVIDE ( 'Web Traffic'[Desktop] - PrevYear, PrevYear )

# Custom Conditonal formatting for Calculation Group items

Choose Calculation Group item(s) in {"...","..."} and select a measure to insert conditional formatting.

    ConF-V1 =
    IF (
    SELECTEDVALUE ( CalGroupDevices[Name] ) IN { "item1", "item2" },
    [Measure],
    BLANK ()
    )    
    
    
    
[Back to Top](https://github.com/SiriShultz/sitelinks/tree/main/Web-Analytics#date-table)
