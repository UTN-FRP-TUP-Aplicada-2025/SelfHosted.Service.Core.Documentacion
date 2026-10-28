# ia-db · SelfHosted.Service.Core

> **Instrucción para IA.** Este archivo es el **punto de entrada único** a la base de conocimiento del repositorio `SelfHosted.Service.Core`. Leelo completo antes de explorar el repositorio.
> **Cómo usarlo**: identificá tu tema en la tabla de navegación, abrí **únicamente** el índice que te corresponde (uno o dos, no más) y ampliá a los documentos fuente sólo si el índice resulta insuficiente. **No recorras el repositorio completo** ni cargues la documentación entera: para eso existe esta base.
> **Advertencia de ubicación**: la ia-db vive en el repositorio `SelfHosted.Service.Core.Documentacion`, y el corpus que indexa vive en el repositorio hermano `SelfHosted.Service.Core`. Todas las rutas con prefijo `SDD/...` son **relativas a la raíz de `SelfHosted.Service.Core`**, que desde acá es `../../SelfHosted.Service.Core/`.

---

## Navegación

| Necesitás saber… | Leé este índice |
|---|---|
| De qué trata el repositorio, cómo está organizado, en qué estado está | [`indexes/00_MASTER-INDEX.md`](indexes/00_MASTER-INDEX.md) |
| Qué es el producto, alcance, roadmap, stack, plataformas, riesgos | [`indexes/01_Contexto-Y-Producto.md`](indexes/01_Contexto-Y-Producto.md) |
| Qué problemas de negocio resuelve y con qué criterios de éxito | [`indexes/02_Necesidades-De-Negocio.md`](indexes/02_Necesidades-De-Negocio.md) |
| Qué hace el sistema: casos de uso y trazabilidad NB→CU→RN→US | [`indexes/03_Casos-De-Uso.md`](indexes/03_Casos-De-Uso.md) |
| Qué está permitido y qué prohibido: reglas de negocio | [`indexes/04_Reglas-De-Negocio.md`](indexes/04_Reglas-De-Negocio.md) |
| Qué entidades existen, cómo se relacionan y qué invariantes las rigen | [`indexes/05_Modelo-De-Datos.md`](indexes/05_Modelo-De-Datos.md) |
| Cómo se ve y se navega: superficies, wireframes, representaciones | [`indexes/06_UX-UI-DX.md`](indexes/06_UX-UI-DX.md) |
| Qué es la maqueta HTML, cómo se sirve y qué materializa | [`indexes/07_Maqueta-De-Validacion.md`](indexes/07_Maqueta-De-Validacion.md) |
| Cómo se trabaja acá: fases SDD, decisiones, versionado, estado de la corrida | [`indexes/08_Proceso-SDD-Y-Estado.md`](indexes/08_Proceso-SDD-Y-Estado.md) |
| Qué se auditó, con qué veredicto, y qué hallazgos siguen abiertos | [`indexes/09_Auditorias-Y-Migracion.md`](indexes/09_Auditorias-Y-Migracion.md) |
| Qué significa un término y con qué no hay que confundirlo | [`indexes/10_Glosario.md`](indexes/10_Glosario.md) |

**Rutas de entrada más frecuentes**, para no leer de más:

| Tarea | Secuencia mínima |
|---|---|
| Entender el producto de cero | `00` → `01` |
| Especificar o revisar una funcionalidad | `03` → `04` (y `05` si toca datos) |
| Diseñar o revisar una pantalla | `06` → `07` |
| Retomar la corrida SDD donde quedó | `08` → `09` |
| Escribir cualquier documento del corpus | `10` (vocabulario) → `08` (versionado y archivado) |

---

## Resumen ejecutivo

| Dato | Valor |
|---|---|
| Repositorio indexado | `SelfHosted.Service.Core` · rama `main` · `github.com/UTN-FRP-TUP-Aplicada-2025/SelfHosted.Service.Core` |
| Producto | **SelfHosted Service** |
| Tipo de contenido | **Corpus documental SDD** (especificación). **No hay código**: el proyecto de código no está iniciado |
| Framework normativo | Conjunto SDD **6.0**, cerrado el 2026-07-30 |
| Stack previsto | .NET 10 · Blazor Interactive Server · MudBlazor · Z.Blazor.Diagrams · EF Core / SQLite (WAL) · Docker.DotNet tras `IContenedorEngine` |
| Volumen | 165 documentos vigentes (37.172 líneas) + 375 archivos en `_legacy/` |
| Estado | Fases A y B **aprobadas**; Fase B2 (maqueta) con re-audit aprobado el 2026-08-02; **detenida** en la reconstrucción de la maqueta; categorías 05–11 no iniciadas |

**Función principal del producto.** Panel web autoalojado para declarar, desplegar y operar arquitecturas de contenedores en un servidor de red local, con un lienzo visual como superficie central. Su diferencial declarado es la **adopción del parque existente sin reinstanciar**: incorpora contenedores que ya corren en lugar de exigir recrearlos. Un solo usuario administrador, una sola instancia, sin salida a internet.

**Arquitectura prevista, en una línea.** Un único proyecto de código `SelfHosted.Service.Core` de tipo `web-monolith` —el **caso degenerado** del framework— con las cuatro capas de Clean Architecture separadas por espacio de nombres (`Domain` → `Application` → `Infrastructure` → `Web`) y la regla de dependencia impuesta por un test de arquitectura bloqueante, no por el compilador.

**Inventario del corpus**: 8 necesidades de negocio · 39 casos de uso · 40 reglas de negocio · 15 entidades y 19 reglas conceptuales · 19 superficies con sus wireframes · 4 representaciones visuales · 20 páginas de maqueta · 14 informes de auditoría.

---

## Estructura

```
ia-db/
├── README.md                        ← este archivo · punto de entrada único
└── indexes/
    ├── 00_MASTER-INDEX.md           Visión general, mapa del corpus, decisiones estructurales, estado, vigencia
    ├── 01_Contexto-Y-Producto.md    Visión, alcance (F-xx), roadmap, acuerdo de equipo, plataformas, stack, flags
    ├── 02_Necesidades-De-Negocio.md Catálogo NB-01..NB-08 con criterios de éxito y actores
    ├── 03_Casos-De-Uso.md           Catálogo CU-01..CU-39, áreas funcionales, matriz de trazabilidad
    ├── 04_Reglas-De-Negocio.md      Catálogo RN-01..RN-40 por área, índices inversos CU→RN y momento→RN
    ├── 05_Modelo-De-Datos.md        Modelo conceptual (erDiagram), catálogo RC-01..RC-19, brechas
    ├── 06_UX-UI-DX.md               Superficies SUP-01..SUP-19, mapa de navegación, 4 representaciones
    ├── 07_Maqueta-De-Validacion.md  Maqueta HTML/CSS/JS: inventario, assets, criterio de construcción
    ├── 08_Proceso-SDD-Y-Estado.md   Fases SDD, decisiones D-x, versionado y archivado, puntos abiertos
    ├── 09_Auditorias-Y-Migracion.md 14 informes con veredicto y severidades, migración 4.1 → 6.0
    └── 10_Glosario.md               120 términos de dominio + 72 de interfaz, pares que se confunden
```

Correspondencia con el repositorio indexado (rutas relativas a la raíz de `SelfHosted.Service.Core`):

```
SelfHosted.Service.Core/
├── CHANGELOG.md                     ⚠ desactualizado en los conteos — ver 00 §6
├── README.md                        una línea
└── SDD/
    ├── Intake/                      intake del cliente + manifiesto derivado   → 01
    ├── Docs/
    │   ├── 00-Contexto/                                                        → 01
    │   ├── 01-Necesidades-Negocio/                                             → 02
    │   ├── 02-Especificacion-Funcional/  casos de uso, reglas, modelo          → 03, 04, 05, 10
    │   ├── 03-UX-UI-DX/             wireframes y representaciones              → 06, 10
    │   └── Audit/                   14 informes                                → 09
    ├── Estado/                      documentos de proceso de la corrida        → 08
    └── Maquetas/                    maqueta de validación visual               → 07
```

Las carpetas `_legacy/<fecha>/` distribuidas por el árbol (375 archivos) **no están indexadas**: son estados previos archivados. Su convención se explica en el índice `08`.

---

## Restricciones para IA

Qué **no** debe hacer un agente que consuma esta base:

1. **No leer el repositorio completo.** Entrá por este README, cargá uno o dos índices y ampliá a fuentes sólo ante insuficiencia comprobada.
2. **No indexar ni citar `_legacy/`.** Son estados superados. Si necesitás historia, leé el control de cambios del documento vigente.
3. **No usar `CHANGELOG.md` como fuente de conteos** — declara 36 CU y 37 RN, y en disco hay 39 y 40. La fuente correcta es `SDD/Docs/02-Especificacion-Funcional/Especificacion-Funcional.md` §1.
4. **No inferir lo que las fuentes no declaran.** El corpus marca sus brechas explícitamente; una brecha se reporta, no se completa.
5. **No modificar el repositorio indexado desde esta base.** La ia-db es de sólo lectura respecto de `SelfHosted.Service.Core`.
6. **No escribir en el corpus sin respetar su protocolo**: archivado previo en el `_legacy/<fecha>/` de la propia carpeta, bump de versión y fila nueva en el control de cambios; las filas históricas nunca se reescriben. Detalle en el índice `08`.
7. **No confundir el framework con el destino.** El framework normativo SDD vive en `IA/IA.SDD`, es otro repositorio, y el orquestador de un destino **nunca lo modifica**.
8. **No tratar los artefactos como aprobados individualmente**: los de las categorías 02 y 03 están en estado `Propuesto`. Lo aprobado son los cortes de fase.
9. **No dar por resueltos los hallazgos abiertos.** Hay 30 verificables (0 P0, 1 P1, 17 P2, 12 P3) y varios P2/P3 sin cierre declarado.
10. **Ante divergencia entre índice y fuente, prevalece la fuente**: corregí el índice y eliminá la referencia obsoleta.

---

## Manifiesto de generación

- Generado por : `/IA/IA.Prompts/Tool-Prompts/Indexado-Documentado/Iniciar-Indexado.md`
- Profile       : `/IA/IA.Prompts/PromptFramework/Profiles/Knowledge-Indexing.md`
- Alcance       : `DEV/SelfHosted.Service.Core` (modo proyecto, destino explícito en el repositorio hermano `DEV/SelfHosted.Service.Core.Documentacion/ia-db`)
- Fuentes       : `SDD/Intake/`, `SDD/Docs/00-Contexto/`, `SDD/Docs/01-Necesidades-Negocio/`, `SDD/Docs/02-Especificacion-Funcional/`, `SDD/Docs/03-UX-UI-DX/`, `SDD/Docs/Audit/`, `SDD/Estado/`, `SDD/Maquetas/`, `CHANGELOG.md`, `README.md`
- Exclusiones   : `.git/`, `**/_legacy/`, y las exclusiones estándar del Profile (dependencias, artefactos de build, binarios)
- Estado leído  : árbol de trabajo en disco, por delante del commit `82fb235`
- Generado      : 2026-08-07 · Versión: 1.0
- Actualizar    : `/IA/IA.Prompts/Tool-Prompts/Indexado-Documentado/Actualizar-Indexado.md`
