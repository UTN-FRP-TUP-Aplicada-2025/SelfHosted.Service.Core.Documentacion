# Blazor Interactive Server, el navegador y por qué hace falta declarar una matriz

**Fecha:** 2026-07-28
**Contexto:** cierre de la Fase A del orquestador SDD sobre `DEV/SelfHosted.Service.Core`. La matriz de navegadores es la última pendiente que espera decisión del agente humano y bloquea `03-UX-UI-DX` de `SelfHosted-Web`.
**Estado del intake:** v1.2, aprobado. Declara hoy «navegador de escritorio con soporte de WebSockets **[S]**», que es un supuesto sin valor concreto.
**Fuentes:** `SOLUTION-INTAKE-SelfHosted-Service-Core-v1.2.md` §17.1 P.1, P.9 y P.10; `Analisis-SaaS-Service/Analisis-Rayway.md` §8.2.

---

## Tabla de contenido

- [§1 La diferencia de fondo](#1--la-diferencia-de-fondo)
- [§2 Cuatro situaciones concretas](#2--cuatro-situaciones-concretas)
- [§3 El servicio corre en red local: qué cambia y qué no](#3--el-servicio-corre-en-red-local-qué-cambia-y-qué-no)
- [§4 Qué cambia según lo que se declare](#4--qué-cambia-según-lo-que-se-declare)
- [§5 Recomendación](#5--recomendación)
- [§6 Lo que falta decidir](#6--lo-que-falta-decidir)

---

## 1 · La diferencia de fondo

Una aplicación web tradicional envía HTML y JavaScript, y a partir de ahí el navegador resuelve solo. Si algo no está soportado, la pantalla se ve distinta pero la aplicación funciona.

**Blazor Interactive Server no funciona así.** Toda la interfaz vive en el servidor y el navegador es una pantalla conectada por una conexión permanente: un circuito SignalR sobre WebSockets. Cada clic, cada arrastre de un nodo del lienzo y cada tecla es un viaje de ida y vuelta.

El análisis de la plataforma de referencia lo señala como el riesgo dominante del proyecto, citando textualmente que *"every user interaction involves a network hop"*, y por eso el intake declara la puerta técnica PT-01 antes de comprometer el corte del lienzo.

La consecuencia práctica: **el navegador no afecta el aspecto, afecta si la aplicación funciona**.

---

## 2 · Cuatro situaciones concretas

### 2.1 La conexión cae a sondeo largo

SignalR tiene un plan alternativo: si no consigue establecer WebSockets, pasa a hacer preguntas repetidas por HTTP. La aplicación sigue funcionando, pero cada interacción pasa de milisegundos a cientos de milisegundos.

En una pantalla de formularios eso no se nota. **En el lienzo, arrastrar un nodo se convierte en una animación a saltos**, y la puerta técnica PT-01 —treinta nodos con fluidez— no pasa.

El intake ya lo identifica: garantizar WebSockets y no sondeo largo en la publicación del contenedor es la mitigación M4 del riesgo RG-01.

Ocurre con un proxy en el medio, con ciertas extensiones de navegador, o con configuraciones de seguridad que bloquean la actualización de protocolo.

### 2.2 El navegador suspende la pestaña en segundo plano

Los navegadores modernos duermen las pestañas que no están en foco para ahorrar batería y memoria. Si el panel queda abierto en otra pestaña y se vuelve a él después de un rato, **el circuito se cayó** y la aplicación muestra un intento de reconexión.

En este producto pesa más que en otros: el uso previsto incluye dejar el panel abierto mientras se hace otra cosa y volver a mirar cómo avanza un despliegue. Cada navegador tiene su propia política de suspensión y no son equivalentes.

### 2.3 El lienzo depende de capacidades gráficas del motor

La librería del lienzo dibuja nodos, aristas, minimapa y transformaciones. Las diferencias entre motores de renderizado en el manejo de gráficos vectoriales y de transformaciones son reales, y ahí es donde un navegador antiguo o uno con motor distinto muestra aristas corridas o zoom con saltos.

### 2.4 El guion de demostración no tiene contra qué correrse

El intake declara que **cada etapa cierra con un guion de demostración que se ejecuta en el navegador del host** y que el agente humano valida en un punto de control bloqueante.

Si no está declarado cuál es ese navegador, el guion dice «abrí el panel y arrastrá un nodo» sin especificar dónde, y dos ejecuciones del mismo guion pueden dar resultados distintos. El criterio de aceptación deja de ser verificable, que es lo que el propio intake exige de cada etapa.

---

## 3 · El servicio corre en red local: qué cambia y qué no

**Declaración del agente humano, 2026-07-28:** el servicio va a correr en red local, de modo que el costo del viaje de ida y vuelta no debería ser un problema.

Es correcto y reduce el riesgo principal. Conviene precisar el alcance de esa reducción, porque el riesgo tiene cuatro componentes y la red local sólo alcanza a uno.

**El intake ya asume red local.** No es una condición nueva: PT-01 mide *"con 30 nodos y 40 aristas… **en red local**"*, y el supuesto crítico está declarado como *"que un lienzo de treinta nodos sea fluido bajo Blazor Interactive Server **en red local**"*. El término aparece nueve veces en el documento. Los umbrales de la puerta técnica ya están fijados suponiendo esa condición.

Qué cambia y qué no:

| Componente del riesgo | ¿La red local lo resuelve? |
|---|---|
| **Latencia del viaje** | **Sí, en buena medida.** En red local el trayecto es de aproximadamente un milisegundo contra decenas o cientos en internet. Es el componente que la observación elimina |
| **Fallback a sondeo largo** (§2.1) | **Parcialmente.** Sigue ocurriendo si algo bloquea la actualización de protocolo, pero en red local su costo es mucho menor: el sondeo agrega latencia sobre un trayecto que ya es corto |
| **Suspensión de la pestaña** (§2.2) | **No.** Es una decisión del navegador sobre sus propias pestañas y no tiene relación con la red |
| **Capacidades gráficas del motor** (§2.3) | **No.** El dibujado ocurre enteramente en el navegador |
| **Memoria del circuito en el servidor** | **No.** PT-01 mide el consumo por circuito tras quince minutos de uso continuo, y eso depende del estado que el servidor mantiene por pestaña abierta, no del trayecto |

**Conclusión.** La red local elimina el componente de latencia, que era el más grande, y reduce el segundo. Los otros tres siguen intactos y son exactamente los que dependen del navegador. La observación **no elimina la necesidad de declarar la matriz**: la reduce a las tres razones que quedan, que son todas de navegador y ninguna de red.

Hay además una consecuencia favorable que conviene registrar: si la latencia deja de ser el factor dominante, **la mitigación M1 del riesgo RG-01 —mover el arrastre a JavaScript y notificar sólo al soltar— es menos probable que haga falta**. El intake ya la declara como decisión abierta para el Sprint 0, a resolverse midiendo en PT-01 y no antes. La red local hace más probable que la medición diga que no hace falta.

---

## 4 · Qué cambia según lo que se declare

| Si se declara | Qué ocurre |
|---|---|
| «Navegador de escritorio con soporte de WebSockets», que es lo de hoy | No hay criterio verificable. «No me anda» y «a mí sí» son las dos afirmaciones posibles y ninguna se puede dirimir. La categoría de experiencia de uso no sabe contra qué diseñar, y la de pruebas no sabe contra qué verificar |
| Una sola familia con versión mínima | El equipo prueba ahí, el guion de demostración dice ahí, y un fallo en otro navegador **no es un defecto**. Cierra la discusión antes de que exista |
| Dos o más familias | Se prueba en todas. Duplica el esfuerzo de los guiones de demostración, que el intake declara manuales y ejecutados por el agente humano en cada punto de control |

---

## 5 · Recomendación

Declarar **el navegador que el agente humano usa, con su versión mínima, y el resto como no soportado**.

No es una limitación del producto: es reconocer lo que ya es cierto. Hay un único usuario, el panel no sale de la red local, y el intake ya declara que no hay soporte para Windows ni macOS, ni como plataforma de ejecución ni de desarrollo. Declarar una sola familia de navegador es coherente con esa decisión.

No cierra ninguna puerta: agregar otra familia más adelante es barato y su único costo es ejecutar los guiones de demostración dos veces.

---

## 6 · Lo que falta decidir

Una sola cosa: **qué familia de navegador y desde qué versión**.

Si la versión exacta no está a mano, alcanza con la familia y se toma como mínima la instalada al momento de declararlo.

Con eso se cierra la última pendiente que bloquea `03-UX-UI-DX` de `SelfHosted-Web`, y las tres razones de §3 que la red local no resuelve pasan a tener un contexto verificable.

> **Decisión:**
>
>

---

## Control de cambios

| Versión | Fecha | Cambios | Autor |
|---|---|---|---|
| 1.0 | 2026-07-28 | Registro del análisis sobre la sensibilidad de Blazor Interactive Server al navegador y por qué hace falta declarar una matriz. Incluye las cuatro situaciones concretas, el contraste de qué componentes del riesgo resuelve la red local y cuáles no —a partir de la declaración del agente humano de que el servicio corre local—, y la verificación de que el intake ya asume esa condición en los umbrales de PT-01. Se registra la consecuencia favorable de que la mitigación M1 de RG-01 sea menos probable que haga falta. | Orquestador SDD (Claude Code) |
