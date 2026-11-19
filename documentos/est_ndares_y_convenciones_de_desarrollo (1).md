# Estándares y Convenciones de Desarrollo

**Proyecto:** Plataforma de Red Social Orientada a la Privacidad

**Documento:** SDD - Sección 3: Estándares de Código y Buenas Prácticas

**Stack:** HTML5, CSS3, JavaScript (ES6+), Node.js, SQLite3

---

## 1. Convenciones de Nomenclatura

Para garantizar la legibilidad y coherencia entre las capas del sistema, se definen los siguientes estándares por lenguaje:

| Elemento | Convención | Ejemplo |
| :--- | :--- | :--- |
| **Variables y Funciones (JS)** | `camelCase` | `const userEmail = ''`, `function getPosts()` |
| **Clases y Componentes (JS)** | `PascalCase` | `class UserSession {}` |
| **Constantes Globales (JS)** | `UPPER_SNAKE_CASE` | `const MAX_LOGIN_ATTEMPTS = 5;` |
| **Archivos JavaScript / Node.js** | `camelCase.js` | `authController.js`, `postModel.js` |
| **Archivos HTML / CSS** | `kebab-case` | `login-page.html`, `main-styles.css` |
| **Clases CSS (Metodología BEM)** | `kebab-case` | `.card-profile`, `.card-profile__title` |
| **Tablas y Columnas (SQLite)** | `snake_case` (Plural/Singular) | Tabla: `users`, Columna: `created_at` |
| **Rutas / Endpoints (API REST)** | `kebab-case` (Plural) | `/api/v1/user-profiles`, `/api/v1/auth/login` |

---

## 2. Estándares de Código Frontend (HTML, CSS, JS)

### 2.1 HTML5
* Usar etiquetas semánticas (`<header>`, `<nav>`, `<main>`, `<article>`, `<footer>`).
* Todos los elementos de formulario deben incluir su correspondiente `<label>` asociado mediante `for`/`id`.
* Prohibido el uso de estilos en línea (`style="..."`).

### 2.2 CSS3
* **Variables Globales:** Todo color, fuente o espaciado recurrente debe ser extraído a la pseudoclase `:root` en `styles.css`.
* **Metodología de Clases:** Preferir especificidad baja mediante clases sencillas (`.btn`, `.btn-primary`). Evitar selectores profundos anidados (`div > ul > li > a`).
* **Diseño Responsivo:** Usar unidades relativas (`rem`, `em`, `%`, `vh/vw`) en lugar de valores absolutos en píxeles (`px`) para layouts y tipografía.

### 2.3 JavaScript Vanilla (ES6+)
* **Declaración de Variables:** Usar exclusivamente `const` por defecto y `let` únicamente cuando la variable requiera reasignación. **Prohibido el uso de `var`**.
* **Funciones Asíncronas:** Emplear `async/await` en lugar de encadenamiento de promesas (`.then().catch()`) para mantener un código limpio al realizar peticiones `fetch`.
* **Manipulación del DOM:** Delegación de eventos y métodos nativos eficientes (`querySelector`, `addEventListener`). Evitar innerHTML directo con datos de usuario no sanitizados para prevenir ataques **XSS (Cross-Site Scripting)**.

```javascript
// Ejemplo de estándar en Frontend (api.js)
async function fetchUserPosts(userId) {
  try {
    const response = await fetch(`/api/posts?userId=${userId}`);
    if (!response.ok) throw new Error('Error al obtener publicaciones');
    return await response.json();
  } catch (error) {
    console.error('API Error:', error.message);
    throw error;
  }
}
```

---

## 3. Estándares de Código Backend (Node.js & Express)

### 3.1 Estructura y Modularidad
* Principio de Responsabilidad Única (SRP):
  * **Routes:** Solo definen endpoints y aplican middlewares.
  * **Controllers:** Procesan la solicitud HTTP, invocan servicios/modelos y estructuran la respuesta.
  * **Models:** Contienen exclusivamente las operaciones y consultas a la base de datos SQLite.

### 3.2 Manejo de Errores
* Todas las funciones controladoras asíncronas deben estar envueltas en bloques `try...catch` o utilizar un middleware de captura global.
* Las respuestas de error de la API deben seguir un formato JSON unificado:

```json
{
  "status": "error",
  "code": 400,
  "message": "Descripción clara del error para el cliente"
}
```

### 3.3 Respuestas Exitosas de la API
* Toda respuesta correcta debe retornar el código de estado HTTP adecuado (`200 OK`, `201 Created`) y mantener una estructura uniforme:

```json
{
  "status": "success",
  "data": { ... }
}
```

---

## 4. Estándares de Persistencia y Seguridad (SQLite3)

### 4.1 Prevención de Inyección SQL
* **Prohibido** concatenar variables en cadenas SQL (ej: `"SELECT * FROM users WHERE email = '" + email + "'"`).
* **Obligatorio** el uso de Sentencias Preparadas (*Prepared Statements* / parámetros de enlace `?`):

```javascript
// Ejemplo correcto en Node.js (postModel.js)
const stmt = db.prepare('SELECT * FROM posts WHERE user_id = ? AND visibility = ?');
const posts = stmt.all(userId, 'PUBLIC');
```

### 4.2 Integridad Referencial
* Toda conexión a SQLite debe inicializarse habilitando la verificación de claves foráneas:
```javascript
db.pragma('foreign_keys = ON');
```

---

## 5. Control de Versiones (Git)

### 5.1 Mensajes de Commit
Los commits deben redactarse en presente imperativo e indicar el alcance del cambio:

* `feat(auth): implementar generación de token JWT en login`
* `fix(privacy): corregir filtro de visibilidad en consulta de feed`
* `style(css): actualizar variables de color secundario`
* `docs(sdd): añadir esquema de base de datos`