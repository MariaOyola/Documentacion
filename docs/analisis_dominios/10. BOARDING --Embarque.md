### MÓDULO: BOARDING (Embarque)

## Tablas
- boarding_group
- check_in_status
- check_in
- boarding_pass
- boarding_validation

Modela el proceso de check-in y embarque de pasajeros, incluyendo asignación de grupos, generación de pases de abordar y validación en puerta.

### Relaciones (de arriba hacia abajo)
- Un check-in pertenece a un segmento de tiquete
- Un check-in tiene un estado
- Un check-in puede tener un grupo de embarque
- Un check-in genera un pase de abordar
- Un pase de abordar puede ser validado en puerta
- Una validación puede estar asociada a una puerta de embarque

### RELACIONES IMPORTANTES

ticket_segment → check_in ← check_in_status  
                     ↓  
              boarding_group  

check_in → boarding_pass  
                ↓  
      boarding_validation → boarding_gate  

---

## Entidades clave

### Boarding_Group
Define los grupos de embarque (Ej: Grupo A, Grupo B).

- `group_code`
- `group_name`
- `sequence_no` (orden de embarque)

**Reglas:**
- No pueden repetirse `group_code` ni `group_name`
- `sequence_no` debe ser mayor a 0

---

### Check_In_Status
Define el estado del check-in (Ej: pendiente, completado).

- `status_code`
- `status_name`

**Reglas:**
- No pueden repetirse `status_code` ni `status_name`

---

### Check_In
Representa el proceso de check-in de un pasajero.

- Relación:
  - Pertenece a `ticket_segment`
  - Tiene un `check_in_status`
  - Puede tener `boarding_group`
  - Puede ser realizado por un usuario (`user_account`)
- Datos:
  - `checked_in_at`

**Reglas importantes:**
- Solo puede existir un check-in por `ticket_segment`
- Permite saber quién realizó el check-in

---

### Boarding_Pass
Representa el pase de abordar generado tras el check-in.

- Relación:
  - Pertenece a `check_in`
- Datos:
  - `boarding_pass_code`
  - `barcode_value`
  - `issued_at`

**Reglas importantes:**
- Un check-in solo puede generar un pase de abordar
- No pueden repetirse:
  - `boarding_pass_code`
  - `barcode_value`

---

### Boarding_Validation
Representa la validación del pase de abordar en la puerta.

- Relación:
  - Pertenece a `boarding_pass`
  - Puede estar asociado a `boarding_gate`
  - Puede ser validado por un usuario (`user_account`)
- Datos:
  - `validated_at`
  - `validation_result` (APPROVED, REJECTED, MANUAL_REVIEW)
  - `notes`

**Reglas importantes:**
- `validation_result` solo permite:
  - APPROVED, REJECTED, MANUAL_REVIEW
- Permite registrar múltiples validaciones (histórico)

---