<h1>Churn Analysis Dashboard</h1>
<h2>SQL Server + Power BI | End-to-End Data Analytics Project</h2>

This project demonstrates a complete Churn Analysis pipeline, starting from raw CSV data and ending with an interactive Power BI dashboard.
It showcases skills in ETL, SQL data cleaning, data modeling, and business intelligence reporting.

<h2>Project Overview</h2>

Customer churn is a major concern for subscription-based businesses.
This project helps identify key churn drivers, customer patterns, and business-critical insights using:

SQL Server for data import, cleaning, transformation, and modeling

Power BI for interactive dashboards and visual storytelling

The dashboard helps answer:

What is the total churn rate?

Which states have the highest churn?

How do contract types, internet services, or payment methods affect churn?

Which age groups or gender show higher churn behavior?

What are the top churn categories and reasons?

<h2>Tech Stack</h2>

<table border="1" cellpadding="8" cellspacing="0">
  <tr>
    <th>Component</th>
    <th>Technology</th>
  </tr>
  <tr>
    <td>Database</td>
    <td>Microsoft SQL Server</td>
  </tr>
  <tr>
    <td>Query Language</td>
    <td>T-SQL</td>
  </tr>
  <tr>
    <td>Visualization</td>
    <td>Power BI</td>
  </tr>
  <tr>
    <td>Data Source</td>
    <td>CSV file</td>
  </tr>
</table>


<h1>Project Workflow</h1>

🗂️ 1️⃣ **Data Import (CSV → SQL Server)**

The CSV file was imported into a staging table using SQL Server Import Wizard.

<ul>
  <li>Imported into: stg_Churn</li>
  <li>All columns (except Customer_ID) allowed NULLs to avoid load errors</li>
  <li>BIT columns converted to VARCHAR(50) because of import compatibility issues</li>
</ul>

🔍 2️⃣ **Data Exploration (SQL)**  

**Check distributions**

```bash
SELECT Gender, COUNT(Gender) AS TotalCount,
COUNT(Gender) * 100.0 / (SELECT COUNT(*) FROM stg_Churn) AS Percentage
FROM stg_Churn
GROUP BY Gender;
```

**Contract Type Distribution**  
```bash
SELECT Contract, COUNT(*) AS TotalCount,
COUNT(*) * 100.0 / (SELECT COUNT(*) FROM stg_Churn) AS Percentage
FROM stg_Churn
GROUP BY Contract;
```

**Customer Status Revenue**
```bash
SELECT Customer_Status, COUNT(*) AS TotalCount, SUM(Total_Revenue) AS TotalRev,
SUM(Total_Revenue) * 100.0 / (SELECT SUM(Total_Revenue) FROM stg_Churn) AS RevPercentage
FROM stg_Churn
GROUP BY Customer_Status;
```

🧹 3️⃣ **Data Cleaning (Handling Nulls)**

**Checked null counts for all columns:**

```bash
SELECT 
    SUM(CASE WHEN Customer_ID IS NULL THEN 1 ELSE 0 END) AS Customer_ID_Null_Count,
    SUM(CASE WHEN Gender IS NULL THEN 1 ELSE 0 END) AS Gender_Null_Count,
    ...
FROM stg_Churn;
```

🏗 4️⃣ **Data Transformation (Creating Production Table)**

**Null values were replaced using ISNULL() and loaded into prod_Churn.**

**Example:**

```bash
ISNULL(Internet_Type, 'None') AS Internet_Type,  
ISNULL(Device_Protection_Plan, 'No') AS Device_Protection_Plan,  
ISNULL(Churn_Category, 'Others') AS Churn_Category,
```

**Full Insert:**

```bash
SELECT 
    Customer_ID, Gender, Age, Married, State, Number_of_Referrals,
    Tenure_in_Months, ISNULL(Value_Deal, 'None') AS Value_Deal,
    Phone_Service, ISNULL(Multiple_Lines, 'No') AS Multiple_Lines,
    Internet_Service, ISNULL(Internet_Type, 'None') AS Internet_Type,
    ...
INTO [db_Churn].[dbo].[prod_Churn]
FROM [db_Churn].[dbo].[stg_Churn];
```

🔧 5️⃣ **Creating Views for Power BI**  
**View for Churn + Stayed Customers:**  
```bash
CREATE VIEW vw_ChurnData AS
SELECT * FROM prod_Churn
WHERE Customer_Status IN ('Churned', 'Stayed');
```

**View for New Joiners:**
```bash
CREATE VIEW vw_JoinData AS
SELECT * FROM prod_Churn
WHERE Customer_Status = 'Joined';
```

**Power BI reads data directly from these views.**

📈 6️⃣ **Power BI Dashboard**

**Main KPIs**

    <li>Total Customers — 6418</li>
    <li>New Joiners — 411</li>
    <li>Total Churn — 1732</li>
    <li>Churn Rate — 27%</li>    
  
**Key Visual Sections**

<ul>
  <li>Churn by Gender</li>
  <li>Churn by Age Group</li>
  <li>Churn by Contract Type</li>
  <li>Churn by Payment Method</li>
  <li>Churn by Internet Type</li>
  <li>Churn by Tenure</li>
  <li>Churn by State (Top 5)</li>
  <li>Churn Category Breakdown</li>
  <li>Services Opted vs Churn Rate</li>
</ul>


<h2>Key Insights from Dashboard</h2>

✔ **Month-to-Month contract holders churn the most (46%)**  
✔ **Fiber Optic users show highest churn rates**  
✔ **Top churn reasons include Competitor, Attitude, Dissatisfaction**  
✔ **States like Jammu & Assam show highest churn %**  
✔ **Older customers (>50 yrs) churn more (31%)**  
✔ **Paperless billing + Mailed checks show higher churn**

<h2>Conclusion</h2>

This project demonstrates end-to-end data analytics using SQL and Power BI.
It helps businesses understand customer behavior, identify churn drivers, and improve retention strategies.
