# Guía de Estilos y Diseño de Interfaz (Frontend UI/UX)

**Proyecto:** Plataforma de Red Social Orientada a la Privacidad  
**Documento:** SDD - Guía de Estilos de Interfaz  
**Tecnologías:** HTML5, CSS3 Vanilla, JavaScript  

---

## 1. Concepto Visual y Principios de Diseño

El diseño de la interfaz busca transmitir **seguridad, claridad y simplicidad**. Al tratarse de una red social enfocado en la privacidad de los datos, la experiencia visual debe evitar la sobrecarga de información y priorizar la legibilidad del contenido, así como la distinción clara de los controles de privacidad.

---

## 2. Paleta de Colores

Para mantener la consistencia en el desarrollo con CSS3, los colores se estructuran mediante variables globales en el pseudo-clase `:root`.

### 2.1 Especificación Cromática

| Función | Nombre del Color | Código Hex / HSL | Uso Principal |
| :--- | :--- | :--- | :--- |
| **Primario** | Azul Noche (`--color-primary`) | `#0F172A` | Barras de navegación, encabezados, texto principal. |
| **Secundario** | Azul Cobalto (`--color-secondary`) | `#2563EB` | Enlaces, estados hover, botones secundarios. |
| **Acento** | Verde Esmeralda (`--color-accent`) | `#10B981` | Botones de acción principal (Publicar, Guardar), indicadores de privacidad. |
| **Fondo Global** | Gris Claro (`--color-bg-main`) | `#F8FAFC` | Fondo general de la aplicación. |
| **Fondo Tarjetas** | Blanco Puro (`--color-bg-card`) | `#FFFFFF` | Contenedores de publicaciones, formularios, modales. |
| **Texto Principal**| Antracita (`--color-text-main`) | `#1E293B` | Cuerpos de texto, títulos. |
| **Texto Muted** | Gris Medio (`--color-text-muted`) | `#64748B` | Fechas, subtítulos, etiquetas de privacidad. |
| **Bordes** | Gris Neutro (`--color-border`) | `#E2E8F0` | Líneas divisoras, bordes de inputs y tarjetas. |
| **Alerta/Error** | Rojo Carmín (`--color-error`) | `#EF4444` | Errores de validación, eliminar contenido. |

### 2.2 Variables de Color en CSS

```css
:root {
  --color-primary: #0f172a;
  --color-secondary: #2563eb;
  --color-accent: #10b981;
  --color-bg-main: #f8fafc;
  --color-bg-card: #ffffff;
  --color-text-main: #1e293b;
  --color-text-muted: #64748b;
  --color-border: #e2e8f0;
  --color-error: #ef4444;
}
```

---

## 3. Tipografía

Se utiliza la fuente **Inter** por su alta legibilidad en pantallas digitales, con respaldo en la pila de fuentes sans-serif nativas del sistema operativo.

### 3.1 Pila de Fuentes (Font Family)

```css
:root {
  --font-family-base: 'Inter', system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
}
```

### 3.2 Escala Tipográfica

| Nivel | Tamaño (`rem`/`px`) | Peso (`font-weight`) | Altura de Línea (`line-height`) | Uso |
| :--- | :--- | :--- | :--- | :--- |
| **H1** | `1.875rem` (30px) | Bold (`700`) | `1.2` | Títulos principales de sección/pantalla. |
| **H2** | `1.5rem` (24px) | SemiBold (`600`) | `1.3` | Títulos de publicaciones, nombres de perfil. |
| **H3** | `1.25rem` (20px) | Medium (`500`) | `1.4` | Subtítulos de módulos, títulos de modales. |
| **Body** | `1rem` (16px) | Regular (`400`) | `1.5` | Texto principal de la publicación y formularios. |
| **Small** | `0.875rem` (14px) | Regular (`400`) | `1.4` | Meta-información (fechas, nivel de privacidad). |

---

## 4. Estilos de Componentes Base

### 4.1 Botones (`.btn`)

```css
.btn {
  font-family: var(--font-family-base);
  font-size: 0.938rem;
  font-weight: 500;
  padding: 0.625rem 1.25rem;
  border-radius: 6px;
  border: none;
  cursor: pointer;
  transition: background-color 0.2s ease-in-out;
}

.btn-primary {
  background-color: var(--color-accent);
  color: #ffffff;
}

.btn-primary:hover {
  background-color: #059669;
}

.btn-secondary {
  background-color: var(--color-secondary);
  color: #ffffff;
}

.btn-secondary:hover {
  background-color: #1d4ed8;
}
```

### 4.2 Campos de Entrada (`.input-control`)

```css
.input-control {
  width: 100%;
  padding: 0.75rem 1rem;
  font-family: var(--font-family-base);
  font-size: 1rem;
  color: var(--color-text-main);
  background-color: var(--color-bg-card);
  border: 1px solid var(--color-border);
  border-radius: 6px;
  outline: none;
  transition: border-color 0.2s ease-in-out;
}

.input-control:focus {
  border-color: var(--color-secondary);
  box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.15);
}
```

### 4.3 Tarjeta de Contenido / Publicación (`.card`)

```css
.card {
  background-color: var(--color-bg-card);
  border: 1px solid var(--color-border);
  border-radius: 8px;
  padding: 1.5rem;
  margin-bottom: 1.25rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
}
```

---

## 5. Indicadores Visuales de Privacidad

Dado el requerimiento del sistema sobre control de privacidad, cada publicación o dato compartido debe incorporar una etiqueta visual distintiva:

* **Público:** Icono / Etiqueta con color `--color-secondary`.
* **Solo Amigos:** Icono / Etiqueta con color `--color-accent`.
* **Privado:** Icono / Etiqueta con color `--color-text-muted`.