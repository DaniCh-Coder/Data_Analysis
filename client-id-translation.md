# Client ID
## Standardization of Client Identifiers

## Standards and Best Practices for Client ID
The client ID is a critical field that requires rigorous design and management. Let's first look at criteria derived from best practices. Some criteria for this are as follows.

### 1. Structure and Format

- **Uniqueness**: Each ID must be unique and unrepeatable, avoiding duplicates.
- **Fixed Length**: Define a maximum number of characters (example: 8 digits) to facilitate management.
- **Alphanumeric Format**: Combine numbers and letters (example: CLI-2025-001) to avoid confusion with other codes. No spaces or special characters (except common ones like hyphens).
- **Avoid Sensitive Information**: Do not include data such as document numbers or birth dates in the ID.

### 2. Generation and Assignment

- **Automation**: Use systems that generate IDs sequentially or through algorithms (example: UUIDs to avoid duplicates).
- **Documented Rules**: Establish clear protocols for assigning new IDs or modifying them (example: prefixes by client type).

### 3. Validation and Cleaning

- **Format Verification**: Ensure compliance with the defined structure (example: CLI-YYYY-XXX).
- **Duplicate Detection**: Use automation tools to identify and correct repeated IDs.
- **Periodic Audit**: Review obsolete or incorrect IDs and update them according to governance policies.

### 4. Integration and Consistency

- **Mapping with Other Systems**: Ensure that the ID is consistent across all linked databases (example: CRM, ERP).
- **Documentation**: Record the meaning, format, and use of the ID in data catalogs to avoid ambiguities.

**Practical Example**:
If an ID has the format CLI-2025-001, criteria are applied as follows:

- **Validation**: Verify that the year matches the registration (2025) and that the numeric suffix (001) is not repeated.
- **Cleaning**: If there is an ID CLI-2024-001 in 2025, update it to CLI-2025-001 or use a versioning system.

These standards guarantee traceability, reduce errors, and facilitate data integration.

## Standards and Recommendations
- **Global Unique Identifier (GUID)**: Using automatically generated unique identifiers, such as UUID (Universal Unique Identifier), ensures that each client has an unrepeatable ID in distributed systems.
Example: 550e8400-e29b-41d4-a716-446655440000.

- **Compliance with KYC (Know Your Customer)**:
According to USA PATRIOT Act recommendations, the ID must be linked to verifiable data such as name, date of birth, address, and government identification number (e.g., SSN in the USA or TIN for foreigners).

- **Digital Identity Systems**:
Digital systems must ensure that IDs are based on reliable and independent sources, following FATF recommendations for secure digital identification.

## Client ID Management
Client ID management faces multiple irregularities that affect data integrity. Below is an analysis of the most common cases, their probability of occurrence, and technical solutions:

### 1. Missing ID

The absence of an identifier in client records generally occurs due to errors in ETL processes, failed integrations, or incomplete manual entries.

**Probability**: High (30-40% in systems without automatic validation).

#### Example Solution

**ID Regeneration**:

```sql
-- Example in Dynamics 365 (using SQL Server)
UPDATE custtable
SET CustomerID = 'CLI-' + RIGHT('000000' + CAST(ROW_NUMBER() OVER(ORDER BY RecId) AS VARCHAR), 6)
WHERE CustomerID IS NULL;
```

**CRM Tools**:
Use the Excel Add-in for mass updates in non-production environments.

**Prevention**:
Implement NOT NULL + DEFAULT in SQL schemas.

### 2. ID with Incorrect Format

ID with incorrect format is detected when we find identifiers that do not follow defined standards (e.g., "CLI#2025" vs "CLI-2025-001").

**Probability**: Moderate (15-25% in systems with multiple input sources).

#### Detection

**Regular Expressions**:

```sql
ALTER TABLE clientes
ADD CONSTRAINT chk_id_formato
CHECK (CustomerID ~ '^CLI-\d{4}-\d{3}$');
```

**Batch Transformation**:

```python
# Normalization in Python
df['CustomerID'] = df['CustomerID'].str.replace(r'[^a-zA-Z0-9-]', '', regex=True)
```

### 3. Duplicate IDs

Duplicate IDs exist when multiple records share the same ID, generating conflicts in transactions and analysis.

**Probability**: High (20-35% in migrations or integrations without deduplication).

#### Detection

**SQL Detection**:

```sql
WITH Duplicados AS (
 SELECT CustomerID, COUNT(*) AS Total
 FROM clientes
 GROUP BY CustomerID
 HAVING COUNT(*) > 1
)
SELECT * FROM clientes
WHERE CustomerID IN (SELECT CustomerID FROM Duplicados);
```

#### Solution

**Record Merging**:
Use MDM tools like Informatica or Talend to consolidate data.

**UUID as Replacement**:

```sql
ALTER TABLE clientes
ADD UUID CHAR(36) DEFAULT uuid_generate_v4();
```

## Duplicate Client Identifiers

When a client ID is repeated, technical and operational strategies are implemented to correct the duplication, preserve data integrity, and avoid distortions in analysis or business processes. Here are the key actions:

### Steps to Resolve Duplicate Client IDs

#### 1. Identify the Source of the Problem

**Data Origin**:
Verify if duplicates come from manual entries, external integrations (e.g., APIs), or errors in legacy systems. This prevents them from occurring again.

**Example**:
In Dynamics CRM, check if duplicate detection rules are activated and published.

#### 2. Detection and Classification

**Native Tools**:
Use functions such as duplicate detection jobs in CRM to identify repeated records.

**Prioritization**:
Classify duplicates by criticality (e.g., customers with purchase history vs. unqualified leads).

### 4. Prevent Future Duplicates

#### Preventive Measures

**Database Constraints**:
Implement unique keys or unique indexes on critical fields (e.g., email, phone).

**Real-time Validation**:
Use duplicate detection rules in CRM to block redundant insertions.

**Data Standardization**:
Normalize formats (e.g., +54 9 11 50506060 for Argentine phones) to avoid variations.

### Practical Cases

**Example 1: Detection in Dynamics CRM**

- **Enable Rules**: Configure fields such as email or phone to detect duplicates.
- **Run Jobs**: Filter records by entity (e.g., Accounts) and delete or merge duplicates.

**Example 2: Unification with ID-graph**

- **Prioritize Identifiers**:
  Use email > phone > cookie ID to create a unique td_canonical_id.
- **Enrich Data**:
  Link the profile_id with web interaction tables for 360° analysis.

### Advanced Tools and Strategies

- **Cleaning Software**:
  Use solutions like Duplicate Detection to massively merge records and export reports.
- **MDM (Master Data Management)**:
  Centralize IDs in a master database that feeds all corporate systems.
- **ID-graphs**:
  Link identities across multiple domains (e.g., websites + apps) for a unified view.

### Impact of Not Resolving Duplicates

- **Operational Costs**:
  Duplicate efforts in marketing or customer service.
- **Analysis Errors**:
  Distortion of KPIs such as retention rate or customer value.
- **Reputation**:
  Inconsistent experiences for customers (e.g., multiple offers for the same product).

The key is to anticipate duplicates through validation and standardization, and correct them with technical tools and unification strategies.

### 4. Valid Non-Unique IDs

There could be legitimate cases where different clients share an ID due to coincidence (e.g., legacy systems without controls).

**Probability**: Low (<5% in modern systems).

```sql
ALTER TABLE clientes
ADD UNIQUE (CustomerID, Nacionalidad);
```

**Migration to GUID**:
Replace numeric IDs with UUIDv4 to ensure global uniqueness.

### 5. Obsolete/Deprecated IDs

Obsolete identifiers are identifiers from legacy systems incompatible with new standards.

**Probability**: Moderate (10-20% in companies with >5 years of operation).

#### Solution

**Mapping Table**:

| ID_Legacy | ID_Nuevo |
|-----------|----------|
| CLI_001550e | 8400-e29b-41d4... |

**Translation APIs**:

```python
def traducir_id(id_legacy):
   return legacy_map.get(id_legacy, uuid.uuid4())
```

Implementation of real-time validation and standards like UUID reduces these problems by >70% according to documented cases.

For critical systems, it is recommended to combine:

- Database constraints
- MDM tools
- Monthly audits with detection queries.

## References:
[List of 24 references omitted for brevity]
