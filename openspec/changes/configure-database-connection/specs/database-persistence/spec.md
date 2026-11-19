# Spec Delta

## Purpose

Proveer la conexión centralizada a la base de datos relacional SQLite3, garantizando la activación forzosa de la integridad referencial y la gestión segura de errores de conexión.

## ADDED Requirements

### Requirement: Conexión centralizada a SQLite3
El sistema SHALL inicializar una conexión a la base de datos SQLite3 utilizando la ruta especificada en la variable de entorno DB_PATH o el valor predeterminado `./database.sqlite`.

#### Scenario: Inicialización exitosa de la conexión
- **WHEN** el módulo de base de datos es importado por la aplicación
- **THEN** establece la conexión con el archivo de base de datos configurado en `DB_PATH`
- **AND** exporta la instancia del cliente para su reutilización en la capa de modelos

#### Scenario: Ruta de base de datos no configurada
- **WHEN** la variable `DB_PATH` no se encuentra definida en el entorno
- **THEN** el sistema utiliza la ruta relativa `./database.sqlite` como valor predeterminado

### Requirement: Activación obligatoria de claves foráneas
El sistema SHALL ejecutar la directiva PRAGMA foreign_keys = ON de forma inmediata al abrir la conexión para garantizar la integridad referencial relacional.

#### Scenario: Verificación de claves foráneas habilitadas
- **WHEN** se inicializa la conexión a SQLite
- **THEN** la directiva `PRAGMA foreign_keys = ON;` queda aplicada en la sesión
- **AND** la consulta de verificación de `foreign_keys` retorna el valor 1

### Requirement: Manejo de errores de conexión
El sistema SHALL capturar excepciones ocurridas durante la apertura de la base de datos y emitir una traza descriptiva del error en consola.

#### Scenario: Fallo al abrir el archivo de base de datos
- **WHEN** ocurre un error de lectura, permisos o bloqueo al intentar abrir la base de datos
- **THEN** el sistema captura la excepción y registra el mensaje descriptivo del fallo en la consola
- **AND** propaga o interrumpe de forma controlada el proceso evitando un estado inconsistente
