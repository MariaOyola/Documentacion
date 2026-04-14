### MÓDULO: PAYMENT (Pagos)

## Tablas
- payment_status
- payment_method
- payment
- payment_transaction
- refund

Modela la gestión de pagos, incluyendo estados, métodos, transacciones financieras y devoluciones.

### Relaciones (de arriba hacia abajo)
- Un pago pertenece a una venta
- Un pago tiene un estado
- Un pago tiene un método
- Un pago puede tener múltiples transacciones
- Un pago puede generar devoluciones (refunds)

### RELACIONES IMPORTANTES

sale → payment ← payment_status  
              ↓  
        payment_method  

payment → payment_transaction  
payment → refund  

payment → currency  

---

## Entidades clave

### Payment_Status
Define el estado del pago (Ej: pendiente, aprobado, rechazado).

- `status_code`
- `status_name`

**Reglas:**
- No pueden repetirse `status_code` ni `status_name`

---

### Payment_Method
Define los métodos de pago (Ej: tarjeta, efectivo, transferencia).

- `method_code`
- `method_name`

**Reglas:**
- No pueden repetirse `method_code` ni `method_name`

---

### Payment
Representa un pago realizado sobre una venta.

- Relación:
  - Pertenece a `sale`
  - Tiene `payment_status`
  - Tiene `payment_method`
  - Usa `currency`
- Datos:
  - `payment_reference`
  - `amount`
  - `authorized_at`

**Reglas importantes:**
- `payment_reference` es único
- `amount` debe ser mayor a 0

---

### Payment_Transaction
Representa transacciones del pago (nivel proveedor).

- Relación:
  - Pertenece a `payment`
- Datos:
  - `transaction_reference`
  - `transaction_type`
  - `transaction_amount`
  - `processed_at`
  - `provider_message`

**Reglas importantes:**
- `transaction_reference` es único
- `transaction_type` solo permite:
  - AUTH, CAPTURE, VOID, REFUND, REVERSAL
- `transaction_amount` debe ser mayor a 0

---

### Refund
Representa devoluciones de dinero.

- Relación:
  - Pertenece a `payment`
- Datos:
  - `refund_reference`
  - `amount`
  - `requested_at`
  - `processed_at`
  - `refund_reason`

**Reglas importantes:**
- `refund_reference` es único
- `amount` debe ser mayor a 0
- `processed_at` debe ser mayor o igual a `requested_at` (si existe)

---