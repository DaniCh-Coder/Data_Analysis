# Standardization of identification documents for individuals and companies

## Personal identification documents

The identification documentation for individuals (and companies) varies according to each country's implementations. However, there are some international standards that attempt to standardize this topic with a focus on individuals.

### International standards
- ISO/IEC 7501: Defines the format for machine-readable travel documents (MRTD)
- ICAO 9303: International standard for passports and travel documents
- ISO 14443: Specification for identification cards with contactless integrated circuits

### Structure and format by country
Let's look at how personal identification works in Argentina, USA, and Canada.

#### United States
- SSN (Social Security Number): Format XXX-XX-XXXX
- United States passport: 9 characters (combination of letters and numbers)
- Driver's license: Varies by state, generally alphanumeric

#### Canada
- SIN (Social Insurance Number): Format XXX-XXX-XXX
- Canadian passport: 8 characters (2 letters followed by 6 digits)
- Driver's license: Varies by province

#### Argentina
- DNI (National Identity Document): 7-8 digits
- CUIL/CUIT (for individuals): Format XX-XXXXXXXX-X
- Argentinian passport: Letter + 8 numeric digits

### Best practices for standardization

#### Format normalization
- Remove spaces, hyphens, and special characters for storage
- Preserve the display format with appropriate separators
- Define capitalization rules for alphabetical components

#### Validation according to specific algorithms
- Check digits (checksums)
- Validation of valid ranges
- Verification of issue/expiration dates

```python
import re

from datetime import datetime



class DocumentValidator:

    def __init__(self):

        # Patrones de validación por tipo de documento y país

        self.patterns = {

            'us_ssn': r'^\d{3}-?\d{2}-?\d{4}$',

            'us_passport': r'^[A-Z0-9]{9}$',

            'ca_sin': r'^\d{3}-?\d{3}-?\d{3}$',

            'ca_passport': r'^[A-Z]{2}\d{6}$',

            'ar_dni': r'^\d{7,8}$',

            'ar_cuil': r'^\d{2}-?\d{8}-?\d{1}$'

        }

    

    def validate_format(self, doc_type, document):

        """Valida el formato del documento según el tipo"""

        if doc_type not in self.patterns:

            return False, "Tipo de documento no soportado"

        

        pattern = self.patterns[doc_type]

        if re.match(pattern, document):

            return True, "Formato válido"

        else:

            return False, f"Formato inválido para {doc_type}"

    

    def standardize_document(self, doc_type, document):

        """Estandariza el formato del documento según su tipo"""

        if not document:

            return None

            

        # Eliminar caracteres no alfanuméricos

        doc_clean = re.sub(r'[^A-Z0-9]', '', document.upper())

        

        # Aplicar formato específico según tipo de documento

        if doc_type == 'us_ssn':

            if len(doc_clean) == 9:

                return f"{doc_clean[0:3]}-{doc_clean[3:5]}-{doc_clean[5:9]}"

        

        elif doc_type == 'ca_sin':

            if len(doc_clean) == 9:

                return f"{doc_clean[0:3]}-{doc_clean[3:6]}-{doc_clean[6:9]}"

        

        elif doc_type == 'ar_cuil':

            if len(doc_clean) == 11:

                return f"{doc_clean[0:2]}-{doc_clean[2:10]}-{doc_clean[10]}"

        

        # Para otros tipos, devolver el valor limpio

        return doc_clean

    

    def validate_checksum(self, doc_type, document):

        """Valida el dígito verificador para documentos que lo incluyen"""

        # Implementar algoritmos de verificación específicos

        if doc_type == 'ar_cuil':

            # Eliminar caracteres no numéricos

            doc_clean = re.sub(r'[^0-9]', '', document)

            if len(doc_clean) != 11:

                return False, "Longitud inválida"

                

            # Algoritmo de validación CUIL/CUIT argentino

            factors = [5, 4, 3, 2, 7, 6, 5, 4, 3, 2]

            doc_digits = [int(doc_clean[i]) for i in range(10)]

            

            # Multiplicar cada dígito por su factor

            products = [doc_digits[i] * factors[i] for i in range(10)]

            sum_products = sum(products)

            

            # Calcular dígito verificador

            remainder = sum_products % 11

            check_digit = 11 - remainder

            if check_digit == 11:

                check_digit = 0

            

            # Verificar si coincide con el dígito proporcionado

            if check_digit == int(doc_clean[10]):

                return True, "Dígito verificador válido"

            else:

                return False, "Dígito verificador inválido"

                

        return True, "Validación no implementada para este tipo"



# Ejemplo de uso

validator = DocumentValidator()



# Ejemplos de documentos

test_documents = [

    ('us_ssn', '123-45-6789'),

    ('us_ssn', '123456789'),

    ('ca_sin', '123-456-789'),

    ('ar_dni', '12345678'),

    ('ar_cuil', '20-12345678-9')

]



for doc_type, doc in test_documents:

    is_valid_format, format_msg = validator.validate_format(doc_type, doc)

    print(f"Documento: {doc} ({doc_type})")

    print(f"Formato: {format_msg}")

    

    if is_valid_format:

        standardized = validator.standardize_document(doc_type, doc)

        print(f"Estandarizado: {standardized}")

        

        if doc_type == 'ar_cuil':

            is_valid_checksum, checksum_msg = validator.validate_checksum(doc_type, doc)

            print(f"Checksum: {checksum_msg}")

    

    print("-" * 40)
```

## SQL for standardization and validation

```sql
-- Funciones para estandarización de documentos (PostgreSQL)



-- Estandarización de SSN (EE.UU.)

CREATE OR REPLACE FUNCTION standardize_ssn(input_ssn TEXT)

RETURNS TEXT AS $$

DECLARE

    clean_ssn TEXT;

BEGIN

    -- Eliminar todos los caracteres no numéricos

    clean_ssn := REGEXP_REPLACE(input_ssn, '[^0-9]', '', 'g');

    

    -- Verificar longitud

    IF LENGTH(clean_ssn) <> 9 THEN

        RETURN NULL;

    END IF;

    

    -- Formatear con guiones

    RETURN SUBSTRING(clean_ssn FROM 1 FOR 3) || '-' ||

           SUBSTRING(clean_ssn FROM 4 FOR 2) || '-' ||

           SUBSTRING(clean_ssn FROM 6 FOR 4);

END;

$$ LANGUAGE plpgsql;



-- Estandarización de DNI (Argentina)

CREATE OR REPLACE FUNCTION standardize_dni(input_dni TEXT)

RETURNS TEXT AS $$

DECLARE

    clean_dni TEXT;

BEGIN

    -- Eliminar todos los caracteres no numéricos

    clean_dni := REGEXP_REPLACE(input_dni, '[^0-9]', '', 'g');

    

    -- Verificar longitud válida (7-8 dígitos)

    IF LENGTH(clean_dni) < 7 OR LENGTH(clean_dni) > 8 THEN

        RETURN NULL;

    END IF;

    

    -- Rellenar con ceros a la izquierda hasta completar 8 dígitos

    RETURN LPAD(clean_dni, 8, '0');

END;

$$ LANGUAGE plpgsql;



-- Estandarización de CUIL/CUIT (Argentina)

CREATE OR REPLACE FUNCTION standardize_cuil(input_cuil TEXT)

RETURNS TEXT AS $$

DECLARE

    clean_cuil TEXT;

BEGIN

    -- Eliminar todos los caracteres no numéricos

    clean_cuil := REGEXP_REPLACE(input_cuil, '[^0-9]', '', 'g');

    

    -- Verificar longitud

    IF LENGTH(clean_cuil) <> 11 THEN

        RETURN NULL;

    END IF;

    

    -- Formatear con guiones

    RETURN SUBSTRING(clean_cuil FROM 1 FOR 2) || '-' ||

           SUBSTRING(clean_cuil FROM 3 FOR 8) || '-' ||

           SUBSTRING(clean_cuil FROM 11 FOR 1);

END;

$$ LANGUAGE plpgsql;



-- Estandarización de pasaporte canadiense

CREATE OR REPLACE FUNCTION standardize_ca_passport(input_passport TEXT)

RETURNS TEXT AS $$

DECLARE

    clean_passport TEXT;

BEGIN

    -- Convertir a mayúsculas y eliminar espacios

    clean_passport := UPPER(TRIM(input_passport));

    

    -- Verificar formato (2 letras seguidas de 6 dígitos)

    IF NOT clean_passport ~ '^[A-Z]{2}[0-9]{6}$' THEN

        RETURN NULL;

    END IF;

    

    RETURN clean_passport;

END;

$$ LANGUAGE plpgsql;



-- Tabla para almacenar documentos estandarizados

CREATE TABLE person_identification (

    person_id SERIAL PRIMARY KEY,

    doc_type VARCHAR(20) NOT NULL,

    doc_country VARCHAR(2) NOT NULL,

    doc_number_raw VARCHAR(50) NOT NULL,

    doc_number_std VARCHAR(50),

    is_valid BOOLEAN DEFAULT FALSE,

    validation_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP

);



-- Índices para búsqueda eficiente

CREATE INDEX idx_person_id_doc_type ON person_identification(doc_type, doc_country);

CREATE INDEX idx_person_id_doc_number ON person_identification(doc_number_std);



-- Trigger para estandarizar automáticamente al insertar/actualizar

CREATE OR REPLACE FUNCTION standardize_document()

RETURNS TRIGGER AS $$

BEGIN

    IF NEW.doc_type = 'SSN' AND NEW.doc_country = 'US' THEN

        NEW.doc_number_std := standardize_ssn(NEW.doc_number_raw);

    ELSIF NEW.doc_type = 'DNI' AND NEW.doc_country = 'AR' THEN

        NEW.doc_number_std := standardize_dni(NEW.doc_number_raw);

    ELSIF NEW.doc_type IN ('CUIL', 'CUIT') AND NEW.doc_country = 'AR' THEN

        NEW.doc_number_std := standardize_cuil(NEW.doc_number_raw);

    ELSIF NEW.doc_type = 'PASSPORT' AND NEW.doc_country = 'CA' THEN

        NEW.doc_number_std := standardize_ca_passport(NEW.doc_number_raw);

    ELSE

        NEW.doc_number_std := NEW.doc_number_raw;

    END IF;

    

    -- Marcar como válido si se pudo estandarizar

    NEW.is_valid := NEW.doc_number_std IS NOT NULL;

    NEW.validation_date := CURRENT_TIMESTAMP;

    

    RETURN NEW;

END;

$$ LANGUAGE plpgsql;



CREATE TRIGGER before_person_identification_insert_update

BEFORE INSERT OR UPDATE ON person_identification

FOR EACH ROW EXECUTE FUNCTION standardize_document();
```

## Company identification

### International standards
- ISO 6523: Structure for organization identification
- LEI (Legal Entity Identifier): Global identifier for legal entities
- SWIFT/BIC: Codes for financial institutions

### Structure and format by country

#### United States
- EIN (Employer Identification Number): Format XX-XXXXXXX
- DUNS Number: 9 digits
- SEC CIK: Identifier for companies listed on the stock exchange

#### Canada
- Business Number (BN): Format XXXXXXXXRR001 (9 digits + 2 letters + 3 digits)
- Corporation Number: Alphanumeric assigned by Corporations Canada

#### Argentina
- CUIT (Companies): Format XX-XXXXXXXX-X (similar to CUIL but starts with 30, 33, etc.)
- IGJ/RPC: Registration number with the General Inspection of Justice

### Best practices for standardization

#### 1. Consistent format
- Define clear rules for separator characters
- Uniform handling of prefixes and suffixes
- Normalization of leading zeros

#### 2. Verification and validation
- Country-specific verification algorithms
- Queries against official records when possible
- Detection of obsolete or invalid formats

```python
import re

import requests

from datetime import datetime



class CompanyIdValidator:

    def __init__(self):

        self.patterns = {

            'us_ein': r'^\d{2}-?\d{7}$',

            'us_duns': r'^\d{9}$',

            'ca_bn': r'^\d{9}[A-Z]{2}\d{3}$',

            'ar_cuit': r'^\d{2}-?\d{8}-?\d{1}$'

        }

        

        # Prefijos válidos por tipo de empresa en Argentina

        self.ar_cuit_prefixes = ['30', '33', '34']

    

    def validate_format(self, id_type, company_id):

        """Valida el formato del identificador según el tipo"""

        if id_type not in self.patterns:

            return False, "Tipo de identificador no soportado"

        

        pattern = self.patterns[id_type]

        if re.match(pattern, company_id):

            # Validaciones adicionales específicas

            if id_type == 'ar_cuit':

                # Limpiar formato

                clean_id = re.sub(r'[^0-9]', '', company_id)

                # Verificar prefijo válido para empresas

                if clean_id[0:2] not in self.ar_cuit_prefixes:

                    return False, "Prefijo de CUIT inválido para empresa"

            

            return True, "Formato válido"

        else:

            return False, f"Formato inválido para {id_type}"

    

    def standardize_company_id(self, id_type, company_id):

        """Estandariza el formato del identificador según su tipo"""

        if not company_id:

            return None

            

        # Eliminar caracteres no alfanuméricos

        id_clean = re.sub(r'[^A-Z0-9]', '', company_id.upper())

        

        # Aplicar formato específico según tipo de identificador

        if id_type == 'us_ein':

            if len(id_clean) == 9:

                return f"{id_clean[0:2]}-{id_clean[2:9]}"

        

        elif id_type == 'ar_cuit':

            if len(id_clean) == 11:

                return f"{id_clean[0:2]}-{id_clean[2:10]}-{id_clean[10]}"

        

        # Para otros tipos, devolver el valor limpio

        return id_clean

    

    def validate_checksum(self, id_type, company_id):

        """Valida el dígito verificador para identificadores que lo incluyen"""

        if id_type == 'ar_cuit':

            # Algoritmo idéntico al de CUIL/CUIT personal

            doc_clean = re.sub(r'[^0-9]', '', company_id)

            if len(doc_clean) != 11:

                return False, "Longitud inválida"

                

            # Algoritmo de validación CUIT argentino

            factors = [5, 4, 3, 2, 7, 6, 5, 4, 3, 2]

            doc_digits = [int(doc_clean[i]) for i in range(10)]

            

            # Multiplicar cada dígito por su factor

            products = [doc_digits[i] * factors[i] for i in range(10)]

            sum_products = sum(products)

            

            # Calcular dígito verificador

            remainder = sum_products % 11

            check_digit = 11 - remainder

            if check_digit == 11:

                check_digit = 0

            

            # Verificar si coincide con el dígito proporcionado

            if check_digit == int(doc_clean[10]):

                return True, "Dígito verificador válido"

            else:

                return False, "Dígito verificador inválido"

                

        return True, "Validación no implementada para este tipo"

    

    def verify_company_existence(self, id_type, company_id, api_key=None):

        """

        Verifica la existencia de la empresa contra registros oficiales

        Nota: Esta es una función simulada, en producción conectaría con APIs reales

        """

        # Simulación de verificación

        if id_type == 'us_ein':

            # En producción: conexión con API del IRS (requiere autenticación especial)

            return {'verified': True, 'status': 'active', 'verification_date': datetime.now()}

        

        elif id_type == 'us_duns':

            # En producción: conexión con API de Dun & Bradstreet

            if api_key:

                # Implementación simulada

                return {'verified': True, 'status': 'active', 'verification_date': datetime.now()}

            else:

                return {'verified': False, 'status': 'api_key_required'}

        

        return {'verified': False, 'status': 'not_implemented'}



# Ejemplo de uso

validator = CompanyIdValidator()



# Ejemplos de identificadores de empresas

test_ids = [

    ('us_ein', '12-3456789'),

    ('us_duns', '123456789'),

    ('ar_cuit', '30-12345678-9')

]



for id_type, company_id in test_ids:

    is_valid_format, format_msg = validator.validate_format(id_type, company_id)

    print(f"ID Empresa: {company_id} ({id_type})")

    print(f"Formato: {format_msg}")

    

    if is_valid_format:

        standardized = validator.standardize_company_id(id_type, company_id)

        print(f"Estandarizado: {standardized}")

        

        if id_type == 'ar_cuit':

            is_valid_checksum, checksum_msg = validator.validate_checksum(id_type, company_id)

            print(f"Checksum: {checksum_msg}")

    

    print("-" * 40)
```

## Power Query M for standardization

```
// Funciones de estandarización para identificadores



// Estandarización de EIN (Estados Unidos)

StandardizeEIN = (id as text) as text => 

    let

        // Eliminar caracteres no numéricos

        CleanId = Text.Remove(id, {".", "-", " "}),

        // Verificar longitud

        ValidLength = Text.Length(CleanId) = 9,

        // Formatear con guión si la longitud es válida

        Result = if ValidLength then 

                    Text.Start(CleanId, 2) & "-" & Text.End(CleanId, 7)

                 else

                    null

    in

        Result,



// Estandarización de CUIT (Argentina)

StandardizeCUIT = (id as text) as text => 

    let

        // Eliminar caracteres no numéricos

        CleanId = Text.Remove(id, {".", "-", " "}),

        // Verificar longitud

        ValidLength = Text.Length(CleanId) = 11,

        // Formatear con guiones si la longitud es válida

        Result = if ValidLength then 

                    Text.Start(CleanId, 2) & "-" & 

                    Text.Middle(CleanId, 2, 8) & "-" & 

                    Text.End(CleanId, 1)

                 else

                    null

    in

        Result,



// Validación de dígito verificador para CUIT/CUIL

ValidateCUITCheckDigit = (id as text) as logical => 

    let

        // Eliminar caracteres no numéricos

        CleanId = Text.Remove(id, {".", "-", " "}),

        // Verificar longitud

        ValidLength = Text.Length(CleanId) = 11,

        // Cálculo del dígito verificador

        Result = if ValidLength then

                    let

                        // Factores de multiplicación

                        Factors = {5, 4, 3, 2, 7, 6, 5, 4, 3, 2},

                        // Obtener los primeros 10 dígitos

                        Digits = List.Transform(

                            List.FirstN(Text.ToList(CleanId), 10),

                            each Number.From(Text.From(_))

                        ),

                        // Multiplicar cada dígito por su factor

                        Products = List.Transform(

                            List.Positions(Digits),

                            each Digits{_} * Factors{_}

                        ),

                        // Sumar productos

                        SumProducts = List.Sum(Products),

                        // Calcular resto de división por 11

                        Remainder = Number.Mod(SumProducts, 11),

                        // Calcular dígito verificador

                        CalculatedCheckDigit = if Remainder = 0 then 0 else 11 - Remainder,

                        // Obtener dígito verificador del CUIT

                        ProvidedCheckDigit = Number.From(Text.End(CleanId, 1)),

                        // Comparar

                        IsValid = CalculatedCheckDigit = ProvidedCheckDigit

                    in

                        IsValid

                 else

                    false

    in

        Result,



// Función para estandarizar documentos en una tabla

StandardizeDocuments = (source as table) as table => 

    let

        // Añadir columna con documento estandarizado según tipo

        AddStandardizedColumn = Table.AddColumn(

            source, 

            "DocumentoEstandarizado", 

            each if [TipoDocumento] = "EIN" then StandardizeEIN([NumeroDocumento])

                else if [TipoDocumento] = "CUIT" then StandardizeCUIT([NumeroDocumento])

                else [NumeroDocumento],

            type text

        ),

        

        // Añadir columna con validación

        AddValidationColumn = Table.AddColumn(

            AddStandardizedColumn,

            "Valido",

            each if [TipoDocumento] = "CUIT" then ValidateCUITCheckDigit([NumeroDocumento])

                else if [TipoDocumento] = "EIN" then not (StandardizeEIN([NumeroDocumento]) = null)

                else true,

            type logical

        )

    in

        AddValidationColumn,



// Ejemplo de uso con datos de muestra

DatosMuestra = #table(

    {"ID", "TipoDocumento", "NumeroDocumento"},

    {

        {1, "EIN", "12-3456789"},

        {2, "EIN", "98.7654321"},

        {3, "CUIT", "30-12345678-9"},

        {4, "CUIT", "33.12345678.9"},

        {5, "DUNS", "123456789"}

    }

),



// Aplicar estandarización

DatosEstandarizados = StandardizeDocuments(DatosMuestra)



in

    DatosEstandarizados
```

## Specific challenges and solutions

### 1. International documents and multiple formats
Challenge: A person or company may have multiple valid identifiers.
Solution: Implement a table of multiple identifiers with a priority hierarchy.

```sql
CREATE TABLE entity_identifiers (

    entity_id INTEGER NOT NULL,

    entity_type CHAR(1) NOT NULL,  -- 'P' para persona, 'C' para compañía

    id_type VARCHAR(20) NOT NULL,

    id_country CHAR(2) NOT NULL,

    id_number VARCHAR(50) NOT NULL,

    id_number_std VARCHAR(50),

    is_primary BOOLEAN DEFAULT FALSE,

    is_valid BOOLEAN DEFAULT FALSE,

    CONSTRAINT pk_entity_identifiers PRIMARY KEY (entity_id, id_type, id_country)

);
```

### 2. Changes in historical formats
Challenge: Document formats change over time.
Solution: Maintain a history of formats and validation rules by date.

```python
def validate_with_historical_rules(doc_type, document, issue_date=None):

    """Valida un documento considerando reglas históricas según fecha de emisión"""

    current_date = datetime.now().date()

    

    if doc_type == 'ar_dni':

        # Antes de 1968 los DNI argentinos tenían formatos diferentes

        if issue_date and issue_date.year < 1968:

            # Aplicar reglas históricas

            pass

        # Entre 1968 y 2009 el formato era de 8 dígitos sin puntos

        elif issue_date and 1968 <= issue_date.year < 2009:

            return re.match(r'^\d{8}$', document) is not None

        # A partir de 2009 incorpora nuevo formato

        else:

            return re.match(r'^\d{7,8}$', document) is not None
```

### 3. Privacy and security
Challenge: Identity documents are sensitive data.
Solution: Implement masking and encryption techniques.

```python
def mask_sensitive_id(id_type, document):

    """Enmascara parcialmente un identificador para mostrar en interfaces"""

    if id_type == 'us_ssn':

        # Formato: XXX-XX-1234 (últimos 4 dígitos visibles)

        clean_id = re.sub(r'[^0-9]', '', document)

        if len(clean_id) == 9:

            return f"XXX-XX-{clean_id[-4:]}"

    

    elif id_type == 'ar_dni':

        # Formato: XX.XXX.123 (últimos 3 dígitos visibles)

        clean_id = re.sub(r'[^0-9]', '', document)

        if len(clean_id) == 8:

            return f"XX.XXX.{clean_id[-3:]}"

    

    # Por defecto enmascarar todo excepto los últimos 4 caracteres

    return f"{'X' * (len(document) - 4)}{document[-4:]}" if len(document) > 4 else "XXXX"
```

## Additional best practices

### 1. Maintenance of reference records
- Databases of valid prefixes by country
- History of changes in official formats

### 2. Verification with official sources
- Integration with government APIs when possible
- Periodic validation of active/inactive status

### 3. Quality monitoring
- Metrics on the rate of invalid identifiers
- Alerts for suspicious patterns (duplicates, incorrect formats)

### 4. Legal compliance
- Documentation of validation rules
- Auditing of access to sensitive data

Proper standardization of legal identifiers not only improves data quality but is also fundamental for regulatory compliance and risk management in any organization that handles information about individuals and companies on an international scale.
