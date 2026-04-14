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

