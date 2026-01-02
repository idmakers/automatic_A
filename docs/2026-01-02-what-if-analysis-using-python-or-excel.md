---
title: "What-if Analysis Using Python and Excel for Banking Projects"
tags:
  - python
  - excel
  - what-if analysis
---

# What-if Analysis Using Python and Excel for Banking Projects

## Core Problem
Understanding the impact of increasing interest rates on revenue and delinquency in banking projects.

## Solution & Analysis

### Understanding the Revenue Calculation

Revenue is calculated as the difference between the sum of loan EMIs multiplied by loan tenure and the loan amount/principal for customers with a credit score less than 650.

Mathematical equation:

Revenue = Loan_Amount - (EMI X Loan_Tenure)

Where EMI, Loan_Tenure, and Loan_Amount are features available in the dataset.

### What-if Analysis Using Python

To perform what-if analysis using Python, we can use the following steps:

```python
import pandas as pd

# Load the dataset
df = pd.read_csv('banking_data.csv')

# Filter customers with credit score less than 650
df_filtered = df[df['Credit_Score'] < 650]

# Calculate original revenue
df_filtered['Original_Revenue'] = df_filtered['Loan_Amount'] - (df_filtered['EMI'] * df_filtered['Loan_Tenure'])

# Define a function to calculate new EMI with increased interest rate
def calculate_new_emi(interest_rate, loan_amount, loan_tenure):
    new_emi = loan_amount * (interest_rate / 12) * (1 + interest_rate / 100)**loan_tenure / ((1 + interest_rate / 100)**loan_tenure - 1)
    return new_emi

# Calculate new revenue with increased interest rate
df_filtered['New_Revenue'] = df_filtered.apply(lambda row: row['Loan_Amount'] - calculate_new_emi(row['Interest_Rate'], row['Loan_Amount'], row['Loan_Tenure']) * row['Loan_Tenure'], axis=1)

# Calculate the impact of increasing interest rate on revenue
impact_on_revenue = df_filtered['New_Revenue'].max() - df_filtered['Original_Revenue'].min()

print("Impact on Revenue:", impact_on_revenue)
```

### What-if Analysis Using Excel

To perform what-if analysis using Excel, you can use the following steps:

1. Create a new worksheet and copy the data for customers with credit score less than 650.
2. Calculate the original revenue by subtracting the EMI multiplied by loan tenure from the loan amount/principal.
3. Create a table to store the results of what-if analysis:
| Interest Rate | New EMI | New Revenue |
| --- | --- | --- |
4. Use the `What If` Analysis tab to calculate the new EMI with increased interest rate for each customer.
5. Calculate the new revenue by subtracting the new EMI multiplied by loan tenure from the loan amount/principal.
6. Calculate the impact of increasing interest rate on revenue by comparing the new revenue with the original revenue.

Note: This is a simplified example and you may need to adjust the formulae according to your specific dataset and requirements.

### Impact of Delinquency

To study the impact of delinquency, you can create another table to store the results:
| Delinquency Flag | Revenue | New Revenue |
| --- | --- | --- |
7. Calculate the revenue for customers with delinquency by subtracting the EMI multiplied by loan tenure from the loan amount/principal.
8. Calculate the new revenue for customers with delinquency by subtracting the new EMI multiplied by loan tenure from the loan amount/principal.
9. Calculate the impact of increasing interest rate on revenue and delinquency by comparing the new revenue with the original revenue.

Conclusion
----------

In this article, we discussed how to perform what-if analysis using Python and Excel for banking projects. We also covered how to calculate the impact of increasing interest rates on revenue and delinquency. By following these steps, you can gain a better understanding of the potential effects of changes in your business and make informed decisions to drive growth and profitability.