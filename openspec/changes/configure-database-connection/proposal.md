# Proposal

## Why
La capa de modelos y persistencia del sistema requiere una instancia centralizada y segura de conexión a la base de datos relacional SQLite3. Configurar este módulo garantiza la resolución dinámica de la ruta del archivo físico mediante variables de entorno y el cumplimiento obligatorio de las restricciones de integridad referencial antes de ejecutar cualquier consulta.

## What Changes
- Creación del módulo de conexión `src/config/database.js` utilizando `better-sqlite3`.
- Carga de variables de entorno mediante `dotenv` para resolver la ruta de la base de datos (`DB_PATH` o fallback a `./database.sqlite`).
- Ejecución obligatoria del comando PRAGMA de SQLite (`PRAGMA foreign_keys = ON;`) inmediatamente tras abrir la conexión.
- Manejo controlado de errores de inicialización con emisión de traza descriptiva en desarrollo.
- Exportación de la instancia `db` para su reutilización en la capa de modelos (`src/models/`).

## Capabilities

### New Capabilities
- `database-persistence`: Define la inicialización, configuración de integridad referencial y gestión de la conexión centralizada a la base de datos SQLite3.

### Modified Capabilities

## Impact
- **Requerimientos del SRS y SAD afectados**: RNF-04 (Gestión de base de datos SQLite con claves foráneas habilitadas) y Secciones 2 y 4 del SAD (módulo `src/config/database.js` y política de integridad referencial).
- **Alcance**: Creación de `src/config/database.js` y pruebas de importación/activación de claves foráneas.
- **Fuera de alcance**: Definición de esquemas de tablas (`users`, `posts`), ejecución de migraciones DDL y controladores.
- **Dependencias**: Emplea `better-sqlite3` y `dotenv`.
