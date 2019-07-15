---
title: "Power BI Portfolio: Dashboard reporting of Finance, Sales, Procurement, Bank, IT, HR, Product and Manufacture department"
Date: 2019-01-23
tags: [ETL and Dashboard Reorting with Power BI]
header:
  image: "/images/2019-06-20/PBI.jpg"
excerpt: "Power BI, Data Visualization, ETL, Dashboard Reporting"
mathjax: "true"
---

## Introduction
As a part of my Power BI porfolio, I will implement Power BI in different sample data sets in relevant Australian industries to demonstrate competency, expertise in power BI reports, dashboards. All the data, company names used in this portfolio are altered for security and compliance purpose. 
The skills and technical knowledge applied are as below :  

- Perform ETL using power query.
- Access Data from cloud, csv, excel, SQL.
- Data Preparation (update and append table).
- Transform Data into Data Modeling and explore data by removing duplicates, adding conditionl columns, unpivot-splitting columns, normalize-denormalizing tables.
- Create custom tables, relationships, star or snow flakes schema Tables.
- Prepare data Visualization and reporting using Charts(bar, line, column, scatter, pie, donut, ribbon and waterflow ), table, matrix, KPI, slicers, map etc.
- Perform DAX measure calculation using sum, count, calculate, divide, time intelligence.
- Create Dynamic Measure, Dynamic Dimension, Tooltip, Bookmark in reporting. 
- Building Dashboard to highlight the report using Natural Language Query
- create alert,subscription in dashboard
- Set up enterprise gateway.
- Publish, Share and collaborate dashboard, workspace, apps.

## Financial Data Analysis
The first page contains sales report by country, segment, discount band and product. It also contains a multi row card at the top, to display the Total Amounts, Profit % and Average Order. There are few slicers on the right hand side to see the report by different selection range.

The second page of report contains Tooltip reporting which is linked with the third page. DAX Dynamic Measure and Bookmarks technics are also used along with the map, area chart and line-clustered column chart to display total amounts, profit by coutries and month.

<iframe width="800" height="600" src="https://app.powerbi.com/view?r=eyJrIjoiZjA3N2YwODUtMTFhNi00MjZlLTgzYjQtZmIyOGU4ZTk2ODBiIiwidCI6IjJiYWMzNWU3LThlNWMtNDUyNi04OTgxLTg5MjA2YmM0ZDg5MSIsImMiOjN9" frameborder="0" allowFullScreen="true"></iframe>


## Regional Sales Data Analysis
The top section of all the report page contains flag (Country), date, category and sub category slicers along with total and gross amounts. The internet and reseller sales total and margin details are calculated through DAX measure. The third page of report has DAX Dynamic measure for both internet and reseller to select the donut charts individually.

The first and second page displayed the sales, gross profit amounts by country (map) and year (bar charts). Again the matrix tables contain the detailed amounts by product category-sub category level, country-state province level.

<iframe width="800" height="600" src="https://app.powerbi.com/view?r=eyJrIjoiYzE4Mzc2ZDYtZTc0Yi00OGNjLTlkOTEtNWRmZWNjYjg5ZWY2IiwidCI6IjJiYWMzNWU3LThlNWMtNDUyNi04OTgxLTg5MjA2YmM0ZDg5MSIsImMiOjN9" frameborder="0" allowFullScreen="true"></iframe>

## Procurement / Spend Data Analysis
The report is created with date slicers, multi row card to display total amounts and quantities. The Total Purchases and Total PO's are measured against buying package, supplier, stock item, months through donut, bar chart and matrix tables.

<iframe width="800" height="600" src="https://app.powerbi.com/view?r=eyJrIjoiMzg5NTA0MzUtNWVkNS00ZGY2LTg3MDYtMmNjZWYyN2ZmZjBjIiwidCI6IjJiYWMzNWU3LThlNWMtNDUyNi04OTgxLTg5MjA2YmM0ZDg5MSIsImMiOjN9" frameborder="0" allowFullScreen="true"></iframe>

## Bank Data Analysis
The first two pages of report contains customer data (ID, job classification, gender, education, marital status, loan default, age group, balance group) represented in slicers, map, bar, column, pie charts.

The third page measure the customer data against the total balance. A few extra cards are used to show variable amounts and quantities. Visual images are used to clearly differentiate between the cards.

The fourth page is designed with advanced DAX measures such as Dynamic Measures, Dynamic Dimensions. The similar visualization templates are used as previous pages.

<iframe width="680" height="510" src="https://app.powerbi.com/view?r=eyJrIjoiOTdjMjE0YWItMTliYS00NmVmLWFhOWMtYzFiYTg4MTU5ZWEwIiwidCI6IjJiYWMzNWU3LThlNWMtNDUyNi04OTgxLTg5MjA2YmM0ZDg5MSIsImMiOjN9" frameborder="0" allowFullScreen="true"></iframe>

## IT Ticket Analysis
Since the IT tickets need to be measured against multiple variables, I have created slicers for severity, priority, ticket type, satisfaction, departments, requestor seniority and field against cloumns from the dataset. The total amount, quantity and avg values are created at the top as cards. The donut and bar charts are created with DAX Dynamic measure which can be selected against User, Res. Days and Res. Avg Days (visualized as slicer at the top section of the report page).

<iframe width="800" height="600" src="https://app.powerbi.com/view?r=eyJrIjoiYjFjNmI0YjQtMmY3Mi00NDMxLWI2OWUtYWNkNjg2YzY0YTM0IiwidCI6IjJiYWMzNWU3LThlNWMtNDUyNi04OTgxLTg5MjA2YmM0ZDg5MSIsImMiOjN9" frameborder="0" allowFullScreen="true"></iframe>

## HR Emplyee Data Analysis
The first report draws an visual analysis between salary and employees. The second report detailed the avarage values and amounts measured against different variables. The third one is business unit/region report. Apart from different charts, marix tables, slicers, DAX Calculations are used to generate Dynamic Measures and Dynamic Dimensions. 

<iframe width="800" height="600" src="https://app.powerbi.com/view?r=eyJrIjoiNmNhMzYzOTktMWNmMS00NWUyLTlmM2YtOTFmYTM2OGM0M2E1IiwidCI6IjJiYWMzNWU3LThlNWMtNDUyNi04OTgxLTg5MjA2YmM0ZDg5MSIsImMiOjN9" frameborder="0" allowFullScreen="true"></iframe>

## Product Data Analysis
The report starts with the date and flag slicers at the top. Then the total sales are measured against different variables related to products. Visualization templates such as area, donut, pie charts, tables are used in this report.

<iframe width="800" height="600" src="https://app.powerbi.com/view?r=eyJrIjoiMTgyMzVlNTctNGMxNS00YzRkLTg1ZmEtYzk1MDdkYWI5ODIxIiwidCI6IjJiYWMzNWU3LThlNWMtNDUyNi04OTgxLTg5MjA2YmM0ZDg5MSIsImMiOjN9" frameborder="0" allowFullScreen="true"></iframe>

## Manufacturer Market and KPI Analysis
The first reporting page is designed to display market share of leading company,% of growth by manufacturer along with a slicer to select date.
Revenue is measured against country using map, year-manufacturer using line chart and against category-segment-year using play axis (imported from marketplace).

The second page has the measures displayed with bar chart, matrix table, KPI gauge. It also has a bookmark created at top right hand corner. By clicking the down arrow next to "manufacturers" will open the slicer bar to select company logo's.

<iframe width="800" height="600" src="https://app.powerbi.com/view?r=eyJrIjoiOTllMmEwNjctYWFkMi00NGQyLTgwMTItM2YxYjNhM2IzZTkwIiwidCI6IjJiYWMzNWU3LThlNWMtNDUyNi04OTgxLTg5MjA2YmM0ZDg5MSIsImMiOjN9" frameborder="0" allowFullScreen="true"></iframe>

## Domino's Pizza Sales Report
The sales amount is displayed against states, salse manager, pizza types and days using shape map, bar, donut charts. Total sale and states are also presented at the top section as card and slicers respectively.

<iframe width="800" height="600" src="https://app.powerbi.com/view?r=eyJrIjoiYjUyZjE4YmYtOWI1MC00NTVjLWFkNmYtNjdjMDFlZWM0MGYxIiwidCI6IjJiYWMzNWU3LThlNWMtNDUyNi04OTgxLTg5MjA2YmM0ZDg5MSIsImMiOjN9" frameborder="0" allowFullScreen="true"></iframe>

## US 2017 Disaster Analysis Report
The second page contains the summary, losses and deaths report against the states. The total and avg amounts, quantities are displayed as card on left hand side for both the report page. The disaster types (images) are displayed at the top using chiclet slicer importing from marketplace. The first page of report shows the disasters against the states using shape map.

<iframe width="800" height="600" src="https://app.powerbi.com/view?r=eyJrIjoiMDNhYTkyODMtN2VmMy00YTA0LWIwM2QtODZjZWEwMjMzNDU2IiwidCI6IjJiYWMzNWU3LThlNWMtNDUyNi04OTgxLTg5MjA2YmM0ZDg5MSIsImMiOjN9" frameborder="0" allowFullScreen="true"></iframe>