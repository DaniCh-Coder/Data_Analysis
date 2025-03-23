# Standardization of Addresses in Customer Databases
As a data analyst, I understand that address standardization is a critical component of the data cleaning process. Poorly structured addresses can lead to duplicates, shipping errors, and incorrect analysis.

## Best Practices for Address Standardization

### 1. Recommended Address Field Structure
It is advisable to split the address into multiple fields to facilitate searches, analysis, and validations:

- **Street type** (Street, Avenue, Road, etc.)  
- **Street name**  
- **Number/Building**  
- **Floor/Level**  
- **Apartment/Unit**  
- **Neighborhood/District**  
- **City/Town**  
- **State/Province/Region**  
- **Country**  
- **Postal Code**  
- **Geographic coordinates** (latitude/longitude)  

### 2. Component Normalization
- **Consistent abbreviations**: Establish rules for abbreviations (e.g., "Ave." for Avenue, "St." for Street).  
- **Upper/lower case**: Decide on a uniform format (recommended: capitalize the first letter).  
- **Special characters**: Remove or standardize accents, umlauts, etc.  
- **Numeric formatting**: Establish consistent patterns for numbers (e.g., "1st" vs. "1").  

### 3. Validation with Official Sources
- **National postal services**: Cross-check with official databases such as USPS (USA), Royal Mail (UK), Correos (Spain), etc.  
- **Cadastral records**: Validate against official property registry information.  
- **Normalization APIs**: Use services like:  
  - Google Maps Geocoding API  
  - HERE Geocoding API  
  - ArcGIS Geocoding  
  - DataCleaner Address Cleaner  

### 4. Geocoding
Converting addresses into geographic coordinates provides significant advantages:  
- Enables precise location tracking  
- Facilitates geospatial analysis  
- Helps identify non-obvious duplicates  
- Allows visualization on maps  

### 5. Cleaning and Standardization Process
- **Parsing**: Break unstructured addresses into components.  
- **Correction**: Apply normalization rules.  
- **Enrichment**: Fill in missing information.  
- **Geolocation**: Add coordinates.  
- **Verification**: Cross-check with official sources.  
- **Deduplication**: Identify and unify duplicate records.  

### 6. Continuous Maintenance and Management
- **Regular updates**: Addresses change (renamed streets, administrative reorganizations).  
- **Update system**: Implement processes to update addresses periodically.  
- **Change history**: Maintain a record of modifications for traceability.  
- **Feedback loop**: Establish mechanisms to report address errors.  

### 7. Recommended Tools
- **ETL Tools**: Talend, Informatica, Microsoft SSIS.  
- **Specific Libraries**: libpostal (open source).  
- **SaaS Services**: SmartyStreets, Melissa Data, CARTO.  
- **Spatial Databases**: PostGIS, SQL Server Spatial.  

### 8. Regional Considerations
- Adapt the model to the specifics of each country.  
- Consider differences in address formats (Anglo-Saxon vs. Latin American countries).  
- Take into account local regulations on nomenclature.  

### 9. Quality Indicators
To measure process effectiveness:  
- Percentage of standardized addresses.  
- Match rate with official sources.  
- Geocoding accuracy.  
- Successful delivery rate (if applicable).  

Address standardization is an ongoing process that requires continuous maintenance, but the return on investment is reflected in better analysis, more effective communications, and optimized customer experiences.  

---

## Address Standardization Process in Databases
Address standardization involves normalizing formats, correcting errors, and validating data against official sources. This process improves data quality and facilitates further analysis.

### 1. Basic Preprocessing
**Initial cleaning:**
- Remove non-alphanumeric characters (e.g., #, *, @ unrelated to the address).
- Convert everything to uppercase or lowercase to standardize formats.
- Correct common typos (e.g., "Street" vs. "Alley").

**Example of transformation:**  
`"Calle 5 # 12-45"` → `"CALLE 5 #12-45"`

### 2. Component Segmentation
Divide the address into structured elements:  
- Street  
- Number  
- Additional details (Floor, Apartment)  
- Neighborhood/Urbanization  
- City/Town/District  
- Province/State  
- Postal Code  

### 3. Validation Against External Sources
**Reference databases:**  
- **Municipal cadastral records**: Verify the existence of streets and numbers.  
- **Postal services**: Confirm postal codes and their relationship to cities.  
- **Property records**: Cross-check with registered addresses.  
- **Geographical APIs (Google Maps, OpenStreetMap)**: Validate geolocation.  

### 4. Format Normalization
**Common standards:**  
- Abbreviations: `"CL"` for `"Calle"`, `"KR"` for `"Carrera"`.  
- Term unification: `"URBANIZACION"` → `"URB."` or `"Urbanización"`.  
- Numerical consistency: `#12-45` → `12-45` (remove redundant symbols).  

### 5. Handling Inconsistencies
**Common cases:**  
- **Incomplete addresses**: Use APIs to fill in missing fields (e.g., Google Maps).  
- **Nonexistent addresses**: Mark as **INVALID** and require manual verification.  
- **Ambiguous addresses**: Prioritize official sources (e.g., prefer cadastral data over historical records).  

### 6. Documentation and Auditing
- **Change log**: Record transformations applied to each record.  
- **Quality metrics**: Calculate the percentage of validated vs. non-validated addresses.  

---

## Country-Specific Standardization Examples

### 1. United States
**USPS Standard Format:**  
`FULL_NAME | STREET_NUMBER STREET_TYPE_ABBR | CITY STATE_ABBR ZIP_CODE`

```python
import re

def standardize_us_address(address):
    address = address.upper().replace("#", "").replace(",", "")
    abbreviations = {
        'STREET': 'ST', 'DRIVE': 'DR', 'AVENUE': 'AVE',
        'BOULEVARD': 'BLVD', 'APARTMENT': 'APT'
    }
    pattern = r'(\d+)\s+(.*?)\s+([A-Z]{2})\s+(\d{5}(?:-\d{4})?)'
    match = re.search(pattern, address)

    if match:
        number, street, state, zipcode = match.groups()
        for key, val in abbreviations.items():
            street = street.replace(key, val)
        return f"{number} {street} {state} {zipcode}"
    
    return "INVALID ADDRESS"
```

This approach ensures compliance with international postal standards and enables integration with logistics systems like FedEx or USPS.

### 2. Canada
Canada Post Standard Format:

`NAME | STREET_NUMBER STREET_TYPE | CITY PROVINCE_ABBR POSTAL_CODE`

PostgreSQL SQL Code:

```sql
CREATE FUNCTION standardize_ca_address(address TEXT)
RETURNS TEXT AS $$
DECLARE
   postal_code TEXT;
   province TEXT;
BEGIN
   postal_code := (SELECT regexp_matches(address, '[A-Z]\d[A-Z] \d[A-Z]\d'))[1];

   province := CASE
       WHEN address ~ 'ONTARIO' THEN 'ON'
       WHEN address ~ 'QUEBEC' THEN 'QC'
       ELSE 'OTHER'
   END;

   RETURN upper(address) || ' ' || postal_code;
END;
$$ LANGUAGE plpgsql;
```

### 3. Argentina

Recommended format:
NAME | STREET NUMBER | LOCALITY PROVINCE POSTAL_CODE

Power Query M:

```
let
   EstandarizarDireccionAR = (direccion as text) as text =>
   let
       // Convertir a mayúsculas y eliminar caracteres especiales
       CleanText = Text.Upper(Text.Replace(direccion, "#", "")),
       // Extraer Código Postal Argentino (CPA) [8]
       CPA = Text.Combine(List.LastN(Text.Split(CleanText, " "), 4)),
       // Reemplazar términos comunes
       ReplaceTerms = Text.Replace(Text.Replace(CleanText, "CALLE", "C"), "NUMERO", "N°"),
       // Formato final
       Formatted = Text.Combine({
           Text.BeforeDelimiter(ReplaceTerms, CPA),
           " CPA " & CPA
       })
   in
       Formatted
in
   EstandarizarDireccionAR
// Ejemplo de uso:
EstandarizarDireccionAR("calle corrientes 1234 piso 5, CABA, C1078ABC")
// Salida: C CORRIENTES 1234 PISO 5 CPA C1078ABC
```

## Cross-Validation

| Country | Tool | Validation Database | API Endpoint |
|---------|------|---------------------|--------------|
| USA | USPS CASS Certified | USPS Database | FedEx Address Validation |
| Canada | Canada Post CPC | PADB (Postal Address DB) | Loqate International Verification |
| Argentina | Correo Argentino | CPA Database | Local APIs (E.g.: GeorefAR) |

## Recommended Workflow

Preprocessing:

```python
# Eliminar diacríticos y caracteres especiales
direccion_limpia = unicodedata.normalize('NFKD', direccion).encode('ASCII', 'ignore').decode()
```

Validation:

```sql
-- Consulta de código postal válido
SELECT * FROM codigos_postales
WHERE pais = 'AR'
AND codigo SIMILAR TO '[A-Z]\d{4}[A-Z]{3}';
```

Geocoding:

```
text= Geography.FromText(direccion_estandarizada)
```

This approach ensures compliance with international postal regulations and allows integration with logistics systems such as FedEx or USPS.

## Common Challenges When Standardizing Addresses in Different Countries

### Unique Address Formats:
Each country has specific rules for organizing address components (street, number, postal code, city, etc.).
Example: In Japan, addresses go from general (prefecture) to specific (house number), while in the U.S. it's the opposite.

### Local Abbreviations and Conventions:
Inconsistent use of abbreviations for streets or states (example: "California" vs "CA") can cause errors.
In Canada, provinces are abbreviated (e.g., "Ontario" → "ON"), while in Argentina full names or specific abbreviations like "CABA" are used.

### Variable Postal Codes:
Postal code format varies widely:
- USA: 5 digits or 9 digits (ZIP+4).
- Canada: Alphanumeric format (e.g., "K1A 0B1").
- Argentina: Alphanumeric CPA with 8 characters (e.g., "B1858BJQ").

### Common Data Entry Errors:
Missing required information such as postal code.
Typographical errors or inconsistencies in street and city names.

### Validation Against Official Databases:
Lack of access to updated databases makes validation difficult.
Frequent changes in street names or postal codes.

## Differences Between USA, Canada and Argentina

| Aspect | USA | Canada | Argentina |
|--------|-----|--------|-----------|
| Basic Format | Number + Street + City + State + ZIP | Number + Street + City + Province + PC | Street + Number + Locality + Province + CPA |
| Postal Codes | 5 digits (optional 4 additional) | Alphanumeric (e.g., "K1A 0B1") | Alphanumeric CPA with 8 characters |
| Abbreviations | Extensive use for streets and states | Abbreviated provinces | Complete provinces or abbreviated (depending on context) |
| Official Validation | USPS (Publication 28) | Canada Post | Correo Argentino |
| Unique Components | Mandatory state | Mandatory province | Mandatory CPA for shipments |

## Example of Standardization Process

In Python:

```python
def estandarizar_direccion(direccion, pais):
   direccion = direccion.upper().strip()
   if pais == 'EE.UU.':
       abreviaturas = {'STREET': 'ST', 'AVENUE': 'AVE', 'CALIFORNIA': 'CA'}
       for key, val in abreviaturas.items():
           direccion = direccion.replace(key, val)
       return direccion
   elif pais == 'Canadá':
       direccion = direccion.replace("ONTARIO", "ON").replace("QUEBEC", "QC")
       return direccion
   elif pais == 'Argentina':
       direccion = direccion.replace("CALLE", "").replace("CABA", "CIUDAD AUTÓNOMA DE BUENOS AIRES")
       return direccion
   return "Formato no soportado"
# Ejemplo
print(estandarizar_direccion("123 Main Street, Springfield, California", "EE.UU."))
# Resultado: 123 MAIN ST, SPRINGFIELD, CA
```

In SQL:

```sql
CREATE FUNCTION estandarizar_direccion(direccion TEXT, pais TEXT)
RETURNS TEXT AS $$
BEGIN
   IF pais = 'EE.UU.' THEN
       RETURN regexp_replace(direccion, '(STREET|AVENUE)', 'ST', 'gi');
   ELSIF pais = 'Canadá' THEN
       RETURN regexp_replace(direccion, '(ONTARIO|QUEBEC)', CASE WHEN $1 ~* 'ONTARIO' THEN 'ON' ELSE 'QC' END, 'gi');
   ELSIF pais = 'Argentina' THEN
       RETURN regexp_replace(direccion, '(CALLE|CABA)', '', 'gi');
   ELSE
       RETURN 'Formato no soportado';
   END IF;
END;
$$ LANGUAGE plpgsql;
-- Ejemplo
SELECT estandarizar_direccion('123 Main Street, Springfield, California', 'EE.UU.');
-- Resultado: 123 MAIN ST, SPRINGFIELD, CA
```

In Power Query M:

```
let
   EstandarizarDireccion = (direccion as text, pais as text) as text =>
   let
       UpperCase = Text.Upper(direccion),
       Resultado = if pais = "EE.UU." then
                       Text.Replace(UpperCase, "STREET", "ST")
                   else if pais = "Canadá" then
                       Text.Replace(UpperCase, "ONTARIO", "ON")
                   else if pais = "Argentina" then
                       Text.Replace(UpperCase, "CALLE", "")
                   else
                       "Formato no soportado"
   in
       Resultado
in
   EstandarizarDireccion
// Ejemplo de uso:
EstandarizarDireccion("123 Main Street, Springfield, California", "EE.UU.")
// Resultado: 123 MAIN ST, SPRINGFIELD, CALIFORNIA
```

## Conclusion

Address standardization varies significantly between countries due to cultural and logistical differences in their formats.

Challenges include human errors and validation against updated official databases.

Using country-specific tools is essential to ensure accuracy and avoid logistical problems.

## Address Validation with APIs (Including Coordinates and Postal Codes)

### Recommended Tools

| API | Features | Cost |
|-----|----------|------|
| Google Maps | Address validation, reverse geocoding, coordinates and postal codes | Free (100k calls/month) |
| USPS Web API | Address validation in the U.S., format correction and ZIP codes | Free (requires account) |
| HERE Maps | Geocoding and global address validation | Free (250k calls/month) |

### Technical Examples

1. Python (Google Maps Geocoding API)

```python
import requests
def validar_direccion_google(direccion, api_key):
   url = f"https://maps.googleapis.com/maps/api/geocode/json?address={direccion}&key={api_key}"
   response = requests.get(url)
   if response.status_code == 200:
       data = response.json()
       if data['status'] == 'OK':
           return {
               'valida': True,
               'componentes': data['results'][0]['address_components'],
               'coordenadas': data['results'][0]['geometry']['location'],
               'codigo_postal': next((item['long_name'] for item in data['results'][0]['address_components'] if 'postal_code' in item['types']), None)
           }
   return {'valida': False}
# Ejemplo de uso:
direccion = "1600 Amphitheatre Parkway, Mountain View, CA"
print(validar_direccion_google(direccion, "TU_API_KEY"))
```

2. SQL (USPS Web API)

```sql
CREATE FUNCTION validar_direccion_usps(direccion TEXT)
RETURNS JSON AS $$
DECLARE
   respuesta JSON;
BEGIN
   SELECT content INTO respuesta FROM http_get(
       'https://secure.shippingapis.com/ShippingAPI.dll?API=CityStateLookup&XML=<CityStateLookupRequest USERID="TU_USER_ID"><ZipCode ID="0"><Address ID="0"><Address1>' || direccion || '</Address1></Address></ZipCode></CityStateLookupRequest>'
   );

    IF respuesta->>'status' = 'OK' THEN
       RETURN json_build_object(
           'valida', TRUE,
           'ciudad', (respuesta->'CityStateLookupResponse'->'ZipCode'->'Address'->'City'),
           'estado', (respuesta->'CityStateLookupResponse'->'ZipCode'->'Address'->'State'),
           'codigo_postal', (respuesta->'CityStateLookupResponse'->'ZipCode'->'Address'->'Zip5')
       );
   END IF;
   RETURN json_build_object('valida', FALSE);
END;

$$ LANGUAGE plpgsql;
-- Ejemplo de uso:
SELECT validar_direccion_usps('1600 Amphitheatre Parkway, Mountain View, CA');
```

3. Power Query M (Google Maps Geocoding)

```
let
   ValidarDireccionGoogle = (direccion as text, api_key as text) as table =>
   let
       Url = "https://maps.googleapis.com/maps/api/geocode/json?address=" & direccion & "&key=" & api_key,
       Respuesta = Web.Contents(Url),
       Json = Json.Document(Respuesta),
       Resultado = if Json[status] = "OK" then
           Table.FromColumns({
               {Json[results]{0}[address_components]},
               {Json[results]{0}[geometry][location]},
               {List.First(List.Select(Json[results]{0}[address_components], each List.Contains(_, "postal_code")))[long_name]}
           }, {"Componentes", "Coordenadas", "Código Postal"})
       else
           Table.FromColumns({{"Dirección inválida"}}, {"Resultado"})
   in
       Resultado
in
   ValidarDireccionGoogle
// Ejemplo de uso:
ValidarDireccionGoogle("Calle Corrientes 1234, Buenos Aires", "TU_API_KEY")
```

### Recommended Validation Flow

Basic validation:
- Verify postal code format (e.g.: ^\d{5}(?:-\d{4})?$ for USA).
- Remove non-alphanumeric characters (#, @, etc.).

API Integration:
- Google Maps: Ideal for geolocation and global validation.
- USPS: Specific for USA, with automatic address correction.

Error handling:
- If the API returns ZERO_RESULTS, mark as invalid.
- If postal code is missing, use Google Maps to infer it from coordinates.

### Limitations and Considerations

Google Maps:
- Requires API key (free up to 100k calls/month).
- Variable coverage in non-English-speaking countries.

USPS:
- Only valid for USA.
- Requires registration to obtain USER_ID.

HERE Maps:
- Free alternative for reverse geocoding.

For robust implementations, combine multiple APIs according to the target country and validate against official databases such as USPS CASS or Canada Post.

## Address Validation in Excel with Power Query

Power Query allows automating address validation through data transformations and connection with external APIs. Here's a practical approach:

### 1. Data Preparation

Step 1: Create an address table

```
let
   Origen = Excel.CurrentWorkbook(){[Name="TablaDirecciones"]}[Content],
   Tipo = Table.TransformColumnTypes(Origen,{{"Dirección", type text}})
in
   Tipo
```

### 2. Basic Validation (Without APIs)

Example: Format correction

```
let
   Origen = Excel.CurrentWorkbook(){[Name="TablaDirecciones"]}[Content],
   // Eliminar caracteres no alfanuméricos
   Limpiar = Table.TransformColumns(Origen,{{"Dirección", each Text.Replace(_, "#", "")}}),
   // Convertir a mayúsculas
   Mayusculas = Table.TransformColumns(Limpiar,{{"Dirección", Text.Upper}})
in
   Mayusculas
```

### 3. Validation with External APIs

Example: Google Maps Geocoding API

```
let
   Origen = Excel.CurrentWorkbook(){[Name="TablaDirecciones"]}[Content],
   // Llamar a la API para cada dirección
   LlamarAPI = Table.AddColumn(Origen, "Validación", each Json.Document(Web.Contents("https://maps.googleapis.com/maps/api/geocode/json?address=" & [Dirección] & "&key=TU_API_KEY"))),
   // Extraer resultados
   ExtraerDatos = Table.ExpandRecordColumn(LlamarAPI, "Validación", {"results"}, {"Validación.results"}),
   // Filtrar direcciones válidas
   Filtrar = Table.SelectRows(ExtraerDatos, each [Validación.results] <> null)
in
   Filtrar
```

### 4. Postal Code Validation

Example: USPS Web API (USA)

```
let
   Origen = Excel.CurrentWorkbook(){[Name="TablaDirecciones"]}[Content],
   // Extraer código postal (ZIP)
   ExtraerZIP = Table.AddColumn(Origen, "ZIP", each Text.AfterDelimiter([Dirección], " ")),
   // Validar con USPS
   ValidarZIP = Table.AddColumn(ExtraerZIP, "Válido", each Web.Contents("https://secure.shippingapis.com/ShippingAPI.dll?API=CityStateLookup&XML=<CityStateLookupRequest USERID='TU_USER_ID'><ZipCode ID='0'><Address ID='0'><Address1>" & [ZIP] & "</Address1></Address></ZipCode></CityStateLookupResponse>"))
in
   ValidarZIP
```

### 5. Automation with Parameters

Example: Scheduled update

```
let
   // Parámetro para fecha de actualización

    FechaActual = DateTime.Date(DateTime.LocalNow()),
   // Consulta dinámica
   Datos = Excel.CurrentWorkbook(){[Name="TablaDirecciones"]}[Content],
   // Filtrar por fecha actual
   Filtrado = Table.SelectRows(Datos, each [Fecha] = FechaActual)
in
   Filtrado
```

### Recommended Workflow

Import data:
- Use Data > Get Data > From File > Excel to connect address tables.

Transform data:
- Clean special characters and unify formats.

Validate with APIs:
- Integrate Google Maps or USPS to verify addresses and postal codes.

Load results:
- Use Close & Load to display validated data in a worksheet.

### Complete Validation Example

```
let
   Origen = Excel.CurrentWorkbook(){[Name="TablaDirecciones"]}[Content],
   // Limpiar y convertir a mayúsculas
   Preparar = Table.TransformColumns(Origen,{{"Dirección", each Text.Upper(Text.Replace(_, "#", ""))}}),
   // Validar con Google Maps
   Validar = Table.AddColumn(Preparar, "Válido", each Json.Document(Web.Contents("https://maps.googleapis.com/maps/api/geocode/json?address=" & [Dirección] & "&key=TU_API_KEY"))[results] <> null),
   // Filtrar direcciones válidas
   Filtrado = Table.SelectRows(Validar, each [Válido])
in
   Filtrado
```

This approach allows validating addresses in Excel in a structured way, integrating native tools and external APIs to ensure accuracy.
