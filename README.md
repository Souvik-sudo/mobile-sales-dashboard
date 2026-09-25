# 📱 Mobile Sales Dashboard

An interactive Power BI dashboard analyzing Motorola-branded mobile phone sales — 
built as a hands-on learning project while following Satish Dhawale's Power BI course.

## 📊 What It Shows
- **KPIs:** Total Sales, Total Quantity, Transactions, Average Price
- **Total Quantity by Month** — line chart with month/day drill-down
- **Total Sales by City** — map with bubble size by revenue
- **Brand comparison table** — Apple, Samsung, OnePlus, Vivo, Xiaomi
- **Top 3 Models by Quantity** — filtered Top N column chart
- **Payment Method split** — pie chart (UPI, Debit Card, Cash, Credit Card)
- **Customer Ratings** — funnel chart
- **Sales by Day of Week** — area chart
- Slicers: Mobile Model, Payment Method, Brand, Day Name

## 🧮 Key DAX Measures
```dax
Total Sales = SUMX(Sales_Data, Sales_Data[Units Sold] * Sales_Data[Price per Unit])
Total Quantity = SUM(Sales_Data[Units Sold])
Transaction = COUNTROWS(Sales_Data)
Average = AVERAGE(Sales_Data[Price Per Unit])
```



## 🛠️ Tools Used
Power BI Desktop · Power Query · DAX

## 📁 Repository Structure

mobile-sales-dashboard/
├── README.md
├── data/
│ └── Day-30-Mobile-Sales-Data.xlsx
├── dashboard/
│ └── Mobile_Sales_Dashboard.pbix
├── screenshots/
│ └── dashboard_overview.png
└── docs/
└── methodology.md



## 📸 Preview
![Dashboard Overview](screenshots/dashboard_overview.png)

## 🔗 Live Dashboard
[View on Power BI](My-publish-to-web-link-here)

## 🎓 Learning Source
Built while learning Power BI through Satish Dhawale's course (Skill Course).

## 👤 Author
**Souvik Maity** — B.Tech CSE (AI & ML), The Neotia University
