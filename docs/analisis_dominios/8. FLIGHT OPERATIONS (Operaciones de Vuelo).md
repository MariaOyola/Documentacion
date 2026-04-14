### MÓDULO: FLIGHT OPERATIONS (Operaciones de Vuelo)

## Tablas
- flight_status
- delay_reason_type
- flight
- flight_segment
- flight_delay

Modela la operación de vuelos, incluyendo estados, segmentos, horarios y gestión de retrasos.

### Relaciones (de arriba hacia abajo)
- Un vuelo pertenece a una aerolínea
- Un vuelo utiliza una aeronave
- Un vuelo tiene un estado
- Un vuelo tiene un origen y destino
- Un vuelo puede tener múltiples segmentos
- Un segmento pertenece a un vuelo
- Un segmento puede tener retrasos
- Un retraso tiene un tipo de razón

### RELACIONES IMPORTANTES

airline → flight ← aircraft  
              ↓  
        flight_status  

flight → flight_segment  
             ↓  
      flight_delay ← delay_reason_type  

flight → airport (origen/destino)  
flight_segment → airport (origen/destino)  

---

## Entidades clave

### Flight_Status
Define el estado del vuelo (Ej: programado, en vuelo, cancelado).

- `status_code`
- `status_name`

**Reglas:**
- No pueden repetirse `status_code` ni `status_name`

---

### Delay_Reason_Type
Define las razones de retraso (Ej: clima, mantenimiento, tráfico aéreo).

- `reason_code`
- `reason_name`

**Reglas:**
- No pueden repetirse `reason_code` ni `reason_name`

---

### Flight
Representa un vuelo en una fecha específica.

- Relación:
  - Pertenece a `airline`
  - Usa una `aircraft`
  - Tiene un `flight_status`
  - Tiene aeropuerto de origen y destino
- Datos:
  - `flight_number`
  - `service_date`

**Reglas importantes:**
- No se puede repetir:
  - (`airline_id`, `flight_number`, `service_date`)
- El aeropuerto de origen y destino deben ser diferentes
- Se incluye origen/destino directo para consultas rápidas (sin depender de segmentos)

---

### Flight_Segment
Representa tramos de un vuelo (Ej: Bogotá → Lima → Santiago).

- Relación:
  - Pertenece a `flight`
  - Tiene aeropuerto de origen y destino
- Datos:
  - `segment_number`
  - `scheduled_departure_at`
  - `scheduled_arrival_at`
  - `actual_departure_at`
  - `actual_arrival_at`

**Reglas importantes:**
- No se puede repetir:
  - (`flight_id`, `segment_number`)
- El aeropuerto de origen y destino deben ser diferentes
- La hora programada de llegada debe ser mayor a la de salida
- La hora real de llegada debe ser mayor o igual a la de salida (si ambas existen)

---

### Flight_Delay
Representa retrasos en los segmentos de vuelo.

- Relación:
  - Pertenece a `flight_segment`
  - Tiene un `delay_reason_type`
- Datos:
  - `reported_at`
  - `delay_minutes`
  - `notes`

**Reglas importantes:**
- `delay_minutes` debe ser mayor a 0
- Permite registrar múltiples retrasos por segmento

---