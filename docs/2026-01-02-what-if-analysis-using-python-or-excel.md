---
title: What-if Analysis Using Python and Excel for Banking Projects

tags:
  - Python
  - Excel
  - What-if Analysis
  - Banking Project
  - Revenue Calculation

# Core Problem

Understanding what-if analysis in banking projects can be challenging, especially when it comes to calculating revenue and its impact on customers with credit scores below 650. The problem requires analyzing the effect of increasing interest rates by 2% on revenue, while also considering the impact of delinquency on customers who have not been paying their EMIs.

## Solution & Analysis

To solve this problem, we can use both Python and Excel for the analysis.

### Python Approach

We will create a Python function to calculate the initial revenue and new revenue based on the given formulas. We'll also calculate the impact of increasing interest rates by 2% on revenue and delinquency.

```python
import pandas as pd

def calculate_revenue(data):
    # Calculate initial revenue
    data['Revenue'] = data['Loan_Amount'] - (data['EMI'] * data['Loan_Tenure'])
    
    # Calculate new EMI with 2% interest rate increase
    data['Interest_Rate'] = data['Interest_Rate'] + 0.02
    data['new_EMI'] = (data['Loan_Amount'] / (1 + data['Interest_Rate'] / 12)) * 12
    
    # Calculate new revenue
    data['New_Revenue'] = data['Loan_Amount'] - data['new_EMI'] * data['Loan_Tenure']
    
    # Calculate impact of interest rate increase on revenue
    data['Revenue_Impact'] = data['New_Revenue'] - data['Revenue']
    
    return data

# Sample dataset
data = pd.DataFrame({
    'Credit_Score': [600, 550, 700],
    'Loan_Amount': [100000, 150000, 200000],
    'EMI': [5000, 7500, 10000],
    'Interest_Rate': [4.5, 5.5, 6.5]
})

# Calculate revenue
data = calculate_revenue(data)

print("Initial Revenue:")
print(data['Revenue'].sum())

print("\nNew Revenue with 2% Interest Rate Increase:")
print(data['New_Revenue'].sum())

print("\nImpact of Interest Rate Increase on Revenue:")
print(data['Revenue_Impact'].sum())
```

### Excel Approach

To perform what-if analysis in Excel, we can use the What-if Analysis tab. Here's how to do it:

1. Select the dataset range and go to the "Data" menu.
2. Click on "What-if Analysis" and select "Input Data Range".
3. Enter the interest rate increase (2% in this case) and click "OK".
4. The What-if Analysis tab will display the new revenue and impact of interest rate increase on revenue.

To calculate the impact of delinquency, we can create a new column to indicate whether a customer is delinquent or not based on their credit score and EMIs paid. We can then use conditional formatting to highlight the customers who are delinquent.

```excel
=IF(Credit_Score<650, "Delinquent", "Not Delinquent")
```

Assuming this column is in the dataset range and you want to apply it to the Revenue calculation:

1. Select the revenue formula range.
2. Go to the "Formulas" tab.
3. Click on "Conditional Formatting".
4. Select "New Rule" and choose "Use a formula to determine which cells to format".
5. Enter the conditional formatting formula: `=Delinquency` (assuming you've named the column "Delinquency").
6. Choose the formatting options for delinquent customers.

By using both Python and Excel, we can perform what-if analysis and understand the impact of increasing interest rates by 2% on revenue and delinquency for banking projects.

## Conclusion

In this article, we discussed what-if analysis in banking projects using Python and Excel. We created a Python function to calculate initial revenue and new revenue based on given formulas, as well as the impact of increasing interest rates by 2% on revenue and delinquency. In Excel, we used the What-if Analysis tab to perform similar calculations. By applying these techniques, you can make informed decisions for your banking projects and analyze the impact of different scenarios.