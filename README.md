<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Python Projects Report</title>

  <style>
    body{
      margin:0;
      font-family: Arial, sans-serif;
      background: #0f172a;
      color: #e5e7eb;
      padding: 30px;
    }
    .box{
      max-width: 1100px;
      margin: auto;
      background: rgba(255,255,255,0.04);
      border: 1px solid rgba(255,255,255,0.08);
      border-radius: 18px;
      padding: 22px;
    }
    h1{
      font-size: 26px;
      margin-bottom: 6px;
    }
    p{
      color: #94a3b8;
      line-height: 1.6;
      font-size: 15px;
    }
    h2{
      margin-top: 22px;
      font-size: 18px;
      color: #38bdf8;
    }
    pre{
      background: #0b1220;
      border: 1px solid rgba(255,255,255,0.10);
      border-radius: 14px;
      padding: 16px;
      overflow-x: auto;
      font-size: 13px;
      line-height: 1.6;
      color: #e2e8f0;
      white-space: pre;
      font-family: Consolas, monospace;
    }
    button{
      margin-top: 10px;
      padding: 10px 14px;
      border: none;
      border-radius: 12px;
      font-weight: bold;
      cursor: pointer;
      background: #38bdf8;
      color: #0b1220;
    }
    button:hover{
      opacity: 0.9;
    }
  </style>
</head>

<body>
  <div class="box">
    <h1>Python Projects Report</h1>
    <p>
      This report explains three practical projects using Pandas and NumPy:
      Sales Data Analysis, Data Cleaning & EDA, and Customer Cohort Retention Analysis.
      Below is the complete Python code in a clean format for easy copy and run.
    </p>

    <h2>Full Python Code</h2>
    <button onclick="copyCode()">Copy Python Code</button>

    <pre id="codeBlock">import pandas as pd
import numpy as np
from datetime import datetime, timedelta

print("^^^ Project 1: Sales Data Analysis ^^^")

np.random.seed(42)
dates = pd.date_range(start='2025-01-01', end='2025-12-31', freq='D')
data_size = 500

sales_data = {
    'Order_ID': range(1001, 1001 + data_size),
    'Date': np.random.choice(dates, data_size),
    'Product': np.random.choice(['Laptop', 'Monitor', 'Keyboard', 'Mouse', 'Desk Lamp'], data_size),
    'Category': np.random.choice(['Electronics', 'Furniture', 'Accessories'], data_size),
    'Quantity': np.random.randint(1, 6, data_size),
    'Unit_Price': np.random.uniform(20.0, 1200.0, data_size),
    'Region': np.random.choice(['North', 'South', 'East', 'West'], data_size)
}

df_sales = pd.DataFrame(sales_data)

df_sales['Date'] = pd.to_datetime(df_sales['Date'])
df_sales['Total_Revenue'] = df_sales['Quantity'] * df_sales['Unit_Price']
df_sales['Month'] = df_sales['Date'].dt.to_period('M')

monthly_report = df_sales.groupby('Month')['Total_Revenue'].sum().reset_index()

top_products = df_sales.groupby('Product')['Total_Revenue'].sum().sort_values(ascending=False).head(5)

region_perf = df_sales.pivot_table(index='Region', values='Total_Revenue', aggfunc='sum')

print("\n$$$ Sales Analysis Insights $$$")
print(f"Total Yearly Revenue: ${df_sales['Total_Revenue'].sum():,.2f}")
print(f"Top Product: {top_products.index[0]} (${top_products.iloc[0]:,.2f})")
print("\nRegion-wise Performance:")
print(region_perf)
print("\nMonthly Revenue Report:")
print(monthly_report)
print("\n--- Project 2: Data Cleaning & EDA ---")

messy_data = {
    'User_ID': [1, 2, 2, 3, 4, 5, 6],
    'City': ['new york', 'NEW YORK', 'Chicago', 'chicago ', 'San Francisco', np.nan, 'Austin'],
    'Age': [25, 30, 30, 200, 22, np.nan, 28], # 200 is an outlier
    'Spending': [100, 150, 150, 50, 300, 400, 10]
}
df_messy = pd.DataFrame(messy_data)

print(f"Initial row count: {len(df_messy)}")
df_clean = df_messy.drop_duplicates()
print(f"Count after dropping duplicates: {len(df_clean)}")

df_clean['City'] = df_clean['City'].str.strip().str.title().fillna('Unknown')

df_clean['Age'] = pd.to_numeric(df_clean['Age'], errors='coerce')
df_clean['Age'] = df_clean['Age'].fillna(df_clean['Age'].median())

Q1 = df_clean['Age'].quantile(0.25)
Q3 = df_clean['Age'].quantile(0.75)
IQR = Q3 - Q1
lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

df_final_clean = df_clean[(df_clean['Age'] >= lower_bound) & (df_clean['Age'] <= upper_bound)]

print("\n&&& Data Cleaning Insights &&&")
print("Before Clean Mean Age:", df_messy['Age'].mean())
print("After Clean Mean Age (Outliers Removed):", df_final_clean['Age'].mean())
print("\nCleaned Head:")
print(df_final_clean.head())

print("\n*** Project 3: Customer Behavior & Cohort Analysis ***")

n_orders = 1000
cust_ids = np.random.randint(1, 101, n_orders) # 100 unique customers
order_dates = [datetime(2025, 1, 1) + timedelta(days=np.random.randint(0, 180)) for _ in range(n_orders)]

df_cohort = pd.DataFrame({'Customer_ID': cust_ids, 'Order_Date': order_dates})

df_cohort['Order_Month'] = df_cohort['Order_Date'].dt.to_period('M')
df_cohort['Cohort_Month'] = df_cohort.groupby('Customer_ID')['Order_Date'].transform('min').dt.to_period('M')

def get_date_int(df, column):
    year = df[column].dt.year
    month = df[column].dt.month
    return year, month

order_year, order_month = get_date_int(df_cohort, 'Order_Month')
cohort_year, cohort_month = get_date_int(df_cohort, 'Cohort_Month')

years_diff = order_year - cohort_year
months_diff = order_month - cohort_month

df_cohort['Cohort_Index'] = years_diff * 12 + months_diff + 1

cohort_counts = df_cohort.groupby(['Cohort_Month', 'Cohort_Index'])['Customer_ID'].nunique().reset_index()
retention_matrix = cohort_counts.pivot(index='Cohort_Month', columns='Cohort_Index', values='Customer_ID')

cohort_sizes = retention_matrix.iloc[:, 0]
retention_percentage = retention_matrix.divide(cohort_sizes, axis=0)

print("\n^^^ Cohort Analysis Insights ^^^")
print("Retention Percentage Matrix (First 5 Months):")
print(retention_percentage.round(3).iloc[:5, :5])

print("\nBusiness Recommendation:")
print("Look for drops in Month 2 retention. If retention is below 20% by Month 3,")
print("consider implementing a targeted re-engagement discount for new users.")</pre>

  </div>

  <script>
    function copyCode(){
      const code = document.getElementById("codeBlock").innerText;
      navigator.clipboard.writeText(code);
      alert("✅ Python code copied successfully!");
    }
  </script>
</body>
</html>
