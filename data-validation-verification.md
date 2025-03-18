# Data Quality Verification

Data quality verification should be performed during the data exploration stage and with a clear idea of how the data will be used. Among the most common errors affecting data quality, the following stand out:

## Common Data Quality Issues

### Missing Data
Known missing data includes questionnaires left unanswered by some registered users. Without the additional information provided by these questionnaires, these customers may be omitted in some of the subsequent models.

### Typographical Errors
Most data sources are generated automatically, so this is not a severe problem. However, typographical errors in the product database can be detected during the exploration process.

### Measurement Errors
The main source of measurement errors is usually questionnaires. If any elements are not correctly completed, they may not provide the expected information. Again, during the exploration process, it is essential to pay special attention to elements with an unusual distribution of responses.

### Encoding Inconsistencies
These typically include non-standard measurement units or inconsistent values, such as using "M" and "male" to indicate gender.

### Incorrect Metadata
This includes discrepancies between the apparent meaning of a field name or definition.

### Data Temporality
Errors may arise if the dates involved in the project scope are not verified.

## Resolving Errors and Inconsistencies

To address errors and inconsistencies, the following steps should be taken:

- Defining criteria for null or unexpected values.
- Replacing values with more machine-learning-friendly or visualization-friendly alternatives.
- Generating data profiles to extract more information about a specific column before using it.
- Evaluating and transforming column data.
- Applying data structure transformations into table formats.
- Combining queries.
- Applying clear naming conventions and easy-to-understand conversions.

## Data Preprocessing

Data preprocessing is essential. It includes:

- Selecting relevant attributes.
- Removing outliers.
- Normalizing features.

## Data Standardization

Data standardization is key in data analysis as it ensures consistency and quality.

### Example: ZIP Code Normalization in the United States

ZIP codes in the U.S. can appear in different formats, such as:

- **Standard 5-digit format:** `12345`
- **Extended ZIP+4 format:** `12345-6789`
- **Inconsistent formats:** `12345` (with spaces), `123456789` (without a hyphen), `12-345` (incorrectly written)

The U.S. ZIP code system has two formats:

1. **5-digit ZIP Code (Basic)**
   - Introduced in 1963 by USPS.
   - Consists of five digits where:
     - The first three represent a specific region (usually a city or an area within a state).
     - The last two identify a smaller area, such as a post office or a specific sector.
   
   🔹 **Example: `12345`**
   - `123` → Region (could be a city or part of a state).
   - `45` → Post office or specific area within the city.

2. **ZIP+4 (Extended ZIP Code)**
   - Introduced in 1983 to improve mail delivery accuracy.
   - Adds four extra digits to the 5-digit ZIP code (`12345-6789`).
   - The additional 4 digits allow more detailed identification, such as:
     - A group of addresses on a block.
     - A specific building within an apartment complex.
     - A large recipient (like a university or a company with high mail volume).

   🔹 **Example: `12345-6789`**
   - `12345` → Base ZIP code.
   - `6789` → Specific sector (a street, building, floor, etc.).

### Why Do Both Formats Exist?
- The **5-digit ZIP code** is sufficient for most basic mail deliveries.
- The **ZIP+4** format improves accuracy and speed by enabling automated sorting and reducing delivery errors.

### Is ZIP+4 Mandatory?
No, but USPS recommends it because it helps letters and packages arrive faster and with lower error risk.

## Justification for Normalization

Normalization ensures that all ZIP codes follow a uniform format.

### Normalization Methods

1. **Remove Spaces and Invalid Characters**
   - Convert to text (string) if the data is not already in that format.
   - Remove leading and trailing spaces.
   - Eliminate unwanted characters (except the hyphen in ZIP+4).

2. **Ensure Correct Formatting**
   - If a code has 9 digits without a hyphen (`123456789`), format it as `12345-6789`.
   - If it has only 5 digits, leave it as is (`12345`).
   - If it contains invalid characters, mark it as incorrect for review.

3. **Validation Against an Official List**
   - Compare ZIP codes with official USPS postal databases.
   - This is useful for detecting errors or non-existent codes.

## USPS Functions Regarding ZIP Codes

### ZIP Code Assignment
- USPS determines ZIP code allocations based on mail distribution and logistics.
- ZIP codes do not always align with city or county boundaries; they are designed to optimize mail delivery.

### Standardization and Regulations
- Defines the correct ZIP code format (5 digits or ZIP+4).
- Maintains an official database of all ZIP codes in the country.

### Publication and Validation
- Provides APIs and updated databases for address validation.
- Businesses and organizations can verify ZIP codes using USPS services.

## USPS ZIP Code Database and API

USPS offers services for validating and normalizing addresses through:

1. **ZIP Code Lookup Tool (Online):** Allows searching for valid ZIP codes.
2. **USPS Address API:** Can be integrated into systems for automatic address verification and formatting.

To work with real data and validate ZIP codes, the following tools are useful:

- **USPS API** (registration required).
- **U.S. Census Bureau Databases**, which also contain assigned ZIP codes.
- **Address validation services** like SmartyStreets or Melissa Data.

## ZIP Code Validation Criteria

### 1. Correct Format
- A standard ZIP code must have **5 numeric digits** (`12345`).
- A ZIP+4 code must have **9 digits with a hyphen** (`12345-6789`).
- It must not contain letters or special characters (except the hyphen in ZIP+4).

### 2. Valid ZIP Code Range
- U.S. ZIP codes do **NOT** range from `00000` to `99999`.
- **Actual range:**
  - The lowest ZIP codes start at `00501` (Holtsville, NY - IRS Service Center).
  - The highest reach `99950` (Ketchikan, Alaska).
  - Codes like `00000` or `99999` do not exist.

### 3. ZIP Code Prefixes
- The **first digit** of a ZIP code indicates the general region:
  - `0` → New England (Maine, Massachusetts, etc.).
  - `2` → Mid-Atlantic (DC, Virginia, Maryland).
  - `5` → Midwest (Illinois, Missouri, etc.).
  - `9` → West Coast (California, Oregon, Washington).
- The following digits specify smaller areas within the region.

### 4. Validation Against an Official Database
- To verify if a ZIP code exists:
  - Consult the USPS API or its lookup tool.
  - Use U.S. Census Bureau databases or updated ZIP code lists.

## Reliable ZIP Code Databases

1. **GeoNames**
   - Free, open-source geographical database with ZIP codes.
   - Contains over 10 million place names.
   - Available under a **Creative Commons License**.

2. **PostCodeBase.com**
   - Paid database with detailed ZIP code information.
   - Contains approximately **80,163 ZIP codes**.

3. **SimpleMaps.com**
   - Offers both **free** and **paid** ZIP code databases.
   - Includes **geographical and demographic data**.

## Additional Considerations

- **Data Updates:** Ensure the database is current to reflect recent ZIP code changes.
- **License and Usage:** Check licensing terms, especially for commercial use.
- **Integration:** Ensure compatibility with your existing systems.

In summary, both **free** and **paid** options exist for accessing U.S. ZIP code databases. The choice depends on your specific needs, level of detail required, and budget.

# **Data Validation and Data Verification in Data Analysis**

In data analysis, **validation** and **verification** are two fundamental processes to ensure that data is accurate, consistent, and useful for decision-making.

---

## **1. Difference Between Data Validation and Data Verification**  

| **Concept**    | **Data Validation** | **Data Verification** |
|---------------|---------------------|----------------------|
| **Definition** | The process of checking whether data meets predefined rules and criteria. | The process of ensuring that data has been entered, transferred, and stored correctly. |
| **Objective** | Ensuring data is correct and useful. | Confirming that data has not been changed or corrupted during the process. |
| **When It Is Done** | Before and after data is entered into a system. | During data transfer or storage. |
| **Example** | Checking if an email has a valid format (`name@domain.com`). | Comparing values from an original database with a copy to ensure no alterations. |

Both processes are essential to maintaining data quality.

---

## **2. Techniques for Validating and Verifying Data Quality**  

There are several techniques and approaches to ensure data quality. Some of the most commonly used include:

### **2.1. Identifying Data Issues**  

| **Issue**         | **Description** |
|------------------|----------------|
| **Missing Values** | Missing data in critical columns can affect analysis. |
| **Duplicates** | Repeated records can cause errors in calculations and reports. |
| **Formatting Errors** | Poorly structured data, such as dates in different formats (`DD/MM/YYYY` vs. `YYYY-MM-DD`). |
| **Outliers** | Data points that deviate significantly from expected behavior, indicating potential errors or anomalies. |

---

### **2.2. Methods to Detect Issues**  

1. **Detecting Missing Values**  
   - Use `isnull()` or `isna()` in Python (Pandas) to find missing data.  
   - Manually reviewing data in tools like Excel or Power BI.  
   - Applying business rules (e.g., if a user has a purchase, they must have a valid ID).  

2. **Identifying Duplicates**  
   - `duplicated()` in Python or SQL (`GROUP BY` with `COUNT(*) > 1`).  
   - Visual review in spreadsheets using filters.  

3. **Formatting Errors**  
   - Validations with regular expressions (`regex`) for emails, zip codes, etc.  
   - Standardizing date and number formats.  

4. **Detecting Outliers**  
   - Using statistical methods like **Z-score** or **IQR (Interquartile Range)**.  
   - Visualizing with box plots in Python, R, or Power BI.  
   - Business-specific rules (e.g., an age greater than 120 years is suspicious).  

---

## **3. Tools for Checking Data Quality and Consistency**  

### **3.1. Data Profiling and Analysis Tools**  

| **Tool** | **Usage** |
|---------|----------|
| **Pandas (Python)** | Data analysis, cleaning, and validation. |
| **Excel / Google Sheets** | Manual review and validation using conditional rules. |
| **Power BI / Tableau** | Visualization of anomalies and missing values. |
| **SQL (Structured Query Language)** | Searching for inconsistencies and duplicates. |
| **Informatica Data Quality** | Data quality assessment and cleaning for large datasets. |
| **Talend Data Quality** | Automated data integration and validation. |

---

### **3.2. ETL (Extract, Transform, Load) Tools**  

Used to integrate and transform data while ensuring quality.

| **Tool** | **Description** |
|---------|----------------|
| **Apache NiFi** | Automates data flow with validations. |
| **Microsoft SSIS** | Data validation and cleaning in ETL processes. |
| **AWS Glue** | Cloud-based validation for large-scale data. |

---

### **3.3. Tools for Detecting Outliers and Ensuring Data Quality**  

| **Tool** | **Description** |
|---------|----------------|
| **Scikit-learn (Python)** | Algorithms for outlier detection. |
| **PyCaret** | Machine learning models for data validation. |
| **IBM InfoSphere** | Data quality analysis in enterprise environments. |

---

## **4. Regulations, Standards, and Guidelines for Data Validation**  

Several regulations and frameworks guide data validation and quality:

| **Regulation / Standard** | **Description** |
|--------------------------|----------------|
| **ISO 8000** | International standard for data quality. |
| **ISO/IEC 25012** | Data quality model defining characteristics such as accuracy, completeness, consistency, credibility, and timeliness. |
| **DAMA-DMBOK** | Best practices for data management and governance. |
| **GDPR (General Data Protection Regulation)** | Data protection regulation requiring privacy validations. |
| **HIPAA (Health Insurance Portability and Accountability Act)** | Applies to healthcare data, with strict quality controls. |
| **COBIT (Control Objectives for Information and Related Technologies)** | Governance model that includes data quality management. |

---

## **5. Procedures for Data Validation**  

### **5.1. Key Steps in a Data Validation Process**  

1. **Define Validation Criteria**  
   - Business rules (e.g., dates cannot be in the future for historical records).  
   - Format constraints (e.g., a credit card number must have 16 digits).  

2. **Automate Error Detection**  
   - Using tools like Power Query, SQL, and Python.  
   - Implementing alerts in databases.  

3. **Generate Data Quality Reports**  
   - Dashboards in Power BI to visualize inconsistent data.  
   - Periodic reports on missing values and anomalies.  

4. **Correct and Clean Data**  
   - Applying techniques like missing value imputation.  
   - Removing duplicate records.  

5. **Monitor and Update Data Quality**  
   - Implement periodic audits.  
   - Validate data before using it in machine learning models or business reports.  

---

## **Conclusion**  
Validating and verifying data is crucial for high-quality analysis and decision-making. Combining tools, techniques, and regulations ensures that data is reliable and useful. Applying these processes improves operational efficiency and reduces errors in reports and predictive models.

If you want to delve deeper into a specific area (such as automated cleaning or validation in Power BI), let me know, and I'll help you. 🚀
