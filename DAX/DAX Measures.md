# DAX Measures

This document contains the DAX measures used to calculate KPIs and business metrics for the Power BI dashboard.

---

# 💰 Base Measures
Revenue := SUM ( 'public bi_fact_sales'[price] )

Freight := SUM ( 'public bi_fact_sales'[freight_value] )

GMV := [Revenue] + [Freight]

Orders := DISTINCTCOUNT ( 'public bi_fact_sales'[order_id] )

Customers := DISTINCTCOUNT ( 'public bi_fact_sales'[customer_unique_id] )

Items Sold := COUNTROWS ( 'public bi_fact_sales' )

AOV := DIVIDE ( [Revenue], [Orders] )

Items per Order := DIVIDE ( [Items Sold], [Orders] )

Avg Rating := AVERAGE ( 'public bi_fact_sales'[review_score] )

Delivered Orders :=
CALCULATE ( [Orders], 'public bi_fact_sales'[order_status] = "delivered" )

Late Orders :=
CALCULATE ( [Orders], 'public bi_fact_sales'[order_status] = "delivered", 'public bi_fact_sales'[is_late] = 1 )

On-time % :=
DIVIDE ( [Delivered Orders] - [Late Orders], [Delivered Orders] )


<br>

# 📈 Time Measures 

Revenue YTD := TOTALYTD ( [Revenue], DimDate[Date] )

Revenue LY :=
CALCULATE ( [Revenue], SAMEPERIODLASTYEAR ( DimDate[Date] ) )

YoY % := DIVIDE ( [Revenue] - [Revenue LY], [Revenue LY] )

Rolling 30D Revenue :=
CALCULATE(
    [Revenue],
    DATESINPERIOD ( DimDate[Date], MAX ( DimDate[Date] ), -30, DAY )
)

<br>

# 😊 Review Sentiment Measures 

Positive Reviews % :=
DIVIDE(
    CALCULATE( COUNTROWS('public bi_fact_sales'), 'public bi_fact_sales'[review_score] >= 4 ),
    CALCULATE( COUNTROWS('public bi_fact_sales'), NOT ISBLANK('public bi_fact_sales'[review_score]) )
)

Negative Reviews % :=
DIVIDE(
    CALCULATE( COUNTROWS('public bi_fact_sales'), 'public bi_fact_sales'[review_score] <= 2 ),
    CALCULATE( COUNTROWS('public bi_fact_sales'), NOT ISBLANK('public bi_fact_sales'[review_score]) )
)

<br>

# 💳 Payments Measures

Payment Value := SUM ( 'public bi_fact_sales'[payment_value] )

Avg Installments := AVERAGE ( 'public bi_fact_sales'[payment_installments] )




<br>



# 🚚 Dilevery & Logistics 

Avg Delivery Days :=
AVERAGE ( 'public bi_fact_sales'[delivery_days] )


Late % := DIVIDE( [Late Orders], [Delivered Orders] )

<br>

# ⭐  Reviews

Review Bucket :=
SWITCH(
    TRUE(),
    ISBLANK('public bi_fact_sales'[review_score]), "No Review",
   'public bi_fact_sales'[review_score] >= 4, "Positive (4-5)",
    'public bi_fact_sales'[review_score] = 3, "Neutral (3)",
    "Negative (1-2)"
)

<br>

# 🏪 Seller 

Sellers := DISTINCTCOUNT ( 'public bi_fact_sales'[seller_id] )

Revenue per Seller := DIVIDE ( [Revenue], [Sellers] )

<br>

# 🔍 Drillthrough

Drill Title = 
"Drillthrough: " &
COALESCE( SELECTEDVALUE('public bi_fact_sales'[category]), "All" )

<br>

# 📌 Notes

- All measures were created using **DAX** in **Power BI**.
- These measures power the KPIs, charts, cards, and drillthrough pages across the dashboard.
- Additional measures may be added as the project evolves.


