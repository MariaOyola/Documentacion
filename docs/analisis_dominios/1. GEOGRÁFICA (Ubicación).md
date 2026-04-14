### MÓDULO: ZONA GEOGRÁFICA (Ubicación)

## Tablas
- continent
- country
- state_province
- city
- district
- address

Modela la jerarquía geográfica completa del mundo, desde continentes hasta direcciones específicas.

### Relaciones (de arriba hacia abajo)
- Un continente tiene muchos países
- Un país pertenece a un continente
- Un país tiene muchos estados/provincias
- Un estado tiene muchas ciudades
- Una ciudad tiene muchos distritos
- Un distrito tiene muchas direcciones

### Entidades clave

Representa regiones globales (Ej: América, Europa)
continent_code → abreviación (Ej: "SA")

### RELACIONES IMPORTANTES

continent
   ↓
country
   ↓
state_province
   ↓
city ← time_zone
   ↓
district
   ↓
address

## Entidades clave

### Continent
Representa regiones globales (Ej: América, Europa).

- `continent_code`: abreviación (Ej: "SA")

---

### Country
Representa un país (Ej: Colombia).

- Relación: pertenece a un continente
- Códigos estándar:
  - `iso_alpha2`: CO
  - `iso_alpha3`: COL

**Reglas importantes:**
- No pueden repetirse los códigos (`iso_alpha2`, `iso_alpha3`)
- No puede repetirse el nombre del país

---

### State_Province
Representa departamentos o estados (Ej: Cundinamarca).

- Relación: depende de un país

**Reglas:**
- No puede haber dos estados con el mismo nombre dentro del mismo país

---

### City
Representa una ciudad (Ej: Bogotá).

- Depende de:
  - Estado o provincia
  - Zona horaria (`time_zone`)

**Relación importante:**
- Se conecta con la tabla `time_zone`

---

### District
Representa barrios o localidades (Ej: Chapinero).

- Relación: pertenece a una ciudad

---

### Address
Representa la dirección exacta (nivel más bajo de la jerarquía).

**Campos importantes:**
- `address_line_1`: dirección principal
- `latitude`: latitud geográfica
- `longitude`: longitud geográfica

**Reglas:**
- Latitud debe estar entre -90 y 90
- Longitud debe estar entre -180 y 180

------------------------------------------------
