# Phones
## Number Standardization

### Standardization of Phone Numbers

**Previous** | **Next**

Phone number standardization is a critical component of data cleansing, especially when dealing with international data. Here, I will explain the key aspects of this process.

## International Standards and Norms

### ITU-T Recommendation E.164
This is the primary international standard for telephone numbering. It specifies:
- A maximum length of 15 digits (including the country code)
- General format: [+][country code][national number]
- No spaces, dashes, or other separators in the canonical representation

### ITU-T Recommendation E.123
It establishes guidelines for the visual presentation of phone numbers:
- Use of the "+" sign to indicate the international prefix
- Spaces to improve readability (though databases store numbers without spaces)
- Example: +34 91 123 4567

### ISO 3166-1
Defines country codes, which serve as the basis for telephone codes.

## Best Current Practices

### 1. Storage:
- Always store in full E.164 format
- Keep the field as a string (not a number) to preserve leading zeros
- Consider separate fields for components (country code, area code, number)

### 2. Processing:
- Use specialized libraries like Google libphonenumber
- Implement regular validations to ensure data integrity
- Keep national rules updated (they change over time)

### 3. User Presentation:
- Display in local format when context is known
- Use full international format for global communications
- Offer formatting options based on usage context

### 4. Exception Handling:
- Establish protocols for special numbers (emergency, services)
- Document how internal extensions are handled
- Define procedures for numbers that cannot be normalized

## Regional Considerations

- **North America**: Uniform numbering system (NANP) with code +1 and format XXX-XXX-XXXX
- **Europe**: Various national systems with different lengths
- **Asia**: High variability in formats and national rules
- **Latin America**: Different lengths and dialing systems

## Recommended Tools

- **Google libphonenumber**: Reference library for validation and formatting
- **PhoneNumberKit**: For the Apple ecosystem
- **Twilio Lookup API**: Number validation and enrichment
- **NumVerify**: International validation API

Phone number standardization is a complex process that requires attention to detail and knowledge of international norms. When correctly implemented, it eliminates redundancies, facilitates communication, and significantly improves contact data quality.

## ITU-T Recommendation E.164

ITU-T Recommendation E.164 is the international standard defined by the International Telecommunication Union - Telecommunication Standardization Sector (ITU-T) for telephone numbering. Its main purpose is to ensure that phone numbers are unique and can be used globally in public and private telecommunications networks.

### Contents of Recommendation E.164

This standard establishes the rules for structuring and using phone numbers worldwide. Key aspects include:

- **Maximum length**: An international phone number cannot exceed 15 digits (excluding international dialing prefixes such as "+").

- **Number structure**:
  - **Country Code (CC)**: 1 to 3 digits. Identifies each country or region (e.g., +1 for the USA and Canada, +54 for Argentina).
  - **National Destination Code (NDC)**: Optional, depending on the country. Can represent cities or specific regions within a country.
  - **Subscriber Number (SN)**: A unique number assigned to each user within the respective area.

- **Dialing format**: Must be compatible with different telecommunications systems to ensure network interoperability.

- **Number allocation**: Each country regulates the distribution of numbers within its territory.

- **Special numbers**: Includes rules for numbering emergency services, satellite telecommunications, and premium-rate numbers.

### Compliance with the Standard

Most countries follow the E.164 standard because it is essential for global telecommunications interoperability. However, there are variations in how they apply it:

- Some countries have maintained old numbering structures, adapting only certain aspects of E.164.
- Others have internal exceptions, such as shorter numbers for national calls or special services.
- For international calls, all countries must comply with E.164 to ensure communication between networks worldwide.

## Phone Number Standardization Process

### 1. Data Collection and Initial Analysis
- **Source Identification**: Determine where numbers originate (web forms, CRM, bulk imports)
- **Country Classification**: Preliminarily classify numbers by country of origin if possible
- **Pattern Analysis**: Identify common formats and data anomalies

### 2. Basic Cleaning (Pre-processing)
- **Removal of Non-numeric Characters**: Eliminate spaces, dashes, parentheses, and dots
- **Removal of Dialing Prefixes**: Strip sequences like "00" or "011" (international prefixes)
- **Extension Handling**: Separate and store extension sequences such as "ext.", "x", "#"

### 3. Structural Normalization
- **Country Identification**: Determine the country code (if not present)
- **Application of National Rules**: Adapt to country-specific regulations
- **Conversion to E.164 Format**: Transform to [+][country code][national number]
- **Removal of Redundant National Prefixes**: Strip leading "0" in European numbers

### 4. Validation
- **Length Verification**: Ensure compliance with country-specific length requirements
- **Area Code Validation**: Check if area/region codes are valid
- **Assignment Check**: Validate if the number range is assigned (when possible)
- **Special Number Detection**: Identify service or emergency numbers

### 5. Enrichment (Optional)
- **Geolocation**: Add locality/region information
- **Line Type**: Determine if the number is mobile, landline, VoIP, etc.
- **Carrier Identification**: Identify the service provider
- **Time Zone Association**: Associate with the relevant time zone

## Implementation with Tools

### Google libphonenumber

This open-source library developed by Google is the de facto standard for phone number processing.

Example implementation in Python:

```python
import phonenumbers
from phonenumbers import geocoder, carrier, timezone

def standardize_number(input_number, default_country=None):
    try:
        if not input_number.startswith('+') and default_country:
            parsed_number = phonenumbers.parse(input_number, default_country)
        else:
            parsed_number = phonenumbers.parse(input_number)
        
        if not phonenumbers.is_valid_number(parsed_number):
            return {"status": "invalid", "message": "Invalid number according to country rules"}
        
        return {
            "status": "valid",
            "e164": phonenumbers.format_number(parsed_number, phonenumbers.PhoneNumberFormat.E164),
            "international": phonenumbers.format_number(parsed_number, phonenumbers.PhoneNumberFormat.INTERNATIONAL),
            "national": phonenumbers.format_number(parsed_number, phonenumbers.PhoneNumberFormat.NATIONAL),
            "country_code": parsed_number.country_code,
            "country": geocoder.description_for_number(parsed_number, "en"),
            "type": "mobile" if parsed_number.number_type == phonenumbers.PhoneNumberType.MOBILE else "landline"
        }
    except Exception as e:
        return {"status": "error", "message": str(e)}
```

### NumVerify API
This REST API allows you to validate and obtain information about phone numbers.

```python
import requests

def verify_number(number, api_key):
    # Ensure the number does not have the "+" sign
    if number.startswith('+'):
        number = number[1:]
    
    url = f"http://apilayer.net/api/validate?access_key={api_key}&number={number}"
    
    try:
        response = requests.get(url)
        data = response.json()
        
        if data.get("valid"):
            return {
                "status": "valid",
                "e164": "+" + data.get("international_format").replace(" ", ""),
                "international": data.get("international_format"),
                "national": data.get("local_format"),
                "country_code": data.get("country_prefix"),
                "country": data.get("country_name"),
                "type": data.get("line_type"),
                "carrier": data.get("carrier"),
                "location": data.get("location")
            }
        else:
            return {
                "status": "invalid",
                "message": "Invalid number"
            }
    except Exception as e:
        return {
            "status": "error",
            "message": str(e)
        }
```

---

## Practical Examples by Country

### 1. United States

#### Characteristics of the NANP (North American Numbering Plan) system:

- **Country code:** +1  
- **Structure:** NXX-NXX-XXXX (where N=2-9, X=0-9)  
- **10 digits (excluding the country code)**  

#### Standardization examples:

| Input                  | E.164 Result  | International Format | National Format |
|------------------------|--------------|----------------------|-----------------|
| (212) 555-1234        | +12125551234  | +1 212 555 1234      | (212) 555-1234  |
| 646.555.4321          | +16465554321  | +1 646 555 4321      | (646) 555-4321  |
| 1-800-123-4567        | +18001234567  | +1 800 123 4567      | (800) 123-4567  |

#### Processing with libphonenumber:

```python
usa_number = standardize_number("(212) 555-1234", "US")
```

---

### 2. Spain

#### Characteristics:
- **Country code:** +34  
- **Structure:** 9 digits after the country code  
- **Mobile numbers:** Start with 6 or 7  
- **Landline numbers:** Start with 8 or 9  

#### Standardization examples:

| Input                | E.164 Result  | International Format | National Format |
|----------------------|--------------|----------------------|-----------------|
| 91 123 45 67        | +34911234567  | +34 911 23 45 67     | 911 23 45 67    |
| 600.12.34.56        | +34600123456  | +34 600 12 34 56     | 600 12 34 56    |
| 0034 922 123 456    | +34922123456  | +34 922 12 34 56     | 922 12 34 56    |

#### Processing with libphonenumber:

```python
spain_number = standardize_number("91 123 45 67", "ES")
```

---

### 3. Argentina

#### Characteristics:
- **Country code:** +54  
- **Complex structure with variable-length area codes**  
- **Prefix "0" for national calls**  
- **Prefix "15" for local mobile numbers**  

#### Standardization examples:

| Input                 | E.164 Result  | International Format | National Format |
|-----------------------|--------------|----------------------|-----------------|
| (011) 4123-4567      | +541141234567 | +54 11 4123-4567     | 011 4123-4567   |
| 0351 15-678-9012     | +543516789012 | +54 351 678-9012     | 0351 15-678-9012 |
| +54 9 223 456-7890   | +5492234567890| +54 9 223 456-7890   | 0223 15-456-7890 |

#### Processing with libphonenumber:

```python
argentina_number = standardize_number("011 4123-4567", "AR")
```

---

## Advanced Considerations

- **Mobile numbers in Argentina:** The Argentine system uses the prefix "9" in international format for mobile numbers but "15" in national format, adding complexity.  
- **Numbering changes:** National numbering plans change periodically. For example, Spain added an extra digit to its landline numbers in 1998.  
- **Contextual formatting:** The presentation format should adapt depending on the context:  
  - For local users: **National format**  
  - For international communications: **E.164 or international format**  
- **Optimal storage:** Always store numbers in **canonical E.164 format (+CCNNNNN...)**  
- **Error handling:** Implement a strategy for unverifiable numbers (store them in the original format but mark them as unverified).  

The process of standardizing phone numbers requires a combination of general rules and country-specific exceptions, so using libraries like `libphonenumber` greatly simplifies this task and reduces errors.

