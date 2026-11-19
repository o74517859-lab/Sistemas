# Tasks

## 1. Implementación del módulo de base de datos

- [ ] 1.1 Crear el archivo `src/config/database.js` cargando `dotenv`, resolviendo la ruta `DB_PATH`, instanciando `better-sqlite3` y ejecutando `db.pragma('foreign_keys = ON;')`, y verificar la sintaxis con `node --check src/config/database.js`.
- [ ] 1.2 Incorporar en `src/config/database.js` el bloque de captura de errores con registro en consola descriptivo y exportación de la instancia `db`, y verificar la exportación del módulo.

## 2. Pruebas y verificación de integridad referencial

- [ ] 2.1 Crear un script de prueba de verificación para importar `src/config/database.js` y consultar el valor de `PRAGMA foreign_keys;`, verificando que devuelva 1.
- [ ] 2.2 Ejecutar la prueba de importación en Node.js y verificar la creación/apertura exitosa del archivo de base de datos con integridad referencial activa.

## 3. Validación de especificaciones

- [ ] 3.1 Ejecutar `npm run spec:validate` y comprobar que todas las especificaciones y el cambio actual pasen sin errores.
