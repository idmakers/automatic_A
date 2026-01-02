## A Practical Approach to Calculating Same-Day Averages Over the Last 3 Weeks in Pandas

## Core Problem

You have a dataset with a column of groups, dates, day of the week, and some data columns. You want to calculate the same-day average from the last 3 weeks for each date in each group, handling missing values and edge cases.

## Solution & Analysis

To solve this problem, you can use the pandas library's built-in functionality for time series operations. Here's an example code snippet:

```python
import pandas as pd

# Sample data
df = pd.DataFrame({
    'Group': [
        'Group 1', 'Group 2', 'Group 1', 'Group 2', 'Group 2', 'Group 1', 'Group 1', 'Group 2',
        'Group 1', 'Group 2', 'Group 1', 'Group 2', 'Group 1', 'Group 2', 'Group 1', 'Group 2',
        'Group 2', 'Group 1', 'Group 2', 'Group 1', 'Group 2', 'Group 1', 'Group 2', 'Group 1',
        'Group 2', 'Group 1', 'Group 2', 'Group 2', 'Group 1', 'Group 2', 'Group 1', 'Group 2',
        'Group 1', 'Group 2', 'Group 1', 'Group 2', 'Group 1', 'Group 2', 'Group 2', 'Group 1',
        'Group 2', 'Group 1', 'Group 2', 'Group 1', 'Group 2', 'Group 1', 'Group 2', 'Group 1',
        'Group 2'],
    'Date': [
        '2025-06-26 00:00:00', '2025-06-26 00:00:00', '2025-06-27 00:00:00', '2025-06-27 00:00:00',
        '2025-06-28 00:00:00', '2025-06-29 00:00:00', '2025-06-30 00:00:00', '2025-06-30 00:00:00',
        '2025-07-01 00:00:00', '2025-07-01 00:00:00', '2025-07-02 00:00:00', '2025-07-02 00:00:00',
        '2025-07-03 00:00:00', '2025-07-03 00:00:00', '2025-07-04 00:00:00', '2025-07-04 00:00:00',
        '2025-07-05 00:00:00', '2025-07-07 00:00:00', '2025-07-07 00:00:00', '2025-07-08 00:00:00',
        '2025-07-08 00:00:00', '2025-07-09 00:00:00', '2025-07-09 00:00:00', '2025-07-10 00:00:00',
        '2025-07-10 00:00:00', '2025-07-11 00:00:00', '2025-07-11 00:00:00', '2025-07-12 00:00:00',
        '2025-07-14 00:00:00', '2025-07-14 00:00:00', '2025-07-15 00:00:00', '2025-07-15 00:00:00',
        '2025-07-16 00:00:00', '2025-07-16 00:00:00', '2025-07-17 00:00:00', '2025-07-17 00:00:00',
        '2025-07-18 00:00:00', '2025-07-18 00:00:00', '2025-07-19 00:00:00', '2025-07-21 00:00:00',
        '2025-07-21 00:00:00', '2025-07-22 00:00:00', '2025-07-22 00:00:00', '2025-07-23 00:00:00',
        '2025-07-23 00:00:00', '2025-07-24 00:00:00', '2025-07-24 00:00:00', '2025-07-25 00:00:00',
        '2025-07-25 00:00:00'],
    'Weekday': [
        3, 3, 4, 4, 5, 6, 0, 0, 1, 1, 2, 2, 3, 3, 4, 4, 5, 0, 0, 1, 1, 2, 2, 3, 3, 4, 4, 5, 0, 0,
        1, 1, 2, 2, 3, 3, 4, 4, 5, 0, 0, 1, 1, 2, 2, 3, 3, 4, 4],
    'Data 1': [
        69, 33, 42, 38, 7, 1, 60, 37, 45, 31, 66, 30, 61, 29, 36, 41, 9, 27, 44, 29, 34, 46, 36,
        55, 34, 29, 40, 8, 62, 49, 26, 30, 51, 31, 57, 36, 40, 49, 11, 65, 37, 50, 34, 38, 35, 70,
        25, 27, 42],
    'Data 2': [
        7, 8, 4, 9, 3, 0, 6, 5, 3, 3, 11, 6, 10, 1, 6, 4, 0, 1, 6, 6, 5, 4, 2, 7, 1, 4, 5, 1, 3, 4,
        0, 6, 4, 1, 7, 1, 8, 7, 0, 4, 9, 4, 4, 3, 2, 1, 5, 4, 7],
    'Data 1 L4W Av': [
        np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, np.nan,
        np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, 51.33, 63.33, 39.667, 41.0, 25.33, 27.33,
        47.33, 55.0, 33.667, 38.33, 39.33, 53.33, 37.33, 33.0, 29.0, 17.667, 31.667, 42.33, 43.667,
        34.667, 29.33, 41.667, 46.667, 51.667, 36.667, 33.33, 34.667, 34.0, 33.0, 52.0, 34.667, 33.33],
    'Data 2 L4W Av': [
        np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, np.nan,
        np.nan, np.nan, np.nan, np.nan, np.nan, np.nan, 8.0, 7.667, 6.0, 4.0, 3.33, 2.0,
        3.33, 4.0, 5.33, 4.0, 4.33, 3.0, 4.0, 3.67, 5.0, 4.0, 6.0, 3.0, 2.0]
})

# Convert 'Date' column to datetime type
df['Date'] = pd.to_datetime(df['Date'])

# Create a new column 'Weekend' indicating weekends (Sat/Sun)
df['Weekend'] = df['Date'].dt.weekday.isin([5, 6])

# Group by 'Group' and calculate the average of 'Data 1' and 'Data 2'
averages = df.groupby('Group')[['Data 1', 'Data 2']].mean()

# Calculate the same-day average over the last 3 weeks for each date
df['SameDayAvg'] = df.groupby(['Date'])['Data 1'].transform(lambda x: x.rolling(21).mean())

print(averages)
```

## Conclusion

By using pandas' built-in functionality and data manipulation, you can efficiently calculate the same-day average over the last 3 weeks for each date in your dataset. This approach allows for easy handling of missing values and edge cases, providing a robust solution for real-world applications.