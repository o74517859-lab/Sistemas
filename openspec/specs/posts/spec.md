# Publicaciones y Privacidad

## Purpose

Definir la creación, consulta y eliminación de publicaciones, junto con los niveles de privacidad y el filtrado de contenido según la relación entre el solicitante y el propietario, de modo que ningún usuario acceda a información que no le corresponde.

## Requirements

### Requirement: Creación de publicaciones

El sistema SHALL permitir a los usuarios autenticados crear publicaciones de texto o contenido multimedia con un nivel de privacidad.

#### Scenario: Publicación exitosa

- **WHEN** un usuario autenticado envía un texto no vacío y un nivel de privacidad válido
- **THEN** el backend asocia la publicación al ID del usuario autenticado
- **AND** guarda el registro con su marca de tiempo
- **AND** la publicación aparece en la interfaz del usuario

#### Scenario: Publicación vacía

- **WHEN** un usuario intenta publicar un texto vacío
- **THEN** el cliente muestra un error de validación y no envía la solicitud
- **AND** el backend rechaza la solicitud con HTTP 400 Bad Request si se envía de todos modos

#### Scenario: Usuario no autenticado

- **WHEN** una solicitud de creación llega sin un token o sesión válida
- **THEN** el middleware de autenticación responde con HTTP 401 Unauthorized
- **AND** no se crea ninguna publicación

### Requirement: Niveles de privacidad

El sistema SHALL ofrecer los niveles de privacidad PÚBLICO, AMIGOS/CONTACTOS y PRIVADO para cada publicación.

#### Scenario: Nivel público

- **WHEN** una publicación tiene nivel PÚBLICO
- **THEN** cualquier usuario registrado puede verla

#### Scenario: Nivel amigos o contactos

- **WHEN** una publicación tiene nivel AMIGOS/CONTACTOS
- **THEN** solo el creador y los usuarios vinculados como amigos o contactos pueden verla

#### Scenario: Nivel privado

- **WHEN** una publicación tiene nivel PRIVADO
- **THEN** únicamente el creador puede verla

### Requirement: Filtrado de publicaciones por privacidad

El sistema SHALL filtrar las consultas de publicaciones en el backend según la relación entre el usuario solicitante y el propietario del contenido.

#### Scenario: Consulta del feed

- **WHEN** un usuario autenticado solicita el listado de publicaciones
- **THEN** el backend ejecuta una consulta SQL preparada que incluye las publicaciones públicas, las propias y las de amigos o contactos
- **AND** excluye las publicaciones privadas de otros usuarios
- **AND** responde con los datos en formato JSON

#### Scenario: Acceso directo a publicación restringida

- **WHEN** un usuario solicita el detalle de una publicación que no le corresponde por su nivel de privacidad
- **THEN** el sistema responde con HTTP 403 Forbidden o HTTP 404 Not Found
- **AND** no devuelve el contenido de la publicación

### Requirement: Eliminación de publicaciones

El sistema SHALL permitir a un usuario eliminar únicamente las publicaciones creadas por su propia cuenta.

#### Scenario: Eliminación por el propietario

- **WHEN** el creador solicita eliminar su propia publicación
- **THEN** el backend elimina el registro de SQLite
- **AND** responde con HTTP 200 OK

#### Scenario: Eliminación de una publicación ajena

- **WHEN** un usuario autenticado intenta eliminar una publicación de otro usuario
- **THEN** el sistema responde con HTTP 403 Forbidden
- **AND** no modifica la base de datos

#### Scenario: Publicación inexistente

- **WHEN** un usuario solicita eliminar una publicación que no existe
- **THEN** el sistema responde con HTTP 404 Not Found
