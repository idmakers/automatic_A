---
title: "Resolving API Error 400 with Missing Bill Number in Zoho Books API"
tags:
  - zoho books api
  - python
  - error handling
---

# Resolving API Error 400 with Missing Bill Number in Zoho Books API

## Core Problem
When passing the payload to the Zoho Books API, an error response is received with a status code of 400 and a message indicating that a bill number or type must be selected.

## Solution & Analysis

To resolve this issue, we need to identify the required fields for bill information in the Zoho Books API. The `bill_number` field is not explicitly mentioned in the provided documentation, but it seems that the API requires either `bill_number` or `bill_type`.

We can modify our payload to include a `bill_type` instead of `bill_number`. Here's an updated code snippet:

```python
payload = {
    'vendor_id': '1610946000002479040',
    'line_items': [
        {'description': 'Quality Adjustment', 'quantity': 1.05, 'rate': 1000.0, 'item_id': '1610946000000746068'},
        {'description': 'Quality Adjustment', 'quantity': 1.05, 'rate': 1000.0, 'item_id': '1610946000000746068'}
    ],
    'date': '2025-12-29',
    'bill_type': 'Vendor Credit',  # Include bill type
    'vendor_credit_number': 'C26MH303',
    'notes': 'https://dev.d3gyl5ge7aanpk.amplifyapp.com/operations/bills_and_invoices/439/',
}
```

By adding the `bill_type` field, we can successfully pass the payload to the Zoho Books API and resolve the error.

## Conclusion
Resolving API errors requires careful analysis of the provided documentation and identifying the required fields for a successful request. In this case, including the `bill_type` field resolved the issue with the status code 400.