# ADR-001 — Dominio funcional: módulo de notificaciones al pasajero

## Estado 
    Propuesta (no se ha realizado)

## Fecha
2026-04-14

##  Tablas  decisión
Expansión de dominio funcional

## Contexto

El sistema actual gestiona todo el ciclo operativo de la aerolínea (reservas, tickets, abordaje, pagos, fidelización), pero no tiene un mecanismo estructurado para notificar a los pasajeros sobre eventos relevantes: retrasos, cambios de puerta, apertura de check-in o confirmación de boarding pass. Esta información existe en la BD (flight_delay, boarding_gate, check_in, boarding_pass) pero no hay un dominio que la traduzca en comunicaciones al pasajero.

### Problema
Sin un módulo de notificaciones:
- Los pasajeros no reciben comunicación proactiva de eventos que afectan su vuelo.
- No hay trazabilidad de si una notificación fue enviada, entregada o falló.
Las comunicaciones se hacen por canales externos no integrados al sistema, perdiendo consistencia.


## Decisión
Se propone agregar el dominio de notificaciones con las siguientes tablas:
notification_channel:
•Define los canales disponibles: EMAIL, SMS, PUSH. Con channel_code, channel_name y campos de auditoría estándar.

## Justificación técnica

El diseño propuesto es extensible (nuevos canales y tipos de evento se agregan sin cambios de esquema), auditable (todo intento de envío queda registrado) y desacoplado del core operativo (el módulo consume datos existentes pero no los modifica). Las FK hacia reservation_passenger y flight_segment integran el dominio con el flujo comercial y operativo existente sin crear dependencias circulares. El patrón de delivery log con attempt_number permite implementar estrategias de reintento (retry) con backoff exponencial en la capa de aplicación.

## Consecuencias e impacto esperado 

- Los pasajeros reciben notificaciones proactivas de eventos que afectan su itinerario.
