# **Data and structures. Documentation**

In any data project, understanding the different types of data structures is crucial for effective data management and analysis. This section explores the three main categories of data structures—**structured**, **semi-structured**, and **unstructured**—and provides guidance on how to handle them in a project. Additionally, it covers the tools and technologies commonly used to work with these data types, such as relational databases, NoSQL databases, JSON files, and XML.
Understanding data structures is essential for designing efficient data pipelines, ensuring data quality, and selecting the right tools for analysis. By choosing the appropriate data structure, teams can improve performance, reduce costs, and deliver more accurate insights.

1. **Types of Data Structures**:
   - **Structured Data**: Data that is organized in a predefined format, typically stored in tables with rows and columns. Examples include relational databases like MySQL or PostgreSQL.
   - **Semi-Structured Data**: Data that does not conform to a strict schema but has some organizational properties, such as tags or markers. Examples include JSON and XML files.
   - **Unstructured Data**: Data that lacks a predefined structure, making it more challenging to analyze. Examples include text documents, images, videos, and social media posts.

2. **Tools and Technologies**:
   - **Relational Databases**: Ideal for structured data, these databases use SQL (Structured Query Language) to manage and query data. Examples include MySQL, PostgreSQL, and Oracle.
   - **NoSQL Databases**: Designed for semi-structured and unstructured data, NoSQL databases (e.g., MongoDB, Cassandra) offer flexibility and scalability for handling diverse data types.
   - **JSON and XML**: Common formats for semi-structured data, often used in web APIs and configuration files. JSON is lightweight and easy to parse, while XML is more verbose but highly structured.

3. **Choosing the Right Data Structure**:
   - The choice of data structure depends on the nature of the project and the type of data being handled. For example:
     - Use **relational databases** for structured data with clear relationships (e.g., financial records).
     - Use **NoSQL databases** for projects requiring scalability and flexibility (e.g., real-time analytics or big data applications).
     - Use **JSON or XML** for data exchange between systems or when working with APIs.

4. **Challenges and Best Practices**:
   - **Data Integration**: Combining data from different structures can be complex. Tools like ETL (Extract, Transform, Load) pipelines can help.
   - **Scalability**: Ensure the chosen data structure can handle the volume and growth of data.
   - **Performance**: Optimize data storage and retrieval processes to meet project requirements.

# **Data Organization and Documentation**

When embarking on a data project, it is very useful to have at least a mental guide of the steps to follow to prepare the data for processing in pursuit of an objective. In other words, data must be preprocessed properly so that when processed, we achieve good results. This is why methodologies for managing data projects exist, such as the **CRISP-DM Standard**.

Performing good preprocessing involves intensive work in data analysis, structure, metadata, needs, and, of course, redundancy, errors, and noise or garbage.

If we follow any data analysis methodology, we see that between the stages of data understanding, preprocessing, and modeling, it is important to define and/or have well-structured tables or files. This implies a neat organization of metadata.

Having a good structure in each table or file, with metadata properly ordered and normalized, is the starting point for cleaning and refining existing data (if any) and for processing it. The same is important if, instead of preexisting data, we are defining a data model from scratch.

---

## Example

Suppose we need to work on refining a commercial management system. Let's take, within this case, the base, table, or file of customers and consider:

- What are the most important attributes?
- Which are indispensable for the business logic?
- Which attributes might be redundant and generate noise?
- What should be the type and format of each data point?
- What standard should each data point adhere to?
- How well is the data structure documented, or how well will we document it?

### Current State of the Data Structure

Let's observe the first records and ask ourselves these questions. Now, let's examine the structure using the **M language in Power Query**. The output is shown in Figure 2. Let's observe the output and ask ourselves the same type of questions mentioned earlier. For example, we can see that:

- By the names of the fields or columns, we can deduce what each field represents. However, this is not formally documented. It should be documented because when files are large in dimensions and content, the lack of documentation often leads to misunderstandings.
- Only two data types are used: text and number. However, there is no definition of the data type, its scope, or validation. For example, it is easy to wonder what normalization columns like `email` or `zip_code` have. Can they have any length or format?
- All fields can be null. It is hard to believe there are no missing data points with this observation.
- Normalization and validation criteria for the data are not observed and, therefore, are unclear. The negative impact this generates is enormous in terms of data cleaning needs and data validity.

### Desired State of the Data Structure

The observation of the current state of the data leads us to envision the desired state in terms of organization and metadata definition. Let's look at Figure 3, where a first version of the desired state is proposed. In correspondence with the three points mentioned earlier in the current state, we can now observe that:

- In addition to the column names, a description field has been added where each field in the customer table is now properly documented. This adds clarity, avoids confusion, and contributes to reducing the dimensions of files and databases, which over time tend to become redundant and unnecessarily large.
- Only two data types are used: text and number. However, restrictions and normalization conditions have been added. For example, the `str_num` field corresponding to a postal address number cannot exceed 6 digits. The field corresponding to the floor cannot exceed 3 digits.
- In the current state, all 17 fields can be null. In the desired state, only 5 of the 17 fields can be null.
- Definitions have been added in two columns: one for normalization and another for validation.

The organization and definitions in the desired state now allow for a thorough data cleansing process.
