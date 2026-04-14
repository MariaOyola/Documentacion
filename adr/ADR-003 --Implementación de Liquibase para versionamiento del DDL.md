# ADR-001 — Implementación de Liquibase para versionamiento del DDL 

## Estado 
    Propuesta

## Fecha
2026-04-14

## Tablas afectadas
 Todo el esquema asta geografy   si queda mas tiempo pues toda la base de datos
##  Tablas  decisión
Gestión de migraciones / DevOps / Herramienta de BD

## Contexto

El esquema de la base de datos del sistema de aerolínea tiene más de 60 tablas, múltiples constraints, índices parciales y relaciones circulares resueltas con ALTER TABLE. Cualquier cambio al DDL en un equipo de desarrollo requiere coordinación precisa entre desarrolladores, ambientes (desarrollo, QA, producción) y despliegues. Sin una herramienta de control de versiones para el DDL, los cambios se aplican manualmente, lo que genera inconsistencias entre ambientes y pérdida de trazabilidad.

### Problema
Sin una herramienta de migración, los equipos enfrentan los siguientes riesgos:
•Desincronización entre ambientes: desarrollo, QA y producción pueden tener versiones distintas del esquema.
•Imposibilidad de rollback controlado ante un error en producción.
•Pérdida de historial: no se sabe qué cambio se aplicó en qué momento ni por quién.
Dependencia de scripts ejecutados manualmente, propensos a error humano.


## Decisión
Se adopta Liquibase como herramienta de administración y versionamiento del DDL. La estrategia de implementación es:
Organización de changelogs:
- db/changelog/db.changelog-master.xml — archivo maestro que incluye todos los demás.
- db/changelog/releases/YYYY-MM-DD-NNN-descripcion.sql — un archivo por cambio.
Convención de nombres para changeSets:
- ID: YYYY-MM-DD-NNN-descripcion-corta (ej: 2026-04-14-001-create-person-table).
- Author: nombre del desarrollador o equipo responsable.
Estrategia de rollback:
- Todo changeSet que altere datos o elimine objetos debe incluir su bloque rollback.
Los CREATE TABLE tienen rollback automático (DROP TABLE).

## Justificación técnica

Liquibase se elige sobre Flyway por las siguientes razones:
- Liquibase soporta múltiples formatos (XML, YAML, JSON, SQL) y permite rollback declarativo; Flyway Pro lo requiere de pago.
- El checksum de Liquibase detecta modificaciones accidentales a changelogs ya ejecutados.
- La tabla DATABASECHANGELOG registra cada ejecución con autor, fecha y checksum, dando trazabilidad completa.
Integración nativa con herramientas CI/CD (GitHub Actions, Jenkins) mediante el comando liquibase update.

## Consecuencias e impacto esperado 

- Todos los ambientes aplican exactamente el mismo conjunto de migraciones en el mismo orden.
- Los despliegues a producción se automatizan mediante el pipeline CI/CD sin intervención manual.
- Se puede hacer rollback controlado hasta cualquier punto del historial.
- Overhead inicial: el DDL existente debe convertirse a changelogs de Liquibase como migración base (baseline).
Los desarrolladores deben respetar la convención: nunca modificar un changeSet ya ejecutado.