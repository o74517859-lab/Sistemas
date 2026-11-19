# Documento de Arquitectura de Software (SAD)

**Proyecto:** Plataforma de Red Social Orientada a la Privacidad  
**Documento:** SDD - Sección 2: Arquitectura del Sistema  
**Estilo Arquitectónico:** Monolito Ligero / Arquitectura Cliente-Servidor (Capas Desacopladas)  
**Stack:** Node.js (Express), SQLite3, HTML5 / CSS3 / JavaScript (Vanilla)

---

## 1. Visión General de la Arquitectura

El sistema utiliza un patrón **Cliente-Servidor de 3 Capas**, diseñado para maximizar el rendimiento y la simplicidad de despliegue en un entorno liviano.

1. **Capa de Presentación (Frontend):** Cliente estático construido en HTML5, CSS3 nativo y JavaScript Vanilla. Se comunica de forma asíncrona mediante la API `fetch` en formato JSON.
2. **Capa de Negocio (Backend):** Servidor HTTP desarrollado en Node.js con Express. Maneja la lógica de control de acceso, enrutamiento, validaciones de seguridad, cifrado de datos y reglas de privacidad.
3. **Capa de Datos (Persistencia):** Motor de base de datos relacional relacional embebido **SQLite3**. Accedido a través del driver asíncrono en Node.js.

```
+-------------------------------------------------------------+
|                CAPA DE PRESENTACIÓN (CLIENTE)               |
|            HTML5 + CSS3 + JavaScript (Vanilla)              |
+-------------------------------------------------------------+
                               |
                        Peticiones HTTP/REST (JSON / fetch)
                               v
+-------------------------------------------------------------+
|                 CAPA DE NEGOCIO (BACKEND)                   |
|                   Node.js + Express.js                      |
|                                                             |
|   +-------------------+  +-------------------------------+  |
|   | Controller Layer  |  | Auth & Privacy Middleware     |  |
|   +-------------------+  +-------------------------------+  |
+-------------------------------------------------------------+
                               |
                       Consultas SQL (Better-SQLite3)
                               v
+-------------------------------------------------------------+
|                CAPA DE DATOS (PERSISTENCIA)                 |
|                     Base de Datos SQLite3                   |
+-------------------------------------------------------------+
```

---

## 2. Estructura de Directorios del Proyecto

Organización modular del proyecto para separar la lógica del servidor de los activos públicos del cliente.

```text
/red-social-privacidad
├── /src
│   ├── /config
│   │   └── database.js         # Configuración y conexión a SQLite3
│   ├── /controllers
│   │   ├── authController.js   # Lógica de login y registro
│   │   ├── userController.js   # Gestión de usuarios y perfiles
│   │   └── postController.js   # Lógica de publicaciones y privacidad
│   ├── /middlewares
│   │   ├── authMiddleware.js   # Verificación de tokens de sesión
│   │   └── errorMiddleware.js  # Captura global de errores
│   ├── /models
│   │   ├── userModel.js        # Consultas SQL relacionadas con usuarios
│   │   └── postModel.js        # Consultas SQL de publicaciones y permisos
│   └── /routes
│       ├── authRoutes.js       # Endpoints de autenticación
│       ├── userRoutes.js       # Endpoints de usuarios
│       └── postRoutes.js       # Endpoints de publicaciones
├── /public                     # Archivos estáticos servidos al frontend
│   ├── /css
│   │   └── styles.css          # Variables globales y componentes CSS
│   ├── /js
│   │   ├── api.js              # Envoltorio para peticiones fetch
│   │   ├── auth.js             # Lógica del cliente para inicio de sesión
│   │   └── feed.js             # Renderizado dinámico del contenido
│   ├── login.html
│   ├── register.html
│   └── index.html
├── .env                        # Variables de entorno (Secret keys, puerto)
├── database.sqlite             # Archivo físico de la base de datos
├── app.js                      # Punto de entrada de la aplicación Node.js
└── package.json
```

---

## 3. Flujo de Datos y Comunicaciones

### 3.1 Flujo de Autenticación y Autorización
1. El usuario envía sus datos desde `login.html`.
2. `auth.js` realiza un `POST` a `/api/auth/login`.
3. `authController.js` consulta a `userModel.js` para recuperar el usuario de SQLite.
4. Se compara la clave hash con `bcrypt`.
5. Si es correcto, el backend emite un token (JWT o Cookie HTTP-Only).
6. Las siguientes peticiones protegidas incluyen este token en la cabecera `Authorization`.

### 3.2 Flujo de Consulta de Publicaciones con Filtro de Privacidad
1. El cliente efectúa una petición `GET` a `/api/posts`.
2. El middleware `authMiddleware.js` extrae el `userID` del solicitante.
3. `postModel.js` ejecuta una consulta SQL a la base de datos SQLite filtrando los registros:
   ```sql
   SELECT * FROM posts 
   WHERE visibility = 'PRACTICE_PUBLIC' 
      OR user_id = ? 
      OR (visibility = 'PRACTICE_FRIENDS' AND user_id IN (SELECT friend_id FROM friends WHERE user_id = ?));
   ```
4. El servidor responde con los datos procesados en formato JSON.
5. `feed.js` manipula el DOM para mostrar las publicaciones al usuario.

---

## 4. Estrategia de Seguridad y Privacidad

* **Protección de Credenciales:** Hashing de contraseñas mediante `bcrypt` con factor de coste ajustado (mínimo 10 rondas).
* **Control de Acceso Basado en Roles/Propiedad:** Validación estricta a nivel de servidor (Middleware) para verificar que ningún usuario modifique o elimine recursos ajenos.
* **Integridad Referencial:** Habilitación explícita de `PRAGMA foreign_keys = ON;` en cada apertura de conexión con SQLite3.
* **Inyección SQL:** Consultas preparadas (*Prepared Statements*) mediante los métodos nativos del driver de SQLite para sanitizar cualquier entrada del usuario.