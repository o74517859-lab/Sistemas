# Tasks

## 1. Configuración de dependencias y scripts

- [x] 1.1 Actualizar `package.json` agregando los scripts `"start": "node app.js"` y `"dev": "nodemon app.js"` preservando los scripts de `openspec`, y verificar con `npm run` que los comandos estén registrados.
- [x] 1.2 Instalar las dependencias de producción (`express`, `better-sqlite3`, `bcryptjs`, `jsonwebtoken`, `dotenv`, `cors`) y desarrollo (`nodemon`), y verificar la instalación con `npm list --depth=0`.

## 2. Variables de entorno y reglas de git

- [x] 2.1 Crear el archivo `.env` en la raíz con las variables `PORT=3000`, `NODE_ENV=development`, `JWT_SECRET=tu_clave_secreta_super_segura` y `DB_PATH=./database.sqlite`, y verificar su existencia física.
- [x] 2.2 Crear el archivo `.env.example` como plantilla para el repositorio sin exponer secretos confidenciales, y verificar su contenido.
- [x] 2.3 Configurar `.gitignore` para excluir `node_modules/`, `.env` y archivos de base de datos `*.sqlite` o `database.sqlite`, y verificar con `git status` que los archivos privados queden ignorados.

## 3. Andamiaje de directorios (SAD)

- [x] 3.1 Crear la estructura de carpetas de backend (`src/config/`, `src/controllers/`, `src/middlewares/`, `src/models/`, `src/routes/`), y verificar su creación en disco.
- [x] 3.2 Crear la estructura de carpetas de frontend (`public/css/`, `public/js/`), y verificar que los directorios existan conforme a la arquitectura.

## 4. Verificación y validación

- [x] 4.1 Ejecutar `npm run spec:validate` y verificar que todas las especificaciones y el cambio actual pasen sin errores.
- [x] 4.2 Realizar una prueba rápida de carga de variables ejecutando `node -e "require('dotenv').config(); console.log(process.env.PORT);"` y comprobar que imprima `3000`.
