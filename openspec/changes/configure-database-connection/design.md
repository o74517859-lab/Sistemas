# Design

## Context

Conforme a la Sección 2 del SAD, la capa de datos de la plataforma delega en `src/config/database.js` la inicialización centralizada del motor SQLite3. Los modelos del sistema (`src/models/userModel.js` y `src/models/postModel.js`) consumirán este módulo para ejecutar sentencias preparadas. Véase `proposal.md` para la justificación general.

## Goals / Non-Goals

**Goals:**
- Implementar `src/config/database.js` encapsulando la inicialización del cliente `better-sqlite3`.
- Cargar las variables de entorno mediante `dotenv` y resolver de forma absoluta la ruta de la base de datos a partir de `DB_PATH` o `./database.sqlite`.
- Forzar la integridad referencial ejecutando inmediatamente `db.pragma('foreign_keys = ON;')`.
- Manejar excepciones en la apertura de la base de datos reportando logs claros en desarrollo y evitando arrancar en estado inconsistente.
- Exportar la instancia de base de datos `db` como un singleton reutilizable para la capa de modelos.

**Non-Goals:**
- No incluye la creación de esquemas, tablas ni migraciones DDL (estas corresponden a cambios futuros en la capa de modelos o scripts de migración).
- No incluye la ejecución de consultas SQL de negocio ni controladores.

## Decisions

### Decisión 1: Exportación como Singleton
- **Contexto:** SQLite es un motor embebido basado en archivo físico; abrir múltiples instancias concurrentes puede causar bloqueos innecesarios (`SQLITE_BUSY`).
- **Elección:** Instanciar y exportar un único objeto `db` en `src/config/database.js`.
- **Justificación:** El sistema de módulos CommonJS (`require`) almacena en caché las exportaciones, garantizando que todos los modelos compartan la misma conexión y el mismo contexto PRAGMA.

### Decisión 2: Driver sincrónico `better-sqlite3` y directiva PRAGMA
- **Contexto:** Se requiere sentencias preparadas rápidas y habilitación estricta de `PRAGMA foreign_keys = ON;` (RNF-04 y SAD Sección 4).
- **Elección:** Usar `better-sqlite3` ejecutando `db.pragma('foreign_keys = ON')` inmediatamente tras instanciar la conexión.
- **Justificación:** Al ser síncrono, la configuración de claves foráneas queda garantizada antes de que cualquier otro módulo realice una operación SQL.

### Decisión 3: Resolución robusta de la ruta del archivo de base de datos
- **Contexto:** El servidor puede ejecutarse desde distintas carpetas de trabajo según el comando (`npm start`, `node src/app.js`, etc.).
- **Elección:** Utilizar `path.resolve(process.cwd(), process.env.DB_PATH || './database.sqlite')`.
- **Justificación:** Asegura que la ruta relativa declarada en `.env` apunte de manera uniforme a la raíz del proyecto.

### Decisión 4: Manejo de errores de conexión y logs descriptivos
- **Contexto:** Facilitar la depuración sin ocultar fallos críticos de infraestructura.
- **Elección:** Envolver la inicialización en un bloque `try...catch`. Si ocurre un fallo, emitir una traza con `console.error` indicando la ruta y el motivo del fallo, y propagar el error para interrumpir el arranque del servicio.

## Risks / Trade-offs

- **[Fallo por directorio contenedor inexistente]** → **Mitigación:** Asegurar que la ruta apunte a un directorio existente o resolver la ruta en la raíz del proyecto.
- **[Omisión accidental de PRAGMA en nuevas conexiones]** → **Mitigación:** Al usar una única instancia exportada, toda consulta pasa indefectiblemente por la conexión con `foreign_keys = ON`.
