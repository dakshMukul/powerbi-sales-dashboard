% Diff Profit = 
VAR _Perc = FORMAT(DIVIDE([Profit_CY]-[Profit_PY], [Profit_PY]), "0.0%;0.0%")
RETURN
    IF([Profit_CY]-[Profit_PY] > 0, 
        UNICHAR(9650) & " " & _Perc,
        UNICHAR(9660) & " " & _Perc
    )

% Diff Qty = 
VAR CY = [Qty_CY]
VAR PY = [Qty_PY]
var _Perc = FORMAT(DIVIDE([Qty_CY]-[Qty_PY], [Qty_PY]), "0.0%;0.0%")
RETURN
    IF(CY-PY > 0 ,
        UNICHAR(9650) & " " & _Perc,
        UNICHAR(9660) & " " & _Perc
    )

% Diff Sales = 
    VAR CY = [sales_CY]
    VAR LY = 
    CALCULATE([Total Sales],
    'Calendar'[year] = MAX('Calendar'[year])-1
    )
    VAR _perc = FORMAT(DIVIDE(CY-LY, LY), "0.0%;0.0%")
    RETURN
    IF(
        CY-LY > 0 , 
        UNICHAR(9650) & " " & _perc,
        UNICHAR(9660) & " " & _perc
    )

Avg Weekly Profit CY = 
VAR SelectedYear = SELECTEDVALUE('Calendar'[year])
VAR TargetYear = COALESCE(
    SelectedYear,
    CALCULATE(
        MAX('Calendar'[year]),
        REMOVEFILTERS('Calendar')
    )
)
VAR WeeklyProfit = CALCULATETABLE(
    ADDCOLUMNS(
        VALUES('Calendar'[week]),
        "Profit", [Total Profit]
    ),
    REMOVEFILTERS('Calendar'),
    'Calendar'[year] = TargetYear
)
VAR AvgValue = AVERAGEX(WeeklyProfit, [Profit])
RETURN
AvgValue

Avg weekly Sales CY = 
VAR SelectedYear = SELECTEDVALUE('Calendar'[year])
VAR TargetYear = 
    COALESCE(
        SelectedYear,
        CALCULATE(
            MAX('Calendar'[year]),
            REMOVEFILTERS('Calendar')
        )
    )
VAR AvgWeeklySales = 
    CALCULATETABLE(
        ADDCOLUMNS(
            VALUES('Calendar'[week]),
            "@Sales", [Total Sales]
        ),
        REMOVEFILTERS('Calendar'),
        'Calendar'[year] = TargetYear
    )
VAR AvgValue = AVERAGEX(AvgWeeklySales, [@Sales])
RETURN
AvgValue

Profit BG Color = 
VAR CY = [Profit_CY]
VAR LY = [Profit_PY]
RETURN
SWITCH(
    TRUE(),
    CY-LY > 0, "#EAF8EC",
    CY-LY < 0, "#FFDCDC",
    "#F2F2F2"
)


Profit Max Point = 
VAR SelectedYear = SELECTEDVALUE('Calendar'[year])
VAR TargetYear = COALESCE(
    SelectedYear,
    CALCULATE(
        MAX('Calendar'[year]),
        REMOVEFILTERS('Calendar')
    )
)
VAR MonthlyProfit = CALCULATETABLE(
    ADDCOLUMNS(
        VALUES('Calendar'[month_start]),
        "@values", [Total Profit]
    ),
    REMOVEFILTERS('Calendar'),
    'Calendar'[year] = TargetYear
)

VAR MaxSales = MAXX(MonthlyProfit, [@values])
VAR CurrentMonthSales = [Profit_CY]
RETURN
    IF(CurrentMonthSales = MaxSales, MaxSales, BLANK())


Profit Min Point = 
VAR SelectedYear = SELECTEDVALUE('Calendar'[year])
VAR TargetYear = COALESCE(
    SelectedYear,
    CALCULATE(
        MAX('Calendar'[year]),
        REMOVEFILTERS('Calendar'[year])
    )
)
VAR MonthlyProfit = CALCULATETABLE(
     ADDCOLUMNS(
        VALUES('Calendar'[month_start]),
        "@values", [Total Profit]
     ),
     REMOVEFILTERS('Calendar'),
     'Calendar'[year] = TargetYear
)
VAR MinProfit = MINX(MonthlyProfit, [@values])
VAR currentMonthProfit = [Profit_CY]
RETURN
    IF(currentMonthProfit = MinProfit, MinProfit, BLANK())

Profit Text Color = 
VAR CY = [Profit_CY]
VAR LY = CALCULATE(
    [Total Profit],
    REMOVEFILTERS('Calendar'),
    'Calendar'[year] = MAX('Calendar'[year]) - 1
)
RETURN
SWITCH(
    TRUE(),
    CY-LY > 0, "Green",
    CY-LY < 0, "Red",
    "Grey"
)

Profit_CY = 
    CALCULATE(
        [Total Profit],
        'Calendar'[year] = max('Calendar'[year])
    )

Profit_PY = 
    CALCULATE(
        [Total Profit],
        'Calendar'[year] = max('Calendar'[year])-1
    )

Profit_PY_KPI = 
VAR PY = CALCULATE(
        [Total Profit],
        'Calendar'[year] = max('Calendar'[year])-1
    )
RETURN 
    FORMAT(PY, "$#,0,.0K") & " vs PY"

Qty BG Color = 
VAR CY = [Qty_CY]
VAR LY = [Qty_PY]
RETURN
SWITCH(
    TRUE(),
    CY-LY > 0, "#EAF8EC",
    CY-LY < 0, "#FFDCDC",
    "#F2F2F2"
)

Qty Max Point = 
VAR SelectedYear = SELECTEDVALUE('Calendar'[year])
VAR TargetYear = COALESCE(
    SelectedYear,
    CALCULATE(
        MAX('Calendar'[year]),
        REMOVEFILTERS('Calendar')
    )
)
VAR MonthlyQty = 
    CALCULATETABLE(
        ADDCOLUMNS(
            VALUES('Calendar'[month_start]),
            "@Qty", [Total Qty]
        ),
        REMOVEFILTERS('Calendar'),
        'Calendar'[year] = TargetYear
    )
VAR MaxQty = MAXX(MonthlyQty, [@Qty])
VAR CurrentQty = [Qty_CY]
RETURN
    IF(CurrentQty = MaxQty, MaxQty, Blank())

Qty Min Point = 
VAR SelectedYear = SELECTEDVALUE('Calendar'[year])
VAR TargetYear = COALESCE(
    SelectedYear,
    CALCULATE(
        MAX('Calendar'[year]),
        REMOVEFILTERS('Calendar')
    )
)
VAR MonthlyQty = 
    CALCULATETABLE(
        ADDCOLUMNS(
            VALUES('Calendar'[month_start]),
            "@value", [Total Qty]
        ),
        REMOVEFILTERS('Calendar'),
        'Calendar'[year] = TargetYear
    )
VAR MaxQty = MINX(MonthlyQty, [@value])
VAR CurrentQty = [Qty_CY]
RETURN
    IF(CurrentQty = MaxQty, MaxQty, Blank())

Sales Text Color = 
VAR CY = [sales_CY]
VAR LY = 
    CALCULATE([Total Sales],
    'Calendar'[year] = MAX('Calendar'[year])-1
    )
RETURN
SWITCH(
    TRUE(),
    CY-LY > 0, "Green",
    CY-LY < 0, "Red",
    "Grey"
)

sales_CY = 
  CALCULATE([Total Sales],
  'Calendar'[year] = MAX('Calendar'[year])
  )

sales_PY = CALCULATE([Total Sales], 
  'Calendar'[year] = MAX('Calendar'[year])-1
)

sales_PY_KPI = 
 VAR py = CALCULATE([Total Sales], 
  'Calendar'[year] = MAX('Calendar'[year])-1
)
return 
FORMAT(py, "$#,0,.0K") & " vs PY"

SelectedYear = 
VAR SelectedYear = SELECTEDVALUE('Calendar'[year])
RETURN
    COALESCE(
        SelectedYear,
        CALCULATE(
            MAX('Calendar'[year]),
            REMOVEFILTERS('Calendar')
        )
    )

Total Profit = SUM(fact_orders[Profit])

Total Qty = SUM(fact_orders[Quantity])

Total Sales = SUM(fact_orders[Sales])

% Diff Cust = 
VAR CY = [Customer CY]
VAR PY = [Customer PY]
VAR diff = FORMAT(DIVIDE(CY-PY, PY), "0.0%;0.0%")
RETURN
    IF(CY-PY >  0, 
    UNICHAR(9650) & " " & diff,
    UNICHAR(9660) & " " & diff
    )

% Diff Order = 
VAR CY = [Order CY]
VAR PY = [Order PY]
VAR V =  FORMAT(DIVIDE(CY-PY,PY), "0.0%;0.0%")
RETURN
    IF(CY - PY > 0, 
    UNICHAR(9650) & " " & V,
    UNICHAR(9660) & " " & V
    )

% Diff Sales Per Cust = 
VAR CY = [Sales Per Cust CY]
VAR PY = [Sales Per Cust PY]
VAR V = FORMAT(DIVIDE(CY - PY, PY), "0.0%;0.0%")
RETURN
    IF(CY-PY > 0, 
        UNICHAR(9650) & " " & V,
        UNICHAR(9660) & " " & V
    )

Cust BG Color = 
VAR CY = [Customer CY]
VAR LY = [Customer PY]
RETURN
    SWITCH(
        TRUE(),
        CY-LY > 0, "#EAF8EC",
        CY-LY < 0, "#FFDCDC",
        "#F2F2F2"
    )

Cust Max Point = 
VAR MonthlyCust = CALCULATETABLE(
    ADDCOLUMNS(
        VALUES('Calendar'[month_start]),
        "@value", [Total Customer]
    ), 
    REMOVEFILTERS('Calendar'),
    'Calendar'[year] = max('Calendar'[year])
)
VAR MaxPoint = MAXX(MonthlyCust, [@value])
VAR currentValue = [Customer CY]
RETURN
    IF(MaxPoint = currentValue, MaxPoint, BLANK())

Cust Min Point = 
VAR MonthlyCust = 
    CALCULATETABLE(
        ADDCOLUMNS(
            VALUES('Calendar'[month_start]),
            "@Value", [Total Customer]
        ),
        REMOVEFILTERS('Calendar'),
        'Calendar'[year] = MAX('Calendar'[year])
    )
VAR MinPoint = MINX(MonthlyCust, [@Value])
VAR currentValue = [Customer CY]
RETURN  
    IF(MinPoint = currentValue, MinPoint, blank())

Cust Text Color = 
VAR CY = [Customer CY]
VAR LY = [Customer PY]
RETURN
SWITCH(
    TRUE(),
    CY-LY > 0, "Green",
    CY-LY < 0, "Red",
    "Grey"
)

Customer CY = CALCULATE(
    [Total Customer],
    'Calendar'[year] = MAX('Calendar'[year])
)

Customer PY = CALCULATE(
    [Total Customer],
    'Calendar'[year] = MAX('Calendar'[year]) - 1
)

Customer PY KPI = 
VAR PY = CALCULATE(
    [Total Customer],
    'Calendar'[year] = MAX('Calendar'[year]) - 1
)
RETURN
    PY & " " & "vs PY"


Customers by Order Count = 
VAR OrderCount = SELECTEDVALUE('Order Buckets'[Orders])
VAR CustomerOrderSummary = 
    FILTER(
        CALCULATETABLE( 
                ADDCOLUMNS(
                VALUES(fact_orders[Customer ID]),
                    "@OrderPlaced", CALCULATE(DISTINCTCOUNT(fact_orders[Order ID]))
            ),
            'Calendar'[year] = MAX('Calendar'[year])
        ),
        NOT ISBLANK([@OrderPlaced])
    )
RETURN
    COUNTROWS(FILTER(CustomerOrderSummary, [@OrderPlaced] = OrderCount))


LastOrderDate = 
VAR CustName = SELECTEDVALUE(dim_customers[Customer Name])
RETURN CALCULATE(
    MAX(fact_orders[Order Date]),
    dim_customers[Customer Name] = CustName
)

Order BG Color = 
VAR CY = [Order CY]
VAR LY = [Order PY]
RETURN
    SWITCH(
        TRUE(),
        CY-LY > 0, "#EAF8EC",
        CY-LY < 0, "#FFDCDC",
        "#F2F2F2"
    )

Order CY = CALCULATE(
    [Total Order], 
    'Calendar'[year] = MAX('Calendar'[year])
)   

Order Max Point = 
VAR MonthlyCust = CALCULATETABLE(
    ADDCOLUMNS(
        VALUES('Calendar'[month_start]),
        "@value", [Total Order]
    ), 
    REMOVEFILTERS('Calendar'),
    'Calendar'[year] = max('Calendar'[year])
)
VAR MaxPoint = MAXX(MonthlyCust, [@value])
VAR currentValue = [Order CY]
RETURN
    IF(MaxPoint = currentValue, MaxPoint, BLANK())

Order Min Point = 
VAR MonthlyCust = CALCULATETABLE(
    ADDCOLUMNS(
        VALUES('Calendar'[month_start]),
        "@value", [Total Order]
    ), 
    REMOVEFILTERS('Calendar'),
    'Calendar'[year] = max('Calendar'[year])
)
VAR MaxPoint = MINX(MonthlyCust, [@value])
VAR currentValue = [Order CY]
RETURN
    IF(MaxPoint = currentValue, MaxPoint, BLANK())

Order PY = CALCULATE(
    [Total Order], 
    'Calendar'[year] = MAX('Calendar'[year]) - 1
)

Order PY KPI = 
VAR v = CALCULATE(
    [Total Order], 
    'Calendar'[year] = MAX('Calendar'[year]) - 1
)
RETURN
    v & " " & "vs PY"

Order Text Color = 
VAR CY = [Order CY]
VAR LY = [Order PY]
RETURN
SWITCH(
    TRUE(),
    CY-LY > 0, "Green",
    CY-LY < 0, "Red",
    "Grey"
)

Sales Per Cust BG Color = 
VAR CY = [Sales Per Cust CY]
VAR LY = [Sales Per Cust PY]
RETURN
    SWITCH(
        TRUE(),
        CY-LY > 0, "#EAF8EC",
        CY-LY < 0, "#FFDCDC",
        "#F2F2F2"
    )

Sales Per Cust CY = 
    CALCULATE([Total Sales Per Cust],
    'Calendar'[year] = MAX('Calendar'[year])
    )

Sales Per Cust Max Point = 
VAR MonthlyCust = CALCULATETABLE(
    ADDCOLUMNS(
        VALUES('Calendar'[month_start]),
        "@value", [Total Sales Per Cust]
    ), 
    REMOVEFILTERS('Calendar'),
    'Calendar'[year] = max('Calendar'[year])
)
VAR MaxPoint = MAXX(MonthlyCust, [@value])
VAR currentValue = [Sales Per Cust CY]
RETURN
    IF(MaxPoint = currentValue, MaxPoint, BLANK())

Sales Per Cust Min Point = 
VAR MonthlyCust = CALCULATETABLE(
    ADDCOLUMNS(
        VALUES('Calendar'[month_start]),
        "@value", [Total Sales Per Cust]
    ), 
    REMOVEFILTERS('Calendar'),
    'Calendar'[year] = max('Calendar'[year])
)
VAR MaxPoint = MINX(MonthlyCust, [@value])
VAR currentValue = [Sales Per Cust CY]
RETURN
    IF(MaxPoint = currentValue, MaxPoint, BLANK())

Sales Per Cust PY = 
    CALCULATE(
        [Total Sales Per Cust],
        'Calendar'[year] = MAX('Calendar'[year]) - 1
    )

Sales Per Cust PY KPI = 
VAR V = DIVIDE([sales_PY], 
        CALCULATE(
            DISTINCTCOUNT(fact_orders[Customer ID]),
            'Calendar'[year] = MAX('Calendar'[year])-1
        )
    )
RETURN
    FORMAT(V, "$#,0.0" )& " " & "vs PY"


Sales Per Cust Text Color = 
VAR CY = [Sales Per Cust CY]
VAR LY = [Sales Per Cust PY]
RETURN
SWITCH(
    TRUE(),
    CY-LY > 0, "Green",
    CY-LY < 0, "Red",
    "Grey"
)

Total Customer = DISTINCTCOUNT(fact_orders[Customer ID])

Total Order = DISTINCTCOUNT(fact_orders[Order ID])

Total Sales Per Cust = 
    DIVIDE(
        [Total Sales],
        DISTINCTCOUNT(fact_orders[Customer ID])
    )