# Data and Metadata

## The importance of metadata in a project

Metadata, or "data about data," is fundamental in any data project for several reasons:

- **Understanding the origin**: They document the provenance of the data, including when, how, and by whom it was collected.
- **Clear structure**: They define the organization and format of the data, facilitating its interpretation.
- **Contextual meaning**: They provide context and definitions for each data element.
- **Quality assessment**: They include information about accuracy, completeness, and reliability.
- **Facilitate searching**: They allow for quickly finding and filtering relevant data.
- **Compatibility**: They ensure that data can be shared between systems and organizations.
- **Traceability**: They allow tracking the lifecycle of data through transformations and uses.
- **Regulatory compliance**: They help meet legal requirements for data management.

## International metadata standards

There are numerous international standards for metadata, adapted to different domains:

1. **Dublin Core**: A simple set of 15 elements to describe digital resources (author, title, date, etc.). Widely used in libraries and digital archives.

2. **ISO 19115**: Specific standard for geographic information and geospatial services.

3. **DDI (Data Documentation Initiative)**: Focused on documentation of social and behavioral science data.

4. **DCAT (Data Catalog Vocabulary)**: Facilitates interoperability between data catalogs on the web.

5. **Schema.org**: Set of schemas for structured data on the internet, supported by major search engines.

6. **PREMIS (Preservation Metadata)**: Focused on metadata for long-term digital preservation.

7. **CSDGM (Content Standard for Digital Geospatial Metadata)**: Used for geospatial data in the U.S.

8. **ISO/IEC 11179**: Standard for metadata registry and data administration.

9. **CERIF (Common European Research Information Format)**: For scientific research information.

10. **METS (Metadata Encoding and Transmission Standard)**: For managing objects in digital libraries.

11. **EML (Ecological Metadata Language)**: Specific for ecological and environmental data.

## Discussion with data

When tackling any project, you need to "become fond of" the data. By this, I mean that you must understand them and understand what you intend to do with them.

Usually, this process of understanding the data tends to have two major components:

1. Understanding which data are necessary for the project's objective. This understanding exercise has a lot to do with understanding the business and its rules. (See the section on Data necessary for a project on this same website)

2. Defining (putting in black and white, or bringing down to earth) the utility function of the data. This dynamic has a lot to do with data cleansing of the dataset. (See the section DUF: data utility on this same website)

In this process of steps 1 and 2 of the mentioned data understanding routine, what is done is to understand, define, or redefine the data structure and metadata.

## Metadata

Metadata is "data about data." That is, it is structured information that describes, explains, locates, or facilitates the use of data.

📌 Simple example:
Imagine you have a sales table with this structure:
Customer | Date | Amount
---------|------|-------
Juan | 2024-03-10 | $100
Ana | 2024-03-11 | $250

The data are the values in the table (Juan, 2024-03-10, $100).

The metadata would be information about these data, that is:
- Column names: Customer, Date, Amount.
- Data types: Text, Date, Number.
- Constraints: "Amount cannot be negative."
- Description: "The 'Date' field indicates the day of purchase."

There are three basic types of metadata in databases:
- Technical
- Descriptive
- Administrative

**Technical metadata**: Contains information about the data structure. For example: Data type, maximum length, primary keys.

**Descriptive metadata**: Explains the meaning of the data. For example: Descriptions, standard names, labels.

**Administrative metadata**: Indicates who can access the data and how they are managed. For example: User permissions, security rules.

## Conventions and standards for data and their structure

There are several international standards that regulate how data in relational databases are documented and structured. Among the most well-known and respected worldwide are:

### A. SQL INFORMATION_SCHEMA (ISO/IEC 9075)
It is a set of standard SQL views that allow querying metadata about databases. It is defined by the SQL standard (ISO/IEC 9075) and is widely adopted in systems such as SQL Server, PostgreSQL, MySQL, and Oracle.

Usage:
- Obtain information about tables, columns, data types, primary and foreign keys.
- Identify constraints, relationships, and user permissions.
- Verify if data comply with integrity standards.

Example of a query to view metadata in SQL Server:
```sql
SELECT COLUMN_NAME, DATA_TYPE, IS_NULLABLE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'Sales';
```

### B. ISO/IEC 11179 - Metadata Registry Standard
An international metadata management standard that defines how data should be documented in databases and information systems. It is used by the industry to maintain consistency and data quality in large organizations.

Usage:
- Defines rules for data names, definitions, and descriptions.
- Establishes how to structure metadata catalogs in databases.
- Used in data governance and data warehouses.

Example:
If you have a database with a column called client_id, ISO 11179 recommends documenting its meaning, for example:
- Standard Name: Customer Identifier
- Description: A unique number assigned to each customer in the system.
- Example Value: C12345

### C. Dublin Core Metadata Initiative (DCMI)
It is a widely used metadata standard to describe digital resources (documents, databases, APIs, etc.). It defines 15 main elements, such as title, description, creator, date, format, etc.

📌 What is it used for?
- Describe and classify data in document databases and data repositories.
- Create open data catalogs (Open Data).
- Apply metadata on the web, digital libraries, and databases.

📌 Example of application in Power Query:
If a database field has a description defined in its source, Power Query can map it to Documentation.Description, following Dublin Core principles.

🔹 This standard influences how Power Query handles metadata within Table.Schema().
