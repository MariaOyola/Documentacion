### MÓDULO: AIRLINE (Aerolíneas)

## Tablas
- airline

Modela la información de las aerolíneas, incluyendo su país de origen, códigos de identificación y estado operativo.

### Relaciones (de arriba hacia abajo)
- Una aerolínea pertenece a un país
- Un país puede tener muchas aerolíneas

### RELACIONES IMPORTANTES

country
   ↓
airline

## Entidades clave

### Airline
Representa una aerolínea (Ej: Avianca, LATAM).

- Relación: pertenece a un país (`home_country_id`)
- Identificadores:
  - `airline_code`: código interno del sistema
  - `iata_code`: código estándar IATA (2 caracteres)
  - `icao_code`: código estándar ICAO (3 caracteres)
- `airline_name`: nombre de la aerolínea
- `is_active`: indica si la aerolínea está operativa

**Reglas importantes:**
- No pueden repetirse:
  - `airline_code`
  - `airline_name`
  - `iata_code`
  - `icao_code`
- El `iata_code` debe tener exactamente 2 caracteres (si no es NULL)
- El `icao_code` debe tener exactamente 3 caracteres (si no es NULL)
- Toda aerolínea debe estar asociada a un país (`home_country_id` obligatorio)
- El campo `is_active` permite manejar el estado sin eliminar el registro