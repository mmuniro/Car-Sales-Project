## Project Overview

This project analyzes car sales data from Kaggle to identify sales trends and create an interactive dashboard. We'll use Python for data analysis and visualizations, as well as Power BI for creating a dashboard.

### Objectives:
- Import and clean the car sales dataset
- Perform exploratory data analysis to identify key trends
- Create visualizations to highlight sales patterns
- Build an interactive dashboard to present findings

---

## Step 1: Setup and Data Import

```python
import numpy as np 
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Read CSV file into pandas dataframe
df = pd.read_csv("Car Sales.csv")

# Display basic information
# Display first 5 rows
print(df.head())

# Check basic information about the dataset
print("\nDataset Information:")
print(df.info())

# Get summary statistics
print("\nSummary Statistics:")
print(df.describe())
```

---

## Step 2: Data Cleaning and Preparation

```python
# Check for missing values
print("\nMissing Values:")
print(df.isnull().sum())

# Handle duplicates
duplicate_count = df.duplicated().sum()
print(f"\nNumber of duplicate rows: {duplicate_count}")
if duplicate_count > 0:
    df = df.drop_duplicates()
    print("Duplicates removed.")

# Rename Price ($) column to Price
df.rename(columns={'Price ($)': 'Price'}, inplace=True)

# Clean up column names by removing spaces
df.columns = df.columns.str.strip()

# Change date column datatype to datetime
df['Date'] = pd.to_datetime(df['Date'])

# Extract year, month for trend analysis
df['Year'] = df['Date'].dt.year
df['Month'] = df['Date'].dt.month

# Remove outliers from columns using IQR method
def remove_outliers(df, column):
    # Calculate Q1, Q3 and IQR
    Q1 = df[column].quantile(0.25)
    Q3 = df[column].quantile(0.75)
    IQR = Q3 - Q1
    
    # Define bounds for outliers
    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR
    
    # Print statistics about outliers
    outliers = df[(df[column] < lower_bound) | (df[column] > upper_bound)]
    outlier_count = outliers.shape[0]
    print(f"\nOutlier statistics for {column}:")
    print(f"Q1: {Q1:.2f}, Q3: {Q3:.2f}, IQR: {IQR:.2f}")
    print(f"Lower bound: {lower_bound:.2f}, Upper bound: {upper_bound:.2f}")
    print(f"Outliers found: {outlier_count} ({outlier_count/df.shape[0]*100:.2f}% of data)")
    
    # Create a new dataframe without outliers
    df_clean = df[(df[column] >= lower_bound) & (df[column] <= upper_bound)]
    print(f"Rows after outlier removal: {df_clean.shape[0]} (removed {df.shape[0] - df_clean.shape[0]} rows)")
    
    return df_clean

# Define a function to categorise income
def categorise_income(income):
    if pd.isna(income):
        return 'Unknown'
    
    if income < 50000:
        return 'Low Income (<50K)'
    elif income < 200000:
        return 'Middle Income (<200K)'
    elif income < 500000:
        return 'Upper Middle Income (<500K)'
    elif income < 1000000:
        return 'High Income (<1M)'
    else:
        return 'Very High Income (>1M)'
        
# Apply the function to create a new column
df['Income Class'] = df['Annual Income'].apply(categorise_income)
```

---

## Step 3: Exploratory Data Analysis

### Analyzing Sales Trends

```python
# Monthly sales trend
monthly_sales = df.groupby(['Year', 'Month']).size().reset_index(name='sales_count')
print("\nMonthly Sales Trend:")
print(monthly_sales.head())

# Company monthly sales trend
company_monthly_sales = df.groupby(['Company', 'Year', 'Month']).size().reset_index(name='sales_count')
print("\nCompany Monthly Sales Trend:")
print(company_monthly_sales.head())

# Average price by company
average_price_by_company = df.groupby('Company')['Price'].mean().sort_values(ascending=False).reset_index(name='Average Price')
print("\nAverage Price by Company:")
print(average_price_by_company.head())

# Average price by model
average_price_by_model = df.groupby('Model')['Price'].mean().sort_values(ascending=False).reset_index(name='Average Price')
print("\nAverage Price by Model:")
print(average_price_by_model.head())

# Total sales for each company
total_sales_by_company = df['Company'].value_counts().sort_values(ascending=False).reset_index(name='Total Sales')
print("\nTotal Sales by Company:")
print(total_sales_by_company.head())

# Total revenue by company
total_revenue_by_company = df.groupby('Company')['Price'].sum().sort_values(ascending=False).reset_index(name='Total Revenue')
print("\nTotal Revenue by Company:")
print(total_revenue_by_company.head())
```

---

## Step 4: Visualization for Better Understanding (one example)

<img width="1000" height="600" alt="monthly_sales_trend" src="https://github.com/user-attachments/assets/1b74e141-6750-450e-bf3a-e0420f9f419d" />

---

## Step 5: Power BI Dashboard (one page)

<img width="867" height="489" alt="Dashboard1" src="https://github.com/user-attachments/assets/986340a9-f282-424b-920e-853788d89639" />

---

## Key Findings

### Sales Trends
- Strong sales trends identified in different regions
- Clear seasonal patterns with sales peaking in September and November
- Chevrolet, Ford, and Dodge are the top-selling brands across all areas

### Market Insights
- American manufacturers maintain strong competitive positions
- Market is dominated by male customers
- No correlation found between higher annual income and purchasing more expensive cars

---

## Strategic Recommendations

### 1. Leverage Seasonal Patterns
- Implement targeted marketing campaigns during peak periods (September-November)

### 2. Regional Customization
- Develop region-specific strategies based on identified preferences

### 3. Competitive Monitoring
- Track performance against identified market leaders

---

## Business Impact

This analysis provides actionable insights for:

- **Marketing Strategy**: Seasonal and demographic targeting
- **Inventory Management**: Regional demand forecasting
- **Pricing Strategy**: Competitive positioning and premium opportunities
- **Resource Allocation**: Focus on high-performing regions and periods
- **Competitive Analysis**: Benchmark against market leaders
