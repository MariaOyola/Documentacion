
### MÓDULO: SECURITY (Seguridad y Acceso)

## Tablas
- user_status
- security_role
- security_permission
- user_account
- user_role
- role_permission

Modela la autenticación, autorización y control de acceso del sistema, incluyendo usuarios, estados, roles y permisos.

### Relaciones (de arriba hacia abajo)
- Un usuario pertenece a una persona
- Un usuario tiene un estado
- Un usuario puede tener múltiples roles
- Un rol puede estar asignado a múltiples usuarios
- Un rol puede tener múltiples permisos
- Un permiso puede pertenecer a múltiples roles

---

## Entidades clave

### User_Status
Define el estado de una cuenta de usuario (Ej: activo, inactivo, bloqueado).

- `status_code`: código del estado
- `status_name`: nombre del estado

**Reglas:**
- No pueden repetirse `status_code` ni `status_name`

---

### Security_Role
Define los roles del sistema (Ej: admin, operador, cliente).

- `role_code`: código del rol
- `role_name`: nombre del rol
- `role_description`: descripción del rol

**Reglas:**
- No pueden repetirse `role_code` ni `role_name`

---

### Security_Permission
Define los permisos del sistema (Ej: crear usuario, ver reportes).

- `permission_code`: código del permiso
- `permission_name`: nombre del permiso
- `permission_description`: descripción del permiso

**Reglas:**
- No pueden repetirse `permission_code` ni `permission_name`

---

### User_Account
Representa la cuenta de acceso de un usuario.

- Relación:
  - Pertenece a `person`
  - Tiene un `user_status`
- Datos:
  - `username`
  - `password_hash`
  - `last_login_at`

**Reglas importantes:**
- No puede haber más de una cuenta por persona (`person_id` único)
- No puede repetirse el `username`
- `password_hash` almacena la contraseña en forma segura (no texto plano)
- Maneja auditoría con `created_by` y `updated_by` (auto-referencia)

---

### User_Role
Relaciona usuarios con roles (tabla intermedia).

- Relación:
  - Pertenece a `user_account`
  - Pertenece a `security_role`
- Datos:
  - `assigned_at`
  - `assigned_by_user_id`

**Reglas importantes:**
- No se puede repetir la combinación (`user_account_id`, `security_role_id`)
- Permite asignar múltiples roles a un usuario

---

### Role_Permission
Relaciona roles con permisos (tabla intermedia).

- Relación:
  - Pertenece a `security_role`
  - Pertenece a `security_permission`
- Datos:
  - `granted_at`

**Reglas importantes:**
- No se puede repetir la combinación (`security_role_id`, `security_permission_id`)
- Permite que un rol tenga múltiples permisos

---

## Auditoría (integración global)

- Todas las tablas incluyen:
  - `created_at`, `updated_at`
  - `created_by`, `updated_by`
  - `deleted_at`
- Los campos `created_by` y `updated_by` están relacionados con `user_account`
- Permite trazabilidad completa de quién creó o modificó cada registr


