# Proposal

## Why
El proyecto requiere establecer su infraestructura base de software antes de implementar la lógica de negocio y las interfaces de usuario. Esta base estandariza la gestión de dependencias en Node.js, la parametrización segura mediante variables de entorno y la organización física del repositorio según la arquitectura en tres capas del SAD.

## What Changes
- Configuración de dependencias de producción (`express`, `better-sqlite3`, `bcryptjs`, `jsonwebtoken`, `dotenv`, `cors`) y desarrollo (`nodemon`) en `package.json`.
- Scripts de ejecución estandarizados (`start: node app.js`, `dev: nodemon app.js`).
- Parametrización segura de variables de entorno mediante `.env` y su plantilla pública `.env.example`.
- Creación de la estructura física de directorios modular (`src/config`, `src/controllers`, `src/middlewares`, `src/models`, `src/routes`, `public/css`, `public/js`).
- Reglas de exclusión en `.gitignore` para dependencias, base de datos local y secretos sensibles.

## Capabilities

### New Capabilities
- `server-core`: Define la configuración del entorno de ejecución, la carga de variables de entorno sensibles y la estructura física base del servidor Node.js/Express.

### Modified Capabilities

## Impact
- **Requerimientos del SRS y SAD afectados**: RNF-03 (compatibilidad frontend sin frameworks), RNF-04 (gestión de base de datos embebida SQLite) y estructura modular de 3 capas descrita en el SAD.
- **Alcance**: Configuración de `package.json`, archivos `.env` y `.env.example`, archivo `.gitignore` y el andamiaje de directorios base del proyecto.
- **Fuera de alcance**: Implementación de lógica en controladores, modelos, rutas de la API, creación de tablas en SQLite y archivos HTML/CSS de la interfaz.
- **Dependencias**: Se incorporan `express`, `better-sqlite3`, `bcryptjs`, `jsonwebtoken`, `dotenv`, `cors` y `nodemon`.
