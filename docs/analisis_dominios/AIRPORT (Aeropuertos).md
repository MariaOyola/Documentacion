### MÓDULO: AIRPORT (Aeropuertos)

## Tablas
- airport
- terminal
- boarding_gate
- runway
- airport_regulation

Modela la infraestructura aeroportuaria, incluyendo aeropuertos, terminales, puertas de embarque, pistas y regulaciones.

### Relaciones (de arriba hacia abajo)
- Un aeropuerto tiene una dirección
- Un aeropuerto puede tener múltiples terminales
- Una terminal puede tener múltiples puertas de embarque
- Un aeropuerto puede tener múltiples pistas
- Un aeropuerto puede tener múltiples regulaciones

### RELACIONES IMPORTANTES

address → airport  
             ↓  
         terminal → boarding_gate  

airport → runway  
airport → airport_regulation  

---

## Entidades clave

### Airport
Representa un aeropuerto (Ej: El Dorado, JFK).

- Relación:
  - Pertenece a una `address`
- Datos:
  - `airport_name`
  - `iata_code` (3 caracteres)
  - `icao_code` (4 caracteres)
  - `is_active`

**Reglas importantes:**
- `iata_code` debe tener exactamente 3 caracteres (si no es NULL)
- `icao_code` debe tener exactamente 4 caracteres (si no es NULL)
- No pueden repetirse `iata_code` ni `icao_code`
- `is_active` permite manejar el estado del aeropuerto

---

### Terminal
Representa una terminal dentro de un aeropuerto (Ej: Terminal 1).

- Relación:
  - Pertenece a `airport`
- Datos:
  - `terminal_code`
  - `terminal_name`

**Reglas importantes:**
- No se puede repetir:
  - (`airport_id`, `terminal_code`)

---

### Boarding_Gate
Representa una puerta de embarque (Ej: A1, B12).

- Relación:
  - Pertenece a `terminal`
- Datos:
  - `gate_code`
  - `is_active`

**Reglas importantes:**
- No se puede repetir:
  - (`terminal_id`, `gate_code`)
- `is_active` indica si la puerta está en uso

---

### Runway
Representa una pista de aterrizaje/despegue.

- Relación:
  - Pertenece a `airport`
- Datos:
  - `runway_code`
  - `length_meters`
  - `surface_type`

**Reglas importantes:**
- No se puede repetir:
  - (`airport_id`, `runway_code`)
- `length_meters` debe ser mayor a 0

---

### Airport_Regulation
Representa regulaciones aplicables a un aeropuerto.

- Relación:
  - Pertenece a `airport`
- Datos:
  - `regulation_code`
  - `regulation_title`
  - `issuing_authority`
  - `effective_from`
  - `effective_to`

**Reglas importantes:**
- No se puede repetir:
  - (`airport_id`, `regulation_code`)
- `effective_to` debe ser mayor o igual a `effective_from` (si existe)

---