# Design

## Context

El proyecto sigue una arquitectura monolítica ligera de tres capas desacopladas (Presentación, Negocio y Datos) según lo establecido en el SAD (Sección 2). Véase `proposal.md` para la motivación general. La implementación requiere sentar las bases de configuración de paquetes, variables de entorno, directorios y reglas de exclusión en control de versiones antes de iniciar el código de negocio.

## Goals / Non-Goals

**Goals:**
- Configurar `package.json` con dependencias esenciales de backend (`express`, `better-sqlite3`, `bcryptjs`, `jsonwebtoken`, `dotenv`, `cors`) y dependencias de desarrollo (`nodemon`), preservando los scripts de `openspec`.
- Definir scripts de arranque estandarizados (`start: node app.js`, `dev: nodemon app.js`).
- Parametrizar la configuración del sistema mediante `.env` y documentar la plantilla `.env.example` (`PORT`, `NODE_ENV`, `JWT_SECRET`, `DB_PATH`).
- Crear la estructura física de carpetas según el SAD: `src/config/`, `src/controllers/`, `src/middlewares/`, `src/models/`, `src/routes/`, `public/css/`, `public/js/`.
- Garantizar la exclusión en `.gitignore` de `node_modules/`, `.env` y archivos de base de datos `*.sqlite` o `database.sqlite`.

**Non-Goals:**
- No incluye la codificación del servidor `app.js` ni de los endpoints REST `/api/v1/...`.
- No incluye la implementación de conexiones a SQLite ni la creación de tablas o migraciones.
- No incluye el maquetado HTML ni estilos CSS de las vistas del cliente.

## Decisions

### Decisión 1: Elección de librería para hashing (`bcryptjs`)
- **Contexto:** Se requiere hashing con `saltRounds >= 10` sin texto plano (RNF-01).
- **Elección:** Usar `bcryptjs`.
- **Justificación:** Al ser una implementación pura de JavaScript, elimina la dependencia de compiladores C++ nativos (`node-gyp`, Visual Studio C++ Tools en Windows), asegurando portabilidad inmediata en el laboratorio de desarrollo sin comprometer los requerimientos de seguridad.
- **Alternativas consideradas:** `bcrypt` (requiere compilación nativa en Windows).

### Decisión 2: Driver de base de datos relacional (`better-sqlite3`)
- **Contexto:** Persistencia en archivo local SQLite3 con soporte de sentencias preparadas y `PRAGMA foreign_keys = ON` (RNF-04).
- **Elección:** `better-sqlite3` (o `sqlite3` como respaldo).
- **Justificación:** Proporciona un API síncrono de alto rendimiento para Node.js, facilitando sentencias preparadas limpias (`db.prepare(...)`) y activación inmediata de claves foráneas.

### Decisión 3: Estructura de carpetas desacoplada (SAD Sección 2)
- **Contexto:** Separar responsabilidades de servidor y cliente estático.
- **Elección:**
  - `src/`: Capa de negocio y datos (`config/`, `controllers/`, `middlewares/`, `models/`, `routes/`).
  - `public/`: Capa de presentación estática (`css/`, `js/`).
- **Justificación:** Cumple el principio de responsabilidad única (SRP) detallado en los estándares de desarrollo.

### Decisión 4: Preservación de scripts de OpenSpec en `package.json`
- **Contexto:** El proyecto ya cuenta con comandos de especificación (`spec:view`, `spec:validate`, `spec:update`, `spec:list`).
- **Elección:** Fusionar los nuevos scripts (`start`, `dev`) sin sobrescribir ni eliminar los scripts existentes de OpenSpec.

## Risks / Trade-offs

- **[Riesgo de compilación nativa en Windows con drivers SQLite]** → **Mitigación:** Asegurar la instalación de versiones precompiladas de `better-sqlite3` o usar `sqlite3` si la plataforma no cuenta con herramientas de compilación.
- **[Exposición accidental de variables de entorno y datos locales en Git]** → **Mitigación:** Actualizar y verificar `.gitignore` con `node_modules/`, `.env` y `*.sqlite` antes de ejecutar la inicialización de base de datos.
- **[Inconsistencia en scripts de ejecución]** → **Mitigación:** Definir `start` con `node app.js` y `dev` con `nodemon app.js` para permitir recarga en caliente sin alterar la ejecución en producción.
