# Autenticación y Registro

## Purpose

Definir el registro de usuarios, el inicio de sesión y la protección de credenciales de la plataforma, garantizando el almacenamiento seguro de contraseñas, la unicidad del correo electrónico y la emisión de sesiones válidas para acceder a rutas protegidas.

## Requirements

### Requirement: Registro de usuarios

El sistema SHALL permitir el registro de nuevos usuarios con nombre completo, correo electrónico y contraseña.

#### Scenario: Registro exitoso

- **WHEN** un visitante envía un nombre completo, un correo válido no registrado y una contraseña que cumple la longitud mínima
- **THEN** el sistema almacena la contraseña procesada con bcrypt y un factor de costo mayor o igual a 10
- **AND** responde con HTTP 201 Created
- **AND** redirige al inicio de sesión

#### Scenario: Correo electrónico duplicado

- **WHEN** un visitante intenta registrarse con un correo ya almacenado
- **THEN** el sistema responde con un error HTTP 409 Conflict
- **AND** no crea un nuevo usuario

#### Scenario: Datos de registro inválidos

- **WHEN** el formulario contiene un correo con formato inválido o una contraseña demasiado corta
- **THEN** el cliente muestra el error de validación y no envía la solicitud
- **AND** el backend rechaza la solicitud con HTTP 400 Bad Request si se envía de todos modos

### Requirement: Unicidad del correo electrónico

El sistema SHALL garantizar que cada correo electrónico identifique a un único usuario mediante una restricción UNIQUE en la base de datos y una verificación previa en el backend.

#### Scenario: Restricción a nivel de base de datos

- **WHEN** se intenta insertar un usuario con un correo existente
- **THEN** SQLite rechaza la operación por la restricción UNIQUE de la columna email
- **AND** el backend traduce el fallo en una respuesta HTTP 409 Conflict

#### Scenario: Verificación previa en el backend

- **WHEN** el backend recibe una solicitud de registro
- **THEN** consulta la tabla de usuarios mediante una sentencia preparada antes de insertar
- **AND** responde con HTTP 409 Conflict si el correo ya existe

### Requirement: Almacenamiento seguro de contraseñas

El sistema SHALL almacenar las contraseñas únicamente como hashes bcrypt y MUST NOT persistir contraseñas en texto plano.

#### Scenario: Hash al registrar

- **WHEN** se crea un usuario
- **THEN** la contraseña se transforma con bcrypt antes de insertarse en SQLite
- **AND** la respuesta al cliente no incluye el hash ni la contraseña

#### Scenario: Contraseña nunca expuesta

- **WHEN** cualquier endpoint devuelve información de un usuario
- **THEN** la respuesta omite los campos de contraseña y hash

### Requirement: Autenticación de usuarios

El sistema SHALL permitir el inicio de sesión de usuarios registrados mediante correo electrónico y contraseña.

#### Scenario: Inicio de sesión exitoso

- **WHEN** un usuario envía credenciales válidas
- **THEN** el backend compara la contraseña con el hash almacenado usando bcrypt
- **AND** emite un token de sesión o una cookie HTTP-Only
- **AND** responde con HTTP 200 OK

#### Scenario: Credenciales inválidas

- **WHEN** el correo no existe o la contraseña no coincide
- **THEN** el sistema responde con HTTP 401 Unauthorized
- **AND** no revela cuál de los dos datos es incorrecto

#### Scenario: Acceso a ruta protegida sin sesión

- **WHEN** una solicitud intenta acceder a una ruta que devuelve información privada sin un token o sesión válida
- **THEN** el middleware de autenticación responde con HTTP 401 Unauthorized
- **AND** no ejecuta el controlador de la ruta
