# Data Cleansing

Data Cleansing is the process of cleaning, validating, correcting, enriching, and standardizing data to ensure that it is accurate, complete, coherent, and useful for analysis and decision-making.

## Differences between Data Cleaning and Data Cleansing

## Steps of Data Cleansing (Beyond Data Cleaning)

### 1. Data Quality Analysis
Before cleaning the data, its state must be evaluated. Some key criteria include:
* Completeness: Are data missing in some columns or records?
* Coherence: Does the data have contradictory values?
* Accuracy: Does the data reflect reality?
* Validity: Does it comply with predefined business rules?
* Uniformity: Are the formats consistent?

Example: If a company has customer data in different databases, there may be inconsistencies in names or addresses.

### 2. Data Standardization
For data to be comparable and useful, it must follow a standard. This involves:
* Normalization of dates (Ex: "12/03/24" → "2024-03-12").
* Uniform text formats (Ex: "CDMX" vs. "Mexico City").
* Homogenization of names and categories (Ex: "Mexico" and "MX" should refer to the same thing).

Example in SQL to standardize city names:
```sql
UPDATE clients 
SET city = 'Mexico City' 
WHERE city IN ('CDMX', 'Mexico City', 'D.F.');
```

To delve deeper into data standardization (which is part of the data cleansing process), let's take as an example a [**customer database example**](customer-data-standarization.md) that has the following dimensions:
Customer Id., Name, Identity document, adress, postal code, e-mail, phone, date.

### 3. Data Validation
Here, business rules are applied to ensure that the data is correct.

Validation methods:
* Predefined rules (Ex: a phone number must have 10 digits).
* Cross-checking with reliable sources (Ex: validating addresses with official databases).
* Integrity tests (Ex: verifying that relationships between tables are correct).

Example in Python to validate email addresses:
```python
import re

def validate_email(email):
    pattern = r'^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$'
    return bool(re.match(pattern, email))

email_test = "user@company.com"
print(validate_email(email_test))  # Returns True if valid
```

### 4. Data Enrichment
Consists of complementing the data with additional information to improve its value.

Examples:
* Adding geographic coordinates to an address database.
* Incorporating demographic data into customer records.
* Integrating external data, such as market prices or exchange rates.

Example in Power BI:
You can connect sources such as APIs or external databases to enrich the data.

### 5. Removing and Merging Duplicate Data
When a company has multiple data sources, it's common to find repeated or similar records. You should:
* Identify duplicates based on key criteria (Ex: ID, name, date).
* Decide whether to consolidate or eliminate records.
* Apply rules for data merging (Ex: take the most recent or most complete data).

Example in Power Query (Power BI) to remove duplicates:
* Select the key column.
* Go to Home > Remove Duplicates.
* Apply combination rules if necessary.

Example in Python:
```python
df = df.drop_duplicates(subset=['Customer_ID'], keep='last')
```

### 6. Data Integration and Consolidation
If the data comes from different sources, it must be properly integrated.

Example of integration in SQL (Joining customer and order tables):
```sql
SELECT customers.name, orders.total
FROM customers
JOIN orders ON customers.customer_id = orders.customer_id;
```

Example in Power BI:
* Create relationships between tables using primary and foreign keys.
* Use the MERGE function in Power Query to combine datasets.

### 7. Data Quality Auditing and Monitoring
To prevent problems from recurring, quality controls must be implemented:
* Data quality dashboards in Power BI.
* Automation of validations with Python or scheduled SQL scripts.
* Business rules in databases (Constraints, Triggers).

Example in SQL to monitor missing data:
```sql
SELECT column, COUNT(*) AS null_count 
FROM table 
WHERE column IS NULL 
GROUP BY column;
```

Example in Power BI to measure data quality:
Create a DAX measure to count null values:
```
NullValues = COUNTROWS(FILTER(Table, Table[Column] = BLANK()))
```
Display it in a bar chart to identify problematic fields.

## Conclusion: Data Cleansing is more than just cleaning data
* It's a structured process that involves analysis, validation, standardization, enrichment, and monitoring.
* It goes beyond Data Cleaning, as it seeks to improve the quality, coherence, and usefulness of data.
* It requires advanced tools such as Power Query, SQL, Python, ETL tools, and Data Governance.
