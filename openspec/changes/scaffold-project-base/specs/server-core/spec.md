# Spec Delta

## Purpose

Define la configuración base del entorno de ejecución, el andamiaje modular de directorios, la gestión de dependencias y las reglas de aislamiento para la plataforma.

## ADDED Requirements

### Requirement: Configuración del entorno de ejecución
El sistema SHALL cargar la configuración del servidor desde variables de entorno y proveer una plantilla estandarizada sin valores sensibles.

#### Scenario: Carga de variables de entorno
- **WHEN** la aplicación inicializa el proceso del servidor
- **THEN** carga las variables definidas en `.env` mediante `dotenv`
- **AND** establece `PORT=3000`, `NODE_ENV=development` y `DB_PATH=./database.sqlite` si no se especifican otros valores

#### Scenario: Plantilla de variables para control de versiones
- **WHEN** un desarrollador inspecciona el repositorio base
- **THEN** encuentra un archivo `.env.example` con las claves requeridas `PORT`, `NODE_ENV`, `JWT_SECRET` y `DB_PATH` sin exponer secretos reales

### Requirement: Scripts de ejecución del servidor
El sistema SHALL definir scripts de arranque en `package.json` para facilitar la ejecución tanto en producción como en entornos de desarrollo local.

#### Scenario: Inicio en modo estándar o producción
- **WHEN** se ejecuta el comando `npm start`
- **THEN** el sistema invoca el comando `node app.js` para iniciar el servidor

#### Scenario: Inicio en modo desarrollo con recarga automática
- **WHEN** se ejecuta el comando `npm run dev`
- **THEN** el sistema invoca `nodemon app.js` para reiniciar el proceso ante modificaciones de código

### Requirement: Estructura modular del proyecto
El sistema SHALL estructurar su código fuente y activos estáticos en directorios desacoplados conforme a la arquitectura de 3 capas del SAD.

#### Scenario: Verificación del layout de directorios
- **WHEN** se inspecciona la estructura física del proyecto
- **THEN** existen las carpetas `src/config/`, `src/controllers/`, `src/middlewares/`, `src/models/` y `src/routes/` para el backend
- **AND** existen las carpetas `public/css/` y `public/js/` para el frontend

### Requirement: Aislamiento de secretos y datos locales
El sistema SHALL impedir que secretos de configuración, dependencias locales y archivos físicos de base de datos se almacenen en el control de versiones.

#### Scenario: Exclusión mediante archivo de ignorados
- **WHEN** se genera el archivo `.gitignore` en la raíz del proyecto
- **THEN** incluye reglas explícitas para excluir `node_modules/`, `.env` y `*.sqlite` o `database.sqlite`
- **AND** Git omite el seguimiento de estos archivos locales
