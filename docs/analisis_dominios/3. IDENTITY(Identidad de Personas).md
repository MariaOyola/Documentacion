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