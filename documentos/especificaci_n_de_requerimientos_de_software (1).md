# Especificación de Requerimientos de Software (SRS)
**Proyecto:** Plataforma de Red Social Orientada a la Privacidad  
**Arquitectura:** Monolítica ligera / API RESTful  
**Stack Tecnológico:** HTML5, CSS3, JavaScript (Vanilla), Node.js (Express), SQLite3  
**Documento del Sistema:** SDD - Sección 1: Requerimientos  

---

## 1. Introducción

### 1.1 Propósito
El presente documento define la Especificación de Requerimientos de Software (SRS) para el desarrollo de una plataforma de red social centrada en la protección de datos, control de privacidad y comunicación entre usuarios (Clientes). Este documento sirve como base técnica para el diseño de arquitectura, modelado de base de datos e implementación del sistema.

### 1.2 Alcance del Sistema
El sistema permitirá el registro de usuarios, autenticación segura mediante credenciales (correo electrónico y contraseña), publicación e interacción con contenido, y control explícito sobre la visibilidad de los datos compartidos. 

El backend estará construido en **Node.js**, utilizando una base de datos relacional embebida **SQLite3**. La interfaz web (Frontend) se desarrollará utilizando estándares web fundamentales (**HTML5, CSS3 y JavaScript Vanilla**).

---

## 2. Descripción General

### 2.1 Perspectiva del Producto
La aplicación opera bajo un esquema cliente-servidor HTTP/HTTPS. El cliente envía peticiones asíncronas (`fetch` API en JavaScript) al servidor backend en Node.js, el cual procesa las reglas de negocio y persiste la información en SQLite.

### 2.2 Clasificación de Actores
* **Cliente (Usuario Final):** Usuario registrado en la plataforma con capacidades para gestionar su perfil, publicar contenido, interactuar con otros usuarios y configurar la privacidad de sus datos.

---

## 3. Requerimientos Funcionales (RF)

### RF-01: Registro de Usuarios
* **Descripción:** El sistema debe permitir el registro de nuevos usuarios en la plataforma.
* **Entradas:** Nombre completo, dirección de correo electrónico, contraseña.
* **Proceso:**
  1. El usuario ingresa sus datos en el formulario HTML.
  2. El cliente valida el formato del correo electrónico y la longitud de la contraseña mediante JavaScript.
  3. El backend verifica que el correo electrónico no esté registrado previamente en la base de datos SQLite.
  4. La contraseña se procesa mediante un algoritmo de hashing seguro (`bcrypt`) antes de ser almacenada.
  5. Se inserta el nuevo registro en la base de datos.
* **Salida:** Respuesta de éxito (HTTP 201 Created) y redirección al inicio de sesión.

### RF-02: Autenticación de Usuarios (Login)
* **Descripción:** El sistema debe permitir el acceso seguro a usuarios registrados mediante correo electrónico y contraseña.
* **Entradas:** Correo electrónico y contraseña.
* **Proceso:**
  1. El servidor recibe las credenciales y consulta la base de datos SQLite por el correo proporcionado.
  2. Se compara la hash de la contraseña ingresada con la hash almacenada utilizando la librería de seguridad del backend.
  3. Si la verificación es satisfactoria, el servidor genera un token de sesión/autorización (JWT o Cookie de Sesión).
* **Salida:** Confirmación de autenticación (HTTP 200 OK) y entrega del token/sesión al cliente.

### RF-03: Gestión de Publicaciones
* **Descripción:** Los usuarios autenticados deben poder crear, consultar y eliminar publicaciones de texto o contenido multimedia dentro de la red social.
* **Entradas:** Texto del estado/publicación, nivel de privacidad seleccionado.
* **Proceso:**
  1. El cliente valida que la publicación no esté vacía.
  2. El servidor asocia la publicación al ID del usuario autenticado y guarda el registro en la tabla `Publicaciones` con su respectiva marca de tiempo (`timestamp`).
* **Salida:** Publicación reflejada de manera dinámica en la interfaz gráfica.

### RF-04: Control de Privacidad de Contenido
* **Descripción:** El sistema debe ofrecer opciones explícitas para restringir el alcance de la información compartida por el usuario.
* **Niveles de Privacidad:**
  * `PÚBLICO`: Visitable por cualquier usuario registrado.
  * `AMIGOS/CONTACTOS`: Visible solo por usuarios vinculados.
  * `PRIVADO`: Visible únicamente por el creador del contenido.
* **Proceso:** El backend filtra las consultas SQL (`SELECT`) aplicando cláusulas `WHERE` basadas en la relación entre el usuario solicitante y el propietario del contenido.

---

## 4. Requerimientos No Funcionales (RNF)

### RNF-01: Seguridad en el Almacenamiento
* Las contraseñas **nunca** deben guardarse en texto plano dentro de la base de datos SQLite.
* Se debe implementar un factor de costo adecuado para el algoritmo de hashing (ej. `bcrypt` con `saltRounds >= 10`).

### RNF-02: Integridad y Unicidad de Datos
* La dirección de correo electrónico debe ser única dentro del sistema. Esto se garantizará mediante la restricción de base de datos `UNIQUE` en la columna `email` de la tabla `Usuarios` y una verificación previa en Node.js.

### RNF-03: Rendimiento y Compatibilidad
* El frontend debe renderizarse correctamente en navegadores web modernos sin requerir frameworks adicionales de JavaScript (React, Angular o Vue).
* Las peticiones al backend deben realizarse de forma asíncrona mediante `Fetch API` para evitar la recarga completa de la página.

### RNF-04: Gestión de Base de Datos (SQLite)
* La base de datos estará estructurada en un archivo local `.sqlite` gestionado por el driver nativo de Node.js (`sqlite3` o `better-sqlite3`).
* Se deberán habilitar las restricciones de claves foráneas (`FOREIGN KEYS = ON`) en cada conexión para mantener la integridad referencial.

---

## 5. Reglas de Negocio (RN)

* **RN-01 (Unicidad de Cuenta):** Un usuario no puede registrarse con un correo electrónico que ya se encuentre almacenado en la base de datos.
* **RN-02 (Acceso Restringido):** Ninguna ruta del backend que devuelva información privada de publicaciones o perfiles puede responder a solicitudes sin un token o sesión válida de usuario.
* **RN-03 (Propiedad de Modificación):** Un usuario únicamente puede modificar o eliminar publicaciones creadas por su propio ID de cuenta.