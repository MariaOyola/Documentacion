# ADR-001 — Control de tier activo único en loyalty_account_tier

## Estado 
Implementado

## Fecha
2026-04-14

## Tablas afectadas
Integridad referencial / Índice de base de datos

## Contexto

La tabla loyalty_account_tier registra el historial de niveles (tiers) asignados a cada cuenta de lealtad. El constraint original era:
UNIQUE (loyalty_account_id, assigned_at)
Este diseño permitía que una misma cuenta tuviera múltiples tiers sin fecha de expiración (expires_at = NULL) simultáneamente, siempre que tuvieran distinto assigned_at. No existía ninguna restricción en el motor que garantizara la unicidad del tier vigente.

### Problema
Sin una restricción en el motor de base de datos, es posible insertar múltiples filas con expires_at = NULL para la misma loyalty_account_id. Esto significa que un error de programación en la capa de aplicación puede crear múltiples tiers activos para una misma cuenta sin que el motor lo detecte ni lo rechace, comprometiendo la integridad comercial del programa de lealtad.


## Decisión
Se implementa un índice único parcial en PostgreSQL que garantiza un solo tier activo por cuenta directamente en el motor:
CREATE UNIQUE INDEX uq_loyalty_account_active_tier
    ON loyalty_account_tier (loyalty_account_id)
    WHERE expires_at IS NULL AND deleted_at IS NULL;

## Justificación técnica

Un índice parcial aplica únicamente sobre las filas que cumplen la condición WHERE, por lo que no genera overhead en el historial de tiers cerrados (con expires_at poblado). La condición WHERE expires_at IS NULL AND deleted_at IS NULL garantiza que solo existe un tier activo por cuenta en cada momento, cumpliendo la regla de negocio en el motor sin costo adicional sobre filas históricas. Esto es superior a delegar la lógica a la aplicación porque un bug en el código no puede violar la integridad.

## Consecuencias e impacto esperado 

Impacto positivo:
 - Integridad garantizada a nivel de motor — no depende de lógica de aplicación.
•Sin costo de indexación en filas históricas (tiers ya expirados).
- Al asignar un nuevo tier se debe cerrar el anterior (expires_at = NOW()) antes de insertar el nuevo; de lo contrario la BD rechaza la inserción.
Restricción operativa:
La capa de aplicación debe actualizar expires_at del tier anterior en la misma transacción que inserta el nuevo tier activo.