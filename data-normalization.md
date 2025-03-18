# Data Normalization

Data normalization is the process of organizing data in a database to reduce redundancy and improve data integrity. 
This process involves dividing large tables into smaller, related tables and establishing relationships between them using primary and foreign keys.

## Main Objectives of Normalization:
- Eliminate data redundancy
- Reduce update, insertion, and deletion anomalies
- Improve data integrity
- Simplify queries
- Facilitate database maintenance

## Example of Normalization

### Unnormalized Table:

| Order_ID | Customer   | Customer_Phone | Product  | Price | Quantity |
|----------|-----------|---------------|----------|------|---------|
| 1001     | Ana López | 555-1234      | Laptop   | 1200 | 1       |
| 1001     | Ana López | 555-1234      | Mouse    | 25   | 2       |
| 1002     | Juan Pérez | 555-5678     | Laptop   | 1200 | 1       |
| 1003     | Ana López | 555-1234      | Keyboard | 45   | 1       |

### Problems:
- **Redundancy:** Ana López's information is repeated.
- **Anomalies:** If Ana's phone number changes, it must be updated in multiple rows.
- **Wasted space.**

### Normalized Tables (3NF):

#### Customers Table:

| Customer_ID | Name       | Phone    |
|------------|-----------|---------|
| C001       | Ana López | 555-1234 |
| C002       | Juan Pérez | 555-5678 |

#### Products Table:

| Product_ID | Name     | Price |
|-----------|--------|------|
| P001      | Laptop  | 1200 |
| P002      | Mouse   | 25   |
| P003      | Keyboard | 45   |

#### Orders Table:

| Order_ID | Customer_ID |
|---------|------------|
| 1001    | C001       |
| 1002    | C002       |
| 1003    | C001       |

#### Order Details Table:

| Order_ID | Product_ID | Quantity |
|---------|------------|---------|
| 1001    | P001       | 1       |
| 1001    | P002       | 2       |
| 1002    | P001       | 1       |
| 1003    | P003       | 1       |

---

## Techniques and Tools for Data Normalization

### Normal Forms:
- **First Normal Form (1NF):** Remove repeating groups.
  - Each column must contain an atomic (indivisible) value.
  - Each row must be unique.
- **Second Normal Form (2NF):** Comply with 1NF.
  - All non-key attributes must fully depend on the primary key.
- **Third Normal Form (3NF):** Comply with 2NF.
  - Remove transitive dependencies (non-key attributes depending on other non-key attributes).
- **Boyce-Codd Normal Form (BCNF):** A stricter version of 3NF.
  - Every determinant must be a candidate key.
- **Fourth Normal Form (4NF):** Eliminate multivalued dependencies.
- **Fifth Normal Form (5NF):** Eliminate join dependencies.

### Tools for Normalization

#### Data Modeling Tools:
- Erwin Data Modeler
- MySQL Workbench
- Oracle SQL Developer Data Modeler
- PowerDesigner
- Lucidchart

#### ETL (Extract, Transform, Load) Tools:
- Talend
- Informatica PowerCenter
- Microsoft SSIS (SQL Server Integration Services)
- Apache NiFi
- AWS Glue

#### Data Profiling Tools:
- Informatica Data Quality
- Talend Data Quality
- Ataccama ONE
- IBM InfoSphere Information Analyzer

---

## Standards and Frameworks
- **ISO/IEC 11179:** A metadata registry standard that facilitates data normalization and exchange between organizations.
- **DAMA-DMBOK:** A data management framework that includes normalization practices.
- **COBIT:** IT governance and management framework that includes data management processes.
- **The Open Group Architecture Framework (TOGAF):** Enterprise architecture framework that includes normalized data models.
- **CMMI-DEV:** Maturity model that includes practices for data management and normalization.

---

## Analysis for Data Normalization

### Fundamental Steps:

1. **Requirement Analysis:**
   - Identify the information to be stored.
   - Define the purpose of the database.
   - Establish system boundaries.

2. **Identification of Entities and Attributes:**
   - Define main entities (customers, products, orders).
   - Identify attributes for each entity.
   - Determine primary keys.

3. **Identifying Relationships:**
   - Establish relationships between entities.
   - Determine cardinality (one-to-one, one-to-many, many-to-many).

4. **Mapping Functional Dependencies:**
   - Identify which attributes depend on others.
   - Document these dependencies.

5. **Applying Normal Forms:**
   - Start with 1NF and progress as needed (usually up to 3NF is sufficient).
   - Evaluate the impact on performance.

6. **Selective Denormalization:**
   - Evaluate if denormalization is necessary to improve performance.
   - Document denormalization decisions.

7. **Model Validation:**
   - Ensure the model supports all requirements.
   - Review with stakeholders.

8. **Documentation:**
   - Create a data dictionary.
   - Document the data model and normalization decisions.

---

## Additional Considerations

### Balance Between Normalization and Performance:
- Full normalization can impact performance due to the need for joins.
- Consider data volume and access patterns.

### Historical Data:
- Evaluate if historical data needs to be maintained.
- Consider modeling techniques for historical data (SCD - Slowly Changing Dimensions).

### Scalability:
- Consider future data growth.
- Assess if the normalized model will scale effectively.

### Change Management:
- Establish procedures to manage changes in the data model.
- Document the impact of changes on applications and processes.

### Data Quality:
- Implement validation rules and integrity constraints.
- Establish processes for data cleansing.

### Security and Privacy:
- Consider security requirements in model design.
- Implement access controls at table and column levels.

---

Data normalization is a critical process in database design that ensures data integrity and consistency, facilitates long-term maintenance, and improves database operation efficiency.
