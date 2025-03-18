# Data needed for a project

Understanding what data is necessary for a data analysis project helps define its utility function. In general, best practices emerge from experience, both for defining essential data and for understanding the type of data that best represents it.
The table in the figure shows, for example, a data structure for a customer file or table.

![Tabla Clientes](client-table-struct.png)

This is a fairly straightforward data structure. 
To understand this, let's review the basic data types in a relational table or file.

---

## Explanation of Basic Data Types in Relational Databases

- **VARCHAR(n)**: Used for short text strings, where `n` is the maximum length.
- **TEXT**: Used to store long text, such as addresses and comments.
- **DATE**: For dates (format: YYYY-MM-DD).
- **TIMESTAMP**: For exact dates and times (format: YYYY-MM-DD HH:MM:SS).
- **DECIMAL(m, d)**: For monetary values, where `m` is the total number of digits and `d` is the number of decimal places.

---

## Key Validations
Data types, whether basic or not, must be respected throughout the data lifecycle.
For this purpose, there is the concept of validation, which consists of verifying that the data adheres to the format and premises under which it should be loaded, stored, and processed.

- **Email**: Must follow the format `user@domain.com`.
- **Phone**: Recommended international format `+54 9 11 1234 5678`.
- **Postal Code**: Must be valid according to the country.
- **Date of Birth**: Cannot be a future date.
- **Balance**: Cannot be negative.

---

## Analysis of Customer Address

When analyzing the customer data structure, we see that it does provide sufficient information. Indeed, the table in the following figure shows a well-detailed data structure for a customer address. 
This includes a more comprehensive breakdown with data such as:

- Address number
- Neighborhood
- Floor
- Apartment
- Latitude
- Longitude for geolocation

# Criteria for Determining Necessary Data for a Project

## Fundamental Criteria

1. **Project objectives**: Data must align with the specific objectives and questions the project seeks to answer.

2. **Relevance**: Select data that has a direct relationship with the problem to be solved or the hypothesis to be tested.

3. **Completeness**: Ensure that the dataset contains all the variables necessary for the analysis.

4. **Data quality**: Evaluate the accuracy, integrity, consistency, and currency of the data.

5. **Granularity**: Determine the required level of detail (daily, monthly, individual, aggregated, etc.).

6. **Volume**: Estimate the amount of data needed to obtain statistically significant results.

7. **Time period**: Define the relevant timeframe for the analysis.

8. **Availability**: Verify if the required data is available or can be obtained.

9. **Acquisition cost**: Evaluate the cost-benefit of obtaining certain data.

10. **Ethical and legal considerations**: Compliance with privacy and data protection regulations.

## Regulations

There are relevant regulations, primarily related to:

- **GDPR**: In Europe, regulates the protection of personal data.
- **CCPA/CPRA**: In California, establishes rights over personal data.
- **HIPAA**: In the US, for health data.
- **ISO/IEC 27001**: International standard for information security management.
- **Specific sector laws**: Financial, health, or government regulations depending on the sector.

Each country or region may have its own regulations on data collection, storage, and use.

## Templates

There are several templates and methodologies that can help:

1. **Data management plan**: Documents that describe how data will be managed during and after the project.

2. **Data dictionary**: Describes each variable, its type, range, and meaning.

3. **RACI matrix for data**: Defines responsibilities for data management.

4. **Data requirements catalog**: Structured list of data needs.

5. **CRISP-DM**: Standard methodology for data mining projects that includes the "data understanding" phase.

6. **TDSP (Team Data Science Process)**: Microsoft framework that includes templates for data requirements.

