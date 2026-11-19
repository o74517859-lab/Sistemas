# AGENTS.md

## Proyecto

Plataforma de Red Social Orientada a la Privacidad. Monolito ligero cliente-servidor de 3 capas:

- **Frontend:** HTML5, CSS3 y JavaScript Vanilla (sin frameworks).
- **Backend:** Node.js + Express (API REST versionada en `/api/v1/...`).
- **Datos:** SQLite3 con sentencias preparadas y `PRAGMA foreign_keys = ON`.

La documentación fuente está en `documentos/`:

- `especificaci_n_de_requerimientos_de_software (1).md` — SRS (RF, RNF, RN).
- `documento_de_arquitectura_de_software (1).md` — capas y estructura de directorios.
- `est_ndares_y_convenciones_de_desarrollo (1).md` — nomenclatura, API y seguridad.
- `gu_a_de_dise_o_de_interfaz_frontend (1).md` — UI/UX, paleta y componentes.

## Desarrollo guiado por especificaciones

Este proyecto usa OpenSpec. Las especificaciones son la fuente de verdad y todo trabajo empieza como un cambio en `openspec/changes/`.

1. `/opsx-explore` — explorar una idea cuando aún no está definida.
2. `/opsx-propose <descripción>` — crear propuesta, especificaciones, diseño y tareas.
3. `/opsx-apply` — implementar las tareas del cambio activo.
4. `/opsx-sync` — sincronizar especificaciones sin archivar el cambio.
5. `/opsx-update` — revisar los artefactos de un cambio.
6. `/opsx-archive` — archivar el cambio y actualizar `openspec/specs/`.

Antes de archivar, ejecutar `npm run spec:validate`.

## Reglas no negociables

- Las contraseñas se almacenan con bcrypt (`saltRounds >= 10`); nunca en texto plano.
- Todo SQL usa sentencias preparadas; prohibido concatenar entradas del usuario.
- Las rutas que devuelven información privada requieren sesión válida.
- Un usuario solo puede modificar o eliminar sus propios recursos.
- Las publicaciones se filtran en el backend según su nivel de privacidad.
- Los errores de la API usan `{"status":"error","code":400,"message":"..."}`.
- Las respuestas exitosas usan `{"status":"success","data":{...}}`.
- Escribir especificaciones y documentación en español; conservar las palabras estructurales `SHALL`/`MUST` y los encabezados de OpenSpec en inglés.

## Comandos

- `npm run spec:view` — panel de especificaciones y cambios.
- `npm run spec:validate` — validar todo.
- `npm run spec:update` — refrescar archivos generados.
- `npx openspec list` / `npx openspec show <nombre>` — consultar cambios y especificaciones.
