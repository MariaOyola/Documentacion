# ADR-006 — índice parcial para tier activo único y ruta maestra en flight

## Estado 
   Implementado

## Fecha
2026-04-14

## Tablas afectadas 
loyalty_account_tier, flight

##  Tablas  decisión
Optimización de integridad y performance — decisión del aprendiz


## Contexto

El sistema actual gestiona todo el ciclo operativo Durante el análisis del esquema se identificaron dos puntos donde la integridad y la mantenibilidad del sistema podían fortalecerse con cambios de bajo costo pero alto impacto:
•En loyalty_account_tier, no existía garantía en el motor de que solo hubiera un tier activo por cuenta en un momento dado.
En flight, para conocer el origen y destino del vuelo completo era necesario hacer JOIN a todos sus flight_segment ordenados, generando consultas costosas para operaciones frecuentes.

### Problema
Problema 1 — Integridad del tier activo:
Un error en la capa de aplicación puede crear múltiples tiers activos para una misma cuenta sin que la BD lo detecte, causando inconsistencias comerciales en el programa de lealtad.


## Decisión
- Decisión 1 — Índice parcial para tier activo único:
CREATE UNIQUE INDEX uq_loyalty_account_active_tier ON loyalty_account_tier (loyalty_account_id) WHERE expires_at IS NULL AND deleted_at IS NULL;

## Justificación técnica

Ambas decisiones siguen principios de diseño probados en sistemas OLTP de aerolíneas:
•El índice parcial tiene costo de indexación casi cero en el historial (filas con expires_at poblado). Solo indexa las filas activas, que son minoría. La garantía de unicidad en el motor es más robusta que cualquier validación en aplicación.
La desnormalización de ruta en flight es una práctica estándar en GDS (Global Distribution Systems) y PSS (Passenger Service Systems) de aerolíneas. El origen/destino del vuelo completo es inmutable una vez creado — no hay riesgo de inconsistencia si se controla en la capa de inserción.

## Consecuencias e impacto esperado 

- Las consultas de búsqueda de vuelos por ruta pasan de requerir JOIN+ORDER BY sobre flight_segment a un simple WHERE en flight.
