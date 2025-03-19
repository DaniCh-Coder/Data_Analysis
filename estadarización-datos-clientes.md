# Estandarización de datos de clientes

## Índice
- [ID de Cliente](#id-de-cliente)
- [Nombre](#nombre)
- [Documento de Identidad](#documento-de-identidad)
- [Domicilio](#domicilio-calle-número)
- [Código Postal](#código-postal-zip-code)
- [E-mail](#e-mail)
- [Implementación en Power BI con Power Query](#implementación-en-power-bi-con-power-query)
- [Consideraciones adicionales](#consideraciones-adicionales)

## ID de Cliente

**Mejores prácticas:**
- Formato único y consistente (numérico, alfanumérico)
- Sin espacios ni caracteres especiales
- Longitud fija cuando sea posible
- Prefijos para segmentar tipos de clientes

**Estándares vigentes:**
- No hay un estándar global único para IDs internos
- En sistemas bancarios suelen usarse números de 10-16 dígitos
- En retail, códigos alfanuméricos con prefijos por categoría

### Power BI
```
// Eliminar espacios y estandarizar formato
ID Limpio = 
TRIM(
    UPPER(
        SUBSTITUTE([ID Cliente], " ", "")
    )
)
```

### Python
```python
# Estandarización de ID
def estandarizar_id(df):
    # Convertir a string, eliminar espacios y aplicar mayúsculas
    df['ID_estandarizado'] = df['ID'].astype(str).str.strip().str.upper()
    # Eliminar caracteres especiales
    df['ID_estandarizado'] = df['ID_estandarizado'].str.replace(r'[^\w]', '', regex=True)
    return df
```

## Nombre

**Mejores prácticas:**
- Separar en campos (Nombre, Apellido1, Apellido2)
- Capitalización consistente (primera letra mayúscula)
- Eliminar títulos (Dr., Sr., etc.) o almacenarlos en campo separado
- Eliminar caracteres especiales manteniendo acentos y ñ

**Estándares vigentes:**
- ISO/IEC 8859-1 para caracteres latinos
- Formato NIST para almacenamiento de nombres personales
- Estándar vCard (RFC 6350) para intercambio de información personal

### Power BI
```
// Capitalizar nombre
Nombre Estandarizado = 
PROPER(
    TRIM([Nombre])
)

// Separar nombre completo
Nombre = IF(FIND(" ", [Nombre Completo]) > 0, 
    LEFT([Nombre Completo], FIND(" ", [Nombre Completo]) - 1), 
    [Nombre Completo])

Apellido = IF(FIND(" ", [Nombre Completo]) > 0,
    RIGHT([Nombre Completo], LEN([Nombre Completo]) - FIND(" ", [Nombre Completo])),
    "")
```

### Python
```python
def estandarizar_nombres(df):
    # Dividir nombre completo
    if 'Nombre_Completo' in df.columns:
        df[['Nombre', 'Apellido']] = df['Nombre_Completo'].str.split(' ', n=1, expand=True)
    
    # Capitalizar correctamente
    for col in ['Nombre', 'Apellido']:
        if col in df.columns:
            df[col] = df[col].str.title()
            # Manejo especial para nombres con prefijos como "de la", "del", etc.
            df[col] = df[col].str.replace(r'\bDe La\b', 'de la', regex=True)
            df[col] = df[col].str.replace(r'\bDel\b', 'del', regex=True)
    
    return df
```

## Documento de Identidad

**Mejores prácticas:**
- Formato específico según país/tipo de documento
- Eliminar puntos, espacios y guiones
- Almacenar tipo de documento en campo separado
- Validar mediante algoritmos de verificación si existen

**Estándares vigentes:**
- ISO/IEC 7501 para documentos de viaje legibles por máquina
- Estándares nacionales (DNI en España, CPF en Brasil, CURP en México, etc.)

### Power BI
```
// Limpieza de DNI/documento
Documento Estandarizado = 
UPPER(
    SUBSTITUTE(
        SUBSTITUTE(
            SUBSTITUTE([Documento], ".", ""), 
            "-", ""
        ), 
        " ", ""
    )
)
```

### Python
```python
def estandarizar_documento(df, columna='Documento'):
    # Convertir a string
    df[columna] = df[columna].astype(str)
    
    # Eliminar espacios, puntos y guiones
    df[columna + '_estandarizado'] = df[columna].str.replace(r'[\s\.\-]', '', regex=True)
    
    # Convertir a mayúsculas
    df[columna + '_estandarizado'] = df[columna + '_estandarizado'].str.upper()
    
    return df
```

## Domicilio (Calle, Número)

**Mejores prácticas:**
- Dividir en componentes (calle, número, piso, etc.)
- Abreviaturas estándar (Av., Blvd., etc.)
- Normalización de direcciones contra base oficial
- Geocodificación para validación

**Estándares vigentes:**
- ISO 19160 para direcciones postales
- UPU (Unión Postal Universal) S42 para intercambio internacional
- USPS para abreviaturas en Estados Unidos
- Estándares nacionales de correos

### Power BI
```
// Estandarizar abreviaturas comunes en direcciones
Dirección Estandarizada = 
SUBSTITUTE(
    SUBSTITUTE(
        SUBSTITUTE(
            PROPER(TRIM([Domicilio])),
            "Avenida", "Av."
        ),
        "Calle", "C."
    ),
    "Boulevard", "Blvd."
)
```

### Python
```python
def estandarizar_direcciones(df, columna='Domicilio'):
    # Capitalizar adecuadamente
    df[columna] = df[columna].str.title()
    
    # Diccionario de abreviaturas estándar
    abreviaturas = {
        r'\bAvenida\b': 'Av.', 
        r'\bBoulevard\b': 'Blvd.',
        r'\bCalle\b': 'C.',
        r'\bApartamento\b': 'Apto.',
        r'\bEdificio\b': 'Edif.'
    }
    
    # Aplicar sustituciones
    for patron, abreviatura in abreviaturas.items():
        df[columna] = df[columna].str.replace(patron, abreviatura, regex=True)
    
    # Extraer número si es necesario
    df['Numero'] = df[columna].str.extract(r'(\d+)')
    df['Calle'] = df[columna].str.replace(r'\s+\d+.*$', '', regex=True)
    
    return df
```

## Código Postal (ZIP Code)

**Mejores prácticas:**
- Formato fijo según país
- Validación contra base oficial de códigos postales
- Almacenar sin espacios o con formato consistente
- Relacionar con localidad/provincia para validación cruzada

**Estándares vigentes:**
- S42 de la UPU para códigos postales internacionales
- Formatos específicos por país (5 dígitos en USA, alfanumérico en UK)

### Power BI
```
// Estandarizar ZIP codes
ZIP Estandarizado = 
IF(
    LEN([ZIP Code]) = 4, 
    "0" & [ZIP Code], 
    [ZIP Code]
)
```

### Python
```python
def estandarizar_zipcode(df, columna='zip_code', pais='USA'):
    # Eliminar espacios
    df[columna] = df[columna].astype(str).str.strip()
    
    # Aplicar formato según país
    if pais == 'USA':
        # Asegurar 5 dígitos para USA
        df[columna] = df[columna].str.replace(r'[^\d]', '', regex=True)
        df[columna] = df[columna].str.zfill(5)
    elif pais == 'UK':
        # Formato UK: AA9A 9AA
        df[columna] = df[columna].str.upper()
        # Aquí se podrían agregar reglas de formateo específicas
    
    return df
```

## E-mail

**Mejores prácticas:**
- Convertir a minúsculas
- Validar formato mediante expresiones regulares
- Verificar existencia de dominio
- Eliminar espacios y caracteres no permitidos

**Estándares vigentes:**
- RFC 5322 para formato de correo electrónico
- RFC 3696 para restricciones en direcciones de correo
- IDNA para dominios internacionalizados

### Power BI
```
// Estandarizar correo electrónico
Email Estandarizado = 
LOWER(
    TRIM([e-mail])
)

// Validar formato básico
Email Valido = 
IF(
    CONTAINSSTRING([Email Estandarizado], "@") && 
    CONTAINSSTRING([Email Estandarizado], "."), 
    TRUE, 
    FALSE
)
```

### Python
```python
import re

def estandarizar_email(df, columna='e-mail'):
    # Convertir a string y minúsculas
    df[columna] = df[columna].astype(str).str.lower().str.strip()
    
    # Validación básica con regex
    patron_email = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    
    # Crear columna de validación
    df['email_valido'] = df[columna].apply(lambda x: bool(re.match(patron_email, x)))
    
    return df
```

## Implementación en Power BI con Power Query

Para realizar una estandarización completa en Power BI, el enfoque recomendado es usar Power Query. A continuación, muestro cómo implementar una solución completa:

```
// Función para estandarización completa de datos de clientes
let
    EstandarizarDatosClientes = (Tabla as table) as table =>
    let
        // Estandarizar ID
        ConID = Table.TransformColumns(Tabla, {
            {"ID", each Text.Upper(Text.Trim(Text.Replace(_, " ", "")))}
        }),
        
        // Estandarizar Nombre
        ConNombre = Table.TransformColumns(ConID, {
            {"Nombre", each Text.Proper(Text.Trim(_))}
        }),
        
        // Separar Nombre si es necesario
        ConNombreSeparado = if Table.HasColumns(ConNombre, "Nombre_Completo") then
            Table.SplitColumn(ConNombre, "Nombre_Completo", Splitter.SplitTextByDelimiter(" ", QuoteStyle.None), {"Nombre", "Apellido"})
        else
            ConNombre,
        
        // Estandarizar Documento
        ConDocumento = Table.TransformColumns(ConNombreSeparado, {
            {"Documento", each Text.Upper(Text.Remove(Text.Remove(Text.Remove(_, "."), "-"), " "))}
        }),
        
        // Estandarizar Domicilio
        ConDomicilio = Table.TransformColumns(ConDocumento, {
            {"Domicilio", each Text.Proper(Text.Trim(_))}
        }),
        
        // Abreviaturas en domicilio
        ConDomicilioAbrev = Table.TransformColumns(ConDomicilio, {
            {"Domicilio", each 
                Text.Replace(
                    Text.Replace(
                        Text.Replace(_, "Avenida", "Av."), 
                        "Calle", "C."
                    ),
                    "Boulevard", "Blvd."
                )
            }
        }),
        
        // Estandarizar ZIP code
        ConZIP = Table.TransformColumns(ConDomicilioAbrev, {
            {"zip_code", each if Text.Length(Text.Trim(_)) = 4 then "0" & Text.Trim(_) else Text.Trim(_)}
        }),
        
        // Estandarizar Email
        ConEmail = Table.TransformColumns(ConZIP, {
            {"e-mail", each Text.Lower(Text.Trim(_))}
        }),
        
        // Validar Email
        ConEmailValidado = Table.AddColumn(ConEmail, "Email_Valido", each 
            Text.Contains([e-mail], "@") and Text.Contains([e-mail], "."))
    in
        ConEmailValidado
in
    EstandarizarDatosClientes
```

## Consideraciones adicionales

### Validación y monitoreo

- Implementar reglas de validación de datos después de la estandarización
- Establecer métricas de calidad (% registros válidos, % campos completos)
- Monitorear cambios en los datos a lo largo del tiempo
- Documentar todas las transformaciones realizadas

### Gestión de errores

- Crear campos para identificar registros problemáticos
- Implementar procesos de corrección manual o asistida
- Establecer flujos para manejar excepciones
- Definir políticas para valores faltantes

### Mantenimiento continuo

- Revisar periódicamente los estándares aplicados
- Actualizar diccionarios y reglas según necesidades del negocio
- Documentar metadatos y transformaciones aplicadas
- Capacitar al equipo sobre las reglas de estandarización
