### MÓDULO: ZONA GEOGRÁFICA (Ubicación)

## Tablas
- continent
- country
- state_province
- city
- district
- address

Modela la jerarquía geográfica completa del mundo, desde continentes hasta direcciones específicas.

### Relaciones (de arriba hacia abajo)
- Un continente tiene muchos países
- Un país pertenece a un continente
- Un país tiene muchos estados/provincias
- Un estado tiene muchas ciudades
- Una ciudad tiene muchos distritos
- Un distrito tiene muchas direcciones

### Entidades clave

Representa regiones globales (Ej: América, Europa)
continent_code → abreviación (Ej: "SA")

### RELACIONES IMPORTANTES

continent
   ↓
country
   ↓
state_province
   ↓
city ← time_zone
   ↓
district
   ↓
address

## Entidades clave

### Continent
Representa regiones globales (Ej: América, Europa).

- `continent_code`: abreviación (Ej: "SA")

---

### Country
Representa un país (Ej: Colombia).

- Relación: pertenece a un continente
- Códigos estándar:
  - `iso_alpha2`: CO
  - `iso_alpha3`: COL

**Reglas importantes:**
- No pueden repetirse los códigos (`iso_alpha2`, `iso_alpha3`)
- No puede repetirse el nombre del país

---

### State_Province
Representa departamentos o estados (Ej: Cundinamarca).

- Relación: depende de un país

**Reglas:**
- No puede haber dos estados con el mismo nombre dentro del mismo país

---

### City
Representa una ciudad (Ej: Bogotá).

- Depende de:
  - Estado o provincia
  - Zona horaria (`time_zone`)

**Relación importante:**
- Se conecta con la tabla `time_zone`

---

### District
Representa barrios o localidades (Ej: Chapinero).

- Relación: pertenece a una ciudad

---

### Address
Representa la dirección exacta (nivel más bajo de la jerarquía).

**Campos importantes:**
- `address_line_1`: dirección principal
- `latitude`: latitud geográfica
- `longitude`: longitud geográfica

**Reglas:**
- Latitud debe estar entre -90 y 90
- Longitud debe estar entre -180 y 180

-------------------------------------------------------

### MÓDULO: AIRLINE (Aerolíneas)

## Tablas
- airline

Modela la información de las aerolíneas, incluyendo su país de origen, códigos de identificación y estado operativo.

### Relaciones (de arriba hacia abajo)
- Una aerolínea pertenece a un país
- Un país puede tener muchas aerolíneas

### RELACIONES IMPORTANTES

country
   ↓
airline

## Entidades clave

### Airline
Representa una aerolínea (Ej: Avianca, LATAM).

- Relación: pertenece a un país (`home_country_id`)
- Identificadores:
  - `airline_code`: código interno del sistema
  - `iata_code`: código estándar IATA (2 caracteres)
  - `icao_code`: código estándar ICAO (3 caracteres)
- `airline_name`: nombre de la aerolínea
- `is_active`: indica si la aerolínea está operativa

**Reglas importantes:**
- No pueden repetirse:
  - `airline_code`
  - `airline_name`
  - `iata_code`
  - `icao_code`
- El `iata_code` debe tener exactamente 2 caracteres (si no es NULL)
- El `icao_code` debe tener exactamente 3 caracteres (si no es NULL)
- Toda aerolínea debe estar asociada a un país (`home_country_id` obligatorio)
- El campo `is_active` permite manejar el estado sin eliminar el registro

--------------------------------------------------

### MÓDULO: IDENTITY (Identidad de Personas)

## Tablas
- person_type
- document_type
- contact_type
- person
- person_document
- person_contact

Modela la información de identidad de las personas, incluyendo su tipo, datos personales, documentos oficiales y medios de contacto.

### Relaciones (de arriba hacia abajo)
- Una persona tiene un tipo de persona
- Una persona puede tener una nacionalidad (país)
- Una persona puede tener múltiples documentos
- Un documento pertenece a una persona
- Un documento tiene un tipo de documento
- Una persona puede tener múltiples contactos
- Un contacto pertenece a una persona
- Un contacto tiene un tipo de contacto

### RELACIONES IMPORTANTES

person_type → person ← country (nacionalidad)  
                    ↓  
            person_document ← document_type  
                    ↓  
            person_contact ← contact_type  

---

## Entidades clave

### Person_Type
Define el tipo de persona (Ej: pasajero, empleado).

- `type_code`: código interno
- `type_name`: nombre del tipo

**Reglas:**
- No pueden repetirse `type_code` ni `type_name`

---

### Document_Type
Define los tipos de documentos (Ej: cédula, pasaporte).

- `type_code`: código del documento
- `type_name`: nombre del documento

**Reglas:**
- No pueden repetirse `type_code` ni `type_name`

---

### Contact_Type
Define los tipos de contacto (Ej: email, teléfono).

- `type_code`: código del contacto
- `type_name`: nombre del tipo de contacto

**Reglas:**
- No pueden repetirse `type_code` ni `type_name`

---

### Person
Representa una persona en el sistema.

- Relación:
  - Pertenece a `person_type`
  - Puede tener nacionalidad (`country`)
- Datos personales:
  - `first_name`, `last_name`
  - `birth_date`
  - `gender_code` (F, M, X)

**Reglas importantes:**
- `gender_code` es obligatorio y solo permite valores: F, M, X
- Puede existir unicidad natural:
  - (`first_name`, `last_name`, `birth_date`) cuando `birth_date` no es NULL
- Maneja eliminación lógica con `deleted_at`

---

### Person_Document
Representa los documentos de una persona.

- Relación:
  - Pertenece a `person`
  - Tiene un `document_type`
  - Puede tener país emisor (`country`)
- Datos:
  - `document_number`
  - `issued_on`
  - `expires_on`

**Reglas importantes:**
- No se puede repetir:
  - (`document_type_id`, `issuing_country_id`, `document_number`)
- La fecha de expiración debe ser mayor o igual a la de emisión (si ambas existen)

---

### Person_Contact
Representa los medios de contacto de una persona.

- Relación:
  - Pertenece a `person`
  - Tiene un `contact_type`
- Datos:
  - `contact_value` (ej: correo, teléfono)
  - `is_primary` (indica si es el principal)

**Reglas importantes:**
- No se puede repetir:
  - (`person_id`, `contact_type_id`, `contact_value`)
- Una persona puede tener múltiples contactos, pero se puede marcar uno como principal

------------------------------------------------

### MÓDULO: SECURITY (Seguridad y Acceso)

## Tablas
- user_status
- security_role
- security_permission
- user_account
- user_role
- role_permission

Modela la autenticación, autorización y control de acceso del sistema, incluyendo usuarios, estados, roles y permisos.

### Relaciones (de arriba hacia abajo)
- Un usuario pertenece a una persona
- Un usuario tiene un estado
- Un usuario puede tener múltiples roles
- Un rol puede estar asignado a múltiples usuarios
- Un rol puede tener múltiples permisos
- Un permiso puede pertenecer a múltiples roles

---

## Entidades clave

### User_Status
Define el estado de una cuenta de usuario (Ej: activo, inactivo, bloqueado).

- `status_code`: código del estado
- `status_name`: nombre del estado

**Reglas:**
- No pueden repetirse `status_code` ni `status_name`

---

### Security_Role
Define los roles del sistema (Ej: admin, operador, cliente).

- `role_code`: código del rol
- `role_name`: nombre del rol
- `role_description`: descripción del rol

**Reglas:**
- No pueden repetirse `role_code` ni `role_name`

---

### Security_Permission
Define los permisos del sistema (Ej: crear usuario, ver reportes).

- `permission_code`: código del permiso
- `permission_name`: nombre del permiso
- `permission_description`: descripción del permiso

**Reglas:**
- No pueden repetirse `permission_code` ni `permission_name`

---

### User_Account
Representa la cuenta de acceso de un usuario.

- Relación:
  - Pertenece a `person`
  - Tiene un `user_status`
- Datos:
  - `username`
  - `password_hash`
  - `last_login_at`

**Reglas importantes:**
- No puede haber más de una cuenta por persona (`person_id` único)
- No puede repetirse el `username`
- `password_hash` almacena la contraseña en forma segura (no texto plano)
- Maneja auditoría con `created_by` y `updated_by` (auto-referencia)

---

### User_Role
Relaciona usuarios con roles (tabla intermedia).

- Relación:
  - Pertenece a `user_account`
  - Pertenece a `security_role`
- Datos:
  - `assigned_at`
  - `assigned_by_user_id`

**Reglas importantes:**
- No se puede repetir la combinación (`user_account_id`, `security_role_id`)
- Permite asignar múltiples roles a un usuario

---

### Role_Permission
Relaciona roles con permisos (tabla intermedia).

- Relación:
  - Pertenece a `security_role`
  - Pertenece a `security_permission`
- Datos:
  - `granted_at`

**Reglas importantes:**
- No se puede repetir la combinación (`security_role_id`, `security_permission_id`)
- Permite que un rol tenga múltiples permisos

---

## Auditoría (integración global)

- Todas las tablas incluyen:
  - `created_at`, `updated_at`
  - `created_by`, `updated_by`
  - `deleted_at`
- Los campos `created_by` y `updated_by` están relacionados con `user_account`
- Permite trazabilidad completa de quién creó o modificó cada registro

------

### MÓDULO: CUSTOMER AND LOYALTY (Clientes y Fidelización)

## Tablas
- customer_category
- benefit_type
- loyalty_program
- loyalty_tier
- customer
- loyalty_account
- loyalty_account_tier
- miles_transaction
- customer_benefit

Modela la gestión de clientes y programas de fidelización, incluyendo categorías, acumulación de millas, niveles (tiers) y beneficios.

### Relaciones (de arriba hacia abajo)
- Un cliente pertenece a una aerolínea y a una persona
- Un cliente puede tener una categoría
- Una aerolínea tiene programas de lealtad
- Un programa de lealtad tiene niveles (tiers)
- Un cliente puede tener cuentas de lealtad
- Una cuenta de lealtad pertenece a un programa
- Una cuenta puede tener historial de niveles
- Una cuenta genera transacciones de millas
- Un cliente puede recibir beneficios

## Entidades clave

### Customer_Category
Define categorías de clientes (Ej: regular, corporativo, VIP).

- `category_code`: código de la categoría
- `category_name`: nombre de la categoría

**Reglas:**
- No pueden repetirse `category_code` ni `category_name`

---

### Benefit_Type
Define tipos de beneficios (Ej: upgrade, acceso a sala VIP).

- `benefit_code`: código del beneficio
- `benefit_name`: nombre del beneficio
- `benefit_description`: descripción

**Reglas:**
- No pueden repetirse `benefit_code` ni `benefit_name`

---

### Loyalty_Program
Representa un programa de fidelización de una aerolínea.

- Relación:
  - Pertenece a `airline`
  - Usa una moneda (`currency`)
- Datos:
  - `program_code`
  - `program_name`
  - `expiration_months`

**Reglas importantes:**
- No se pueden repetir:
  - (`airline_id`, `program_code`)
  - (`airline_id`, `program_name`)
- `expiration_months` debe ser mayor a 0 (si existe)

---

### Loyalty_Tier
Representa los niveles dentro de un programa (Ej: Silver, Gold, Platinum).

- Relación:
  - Pertenece a `loyalty_program`
- Datos:
  - `tier_code`
  - `tier_name`
  - `priority_level`
  - `required_miles`

**Reglas importantes:**
- No se pueden repetir:
  - (`loyalty_program_id`, `tier_code`)
  - (`loyalty_program_id`, `tier_name`)
- `priority_level` debe ser mayor a 0
- `required_miles` debe ser mayor o igual a 0

---

### Customer
Representa un cliente de una aerolínea.

- Relación:
  - Pertenece a `airline`
  - Pertenece a `person`
  - Puede tener `customer_category`
- Datos:
  - `customer_since`

**Reglas importantes:**
- No puede repetirse:
  - (`airline_id`, `person_id`)
- Un cliente es único por aerolínea

---

### Loyalty_Account
Representa la cuenta de fidelización de un cliente.

- Relación:
  - Pertenece a `customer`
  - Pertenece a `loyalty_program`
- Datos:
  - `account_number`
  - `opened_at`

**Reglas importantes:**
- `account_number` es único
- No se puede repetir:
  - (`customer_id`, `loyalty_program_id`)

---

### Loyalty_Account_Tier
Historial de niveles (tiers) de una cuenta de fidelización.

- Relación:
  - Pertenece a `loyalty_account`
  - Pertenece a `loyalty_tier`
- Datos:
  - `assigned_at`
  - `expires_at`

**Reglas importantes:**
- No se puede repetir:
  - (`loyalty_account_id`, `assigned_at`)
- `expires_at` debe ser mayor a `assigned_at` (si existe)
- Solo puede existir un tier activo por cuenta:
  - (`expires_at IS NULL AND deleted_at IS NULL`)

---

### Miles_Transaction
Representa movimientos de millas.

- Relación:
  - Pertenece a `loyalty_account`
- Datos:
  - `transaction_type` (EARN, REDEEM, ADJUST)
  - `miles_delta`
  - `occurred_at`
  - `reference_code`

**Reglas importantes:**
- `transaction_type` solo permite: EARN, REDEEM, ADJUST
- `miles_delta` no puede ser 0

---

### Customer_Benefit
Representa beneficios otorgados a clientes.

- Relación:
  - Pertenece a `customer`
  - Pertenece a `benefit_type`
- Datos:
  - `granted_at`
  - `expires_at`
  - `notes`

**Reglas importantes:**
- No se puede repetir:
  - (`customer_id`, `benefit_type_id`, `granted_at`)
- `expires_at` debe ser mayor a `granted_at` (si existe)

---

