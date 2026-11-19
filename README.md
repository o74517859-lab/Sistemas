# Red Social Orientada a la Privacidad

Proyecto desarrollado con **Spec-Driven Development (SDD)** usando [OpenSpec](https://openspec.dev). Las especificaciones, propuestas y tareas viven en `openspec/` y son la fuente de verdad antes de escribir código.

## Estado actual

- Documentación base en `documentos/` (SRS, arquitectura, estándares y guía de interfaz).
- OpenSpec inicializado con el perfil `core` y los workflows instalados para OpenCode en `.opencode/`.
- Especificaciones iniciales derivadas del SRS en `openspec/specs/auth` y `openspec/specs/posts`.
- Aún no existe código de la aplicación.

## Requisitos

- Node.js 22.12.0 o superior.
- npm 10 o superior.

## Instalación

OpenSpec se instala como dependencia de desarrollo del proyecto, sin necesidad de una instalación global:

```bash
npm install
npx openspec --version
```

## Comandos útiles

| Comando | Descripción |
| --- | --- |
| `npm run spec:view` | Panel general de especificaciones y cambios. |
| `npm run spec:list` | Lista los cambios activos. |
| `npm run spec:validate` | Valida todas las especificaciones y cambios. |
| `npm run spec:update` | Actualiza los archivos generados por OpenSpec. |
| `npx openspec list --specs` | Lista las capacidades especificadas. |
| `npx openspec show <nombre>` | Muestra un cambio o una especificación. |

## Flujo de trabajo

1. **Explorar** una idea con `/opsx-explore` si todavía no está clara.
2. **Proponer** el cambio con `/opsx-propose <descripción>`. Se crea `openspec/changes/<nombre>/` con propuesta, especificaciones, diseño y tareas.
3. **Revisar** los artefactos generados antes de implementar.
4. **Implementar** con `/opsx-apply`.
5. **Validar** con `npm run spec:validate`.
6. **Archivar** el cambio con `/opsx-archive`; las especificaciones principales se actualizan automáticamente.

## Estructura

```text
.
├── documentos/              # SRS, arquitectura, estándares y guía de UI
├── openspec/
│   ├── config.yaml          # Contexto y reglas del proyecto para la IA
│   ├── specs/               # Especificaciones principales (fuente de verdad)
│   │   ├── auth/spec.md
│   │   └── posts/spec.md
│   └── changes/             # Cambios en curso y archivo histórico
├── .opencode/               # Skills y comandos de OpenSpec para OpenCode
├── package.json
└── .gitignore
```

## Configuración

`openspec/config.yaml` contiene el contexto del proyecto (stack, arquitectura, convenciones y documentos fuente) y reglas por artefacto. Todo cambio en la forma de planificar debe reflejarse allí.

Para cambiar los workflows instalados o el modo de entrega:

```bash
npx openspec config profile
npx openspec update
```

## Documentación

- OpenSpec: <https://openspec.dev/docs>
- Inicio rápido: <https://openspec.dev/docs/quickstart>
- Referencia CLI: <https://openspec.dev/docs/cli>
