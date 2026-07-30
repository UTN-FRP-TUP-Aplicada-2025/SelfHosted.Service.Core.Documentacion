# Etapa `<orden>` · `<Nombre de la etapa>`

> Plantilla del informe de cierre de etapa (Requerimientos-Funcionales §2.5).
> Copiar este archivo como `<orden>-<etapa>.md`, borrar las notas en cita y completar **todas** las
> secciones. Una sección que no aplica se deja escrita con "No aplica" y el motivo; no se elimina.

## 1. Identificación

| Campo | Valor |
|---|---|
| **Etapa** | `<orden>` · `<nombre>` |
| **Tipo** | `HI` (valida el agente humano) / `HD` (se demuestra al cliente) |
| **Fecha de cierre** | `AAAA-MM-DD` |
| **Implementa** | §… del análisis integrado |
| **Estado** | `Pendiente de validación` / `Validada` / `Con correcciones pedidas` |

## 2. Qué se entregó

> Una frase: qué puede hacer el usuario que antes no podía.

Después de la frase, el alcance realmente implementado. Si difiere de lo planificado, explicar la
diferencia y el motivo.

## 3. Qué quedó fuera

- `<cosa que no se implementó>` — se retoma en la etapa `<orden>`.

> Incluye lo declarado fuera de alcance y lo que se pospuso sobre la marcha. Sirve para no reportar
> como falla algo que todavía no existe.

## 4. Cómo lo levanto

**Estado de partida:** `<base de datos vacía / con datos de ejemplo / contenedores previos>` y cómo
se llega a él.

| # | Dónde | Comando | Qué hace |
|---|---|---|---|
| 1 | devcontainer | `scripts/…` | … |
| 2 | host | abrir `http://…` | … |

**URL publicada:** `http://…`

> Sin pasos manuales fuera de `scripts/`. Todo comando listado acá fue ejecutado tal cual está
> escrito.

## 5. Claves y credenciales

| Qué | Valor / dónde está | Quién la genera | Cómo se regenera |
|---|---|---|---|
| `<usuario de prueba>` | `<valor>` | … | … |
| `<contraseña de prueba>` | `<valor>` | … | … |
| `<token / clave / secreto>` | `<archivo o variable>` | … | … |

Cómo volver al estado inicial (borrar credenciales y repetir el alta): `<comando>`.

> Las credenciales de ejemplo del entorno de desarrollo se escriben completas. **Nunca** se
> transcribe un secreto de producción ni una contraseña real elegida por el agente humano: en su
> lugar se indica dónde consultarla.

## 6. Qué probar, paso a paso

| # | Acción | Resultado esperado |
|---|---|---|
| 1 | … | … |
| 2 | … | … |

> Cada paso observable en el navegador o en la consola. "Funciona correctamente" no es un resultado
> esperado.

## 7. Casos de ejemplo

**Datos con los que probar**

- `<campo>`: `<valor concreto>`

**Caso de error esperado**

| Qué hago | Qué debe pasar | Mensaje exacto |
|---|---|---|
| … | se rechaza | `<texto que muestra la interfaz>` |

## 8. Qué debería ver

- **Bien:** `<pantallas, estados, mensajes, códigos de respuesta, filas en la base, contenedores>`.
- **Mal:** `<síntomas que indican que algo falló y qué significan>`.

## 9. Cómo está armado el proyecto

> Prosa, no lista de archivos. Qué proyecto o carpeta aparece o cambia por primera vez en esta
> etapa, qué responsabilidad tiene, cómo se relaciona con las demás capas y por qué está donde
> está. Es la sección que permite entender la solución sin leerla entera.

## 10. Criterios de aceptación

- [ ] `<criterio de la etapa>`
- [ ] `<criterio de la etapa>`

Criterios sin marcar y su motivo: `<…>` / ninguno.

## 11. No-regresión

| Guion re-ejecutado | Resultado | Qué se tocó |
|---|---|---|
| Etapa `a` | pasa / falla | — |

## 12. Problemas conocidos

| Problema | Impacto en la demostración | Cuándo se resuelve |
|---|---|---|
| … | … | … |

> Es preferible declararlos acá a que aparezcan durante el punto de control.

## 13. Qué habilita

- Desbloquea la etapa `<orden>` porque `<motivo>`.
- Decisión tomada en esta etapa que condiciona lo que viene: `<…>`.
