
# Requerimientos Funcionales

Este documento hereda todos los requerimientos técnicos de `/DEV/SelfHosted.Service.Core.Documentos/PROMPTs/02-Ejecutar-Prompt-Integrador-Documento-Intake/INPUTs/Analisis-Final-Integrado.md` y se toman las siguientes decisiones:

## 1. Requerimientos funcionales

- **Un único usuario administrador.** No hay gestión de múltiples usuarios, roles ni permisos.
- **Alta en el primer arranque.** Cuando el repositorio se configura por primera vez, el sistema
  debe solicitar nombre de usuario y contraseña, incluida la validación de la contraseña.
- **Sesión.** En cada inicio posterior debe poder recordar las credenciales de la sessión.
- **Cambio de contraseña.** Debe poder realizarse desde la barra superior, junto con el cierre de
  sesión.

---

## 2. Etapas de desarrollo relativas a la entrega de resultados visibles

### 2.1 Principio rector

Cada sprint termina con un **incremento demostrable**: algo que se pueda ejecutar delante del
cliente y recorrer como un flujo de usuario completo, atravesando todas las capas de la solución
(interfaz → aplicación → dominio → datos → motor de contenedores).

**No se planifican sprints por capa técnica** —un sprint de "entidades", otro de "servicios de
aplicación", otro de "pantallas"—. Cada sprint corta en vertical una funcionalidad acotada y la
entrega operativa de punta a punta. El criterio de corte no es "qué capa toca ahora", sino
"qué puede hacer el usuario al terminar este sprint que antes no podía".

### 2.2 Los dos tipos de hito

Se distinguen dos clases de etapa, y deben nombrarse como tales para no confundir su propósito:

| Tipo | Quién valida | Propósito |
|---|---|---|
| **HI · Hito interno** | El agente humano | Confirmar decisiones estructurales caras de revertir (arquitectura, nomenclatura, fidelidad con la maqueta). Habilita al resto pero **no se muestra al cliente**. |
| **HD · Hito demostrable** | El cliente | Entregar un flujo de usuario completo y operativo. **Se ejecuta y se recorre delante del cliente.** |

Sólo las etapas `a` y `b` son hitos internos. **De la etapa `c` en adelante, todas las etapas son
hitos demostrables**, sin excepción: si una etapa planificada no produce algo que el cliente pueda
recorrer, está mal cortada y debe redividirse.

### 2.3 Plantilla obligatoria de etapa

Toda etapa —las definidas abajo y las que el orquestador planifique después— se especifica con esta
plantilla completa. Una etapa sin criterios de aceptación verificables no se puede iniciar.

| Campo | Contenido |
|---|---|
| **Tipo** | `HI` o `HD`. |
| **Objetivo** | Qué decisión o capacidad se busca confirmar. Una frase. |
| **Alcance** | Qué se implementa. |
| **Fuera de alcance** | Qué **no** se implementa en esta etapa, para evitar sobre-ingeniería y desborde. |
| **Entregable tangible** | El artefacto concreto: solución que compila, servicio levantado en una URL, pantalla operativa. |
| **Guion de demostración** | Pasos numerados y reproducibles: comando que se ejecuta, URL que se abre, acción de cada paso y resultado esperado. Debe poder ejecutarlo alguien que no escribió el código. |
| **Criterios de aceptación** | Lista de afirmaciones verificables, cada una verdadera o falsa. Sin juicios subjetivos. |
| **Punto de control** | El orquestador se detiene, presenta el guion y espera. El agente humano da el OK o pide correcciones. **No se avanza a la etapa siguiente sin OK explícito.** |
| **Informe de cierre** | Documento publicado en `/DEV/SelfHosted.Service.Core.Documentos/Avances/`, redactado según la plantilla de [§2.5](#25-informe-de-cierre-de-etapa). Se escribe **antes** de convocar el punto de control. |

### 2.4 Reglas transversales

1. **No-regresión.** El guion de demostración es acumulativo: al cerrar cada etapa deben seguir
   pasando, sin correcciones, los guiones de todas las etapas anteriores. Si uno se rompe, la etapa
   no está terminada.
2. **La demostración se levanta con los scripts, dentro del devcontainer.** El host de desarrollo no
   tiene el SDK de .NET: todo guion arranca ejecutando los scripts de `scripts/` **dentro del
   devcontainer**, y el resultado se observa en el navegador del host. No se admiten pasos manuales
   de preparación fuera de esos scripts.
3. **Estado de partida reproducible.** Cada guion declara desde qué estado parte (base de datos
   vacía, base con datos de ejemplo, contenedores previos existentes) y cómo se llega a él.
4. **Corte por flujo, no por capa.** Si una etapa planificada no se puede describir como una
   secuencia de acciones del usuario en el navegador, está mal cortada.
5. **Trazabilidad.** Cada etapa referencia la sección del análisis integrado que especifica lo que
   implementa.
6. **Informe antes del punto de control.** Ninguna etapa se da por terminada —ni se convoca al
   agente humano— sin el informe de cierre de §2.5 publicado en `Avances/`. El informe es el
   entregable de documentación de la etapa, al mismo nivel que el código.

### 2.5 Informe de cierre de etapa

Al terminar cada etapa, y **antes** de convocar el punto de control, el orquestador escribe un
informe autocontenido en:

```
/DEV/SelfHosted.Service.Core.Documentos/Avances/<orden>-<etapa>.md
```

donde `<orden>` es `a`, `b`, `c`, `01`, `02`, … según el orden de ejecución, y `<etapa>` es el
nombre de la etapa en minúsculas y con guiones. Ejemplos: `a-esqueleto-ejecutable.md`,
`c-administrador-y-sesion.md`, `01-proyectos.md`.

El informe está escrito para alguien que **no vio escribir el código** y que va a sentarse a
probarlo: debe poder abrirlo, seguirlo de arriba abajo y saber en todo momento qué ejecutar, con
qué credenciales, qué debería ver y cómo darse cuenta de que algo salió mal. No se dan por
sabidos ni los nombres de los proyectos, ni las rutas, ni las claves generadas.

Secciones obligatorias, en este orden:

| # | Sección | Contenido |
|---|---|---|
| 1 | **Identificación** | Etapa, tipo (`HI`/`HD`), fecha de cierre, secciones del análisis integrado que implementa, y estado (`Pendiente de validación` / `Validada` / `Con correcciones pedidas`). |
| 2 | **Qué se entregó** | Una frase con la capacidad nueva —"qué puede hacer el usuario que antes no podía"— seguida del alcance realmente implementado. Si difiere del alcance planificado en §2.3, se explica la diferencia y por qué. |
| 3 | **Qué quedó fuera** | Lo declarado fuera de alcance, más cualquier cosa que se pospuso durante la ejecución, con la etapa donde se retoma. Sirve para no reportar como falla algo que todavía no existe. |
| 4 | **Cómo lo levanto** | Estado de partida (base vacía, base con datos, contenedores previos) y cómo se llega a él; los comandos exactos en orden, indicando si se ejecutan **dentro del devcontainer** o en el host; la URL que queda publicada. Sin pasos manuales fuera de `scripts/`. |
| 5 | **Claves y credenciales** | Toda credencial, clave, token o secreto que la etapa genere o requiera: cuál es, quién la genera, dónde queda guardada, cómo se regenera y cómo se borra para volver a empezar. Las credenciales de ejemplo del entorno de desarrollo se escriben completas; **nunca se transcribe un secreto de producción ni una contraseña real elegida por el agente humano** —en su lugar se indica dónde consultarla—. |
| 6 | **Qué probar, paso a paso** | El guion de demostración de la etapa, numerado, con **acción → resultado esperado** en cada paso. Cada paso debe ser observable en el navegador o en la consola; "funciona correctamente" no es un resultado esperado. |
| 7 | **Casos de ejemplo** | Datos concretos con los que probar: nombres, valores, contraseñas de prueba, imágenes de contenedor, puertos. Incluye al menos un **caso de error esperado** (entrada inválida, credencial incorrecta, conflicto) con el mensaje que el sistema debe mostrar. |
| 8 | **Qué debería ver** | Descripción de la evidencia esperada: pantallas, estados, mensajes, códigos de respuesta, filas en la base, contenedores levantados. Y, en contraste, **qué indicaría que algo está mal**. |
| 9 | **Cómo está armado el proyecto** | Explicación en prosa de las piezas que aparecen o cambian por primera vez en esta etapa: qué proyecto o carpeta se agregó, qué responsabilidad tiene, cómo se relaciona con las demás capas y por qué está donde está. Es la sección que permite entender la solución sin leerla entera. |
| 10 | **Criterios de aceptación** | La lista de la etapa, con cada casilla marcada y, si alguna quedó sin marcar, el motivo. |
| 11 | **No-regresión** | Guiones de etapas anteriores re-ejecutados, con su resultado. Si alguno se rompió y se arregló, se indica qué se tocó. |
| 12 | **Problemas conocidos** | Fallas, limitaciones o deuda técnica que quedan vivas al cerrar, con su impacto en la demostración. Es preferible declararlas a que aparezcan durante el punto de control. |
| 13 | **Qué habilita** | Qué etapa o etapas quedan desbloqueadas, y qué decisión tomada acá condiciona lo que viene. |

Reglas de escritura del informe:

- **Autocontenido.** Se puede leer sin abrir el análisis integrado ni el código. Las referencias
  cruzadas se agregan como complemento, no como sustituto de la explicación.
- **Verificable.** Todo comando que aparece en el informe fue ejecutado tal como está escrito. No se
  documentan pasos supuestos.
- **Honesto.** Si un criterio de aceptación no se cumple, el informe lo dice en la sección 10 y en
  la 12. Un informe que declara terminada una etapa incompleta invalida el punto de control.
- **Acumulativo, no reescrito.** Cada etapa agrega su propio archivo. Los informes anteriores no se
  editan salvo para actualizar su estado en la sección 1 cuando el agente humano valida o pide
  correcciones.
- **Índice.** `Avances/README.md` mantiene la lista de informes en orden, con etapa, tipo, fecha y
  estado.

---

## 3. Etapas iniciales

### a. Esqueleto ejecutable · `HI`

- **Objetivo** — confirmar el entorno de desarrollo, la arquitectura, la organización de carpetas y
  la nomenclatura **antes** de que corregirlas sea caro.
- **Alcance** — devcontainer operativo con el SDK de .NET 10 y acceso al motor de contenedores del
  host; estructura completa de proyectos de la solución según
  [§12 Arquitectura técnica de la solución](Analisis-Final-Integrado.md#12-arquitectura-técnica-de-la-solución);
  scripts `.sh` de `scripts/`; una única página o endpoint de salud que responda.
- **Fuera de alcance** — módulos, carpetas o proyectos vacíos creados "por si acaso"; cualquier
  lógica de negocio; base de datos; interfaz de usuario más allá de la página de salud.
- **Entregable tangible** — la solución compila y se ejecuta desde los scripts, dentro del
  devcontainer.
- **Guion de demostración**
  1. Abrir el repositorio con "Reopen in Container" o `devcontainer up` → el entorno se construye y
     queda listo sin ningún paso manual ni script de arranque propio.
  2. Ejecutar `scripts/build.sh` → termina con código de salida 0 y sin advertencias de compilación.
  3. Ejecutar `scripts/run.sh` → el servicio queda escuchando e informa la URL en consola.
  4. Abrir la URL de salud en el **navegador del host** → responde `200` con el estado del servicio.
- **Criterios de aceptación**
  - [ ] El devcontainer se construye y abre sin pasos manuales, y trae el SDK de .NET 10.
  - [ ] El entorno se levanta de forma declarativa desde `.devcontainer/devcontainer.json`; no
        existe ningún script que haga `docker run` a mano para levantarlo.
  - [ ] Existe un único conjunto de scripts en `scripts/`, sin variantes por entorno.
  - [ ] La depuración funciona con `.vscode/launch.json` y F5, por un camino separado del de los
        scripts.
  - [ ] `scripts/build.sh` termina en 0.
  - [ ] `scripts/run.sh` levanta el servicio y la página de salud responde desde el navegador del host.
  - [ ] El árbol de proyectos y carpetas coincide con el definido en §12.
  - [ ] Los nombres de proyectos, espacios de nombres y carpetas siguen la convención acordada.
  - [ ] No existen proyectos ni carpetas sin contenido real.
  - [ ] Se cumple la puerta técnica **PT-02** del documento de requerimientos técnicos: acceso al
        motor de contenedores del host desde dentro del devcontainer, verificado por código.
- **Punto de control** — el orquestador presenta el árbol de la solución, el resultado de los
  scripts y el de PT-02, y solicita al agente humano la validación de estructura y nomenclatura.
- **Informe de cierre** — `Avances/a-esqueleto-ejecutable.md`, según §2.5. Debe explicar el árbol de
  proyectos pieza por pieza, qué hace cada script de `scripts/`, cómo se levanta el devcontainer y
  cómo se comprueba PT-02 desde el navegador del host.

### b. Cáscara del panel de control · `HI`

- **Objetivo** — confirmar la **fidelidad de la interfaz con la maqueta UX-UI** definida en la etapa
  de especificación.
- **Alcance** — disposición general del panel: menú lateral, barra superior y área de contenido;
  las rutas del mapa de navegación de
  [§9.1 Mapa de navegación](Analisis-Final-Integrado.md#91-mapa-de-navegación), con pantallas
  vacías o de marcador de posición.
- **Fuera de alcance** — autenticación, base de datos y cualquier dato real. Las pantallas son
  cáscaras navegables.
- **Nota de alcance** — la barra superior se completa en la etapa `c` con el menú de usuario
  (cerrar sesión y cambio de contraseña). Aquí se valida su disposición, no su funcionalidad.
- **Entregable tangible** — panel de control navegable en el navegador.
- **Guion de demostración**
  1. Ejecutar `scripts/run.sh` y abrir la raíz de la aplicación en el navegador del host.
  2. Recorrer cada ítem del menú lateral → navega a su ruta sin error.
  3. Comparar contra la maqueta UX-UI, pantalla por pantalla.
  4. Reducir el ancho de la ventana → la disposición responde según la maqueta.
- **Criterios de aceptación**
  - [ ] El menú lateral contiene los ítems de la maqueta, con sus rótulos e íconos.
  - [ ] La barra superior presenta las zonas previstas en la maqueta.
  - [ ] Todas las rutas de §9.1 son navegables y ninguna produce error.
  - [ ] El comportamiento responsivo coincide con el de la maqueta.
  - [ ] Se usan los componentes de MudBlazor, sin estilos improvisados fuera del sistema visual.
- **Punto de control** — el orquestador solicita la validación visual en el navegador contra la
  maqueta.
- **Informe de cierre** — `Avances/b-cascara-del-panel.md`, según §2.5. Debe listar cada ruta
  navegable con su rótulo, indicar qué pantallas son marcadores de posición y en qué anchos de
  ventana se verificó el comportamiento responsivo.

### c. Administrador y sesión · `HD` · **primera demostración al cliente**

- **Objetivo** — entregar el primer flujo de usuario completo y operativo: primer arranque, alta del
  administrador, inicio de sesión, cambio de contraseña y cierre de sesión, persistido en SQLite.
- **Alcance** — integración de SQLite y Entity Framework con las migraciones iniciales; entidades de
  autenticación y autorización; detección de primer arranque; pantalla de alta del administrador con
  validación de contraseña; pantalla de inicio de sesión; pantalla de cambio de contraseña; acciones
  de la barra superior (menú de usuario, cerrar sesión, cambiar contraseña); protección de todas las
  rutas del panel.
- **Fuera de alcance** — autenticación de la API REST y emisión de credenciales para consumidores
  externos, que corresponden a [§8](Analisis-Final-Integrado.md#8-decisión-2--autenticación-de-la-rest-api);
  recuperación de contraseña.
- **Entregable tangible** — aplicación protegida por sesión, con administración de la propia cuenta.
- **Guion de demostración**
  1. Ejecutar `scripts/reset-db.sh` y luego `scripts/run.sh` → la aplicación detecta el primer
     arranque y presenta el alta del administrador.
  2. Intentar una contraseña débil → se rechaza con el mensaje de validación correspondiente.
  3. Crear el administrador con una contraseña válida → redirige a la página principal, con el
     usuario visible en la barra superior.
  4. Cerrar sesión → redirige a la pantalla de inicio de sesión.
  5. Intentar abrir una ruta interna sin sesión → redirige a inicio de sesión.
  6. Iniciar sesión con credenciales incorrectas → se rechaza sin revelar cuál de los dos campos
     falló.
  7. Iniciar sesión correctamente → accede al panel.
  8. Cambiar la contraseña desde la barra superior → se exige la contraseña actual.
  9. Cerrar sesión y volver a entrar con la contraseña nueva → accede; la anterior ya no funciona.
  10. Detener y volver a ejecutar `scripts/run.sh` → el administrador persiste y **no** se vuelve a
      pedir el alta inicial.
- **Criterios de aceptación**
  - [ ] Los diez pasos del guion se cumplen sin intervención manual sobre la base de datos.
  - [ ] La contraseña se almacena con una función de derivación de clave, nunca en claro ni con un
        resumen simple.
  - [ ] Ninguna ruta del panel es accesible sin sesión iniciada.
  - [ ] El alta inicial deja de ofrecerse una vez creado el administrador.
  - [ ] Las migraciones se aplican solas al arrancar sobre una base inexistente.
  - [ ] Siguen pasando los guiones de las etapas `a` y `b`.
- **Punto de control** — esta etapa se demuestra al cliente. El orquestador prepara el estado de
  partida (base eliminada) antes de iniciar el guion.
- **Informe de cierre** — `Avances/c-administrador-y-sesion.md`, según §2.5. Debe detallar la
  contraseña de ejemplo con la que se da de alta el administrador de prueba, la regla de validación
  que hace fallar una contraseña débil, dónde queda el archivo de SQLite y cómo borrarlo con
  `scripts/reset-db.sh` para repetir el primer arranque tantas veces como haga falta.

---

## 4. Planificación de las etapas siguientes

A partir de la etapa `c`, el orquestador planifica el resto de las entregas siguiendo la
progresión de la sección
[2.2 Alcances incrementales](Analisis-Final-Integrado.md#22-alcances-incrementales) del análisis
integrado, con estas condiciones:

- Cada etapa se especifica con la **plantilla completa de §2.3** antes de comenzar a codificarla.
- Cada etapa es un **hito demostrable**: se verifica en el navegador que las pantallas funcionan y
  que el flujo se recorre de punta a punta.
- El Alcance 1 no se entrega como un único bloque. Se subdivide en los cortes verticales de §4.1.
- Cada etapa cierra con su **informe de §2.5** en `Avances/`, numerado según el orden de ejecución
  (`01-proyectos.md`, `02-servicios-del-proyecto.md`, …) y anotado en `Avances/README.md`.

### 4.1 Cortes propuestos del Alcance 1 · Núcleo

Cada corte es una etapa `HD` independiente. El orquestador puede reordenarlos o subdividirlos, pero
no fusionarlos hasta el punto de perder la demostrabilidad intermedia.

| # | Etapa | Objetivo · qué puede hacer el usuario al terminar | Referencia |
|---|---|---|---|
| **1** | **Proyectos** | Crear, listar, renombrar y eliminar proyectos, y ver el listado persistido tras reiniciar. | §5.1 |
| **2** | **Servicios del proyecto** | Agregar servicios a un proyecto y configurarlos desde el panel lateral: origen de la imagen, variables de entorno, puertos, recursos. | §5.2 · §9.4 |
| **3** | **Lienzo** | Ver los servicios como nodos, moverlos, conectarlos y que la disposición se conserve al recargar. | §5.4 · §6 · §9.2 · §9.3 |
| **4** | **Despliegue desde imagen pública** | Desplegar un servicio desde una imagen de registro público y ver su estado real reflejado en el nodo, con acceso a los registros del contenedor. | §4.3 · §5.3 · §9.6 |
| **5** | **Arranque y parada** | Iniciar y detener el proyecto completo y cada servicio por separado, y marcar el autoarranque. | §4.3 |
| **6** | **Cambios pendientes** | Modificar la configuración de un servicio ya desplegado, ver el cambio acumulado en el cajón de cambios pendientes y aplicarlo con redespliegue, advertido de la ventana de indisponibilidad. | §5.5 · §2.4 |
| **7** | **Direcciones IP y conflictos** | Definir el rango de IP de gestión, asignar una IP a cada servicio y ver bloqueado el arranque cuando entra en conflicto con un servicio **activo** de otro proyecto. | §5.8 · §10.3 · §11 |
| **8** | **Escalado manual** | Ajustar réplicas y límites de recursos de un servicio y ver el efecto aplicado. | §2.2 |
| **9** | **Despliegue desde Dockerfile y repositorio** | Desplegar un servicio construyendo la imagen desde un Dockerfile o desde un repositorio de GitHub, siguiendo el progreso de la construcción. | §4.3 · §5.2 |
| **10** | **Descubrimiento y adopción** | Ver los contenedores ya existentes en el servidor y asignarlos a un proyecto **sin reinstanciarlos**. | §5.7 · §10.2 |

> El corte **10** es el diferencial declarado en §2.1 y el que hace la herramienta adoptable sobre
> un servidor en producción. Conviene no relegarlo al final si el cliente valora esa capacidad.

**Puertas técnicas que condicionan estos cortes** (documento de requerimientos técnicos, §9):

- **PT-01 · fluidez del lienzo** debe medirse **antes** de comprometer el corte **3**. Si no pasa,
  cambia la librería del lienzo y ese corte se replanifica.
- **PT-02 · acceso al motor de contenedores desde el devcontainer** debe estar resuelta antes del
  corte **4**; se verifica ya en la etapa `a`.
- Antes del corte **10**, deben estar implementadas las salvaguardas de aislamiento respecto del
  host (prefijo de nombre, etiquetas de pertenencia, confirmación explícita): el descubrimiento
  lista los contenedores reales de la máquina de desarrollo.

### 4.2 Alcances siguientes

Los Alcances 2, 3 y 4 se planifican con el mismo criterio cuando el Alcance 1 esté cerrado y
demostrado:

| Alcance | Cortes previsibles | Referencia |
|---|---|---|
| **2 — Observabilidad** | Estado del servidor; vista general por proyecto; vista por contenedor. Un corte por capa del dashboard. | §9.5 |
| **3 — Portabilidad** | Exportar a Docker Compose; importar desde Docker Compose; catálogo de servicios reutilizables. | §5.6 · §10.4 |
| **4 — Automatización** | Autenticación de la API REST y tokens; disparo de despliegue desde un workflow de GitHub Actions. | §8 · §10.5 |
