# 📞 Call Centre Analysis — DAX Measures Documentation

This document contains all DAX measures used in the Call Centre Analysis Power BI report.  
The goal is to organize all calculated metrics in one place for transparency, reusability, and easier maintenance.

Each measure is stored inside a dedicated **Measure Table**, which helps keep the data model clean and improves report readability.

---

# 📘 Table of Contents

### Measure Table
1. [Measure Table Set Up](#measure-table-set-up)

### Call Volume Metrics
2. [Total Calls](#total-calls)
3. [Total Call Answered](#total-call-answered)
4. [Last Call](#last-call)

### Performance Metrics
5. [Avg Call Handling Time](#avg-call-handling-time)
6. [Speed of Answer](#speed-of-answer)

### Resolution Metrics
7. [Calls Resolved %](#calls-resolved-)
8. [Calls Abandoned %](#calls-abandoned-)

### Customer Experience Metrics
9. [CSAT](#csat)
10. [Satisfaction Level](#satisfaction-level)

### Time/Date Dimensions
11. [Day Name](#day-name)
12. [Week Day Number](#week-day-number)
13. [Hour](#hour)
14. [Hour Order By](#hour-order-by)
15. [Month](#month)

---

# Measure Table Set Up

Created a measure table to store all measures in one organized location instead of mixing them with fact or dimension tables.  
It improves the data model structure, readability, and makes reports easier to maintain.

```DAX
measures_table = GENERATESERIES(-1, 1, 1)
```

---

# Call Volume Metrics

## Total Calls

Calculates the total number of distinct calls.

```DAX
Total calls = DISTINCTCOUNT('Calls '[call id])
```

## Total Call Answered

Calculates the total number of calls that were answered.

```DAX
Total Call Answered = 

CALCULATE(COUNT('Calls '[call id]), 'Calls '[answered (Y/N)] = "Y")
```

## Last Call

Returns the date and time of the most recent call.

```DAX
Last Call = 

MAX('Calls '[date time])
```

---

# Performance Metrics

## Avg Call Handling Time

Calculates the average time agents spend talking to customers during calls.  
Useful for evaluating overall performance and identifying efficiency trends.

```DAX
Avg Call Handling Time =
AVERAGE('Calls'[avgtalkduration])
```

## Speed of Answer

Calculates the average time (in seconds) it takes for calls to be answered.

```DAX
Speed of Answer = 
AVERAGE('Calls '[speed of answer in seconds])
```

---

# Resolution Metrics

## Calls Resolved %

Calculates the percentage of answered calls that were resolved successfully.

```DAX
Calls resolved % = 
VAR TotalResolvedCalls = CALCULATE (COUNT('Calls '[call id]),'Calls '[resolved]= "Y") 
VAR TotalCalls = CALCULATE(COUNT('Calls '[call id]), 'Calls '[answered (Y/N)] = "Y")

RETURN 
    DIVIDE(TotalResolvedCalls, TotalCalls, 0) * 100
```

## Calls Abandoned %

Calculates the percentage of calls that were not answered out of total calls.

```DAX
Calls abandoned % = 

//total resolved calls/total calls answered * 100
VAR CallNotAnswered = CALCULATE (COUNT('Calls '[call id]) , 'Calls '[answered (Y/N)]= "N") 

RETURN 
    DIVIDE(CallNotAnswered, [Total calls], 0 ) * 100
```

---

# Customer Experience Metrics

## CSAT

Calculates the Customer Satisfaction (CSAT) score as a percentage of the maximum possible rating.

```DAX
CSAT = 

VAR TotalRatings = SUM('Calls '[satisfaction rating])
VAR NoofRatings = COUNT('Calls '[satisfaction rating])

RETURN
    FORMAT(
    DIVIDE(TotalRatings, NoofRatings * 5, 0), "0.00%"
    )
```

## Satisfaction Level

Categorizes customer satisfaction ratings into descriptive labels.

```DAX
Satisfaction Level = 

SWITCH(
        TRUE(),
        ISBLANK('Calls '[satisfaction rating]), "Not Served", 
        'Calls '[satisfaction rating] = 1, "Very Dissatisfied",
        'Calls '[satisfaction rating] = 2, "Dissatisfied",        
        'Calls '[satisfaction rating] = 3, "Normal",
        'Calls '[satisfaction rating] = 4, "Satisfied",
        'Calls '[satisfaction rating] = 5, "Very Satisfied"
)
```

---

# Time/Date Dimensions

## Day Name

Returns the day of the week for each call date.

```DAX
Day Name = 
FORMAT ('Calls '[date], "Ddd") to get the day of the week
```

## Week Day Number

Returns the day of the week as a number (Monday = 1, Sunday = 7).

```DAX
Week Day Number = 

WEEKDAY('Calls '[date], 2)
```

## Hour

Extracts the hour from the call time and formats it as "HH:00".

```DAX
Hour = 

FORMAT('Calls '[time], "HH" & ":00")
```

## Hour Order By

Extracts the hour from the call date-time for sorting purposes.

```DAX
Hour Order By = 

HOUR('Calls '[date time])
```

## Month

Formats the call date to show the month and year (e.g., "Jan 23").

```DAX
Month = 

FORMAT('Calls '[date], "Mmm YY")
```
