# EDNA_Call_Center_Data_Report
![Main Page](https://github.com/tirthvyas95/EDNA_Call_Center_Data_Report/blob/0a95575433e4051e7819a5f0e5590dd3d1b4830b/Screenshots/Main%20Page.png)
## Introduciton
Exploratory Data Analysis on a Call Center Dataset provided by Enterprise DNA. This is a portfolio project where we play a role as a data analyst who must create detailed report for senior management for better understanding of the Operations. This Project is part of [Challenges](https://app.enterprisedna.co/app/challenges) published by Enterprise DNA you can learn more on their [Website](https://enterprisedna.co/). 

Our goal here is to act as a report developer where it is our job as an analyst to prepare a report that summarise, the overall service provided by EDNA’s Call Centres(Fictional Call Center) and enabling its senior Management to have a better view of its operations. We are required to utilize the 4 pillars of report development using best practices: data transformation, data modeling, DAX calculations and reporting and visualization.
## Dataset
Here we have a Dataset with Call records of our call center where we have information on the timestamp, duration and call records. Here is the deep dive of the data and the tables that we have

Call Center Data Table(Fact Table):
As you can see in the Dataset folder of this repository the data is broken in 4 years from the beginning 2018 to the end 2021, so first we will use the append query function to combine these 4 tables as they have the same column structure and the same type of data.

1. CallTimestamp = Time stamp when the call was recieved
2. CallType = Type of Call(Dimention Table also Added in the Model)
3. EmployeeID = ID of the Employee who got assigned to the call(Dimention Table also Added in the Model)
4. CallDuration = Duration for which the call lasted
5. WaitTime = Amount of time the customer had to wait
6. CallAbandoned = 1 if abandoned 0 if not
7. CallDate = Date when the call was recieved
8. SLA Compliance = Within SLA if less then 35 sec or Outside SLA
```
SLA Compliance = IF(
    'Call Center Data'[WaitTime] <35, "Within SLA", "Outside SLA"
)
```

CallCharges Table(Dimension Table):
1. Call Type Key = Unique number for each call type
2. Call Type = Name of the call type
3. Call Charges 2018 = Call charges for each type for the year of 2018
4. Call Charges 2019 = Call charges for each type for the year of 2019
5. Call Charges 2020 = Call charges for each type for the year of 2020
6. Call Charges 2021 = Call charges for each type for the year of 2021

Emoloyees Table(Dimension Table):
1. EmployeeID = Unique Identifier of each employee
2. EmoloyeeName = Name of the Employee
3. Site = Site at which the Employee works
4. ManagerName = Name of the Manager

Date Table(Date Dimension):
1. Date = Derived from Min Date and Max Date
2. Calendar Year = "CY 'Year no'", Example: CY 2018, CY 2019
3. Month Name = Name of the month
4. Month Number = Number of the month
5. Weekday = Name of the weekday
6. Weekday Number = Number of Weekday
7. Quarter = Quarter number example: Q1, Q2, Q3
8. DateKey = Date key of the current date like 20180707(Used for sorting)
9. YearKey = Number of the year
10. MonthOfYear = Number of the Month
11. DayOfYear = Day number in the Year
12. Quarter & Year = Q# 20##, ex Q4 2019
13. Monday Non-ISO WeekNumber = Week number based on the Gregorian Calendar
14. ISO WeekNumber = Week Number by using ISO Standard calendar
15. ISO Year = Year Number using ISO Year Standard
16. ISO Year & Week = Week Date of the start of each week
17. ISO Week Index = Week Index using ISO calendar
18. ISO Week Based Quarter and Year = Example: 20182, YYYYQ

Here is the DAX used to make this table:
```
Date = 
VAR MinYear = YEAR (MIN ('Call Center Data'[CallDate]))
VAR MaxYear = YEAR (MAX ('Call Center Data'[CallDate]))
RETURN
ADDCOLUMNS (
    FILTER (
        CALENDARAUTO( ),
        AND ( YEAR ( [Date] ) >= MinYear, YEAR ( [Date] ) <= MaxYear )
    ),
    "Calendar Year", "CY " & YEAR ( [Date] ),
    "Month Name", FORMAT ( [Date], "mmmm" ),
    "Month Number", MONTH ( [Date] ),
    "Weekday", FORMAT ( [Date], "dddd" ),
    "Weekday number", WEEKDAY( [Date], 2 ),
    "Quarter", "Q" & TRUNC ( ( MONTH ( [Date] ) - 1 ) / 3 ) + 1
)
```
For Column DateKey:
```
DateKey = 
YEAR ( 'Date'[Date] ) * 10000 +
MONTH ( 'Date'[Date] ) * 100 +
DAY ( 'Date'[Date] )
```
For Column YearKey:
```
YearKey = YEAR('Date'[Date])
```
For Column MonthOfYear:
```
MonthOfYear = FORMAT('Date'[Date], "MM")
```
For Column DayOfYear:
```
DayOfYear = FORMAT('Date'[Date], "DD")
```
For Column Quarter & Year:
```
Quarter & Year = 'Date'[Quarter] & " " & 'Date'[YearKey]
```
For Column Monday Non-ISO WeekNumber:
```
Monday Non-ISO WeekNumber = WEEKNUM('Date'[Date], 2)
```
For Column ISO WeekNumber:
```
ISO WeekNumber = WEEKNUM ('Date'[Date], 21)
```
For Column ISO Year:
```
ISO Year = 
YEAR ('Date'[Date] - WEEKDAY('Date'[Date], 2) + 4)
```
For Column ISO Year & Week:
```
ISO Year & Week = "Y" & 'Date'[ISO Year] & " W" & 'Date'[ISO WeekNumber]
```
For Column ISO Week Start:
```
ISO Week Start = 
'Date'[Date] - WEEKDAY ('Date'[Date], 2) + 1
```
For Column ISO Week Index:
```
ISO Week Index = 
VAR FirstISOWeekStart =
    CALCULATE (
        MIN ('Date'[ISO Week Start]),
        ALL ('Date')
    )
RETURN
    INT (
        DIVIDE (
            'Date'[ISO Week Start] - FirstISOWeekStart,
            7
        )
    ) + 1
```
For Column ISO Week Based Quarter:
```
ISO Week Based Quarter = IF(
    'Date'[ISO WeekNumber] <= 52,
    QUOTIENT('Date'[ISO WeekNumber] - 1, 13) + 1,
    4
)
```
For Column ISO Week Based Quarter and Year:
```
ISO Week Based Quarter and Year = 'Date'[ISO Year] & 'Date'[ISO Week Based Quarter]
```
## Measures 
Here are the Measures that we are going to use in this Report:
### Base Measures:
1. Total Call Revenue:
```
Total Call Revenue = 
SUMX(
    'Call Center Data',
    'Call Center Data'[CallDuration] * (
        IF(
            YEAR('Call Center Data'[CallDate]) = 2018,
            RELATED(CallCharges[Call Charges 2018]),
            IF(
                YEAR('Call Center Data'[CallDate]) = 2019,
                RELATED(CallCharges[Call Charges 2019]),
                IF(
                    YEAR('Call Center Data'[CallDate]) = 2020,
                    RELATED(CallCharges[Call Charges 2020]),
                    RELATED(CallCharges[Call Charges 2021])
                )
            )
        )
    )
)
```
2. Total Calls:
```
Total Calls = COUNTROWS('Call Center Data')
```
3. Total Revenue Per Call:
```
Total Revenue Per Call = [Total Call Revenue] / [Total Calls]
```
4. Total Calls Witin Compliance:
```
Total Call Within Compliance = CALCULATE(
    COUNTROWS('Call Center Data'),
    'Call Center Data'[SLA Compliance] = "Within SLA"
)
```
6. Compliance Rate:
```
Complaince Rate = DIVIDE(
    [Total Call Within Compliance],
    [Total Calls]
)
```
### Employees Page Measures:
1. Average Call Duration:
```
Average Call Duration = AVERAGE('Call Center Data'[CallDuration])
```
2. Average Call WaitTIme:
```
Average Call WaitTIme = AVERAGE('Call Center Data'[WaitTime])
```
3. Percentage Calls Within SLA %:
```
Percentage Calls Within SLA % = DIVIDE(
    [Total Call Within Compliance],
    [Total Calls]
)
```
### Time Intelligence(Gregorian Calendar):
1. Revenue Month to Date:
```
Revenue Month to Date = TOTALMTD([Total Call Revenue], 'Date'[Date])
```
2. Revenue Quarter to Date:
```
Revenue Quarter to Date = TOTALQTD ([Total Call Revenue], 'Date'[Date])
```
3. Revenue Year to Date:
```
Revenue Year to Date = TOTALYTD([Total Call Revenue], 'Date'[Date])
```
### Time Intelligence(ISO Standard Calendar):
1.Revenue Quarter to Date ISO:
```
Revenue Quarter to Date ISO = 
VAR CurrentFiscalWeek = MAX('Date'[ISO Week Index])
VAR CurrentFiscalQuarter = MAX('Date'[ISO Week Based Quarter and Year])

RETURN
CALCULATE(
    [Total Call Revenue],
    FILTER(
        ALL('Date'),
        'Date'[ISO Week Based Quarter and Year] = CurrentFiscalQuarter &&
        'Date'[ISO Week Index] <= CurrentFiscalWeek
    )
)
```
2. Revenue Week to Date ISO:
```
Revenue Week to Date ISO = 
VAR CurrentFiscalDay = MAX('Date'[Date])
VAR CurrentFiscalWeek = MAX('Date'[ISO Week Index])

RETURN
CALCULATE(
    [Total Call Revenue],
    FILTER(
        ALL('Date'),
        'Date'[Date] = CurrentFiscalDay &&
        'Date'[ISO Week Index] <= CurrentFiscalWeek
    )
)
```
3. Revenue Year to Date ISO:
```
Revenue Year to Date ISO = 
VAR CurrentFiscalWeek = MAX('Date'[ISO Week Index])
VAR CurrentFiscalYear = MAX('Date'[ISO Year])

RETURN
CALCULATE(
    [Total Call Revenue],
    FILTER(
        ALL('Date'),
        'Date'[ISO Year] = CurrentFiscalYear &&
        'Date'[ISO Week Index] <= CurrentFiscalWeek
    )
)
```
Now lets incorporate these measures into Visuals!
## Visuals
### Home Page
- This Includes the Main Filters which also propogate to all other pages along with the option to go to additional filter with a button for reseting them as well.
- On the left side there are buttons with icon pertaining to visual in other pages, you can use them to go to navigate
- To switch to other pages you must come back to home screen and then navigate to them, or else you can just use the page navigator on the bottom
![Main Page](https://github.com/tirthvyas95/EDNA_Call_Center_Data_Report/blob/fd813300623302d88e89147f0d3279e9bd198f85/Screenshots/Main%20Page.png)
### Overview Page
- This page gives a eagle eye view of the operations of the Call Center, stakeholders can navigate to it quickly get an idea as to how things are progressing
![Oerview Page](https://github.com/tirthvyas95/EDNA_Call_Center_Data_Report/blob/fd813300623302d88e89147f0d3279e9bd198f85/Screenshots/Overview%20Page.png)
### Employees Page
- This page contains




