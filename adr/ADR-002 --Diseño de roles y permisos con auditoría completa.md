# ADR-001 — Diseño de roles y permisos con auditoría completa

## Estado 
Implementado

## Fecha
2026-04-14

## Tablas afectadas
security_role, security_permission, user_role, role_permission, user_account (campos created_by, updated_by, deleted_at)

##  Tablas  decisión
Seguridad / Trazabilidad / Modelo RBAC

## Contexto

El sistema maneja datos sensibles de pasajeros, transacciones financieras, documentos de identidad y operaciones de vuelo. Se requiere un modelo de control de acceso que permita diferenciar qué puede hacer cada usuario, y que deje trazabilidad completa de quién creó, modificó o eliminó cada registro en el sistema.

### Problema
Sin un modelo de roles y permisos definido a nivel de base de datos, no es posible auditar quién realizó cada operación ni controlar el acceso diferenciado entre administradores, agentes de reservas, personal de abordaje y otros perfiles. Tampoco existe manera de rastrear eliminaciones lógicas si no se implementa soft delete.


## Decisión
Se implementa el patrón RBAC (Role-Based Access Control) con las siguientes tablas:
•security_role — define los roles del sistema (ADMIN, AGENT, BOARDING_STAFF, etc.).
•security_permission — define permisos granulares (RESERVATION_CREATE, TICKET_VOID, etc.).
•role_permission — asigna permisos a roles.
•user_role — asigna roles a usuarios, con trazabilidad del asignador (assigned_by_user_id).
Adicionalmente, todas las tablas incluyen los campos de auditoría: created_by (uuid → user_account), updated_by (uuid → user_account) y deleted_at (timestamptz) para soft delete.

## Justificación técnica

RBAC es el estándar de la industria para sistemas multi-rol porque separa usuarios, roles y permisos en capas independientes: cambiar los permisos de un rol afecta automáticamente a todos los usuarios con ese rol, sin modificar filas individuales de usuarios. Los campos created_by y updated_by como FK a user_account permiten auditoría completa del ciclo de vida de cada registro. El campo deleted_at habilita el soft delete, lo cual es crítico en sistemas regulados por aviación civil donde los registros no deben eliminarse físicamente.

## Consecuencias e impacto esperado 

- Trazabilidad completa: se sabe quién creó, modificó o borró cada registro.
- Control de acceso diferenciado por perfil de usuario.
- Soft delete preserva historial para cumplimiento regulatorio.
- Las FK de auditoría se crearon con ALTER TABLE posterior para resolver la dependencia circular con user_account.
Restricción: el primer usuario del sistema (bootstrap) debe insertarse sin created_by, aceptando NULL en ese campo inicial.