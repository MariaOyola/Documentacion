### MÓDULO: SALES, RESERVATION, TICKETING (Ventas, Reservas y Tiquetes)

## Tablas
- reservation_status
- sale_channel
- fare_class
- fare
- ticket_status
- reservation
- reservation_passenger
- sale
- ticket
- ticket_segment
- seat_assignment
- baggage

Modela el proceso completo de venta de vuelos: desde la creación de reservas, asignación de pasajeros, generación de ventas, emisión de tiquetes, asignación de asientos y manejo de equipaje.

### Relaciones (de arriba hacia abajo)
- Una reserva tiene un estado y un canal de venta
- Una reserva puede tener múltiples pasajeros
- Una reserva genera una venta
- Una venta genera tiquetes
- Un tiquete pertenece a un pasajero y una tarifa
- Un tiquete tiene segmentos asociados a vuelos
- Un segmento de tiquete puede tener asiento asignado
- Un segmento de tiquete puede tener equipaje
- Una tarifa define precios entre aeropuertos y clases

### RELACIONES IMPORTANTES

reservation_status → reservation ← sale_channel  
                          ↓  
             reservation_passenger → person  

reservation → sale → ticket  
                        ↓  
            ticket_segment → flight_segment  

ticket → fare ← fare_class ← cabin_class  

ticket_segment → seat_assignment → aircraft_seat  

ticket_segment → baggage  

---

## Entidades clave

### Reservation_Status
Define el estado de la reserva (Ej: activa, cancelada, expirada).

- `status_code`
- `status_name`

**Reglas:**
- No pueden repetirse `status_code` ni `status_name`

---

### Sale_Channel
Define los canales de venta (Ej: web, agencia, app).

- `channel_code`
- `channel_name`

**Reglas:**
- No pueden repetirse `channel_code` ni `channel_name`

---

### Fare_Class
Define clases tarifarias (Ej: Economy Flex, Business Promo).

- Relación:
  - Pertenece a `cabin_class`
- Datos:
  - `fare_class_code`
  - `fare_class_name`
  - `is_refundable_by_default`

**Reglas:**
- No pueden repetirse `fare_class_code` ni `fare_class_name`

---

### Fare
Representa tarifas entre aeropuertos.

- Relación:
  - Pertenece a `airline`
  - Tiene origen y destino (`airport`)
  - Pertenece a `fare_class`
  - Usa `currency`
- Datos:
  - `fare_code`
  - `base_amount`
  - `valid_from`, `valid_to`
  - `baggage_allowance_qty`
  - penalidades

**Reglas importantes:**
- No se puede repetir `fare_code`
- El origen y destino deben ser diferentes
- `base_amount` debe ser mayor o igual a 0
- `baggage_allowance_qty` debe ser mayor o igual a 0
- Penalidades deben ser >= 0 (si existen)
- `valid_to` debe ser mayor o igual a `valid_from`

---

### Ticket_Status
Define el estado del tiquete (Ej: emitido, usado, cancelado).

- `status_code`
- `status_name`

**Reglas:**
- No pueden repetirse `status_code` ni `status_name`

---

### Reservation
Representa una reserva de vuelo.

- Relación:
  - Tiene `reservation_status`
  - Tiene `sale_channel`
  - Puede estar asociada a un `customer`
- Datos:
  - `reservation_code`
  - `booked_at`
  - `expires_at`

**Reglas importantes:**
- `reservation_code` es único
- `expires_at` debe ser mayor a `booked_at` (si existe)

---

### Reservation_Passenger
Relaciona pasajeros con una reserva.

- Relación:
  - Pertenece a `reservation`
  - Pertenece a `person`
- Datos:
  - `passenger_sequence_no`
  - `passenger_type` (ADULT, CHILD, INFANT)

**Reglas importantes:**
- No se puede repetir:
  - (`reservation_id`, `person_id`)
  - (`reservation_id`, `passenger_sequence_no`)
- `passenger_sequence_no` debe ser mayor a 0
- `passenger_type` solo permite: ADULT, CHILD, INFANT

---

### Sale
Representa la venta de una reserva.

- Relación:
  - Pertenece a `reservation`
  - Usa `currency`
- Datos:
  - `sale_code`
  - `sold_at`
  - `external_reference`

**Reglas importantes:**
- `sale_code` es único

---

### Ticket
Representa el tiquete emitido.

- Relación:
  - Pertenece a `sale`
  - Pertenece a `reservation_passenger`
  - Tiene `fare`
  - Tiene `ticket_status`
- Datos:
  - `ticket_number`
  - `issued_at`

**Reglas importantes:**
- `ticket_number` es único

---

### Ticket_Segment
Relaciona tiquetes con segmentos de vuelo.

- Relación:
  - Pertenece a `ticket`
  - Pertenece a `flight_segment`
- Datos:
  - `segment_sequence_no`
  - `fare_basis_code`

**Reglas importantes:**
- No se puede repetir:
  - (`ticket_id`, `segment_sequence_no`)
  - (`ticket_id`, `flight_segment_id`)
- `segment_sequence_no` debe ser mayor a 0

---

### Seat_Assignment
Asignación de asiento en un vuelo.

- Relación:
  - Pertenece a `ticket_segment`
  - Pertenece a `aircraft_seat`
- Datos:
  - `assigned_at`
  - `assignment_source` (AUTO, MANUAL, CUSTOMER)

**Reglas importantes:**
- Un ticket_segment solo puede tener un asiento
- Un asiento no puede asignarse dos veces en el mismo vuelo
- `assignment_source` solo permite: AUTO, MANUAL, CUSTOMER
- Usa clave foránea compuesta para evitar inconsistencias

---

### Baggage
Representa equipaje asociado a un segmento.

- Relación:
  - Pertenece a `ticket_segment`
- Datos:
  - `baggage_tag`
  - `baggage_type`
  - `baggage_status`
  - `weight_kg`

**Reglas importantes:**
- `baggage_tag` es único
- `baggage_type` solo permite:
  - CHECKED, CARRY_ON, SPECIAL
- `baggage_status` solo permite:
  - REGISTERED, LOADED, CLAIMED, LOST
- `weight_kg` debe ser mayor a 0

---