# Customer Data Standardization
To delve deeper into data standardization (which is part of the data cleansing process), let's take as an example a customer database that has the following dimensions:
- [Customer ID](#customer-id)
- [Name](#name)
- [Identity Document](#identity-document)
- [Address](#address-street-number)
- [Postal Code](#postal-code-zip-code)
- [E-mail](#e-mail)
- [Phone](#phone)
- [Date](#Date)

See also:
- [Implementation in Power BI with Power Query](#implementation-in-power-bi-with-power-query)
- [Additional Considerations](#additional-considerations)

Based on this structure, let's see what the recommendations are for standardization in each case or dimension.

## Customer ID

**Best Practices:**
- Unique and consistent format (numeric, alphanumeric)
- No spaces or special characters
- Fixed length when possible
- Prefixes to segment customer types

**Current Standards:**
- No single global standard for internal IDs
- Banking systems typically use 10-16 digit numbers
- Retail uses alphanumeric codes with category prefixes

### Power BI
```
// Remove spaces and standardize format
Clean ID = 
TRIM(
    UPPER(
        SUBSTITUTE([Customer ID], " ", "")
    )
)
```
trans
### Python
```python
# ID standardization
def standardize_id(df):
    # Convert to string, remove spaces and apply uppercase
    df['standardized_ID'] = df['ID'].astype(str).str.strip().str.upper()
    # Remove special characters
    df['standardized_ID'] = df['standardized_ID'].str.replace(r'[^\w]', '', regex=True)
    return df
```
_____________________________________________________________________________________________
Go go deeper into the [**standardization of customer identification**](client_id_standar.md).
_____________________________________________________________________________________________

## Name

**Best Practices:**
- Separate into fields (First Name, Last Name1, Last Name2)
- Consistent capitalization (first letter uppercase)
- Remove titles (Dr., Mr., etc.) or store them in a separate field
- Remove special characters while maintaining accents and language-specific characters

**Current Standards:**
- ISO/IEC 8859-1 for Latin characters
- NIST format for personal name storage
- vCard standard (RFC 6350) for personal information exchange

### Power BI
```
// Capitalize name
Standardized Name = 
PROPER(
    TRIM([Name])
)

// Split full name
First Name = IF(FIND(" ", [Full Name]) > 0, 
    LEFT([Full Name], FIND(" ", [Full Name]) - 1), 
    [Full Name])

Last Name = IF(FIND(" ", [Full Name]) > 0,
    RIGHT([Full Name], LEN([Full Name]) - FIND(" ", [Full Name])),
    "")
```

### Python
```python
def standardize_names(df):
    # Split full name
    if 'Full_Name' in df.columns:
        df[['First_Name', 'Last_Name']] = df['Full_Name'].str.split(' ', n=1, expand=True)
    
    # Properly capitalize
    for col in ['First_Name', 'Last_Name']:
        if col in df.columns:
            df[col] = df[col].str.title()
            # Special handling for names with prefixes like "de la", "del", etc.
            df[col] = df[col].str.replace(r'\bDe La\b', 'de la', regex=True)
            df[col] = df[col].str.replace(r'\bDel\b', 'del', regex=True)
    
    return df
```

## Identity Document

**Best Practices:**
- Format specific to country/document type
- Remove periods, spaces, and hyphens
- Store document type in a separate field
- Validate using verification algorithms if available

**Current Standards:**
- ISO/IEC 7501 for machine-readable travel documents
- National standards (DNI in Spain, SSN in USA, CPF in Brazil, etc.)

### Power BI
```
// Clean ID/document
Standardized Document = 
UPPER(
    SUBSTITUTE(
        SUBSTITUTE(
            SUBSTITUTE([Document], ".", ""), 
            "-", ""
        ), 
        " ", ""
    )
)
```

### Python
```python
def standardize_document(df, column='Document'):
    # Convert to string
    df[column] = df[column].astype(str)
    
    # Remove spaces, periods, and hyphens
    df[column + '_standardized'] = df[column].str.replace(r'[\s\.\-]', '', regex=True)
    
    # Convert to uppercase
    df[column + '_standardized'] = df[column + '_standardized'].str.upper()
    
    return df
```

## Address (Street, Number)

**Best Practices:**
- Divide into components (street, number, floor, etc.)
- Standard abbreviations (Ave., Blvd., etc.)
- Normalize addresses against official database
- Geocoding for validation

**Current Standards:**
- ISO 19160 for postal addresses
- UPU (Universal Postal Union) S42 for international exchange
- USPS for abbreviations in the United States
- National postal standards

### Power BI
```
// Standardize common abbreviations in addresses
Standardized Address = 
SUBSTITUTE(
    SUBSTITUTE(
        SUBSTITUTE(
            PROPER(TRIM([Address])),
            "Avenue", "Ave."
        ),
        "Street", "St."
    ),
    "Boulevard", "Blvd."
)
```

### Python
```python
def standardize_addresses(df, column='Address'):
    # Properly capitalize
    df[column] = df[column].str.title()
    
    # Dictionary of standard abbreviations
    abbreviations = {
        r'\bAvenue\b': 'Ave.', 
        r'\bBoulevard\b': 'Blvd.',
        r'\bStreet\b': 'St.',
        r'\bApartment\b': 'Apt.',
        r'\bBuilding\b': 'Bldg.'
    }
    
    # Apply substitutions
    for pattern, abbreviation in abbreviations.items():
        df[column] = df[column].str.replace(pattern, abbreviation, regex=True)
    
    # Extract number if needed
    df['Number'] = df[column].str.extract(r'(\d+)')
    df['Street'] = df[column].str.replace(r'\s+\d+.*$', '', regex=True)
    
    return df
```

## Postal Code (ZIP Code)

**Best Practices:**
- Fixed format according to country
- Validation against official postal code database
- Store without spaces or with consistent format
- Relate to city/state for cross-validation

**Current Standards:**
- UPU S42 for international postal codes
- Country-specific formats (5 digits in USA, alphanumeric in UK)

### Power BI
```
// Standardize ZIP codes
Standardized ZIP = 
IF(
    LEN([ZIP Code]) = 4, 
    "0" & [ZIP Code], 
    [ZIP Code]
)
```

### Python
```python
def standardize_zipcode(df, column='zip_code', country='USA'):
    # Remove spaces
    df[column] = df[column].astype(str).str.strip()
    
    # Apply format according to country
    if country == 'USA':
        # Ensure 5 digits for USA
        df[column] = df[column].str.replace(r'[^\d]', '', regex=True)
        df[column] = df[column].str.zfill(5)
    elif country == 'UK':
        # UK format: AA9A 9AA
        df[column] = df[column].str.upper()
        # Format-specific rules could be added here
    
    return df
```

## E-mail

**Best Practices:**
- Convert to lowercase
- Validate format using regular expressions
- Verify domain existence
- Remove spaces and disallowed characters

**Current Standards:**
- RFC 5322 for email format
- RFC 3696 for restrictions on email addresses
- IDNA for internationalized domains

### Power BI
```
// Standardize email
Standardized Email = 
LOWER(
    TRIM([e-mail])
)

// Validate basic format
Valid Email = 
IF(
    CONTAINSSTRING([Standardized Email], "@") && 
    CONTAINSSTRING([Standardized Email], "."), 
    TRUE, 
    FALSE
)
```

### Python
```python
import re

def standardize_email(df, column='e-mail'):
    # Convert to string and lowercase
    df[column] = df[column].astype(str).str.lower().str.strip()
    
    # Basic validation with regex
    email_pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    
    # Create validation column
    df['valid_email'] = df[column].apply(lambda x: bool(re.match(email_pattern, x)))
    
    return df
```

## Implementation in Power BI with Power Query

To perform a complete standardization in Power BI, the recommended approach is to use Power Query. Here's how to implement a complete solution:

```
// Function for complete standardization of customer data
let
    StandardizeCustomerData = (Table as table) as table =>
    let
        // Standardize ID
        WithID = Table.TransformColumns(Table, {
            {"ID", each Text.Upper(Text.Trim(Text.Replace(_, " ", "")))}
        }),
        
        // Standardize Name
        WithName = Table.TransformColumns(WithID, {
            {"Name", each Text.Proper(Text.Trim(_))}
        }),
        
        // Split Name if necessary
        WithSplitName = if Table.HasColumns(WithName, "Full_Name") then
            Table.SplitColumn(WithName, "Full_Name", Splitter.SplitTextByDelimiter(" ", QuoteStyle.None), {"First_Name", "Last_Name"})
        else
            WithName,
        
        // Standardize Document
        WithDocument = Table.TransformColumns(WithSplitName, {
            {"Document", each Text.Upper(Text.Remove(Text.Remove(Text.Remove(_, "."), "-"), " "))}
        }),
        
        // Standardize Address
        WithAddress = Table.TransformColumns(WithDocument, {
            {"Address", each Text.Proper(Text.Trim(_))}
        }),
        
        // Address abbreviations
        WithAddressAbbrev = Table.TransformColumns(WithAddress, {
            {"Address", each 
                Text.Replace(
                    Text.Replace(
                        Text.Replace(_, "Avenue", "Ave."), 
                        "Street", "St."
                    ),
                    "Boulevard", "Blvd."
                )
            }
        }),
        
        // Standardize ZIP code
        WithZIP = Table.TransformColumns(WithAddressAbbrev, {
            {"zip_code", each if Text.Length(Text.Trim(_)) = 4 then "0" & Text.Trim(_) else Text.Trim(_)}
        }),
        
        // Standardize Email
        WithEmail = Table.TransformColumns(WithZIP, {
            {"e-mail", each Text.Lower(Text.Trim(_))}
        }),
        
        // Validate Email
        WithValidatedEmail = Table.AddColumn(WithEmail, "Email_Valid", each 
            Text.Contains([e-mail], "@") and Text.Contains([e-mail], "."))
    in
        WithValidatedEmail
in
    StandardizeCustomerData
```

## Additional Considerations

### Validation and Monitoring

- Implement data validation rules after standardization
- Establish quality metrics (% valid records, % complete fields)
- Monitor changes in data over time
- Document all transformations performed

### Error Management

- Create fields to identify problematic records
- Implement manual or assisted correction processes
- Establish flows to handle exceptions
- Define policies for missing values

### Continuous Maintenance

- Periodically review applied standards
- Update dictionaries and rules according to business needs
- Document metadata and applied transformations
- Train the team on standardization rules
