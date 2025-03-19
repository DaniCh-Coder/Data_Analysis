# Data Cleaning

Data cleaning or  is the process of identifying and correcting errors in a dataset to improve its quality and accuracy.
- It is a fundamental step in data analysis, as incorrect, duplicate, or incomplete data can lead to incorrect decisions.

## Need and Importance of Data Cleaning

Dirty data can cause issues such as:

- Errors in analysis and reports.
- Decision-making based on incorrect information.
- Problems in machine learning models.
- Inefficiencies in business processes.

## Steps of Data Cleaning

1️. Identifying Errors

Before cleaning data, it is important to detect the most common issues:

- Null or missing values: Empty cells or incomplete information. 
- Duplicates: Repeated records that affect analysis. 
- Inconsistent data: Different formats for the same information (e.g., "Mexico" vs. "MX"). 
- Typographical errors: Misspelled names or codes. 
- Outliers: Data outside an expected range.

2️. Handling Null or Missing Values

Methods for handling missing data:

- Remove rows or columns with too many null values.
- Fill in with a default value (e.g., mean, median, mode).
- Use interpolation or predictive models to estimate values.

3️. Removing Duplicates

Duplicates can be found by:

- Identifying through unique keys (e.g., customer ID).
- Comparing values across multiple columns.
- Using tools like Remove Duplicates in Excel or drop_duplicates() in Python/Pandas.

4️. Correcting Inconsistent Data

Unify formats and conventions:
- Standardize dates, measurement units, and names.
- Convert everything to the same format (e.g., lowercase/uppercase).
- Homogenize categories in categorical variables.

5️. Detecting and Handling Outliers

Methods for finding out-of-range values:
- Percentile or IQR (interquartile range) analysis.
- Visualizing outliers with boxplots.
- Deciding whether to remove, correct, or transform those values.
- Tools for Data Cleansing
- Excel / Google Sheets – Functions like VLOOKUP(), Remove Duplicates, FILTER().
- Power Query (Power BI / Excel) – Ideal for ETL (Extraction, Transformation, and Load).
- Python (Pandas, NumPy, OpenRefine) – For cleaning and transforming large data volumes.
- SQL (queries with CASE, TRIM(), DISTINCT, GROUP BY) – For cleaning data in databases.

## Example in Python with Pandas
```python
import pandas as pd

# Sample dataset
data = {'Name': ['Alice', 'Bob', 'Alice', 'Charlie'],
        'Age': [25, 30, 25, 35],
        'City': ['NY', 'LA', 'NY ', 'SF']}

df = pd.DataFrame(data)

# Remove duplicates
df = df.drop_duplicates()

# Trim whitespace
df['City'] = df['City'].str.strip()

print(df)
```

# Conclusion

Data cleansing is key to ensuring reliable analysis. It can be the slowest stage in a data project.

- However, implementing it correctly saves time and improves the accuracy of any data project.
- Otherwise, the project progresses and then moves backward so many times that the time loss becomes even greater.
