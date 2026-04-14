### MÓDULO: AIRCRAFT (Aeronaves)

## Tablas
- aircraft_manufacturer
- aircraft_model
- cabin_class
- aircraft
- aircraft_cabin
- aircraft_seat
- maintenance_provider
- maintenance_type
- maintenance_event

Modela las aeronaves, su estructura interna (cabinas y asientos) y la gestión de mantenimiento.

### Relaciones (de arriba hacia abajo)
- Un fabricante tiene múltiples modelos de aeronaves
- Un modelo pertenece a un fabricante
- Una aerolínea tiene múltiples aeronaves
- Una aeronave pertenece a un modelo
- Una aeronave tiene múltiples cabinas
- Una cabina pertenece a una clase (económica, ejecutiva, etc.)
- Una cabina tiene múltiples asientos
- Una aeronave puede tener múltiples eventos de mantenimiento
- Un mantenimiento tiene un tipo y puede tener un proveedor

### RELACIONES IMPORTANTES

aircraft_manufacturer → aircraft_model  
                              ↓  
airline → aircraft → aircraft_cabin → aircraft_seat  
                         ↓  
                  cabin_class  

aircraft → maintenance_event ← maintenance_type  
                ↓  
     maintenance_provider  

---

## Entidades clave

### Aircraft_Manufacturer
Representa el fabricante de aeronaves (Ej: Boeing, Airbus).

- `manufacturer_name`: nombre del fabricante

**Reglas:**
- No puede repetirse el nombre del fabricante

---

### Aircraft_Model
Representa un modelo de aeronave (Ej: A320, B737).

- Relación:
  - Pertenece a `aircraft_manufacturer`
- Datos:
  - `model_code`
  - `model_name`
  - `max_range_km`

**Reglas importantes:**
- No se puede repetir:
  - (`aircraft_manufacturer_id`, `model_code`)
  - (`aircraft_manufacturer_id`, `model_name`)
- `max_range_km` debe ser mayor a 0 (si existe)

---

### Cabin_Class
Define las clases de cabina (Ej: Economy, Business, First).

- `class_code`
- `class_name`

**Reglas:**
- No pueden repetirse `class_code` ni `class_name`

---

### Aircraft
Representa una aeronave específica.

- Relación:
  - Pertenece a `airline`
  - Pertenece a `aircraft_model`
- Datos:
  - `registration_number`
  - `serial_number`
  - `in_service_on`
  - `retired_on`

**Reglas importantes:**
- No pueden repetirse:
  - `registration_number`
  - `serial_number`
- `retired_on` debe ser mayor o igual a `in_service_on` (si existe)

---

### Aircraft_Cabin
Representa una cabina dentro de la aeronave.

- Relación:
  - Pertenece a `aircraft`
  - Pertenece a `cabin_class`
- Datos:
  - `cabin_code`
  - `deck_number`

**Reglas importantes:**
- No se puede repetir:
  - (`aircraft_id`, `cabin_code`)
- `deck_number` debe ser mayor a 0

---

### Aircraft_Seat
Representa un asiento dentro de una cabina.

- Relación:
  - Pertenece a `aircraft_cabin`
- Datos:
  - `seat_row_number`
  - `seat_column_code`
  - `is_window`
  - `is_aisle`
  - `is_exit_row`

**Reglas importantes:**
- No se puede repetir:
  - (`aircraft_cabin_id`, `seat_row_number`, `seat_column_code`)
- `seat_row_number` debe ser mayor a 0

---

### Maintenance_Provider
Representa proveedores de mantenimiento.

- Relación:
  - Puede tener una `address`
- Datos:
  - `provider_name`
  - `contact_name`

**Reglas:**
- No puede repetirse el nombre del proveedor

---

### Maintenance_Type
Define tipos de mantenimiento (Ej: preventivo, correctivo).

- `type_code`
- `type_name`

**Reglas:**
- No pueden repetirse `type_code` ni `type_name`

---

### Maintenance_Event
Representa eventos de mantenimiento de una aeronave.

- Relación:
  - Pertenece a `aircraft`
  - Pertenece a `maintenance_type`
  - Puede tener `maintenance_provider`
- Datos:
  - `status_code` (PLANNED, IN_PROGRESS, COMPLETED, CANCELLED)
  - `started_at`
  - `completed_at`
  - `notes`

**Reglas importantes:**
- `status_code` solo permite:
  - PLANNED, IN_PROGRESS, COMPLETED, CANCELLED
- `completed_at` debe ser mayor o igual a `started_at` (si existe)

---