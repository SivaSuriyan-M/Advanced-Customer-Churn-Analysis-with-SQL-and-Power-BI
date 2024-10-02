# Advanced-Customer-Churn-Analysis-with-SQL-and-Power-BI

# Project Report: Churn Analysis

## Project Objective

The goal of this project is to create an end-to-end ETL process and a Power BI dashboard for analyzing customer churn data. The analysis focuses on key metrics, including demographics, geographic distribution, payment and account information, and service usage. Additionally, it seeks to identify churner profiles, suggest marketing interventions, and predict future churners.

## Target Metrics

- **Total Customers**: Count of all customers.
- **Total Churn**: Total number of churned customers.
- **Churn Rate**: Percentage of customers who have churned.
- **New Joiners**: Count of newly joined customers.

---

## Step 1: ETL Process in SQL Server

The ETL (Extract, Transform, Load) process begins with setting up a database using Microsoft SQL Server. This involves importing the raw customer data into SQL Server for further exploration, cleaning, and transformation.

### 1.1 Creating a Database
```sql
CREATE DATABASE db_Churn;
```
A new database, `db_Churn`, is created to hold customer data.

### 1.2 Importing Data
Use the Import Wizard to import the CSV file into a staging table (`stg_Churn`).

- **Primary key**: `Customer_ID`
- Modify columns where necessary, especially changing `Bit` to `Varchar(50)`.

### 1.3 Data Exploration
Check distinct values for different demographic, geographic, and service attributes.

Example SQL query:
```sql
SELECT Gender, COUNT(Gender) as TotalCount, 
       COUNT(Gender) * 1.0 / (SELECT COUNT(*) FROM stg_Churn) as Percentage
FROM stg_Churn 
GROUP BY Gender;
```

### 1.4 Null Values Check
Perform a null values check on key columns to identify any missing data.

Example SQL query:
```sql
SELECT SUM(CASE WHEN Gender IS NULL THEN 1 ELSE 0 END) AS Gender_Null_Count 
FROM stg_Churn;
```

### 1.5 Removing Nulls and Moving Data to Production
Replace null values with defaults and insert clean data into the production table `prod_Churn`.

Example SQL query:
```sql
INSERT INTO prod_Churn 
SELECT Customer_ID, ISNULL(Gender, 'Unknown'), ... 
FROM stg_Churn;
```

### 1.6 Creating Views for Power BI
Create views `vw_ChurnData` and `vw_JoinData` to filter data based on customer status.

Example SQL query:
```sql
CREATE VIEW vw_ChurnData AS 
SELECT * FROM prod_Churn WHERE Customer_Status IN ('Churned', 'Stayed');
```

---

## Step 2: Power BI Transformation

Once the data is loaded into SQL Server, we perform additional transformations in Power BI to prepare the data for visualization.

### 2.1 Adding Columns
- **Churn Status**: A column where 'Churned' customers are marked as `1` and others as `0`.
- **Monthly Charge Range**: Group customers based on their monthly charges.

### 2.2 Age and Tenure Groupings
- **Age Group**: Group customers into categories like '20-35', '36-50', etc.
- **Tenure Group**: Group customers based on their tenure in months.

### 2.3 Unpivot Services Data
Unpivot service columns to create a `Services` column and a `Status` column representing whether the customer uses that service or not.

---

## Step 3: Power BI Measures

The following measures are created to compute important metrics in Power BI:

- **Total Customers**: `COUNT(prod_Churn[Customer_ID])`
- **New Joiners**: `CALCULATE(COUNT(prod_Churn[Customer_ID]), prod_Churn[Customer_Status] = 'Joined')`
- **Total Churn**: `SUM(prod_Churn[Churn Status])`
- **Churn Rate**: `[Total Churn] / [Total Customers]`

---

## Step 4: Power BI Visualization

In this step, a Power BI dashboard is built to visualize the key metrics.

### 4.1 Summary Page
- **Top Cards**: Displays total customers, new joiners, total churn, and churn rate.

### 4.2 Demographic Analysis
- **Gender**: Breakdown of churn rate by gender.
- **Age Group**: Total customers and churn rate by age group.

### 4.3 Account Information
- **Payment Method**: Churn rate by payment method.
- **Contract Type**: Churn rate by contract type.
- **Tenure Group**: Total customers and churn rate by tenure group.

### 4.4 Geographic Analysis
- **Top 5 States**: Churn rate by the top five states with the highest churn.

### 4.5 Churn Distribution
- **Churn Category**: Displays total churn by churn category with a tooltip showing churn reasons.

### 4.6 Service Usage
- **Internet Type**: Churn rate by type of internet service.
- **Service Status**: Percentage churn for various services used by customers.

---

## Conclusion

This project integrates a comprehensive ETL process with a detailed analysis in Power BI, providing insights into customer churn patterns. The dashboard helps identify high-risk customers, assess demographic and geographic factors, and analyze the impact of services and account information on churn rates. This analysis can drive targeted marketing strategies and predict future churners effectively.

---

## Visual Representation
![Churn_Dashboard](https://github.com/user-attachments/assets/65740321-a70c-45f1-b2b3-d954526409f2)

