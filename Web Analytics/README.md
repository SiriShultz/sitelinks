Web Analytics Time Series with Calculation Group and custom conditional formatting.


# Date Table
Create Date table with the first date and last date that exist in the fact table.

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

Total of desktop users.

    Desktop = 
    SUM('Web Traffic'[Desktop Users])
    
# Mobile Users

Total of Mobile users.

    Mobile = 
    SUM('Web Traffic'[Mobile Users])

# Tablet Users

Total of Tablet users.

    Tablet = 
    SUM('Web Traffic'[Tablet Users])
    
# All Devices Users

Total of Tablet users.

    AllDevices =
    SUMX (
    'Web Traffic',
    'Web Traffic'[Desktop Users] + 'Web Traffic'[Mobile Users] + 'Web Traffic'[Tablet Users]
    )
# Device% by Calculation Group

Use SELECTEDMEASURE() in Calculation Group.

    Device% =
    DIVIDE ( SELECTEDMEASURE(), [AllDevices] )


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



    
It will look like this
[FixClicks website](https://www.fixclicks.net/seo)

Word is in brackets - and the link is in the parenthesis

had to go in and replace desktop-tutorial with shared-links on this Skip to link below

Skip to [Day 4](https://github.com/billsteeley/shared-links/blob/main/README.md#day-4)


# Day 1 1/2

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


 

 

# Day 1

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

had to go in and replace desktop-tutorial with shared-links on this Skip to link below

Skip to [Day 4](https://github.com/billsteeley/shared-links/blob/main/README.md#day-4)


# Day 1 1/2

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


