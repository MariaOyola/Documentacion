# ADR-004 — Estrategia de versionamiento con ramas develop, qa y main

## Estado 
    Propuesta

## Fecha
2026-04-14

## Alcance
Repositorio Git del proyecto (código + changelogs Liquibase)

##  Tablas  decisión
Estrategia de ramas

## Contexto

El proyecto requiere que los cambios al esquema de base de datos y al código de la aplicación pasen por distintos niveles de validación antes de llegar a producción. Sin una estrategia de ramas definida, los cambios pueden llegar a producción sin haber sido probados en un ambiente intermedio, o los changelogs de Liquibase pueden aplicarse en el orden incorrecto.

### Problema
Sin un flujo de ramas definido:
- Los desarrolladores pueden integrar código no probado directamente en producción.
- No existe una barrera de calidad (QA) antes del despliegue productivo.
- Los changelogs de Liquibase pueden ejecutarse en ambientes en distinto estado, causando conflictos.
Es imposible auditar qué versión del esquema está corriendo en cada ambiente.


## Decisión
Se adopta un flujo de tres ramas permanentes con promoción progresiva:
develop (integración continua):
•Rama de trabajo del día a día. Los desarrolladores crean feature branches desde develop y hacen PR hacia develop.
- Cada PR a develop ejecuta automáticamente las migraciones Liquibase en el ambiente de desarrollo.
qa (validación):
- Merge de develop a qa se hace al final de cada sprint o cuando un conjunto de funcionalidades está listo.
- El ambiente QA aplica las migraciones y ejecuta pruebas de integración automatizadas.
- Solo un Tech Lead puede aprobar el merge a qa.
main (producción):
- Solo recibe merges desde qa, nunca directamente desde develop o feature branches.
- Requiere al menos dos aprobaciones de revisores y que los tests de QA hayan pasado.
Cada merge a main genera automáticamente un tag de versión (ej: v1.4.0) y aplica liquibase update en producción.

## Justificación técnica

El flujo develop → qa → main provee tres capas de validación antes de producción, reduciendo el riesgo de errores. La sincronización con los changelogs de Liquibase (ADR-003) es directa: cada rama mapea a un ambiente con su propia base de datos, y el pipeline aplica las migraciones pendientes al hacer el merge. Los tags de versión en main permiten correlacionar exactamente qué versión del esquema está activa en producción en cualquier momento, lo cual es requisito de trazabilidad para sistemas aeronáuticos regulados.

## Consecuencias e impacto esperado 

- •Aislamiento de ambientes: cada rama mapea a un ambiente independiente con su propia BD.