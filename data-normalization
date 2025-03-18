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
- **First Normal Form (1NF):** Remove repeating gro
