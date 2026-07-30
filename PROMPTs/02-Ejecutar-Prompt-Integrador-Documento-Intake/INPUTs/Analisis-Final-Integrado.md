# Análisis Final Integrado · `SelfHosted.Service.Core`

> **Tipo:** documento integrador de análisis (entrada del prompt integrador de Documento Intake).
> **Fecha:** 2026-07-26 · **Estado:** `draft` · **Rule Set aplicado:** `RuleSet-Lean`.
> **Propósito:** consolidar en **un único documento autocontenido** la definición del servicio, la
> evaluación de las dos decisiones técnicas abiertas (canvas de arquitectura y autenticación de la
> REST API), la investigación de librerías alternativas, los modelos de datos y los ejemplos
> necesarios para **maquetar la solución web y de datos**.

## Cómo leer este documento

- **Autocontenido.** Todo el material necesario está embebido: definiciones, citas textuales,
  modelos JSON, DDL, diagramas y ejemplos. No hay que abrir otros documentos para entenderlo ni
  para maquetar a partir de él.
- **Marcadores de naturaleza de cada afirmación**, para cumplir la restricción de no inventar
  información:
  - **[E]** *Evidencia* — hecho verificable (cita textual de documentación oficial, versión y fecha
    de un paquete publicado, o dato del relevamiento del entorno). Las fuentes se listan al final,
    en el [Anexo A](#anexo-a--evidencias-y-fuentes), únicamente para permitir su verificación.
  - **[D]** *Diseño* — propuesta de este análisis. No es un hecho: es una decisión de diseño
    argumentada, y como tal es discutible y revisable.
  - **[S]** *Supuesto* — asunción tomada ante información faltante; queda registrada para su
    confirmación.
- **Ofuscación.** Los datos del servidor de referencia sobre el que se piensa montar el servicio
  aparecen **normalizados y ofuscados** (alias del host, sin FQDN ni dominio, sin detalle de
  exposición ni de secretos). Se conservan los valores de capacidad, topología y nomenclatura
  porque son los que se usan para maquetar. La política aplicada está en el
  [Anexo C](#anexo-c--política-de-ofuscación-aplicada).

---

## Tabla de contenidos

1. [Resumen ejecutivo y veredictos](#1-resumen-ejecutivo-y-veredictos)
2. [Definición del servicio y alcance](#2-definición-del-servicio-y-alcance)
   - 2.1 [Qué es y qué no es](#21-qué-es-y-qué-no-es)
   - 2.2 [Alcances incrementales](#22-alcances-incrementales)
   - 2.3 [Requisitos técnicos declarados](#23-requisitos-técnicos-declarados)
   - 2.4 [Fuera de alcance](#24-fuera-de-alcance)
3. [Entorno destino de referencia (ofuscado)](#3-entorno-destino-de-referencia-ofuscado)
   - 3.1 [Perfil de capacidad](#31-perfil-de-capacidad)
   - 3.2 [Topología de red y su consecuencia de diseño](#32-topología-de-red-y-su-consecuencia-de-diseño)
   - 3.3 [Parque de contenedores de referencia](#33-parque-de-contenedores-de-referencia)
4. [Modelo de dominio](#4-modelo-de-dominio)
   - 4.1 [Jerarquía de entidades](#41-jerarquía-de-entidades)
   - 4.2 [La separación configuración / ejecución](#42-la-separación-configuración--ejecución)
   - 4.3 [Ciclo de vida del despliegue](#43-ciclo-de-vida-del-despliegue)
   - 4.4 [Semántica de las aristas del lienzo](#44-semántica-de-las-aristas-del-lienzo)
   - 4.5 [Invariantes del modelo](#45-invariantes-del-modelo)
   - 4.6 [Diagrama entidad-relación](#46-diagrama-entidad-relación)
5. [Modelos de datos](#5-modelos-de-datos)
   - 5.1 [Proyecto con layout de canvas](#51-proyecto-con-layout-de-canvas)
   - 5.2 [Servicio](#52-servicio)
   - 5.3 [Despliegue](#53-despliegue)
   - 5.4 [Enlace del lienzo](#54-enlace-del-lienzo)
   - 5.5 [Changeset de cambios pendientes](#55-changeset-de-cambios-pendientes)
   - 5.6 [Catálogo de servicios reutilizables](#56-catálogo-de-servicios-reutilizables)
   - 5.7 [Adopción de contenedores existentes](#57-adopción-de-contenedores-existentes)
   - 5.8 [Reserva y conflicto de direcciones IP](#58-reserva-y-conflicto-de-direcciones-ip)
   - 5.9 [Esquema relacional SQLite](#59-esquema-relacional-sqlite)
6. [Decisión 1 · Canvas de arquitectura con Blazor.Diagrams](#6-decisión-1--canvas-de-arquitectura-con-blazordiagrams)
   - 6.1 [Requisitos que debe cubrir el lienzo](#61-requisitos-que-debe-cubrir-el-lienzo)
   - 6.2 [Qué ofrece la librería propuesta](#62-qué-ofrece-la-librería-propuesta)
   - 6.3 [El condicionante real: Interactive Server](#63-el-condicionante-real-interactive-server)
   - 6.4 [Veredicto y condición de aceptación](#64-veredicto-y-condición-de-aceptación)
   - 6.5 [Maqueta de integración](#65-maqueta-de-integración)
7. [Investigación de alternativas de canvas](#7-investigación-de-alternativas-de-canvas)
   - 7.1 [Candidatas nativas .NET](#71-candidatas-nativas-net)
   - 7.2 [Candidatas JavaScript vía interoperabilidad](#72-candidatas-javascript-vía-interoperabilidad)
   - 7.3 [Matriz comparativa](#73-matriz-comparativa)
   - 7.4 [Recomendación y plan de contingencia](#74-recomendación-y-plan-de-contingencia)
8. [Decisión 2 · Autenticación de la REST API](#8-decisión-2--autenticación-de-la-rest-api)
   - 8.1 [Qué se decidió y qué problema tiene](#81-qué-se-decidió-y-qué-problema-tiene)
   - 8.2 [Evidencia normativa sobre ROPC](#82-evidencia-normativa-sobre-ropc)
   - 8.3 [Los tres consumidores reales de la API](#83-los-tres-consumidores-reales-de-la-api)
   - 8.4 [Alternativas evaluadas](#84-alternativas-evaluadas)
   - 8.5 [Arquitectura de autenticación propuesta](#85-arquitectura-de-autenticación-propuesta)
   - 8.6 [Si se mantiene ROPC: mitigaciones obligatorias](#86-si-se-mantiene-ropc-mitigaciones-obligatorias)
   - 8.7 [Controladores frente a minimal APIs](#87-controladores-frente-a-minimal-apis)
   - 8.8 [Contratos de la API](#88-contratos-de-la-api)
9. [Maquetado de la solución web](#9-maquetado-de-la-solución-web)
   - 9.1 [Mapa de navegación](#91-mapa-de-navegación)
   - 9.2 [Pantalla del lienzo](#92-pantalla-del-lienzo)
   - 9.3 [Anatomía del nodo de servicio](#93-anatomía-del-nodo-de-servicio)
   - 9.4 [Panel lateral de servicio](#94-panel-lateral-de-servicio)
   - 9.5 [Dashboard del segundo alcance](#95-dashboard-del-segundo-alcance)
   - 9.6 [Lenguaje visual de estados](#96-lenguaje-visual-de-estados)
10. [Ejemplos prácticos de extremo a extremo](#10-ejemplos-prácticos-de-extremo-a-extremo)
    - 10.1 [E1 · Alta de proyecto con API y base de datos](#101-e1--alta-de-proyecto-con-api-y-base-de-datos)
    - 10.2 [E2 · Adopción de un contenedor ya existente](#102-e2--adopción-de-un-contenedor-ya-existente)
    - 10.3 [E3 · Arranque bloqueado por conflicto de IP](#103-e3--arranque-bloqueado-por-conflicto-de-ip)
    - 10.4 [E4 · Exportación a Docker Compose](#104-e4--exportación-a-docker-compose)
    - 10.5 [E5 · Despliegue disparado desde GitHub Actions](#105-e5--despliegue-disparado-desde-github-actions)
11. [Reglas de negocio y validaciones](#11-reglas-de-negocio-y-validaciones)
12. [Arquitectura técnica de la solución](#12-arquitectura-técnica-de-la-solución)
13. [Evaluación general](#13-evaluación-general)
    - 13.1 [Matriz de riesgos](#131-matriz-de-riesgos)
    - 13.2 [Inconsistencias detectadas en los insumos](#132-inconsistencias-detectadas-en-los-insumos)
    - 13.3 [Complejidad relativa por alcance](#133-complejidad-relativa-por-alcance)
    - 13.4 [Decisiones que conviene cerrar antes de codificar](#134-decisiones-que-conviene-cerrar-antes-de-codificar)
14. [Glosario](#14-glosario)
15. [Anexo A · Evidencias y fuentes](#anexo-a--evidencias-y-fuentes)
16. [Anexo B · Limitaciones y puntos no verificados](#anexo-b--limitaciones-y-puntos-no-verificados)
17. [Anexo C · Política de ofuscación aplicada](#anexo-c--política-de-ofuscación-aplicada)

---

## 1. Resumen ejecutivo y veredictos

El proyecto define un **administrador de proyectos de infraestructura basados en contenedores
Docker**, operado desde un **lienzo interactivo**, para un servidor propio pequeño de desarrollo,
construido en **.NET 10 + Blazor Interactive Server + MudBlazor + Entity Framework + SQLite**, con
arquitectura monolítica y un único usuario administrador.

Las dos cuestiones que este documento venía a cerrar tienen veredictos distintos:

| # | Decisión bajo análisis | Veredicto | Fundamento en una línea |
|---|---|---|---|
| 1 | Modelar la arquitectura con un canvas basado en **Blazor.Diagrams** (`Z.Blazor.Diagrams`) | **Se confirma**, con una prueba de concepto como condición de aceptación | Es MIT, publica para `net10.0`, declara soporte de Blazor Server y cubre nodos custom, puertos, enlaces, grupos, minimapa y virtualización sin desarrollo propio **[E]** |
| 2 | REST API con **autenticación ROPC** y JWT bearer | **Se desaconseja ROPC**; se mantienen JWT bearer y controladores | La práctica recomendada vigente del IETF dice textualmente *"The resource owner password credentials grant MUST NOT be used"*, y OAuth 2.1 directamente lo elimina **[E]** |

**Los tres hallazgos de mayor impacto sobre el diseño:**

1. **El riesgo dominante del canvas no es la librería, es el modelo de hosting.** La documentación
   de Microsoft sobre modelos de hospedaje de Blazor enuncia como limitación de Blazor Server que
   *"Higher latency usually exists. Every user interaction involves a network hop."* **[E]**
   Arrastrar un nodo produce decenas de eventos por segundo; si se manejan en C#, cada uno es un
   viaje al servidor. En LAN el impacto es bajo; la mitigación (arrastrar en JavaScript y persistir
   sólo al soltar) debe estar prevista desde el diseño, no improvisada. **[D]**
2. **La interfaz web no necesita ROPC en absoluto.** En Blazor Interactive Server la sesión se
   sostiene por **cookie** sobre el circuito SignalR **[E]**; el token bearer sólo hace falta para
   el consumidor automatizado (GitHub Actions, cuarto alcance). Es decir: ROPC resolvería un
   problema que la aplicación no tiene, a cambio de manipular la contraseña del administrador en
   más lugares de los necesarios. **[D]**
3. **La topología de red del servidor de referencia condiciona el monitoreo.** Cuando los
   contenedores corren en una red **macvlan** con IP propia de la LAN, el host **no los alcanza por
   la misma placa de red** **[E]**. Si el administrador se despliega como contenedor en ese host,
   no podrá hacer comprobaciones de salud HTTP contra esos servicios: deberá observarlos por el
   **socket de Docker** (estado, `healthcheck` del contenedor, estadísticas), no por la red. Esto
   define la estrategia de monitoreo del segundo alcance. **[D]**

**Recomendación de conjunto:** el diseño planteado es viable y coherente; hay que ajustar el
mecanismo de autenticación, blindar el rendimiento del lienzo con una prueba medida y elegir el
cliente de Docker con criterio de mantenimiento (ver [§13](#13-evaluación-general)).

---

## 2. Definición del servicio y alcance

### 2.1 Qué es y qué no es

**Es** un administrador de contenedores **organizado por proyectos**, donde cada proyecto es una
arquitectura de servicios contenedorizados, editable en un lienzo visual, con despliegue
automático y control de arranque y parada por proyecto y por servicio.

**No es** un PaaS multiusuario ni un orquestador de clúster. No hay balanceo de carga, ni
administración de proxies o proxies inversos: ambos están explícitamente fuera de alcance.

Un componente diferencial ya presente en la definición: el **módulo de descubrimiento y adopción de
contenedores**, que consulta al demonio de Docker para que contenedores ya existentes puedan
seleccionarse y asignarse a un proyecto **sin reinstanciarlos**. Es lo que hace que la herramienta
sea adoptable sobre un servidor que ya está en producción, en lugar de exigir empezar de cero.

### 2.2 Alcances incrementales

```mermaid
graph LR
    A1["Alcance 1<br/>Proyectos, servicios,<br/>lienzo, despliegue,<br/>IP y escalado manual"]
    A2["Alcance 2<br/>Dashboard por capas:<br/>servidor, proyecto,<br/>contenedor"]
    A3["Alcance 3<br/>Import/export Compose<br/>y catalogo de servicios"]
    A4["Alcance 4<br/>Despliegue desde<br/>GitHub Actions"]
    A1 --> A2 --> A3 --> A4
```

| Alcance | Capacidades declaradas |
|---|---|
| **1 — Núcleo** | Alta de proyecto con todos sus servicios; cada servicio es un contenedor Docker; despliegue automático desde repositorio GitHub, Dockerfile o imagen de registro público; modelado en espacio visual con bloques y conectores; variables de entorno que definen la ruta privada de un servicio a otro (dirección interna y puerto); iniciar y parar el proyecto completo y cada servicio, con marca de autoarranque; escalado horizontal y vertical **manuales**; asignación **manual** de IP de LAN por servicio con detección de conflictos; un único usuario administrador |
| **2 — Observabilidad** | Dashboard en capas: estado del servidor (memoria, disco, RAM), vista general por proyecto y vista por contenedor |
| **3 — Portabilidad** | Exportar e importar la arquitectura completa del proyecto como Docker Compose; catálogo editable, exportable e importable de servicios genéricos reutilizables |
| **4 — Automatización** | Disparar despliegues desde un workflow de GitHub Actions, con el proyecto, los servicios y los tokens previamente configurados |

**Regla de conflicto de IP, tal como fue definida** — es la regla de negocio más específica del
primer alcance y merece transcripción fiel: el administrador tiene asignado un rango de direcciones
IP exclusivo para gestión y control; **puede haber múltiples servicios configurados con una misma
IP**, pero sólo se permite ejecutar el proyecto cuyos servicios no estén en conflicto con
**servicios activos** de otro proyecto. Si el proyecto en conflicto está detenido, las IP están
libres y el arranque procede. Su formalización está en [§11](#11-reglas-de-negocio-y-validaciones).

### 2.3 Requisitos técnicos declarados

| Requisito | Valor declarado | Estado de verificación |
|---|---|---|
| Plataforma | .NET 10, Blazor con páginas **Interactive Server** | Declarado por el proyecto |
| Componentes UI | **MudBlazor** | Versión 9.7.0, publicada 2026-07-09, licencia MIT, con soporte `net8.0`/`net9.0`/`net10.0` **[E]** |
| Acceso a datos | **Entity Framework** | Declarado por el proyecto |
| Base de datos | **SQLite** | Declarado por el proyecto |
| Lienzo | `Blazor.Diagrams` | Evaluado en [§6](#6-decisión-1--canvas-de-arquitectura-con-blazordiagrams) |
| API REST | ROPC + JWT bearer + **controladores** | Evaluado en [§8](#8-decisión-2--autenticación-de-la-rest-api) |
| Patrón | **Clean Architecture**, estándar .NET, carpetas por módulos | Desarrollado en [§12](#12-arquitectura-técnica-de-la-solución) |
| Despliegue | **Monolito**: front, API y backend en un único servicio o contenedor | Declarado por el proyecto |
| Usuarios | **Un solo administrador** | Declarado por el proyecto |

### 2.4 Fuera de alcance

- Administración de proxies o proxies inversos.
- Balanceo de carga.
- **[D] Consecuencia directa:** sin proxy inverso no hay dominios públicos gestionados ni
  despliegue sin interrupción con solapamiento de versiones. El reemplazo de un servicio será
  *detener y arrancar*, con una ventana de indisponibilidad. Conviene que la interfaz lo diga
  explícitamente al confirmar un redespliegue, en lugar de dejar que el usuario lo descubra.

---

## 3. Entorno destino de referencia (ofuscado)

> Perfil del servidor sobre el que se piensa montar el servicio. Los valores están **normalizados y
> ofuscados** según el [Anexo C](#anexo-c--política-de-ofuscación-aplicada): se conserva lo que
> sirve para dimensionar y maquetar, se elimina lo que identifica o expone al equipo real.

### 3.1 Perfil de capacidad

| Recurso | Valor de referencia | Consecuencia de diseño **[D]** |
|---|---|---|
| Sistema operativo | Linux Debian 13, kernel 6.12, arranque UEFI | El destino final del servicio es un contenedor Linux |
| CPU | 4 núcleos / 8 hilos, generación antigua, con virtualización por hardware | El administrador debe ser liviano: **no** hacer sondeo agresivo de métricas |
| RAM | 32 GB, con aproximadamente la mitad en uso | El presupuesto para el administrador es de cientos de MB, no de GB |
| Swap | ~32 GB, con uso apreciable | Señal de presión de memoria: el dashboard debe mostrar swap, no sólo RAM |
| Disco | Un único SSD de 1 TB, sin RAID ni LVM, ~14 % ocupado | **Sin redundancia**: la exportación del catálogo y de los proyectos es la estrategia de respaldo |
| GPU | Ninguna | Nada de cargas de inferencia local en el propio administrador |
| Motor de contenedores | Docker 26.x con `compose` v5 y `buildx` | La API del motor y el formato Compose objetivo quedan fijados |
| Parque actual | 8 contenedores en ejecución, 18 imágenes | Volumen de nodos por proyecto: **decenas, no cientos** |

**[D] Lectura de dimensionamiento.** Un lienzo de 10–30 nodos y un parque de menos de 50
contenedores son el caso real. Cualquier decisión que optimice para miles de nodos a costa de
complejidad está injustificada; cualquiera que degrade con 30 nodos es inaceptable.

### 3.2 Topología de red y su consecuencia de diseño

El servidor de referencia combina **dos modelos de red** simultáneos, y ambos deben ser
representables en el modelo de datos **[E]**:

| Modelo | Cómo se ve el contenedor | Uso típico |
|---|---|---|
| **macvlan** sobre la placa física | Como un equipo más de la LAN, con **IP propia** del rango de la LAN | Servicios que deben ser alcanzables desde otros equipos de la red |
| **bridge** definida por el usuario | Con IP de una subred privada del motor, resolviéndose entre sí **por nombre de contenedor**, publicando puertos en el host | Grupos de servicios que se consumen desde el propio host |

**[E] Limitación intrínseca del modelo macvlan:** el host **no dialoga directamente** con esos
contenedores a través de la misma placa de red; deben administrarse desde otro equipo de la LAN.

**[D] Tres consecuencias de diseño que se derivan de ahí:**

1. El campo "IP asignada manualmente" del enunciado corresponde al **modo macvlan**. El modelo de
   datos debe soportar *ambos* modos por servicio (`modoRed`: `macvlan` \| `bridge`), porque el
   parque real ya usa los dos.
2. El **monitoreo no puede depender de HTTP** contra la IP del servicio: si el administrador corre
   en el mismo host, no llegará a los contenedores macvlan. La fuente de verdad del estado es el
   **socket de Docker** (estado del contenedor, `healthcheck` declarado en la imagen y
   estadísticas de uso).
3. El vínculo "un backend se conecta a una base de datos por dirección interna y puerto" se
   resuelve distinto en cada modo: por **nombre de contenedor** en una red bridge común, y por **IP
   de LAN** en macvlan. La interfaz debe generar la variable de entorno correcta según el modo, y
   advertir cuando dos servicios enlazados no comparten un canal alcanzable.

```mermaid
graph TB
    subgraph HOST["Host de contenedores"]
        ADM["Servicio administrador<br/>monolito .NET 10"]
        SOCK[("socket de Docker")]
        subgraph MV["Red macvlan · IP de la LAN"]
            C1["servicio-a<br/>192.168.1.130"]
            C2["servicio-b<br/>192.168.1.131"]
        end
        subgraph BR["Red bridge de proyecto"]
            C3["api<br/>172.20.0.2"]
            C4["db<br/>172.20.0.3"]
        end
    end
    ADM -->|"control y estado"| SOCK
    SOCK --> C1
    SOCK --> C2
    SOCK --> C3
    SOCK --> C4
    C3 -->|"db:5432 por nombre"| C4
    ADM -. "sin acceso directo por red" .-x C1
```

### 3.3 Parque de contenedores de referencia

Muestra normalizada del parque real, útil como **juego de datos de maqueta** para poblar el lienzo
y el módulo de adopción. Nombres, imágenes propias e identificadores han sido reemplazados por
equivalentes genéricos; las capacidades y la topología se conservan.

| Contenedor de ejemplo | Imagen | Proyecto Compose | Red y dirección | Persistencia | Límite |
|---|---|---|---|---|---|
| `panel-admin` | `imagen-oficial/panel-ce:latest` | `panel-admin` | macvlan · `192.168.1.130` | volumen nombrado + socket de Docker | — |
| `vm-windows` | `imagen-comunidad/windows` | `print-server` | macvlan · `192.168.1.133` | montaje de directorio (imagen dispersa) | 8 GB / 4 vCPU |
| `bot-mensajeria` | `registro-privado/bot-moderador:latest` | `bot-mensajeria` | macvlan · `192.168.1.134` | montaje de directorio con SQLite | 512 MB |
| `runner-ci` | `registro-privado/runner-ci:2.x` | `runner-ci` | macvlan · `192.168.1.138` | montaje de directorio como caché | 8 GB |
| `print-server` | `registro-privado/print-server:1.4.x` | `print3d-server` | macvlan · `192.168.1.139` | montaje de directorio | 512 MB |
| `ia-api` | `imagen-oficial/modelos:0.32` | `ia-local` | bridge `ia-net` · `172.19.0.2` | montaje de directorio (decenas de GB) | — |
| `ia-webui` | `imagen-oficial/webui:0.10` | `ia-local` | bridge `ia-net` · `172.19.0.3` | montaje de directorio | — |
| `ia-video` | `registro-privado/video:0.3` | `ia-local` | bridge `ia-net` · `172.19.0.4` | montaje de directorio | — |

**[E] Patrones observables en el parque real, que el modelo debe soportar:**

- Servicios con **versión fijada** en la etiqueta de imagen y política de "no actualizar
  automáticamente"; casi ninguno usa `latest` deliberadamente.
- Servicios con **dispositivos del host** anclados (`/dev/...`), incluido USB por identificador
  estable.
- Servicios con **capacidades adicionales** del kernel y con requisitos de privilegios.
- Servicios **efímeros** que se reconstruyen (un runner de CI) frente a servicios permanentes.
- **Todos** con reinicio automático configurado.
- Persistencia mayoritariamente por **montaje de directorio**, no por volumen nombrado.
- Archivos de variables de entorno **no versionados** que contienen credenciales.

**[D] Traducción a requisitos del alta de servicio:** el formulario de servicio no puede limitarse
a *imagen + puertos + variables*. Necesita, como mínimo: etiqueta de imagen explícita con política
de actualización, montajes (volumen nombrado o directorio), dispositivos, capacidades, límites de
CPU y memoria, política de reinicio, modo de red con IP y marca de servicio efímero. El
[modelo de servicio](#52-servicio) refleja exactamente eso.

---

## 4. Modelo de dominio

El modelo se apoya en una abstracción ya validada en el análisis funcional previo del proyecto
(estudio de una plataforma comercial equivalente), y se traduce aquí al mundo Docker autoalojado.

### 4.1 Jerarquía de entidades

```mermaid
graph TD
    A["Administrador<br/>usuario unico"] --> P["Proyecto<br/>arquitectura completa"]
    P --> N["Red del proyecto<br/>bridge o macvlan"]
    P --> S1["Servicio: api<br/>configuracion"]
    P --> S2["Servicio: db<br/>configuracion"]
    S1 --> D1["Despliegue 47 · Activo<br/>contenedor en ejecucion"]
    S1 --> D0["Despliegue 46 · Retirado<br/>historial"]
    S2 --> D2["Despliegue 3 · Activo"]
    S1 --> V["Variables de entorno"]
    S2 --> VOL["Volumen o montaje"]
    S1 --> IP["Reserva de IP"]
    P --> CH["Changeset<br/>cambios pendientes"]
    S1 -.->|"enlace por variable"| S2
```

**Lectura:** el **proyecto** agrupa, el **servicio** configura, el **despliegue** ejecuta. Las
variables, los volúmenes y las reservas de IP cuelgan del **servicio**, no del despliegue: por eso
sobreviven a cualquier redespliegue.

### 4.2 La separación configuración / ejecución

Es la decisión estructural del modelo y de ella dependen casi todas las demás:

| | **Servicio** | **Despliegue** |
|---|---|---|
| Qué es | La **configuración**: origen, imagen, variables, montajes, red, IP, límites, política de reinicio, posición en el lienzo | El **intento concreto de materializar** esa configuración: el contenedor creado |
| Existencia | Existe siempre mientras no se lo borre del proyecto | Se crea en cada despliegue; los anteriores pasan a historial |
| ¿Tiene estado encendido/apagado? | **No** | **Sí**, con máquina de estados |
| Multiplicidad | 1 nodo en el lienzo | N a lo largo del tiempo; normalmente 1 activo, o N si hay réplicas |
| Equivalente Docker | La definición dentro de un `docker-compose.yml` | El contenedor concreto creado a partir de esa definición |

**[D] Por qué importa para la interfaz:** el nodo del lienzo representa al **servicio** —algo
permanente y posicionable— mientras que su color e insignia de estado reflejan al **despliegue
activo** —algo volátil—. Si el nodo representara al despliegue, el lienzo se reconstruiría en cada
arranque y perdería la posición. Es el patrón *estado deseado* frente a *estado actual*.

Y explica por qué "parar un servicio" no borra nada: detiene y elimina el contenedor, conservando
intactas la definición, las variables y los datos del volumen.

### 4.3 Ciclo de vida del despliegue

```mermaid
stateDiagram-v2
    [*] --> Pendiente : despliegue solicitado
    Pendiente --> Construyendo : requiere build de imagen
    Pendiente --> Creando : imagen ya disponible
    Construyendo --> Creando : build exitoso
    Construyendo --> Fallido : error de build
    Creando --> Activo : contenedor iniciado o healthcheck OK
    Creando --> Fallido : error al crear o arrancar
    Activo --> Caido : el proceso muere con codigo distinto de cero
    Activo --> Finalizado : el proceso termina con codigo cero
    Caido --> Activo : reinicio manual o politica de reinicio
    Activo --> Retirado : detener servicio o reemplazo por nuevo despliegue
    Finalizado --> Retirado : limpieza
    Fallido --> [*]
    Retirado --> [*]
```

**[D] Correspondencia con el estado real del motor de contenedores**, que es lo que el servicio
debe consultar para sincronizar:

| Estado del contenedor | Estado del despliegue | Nota |
|---|---|---|
| `created` | `Pendiente` | Creado, aún sin arrancar |
| `running` sin healthcheck | `Activo` | |
| `running` con healthcheck `starting` | `Creando` | Todavía no confirmado |
| `running` con healthcheck `healthy` | `Activo` | |
| `running` con healthcheck `unhealthy` | `Activo (degradado)` | Estado visible en el nodo; no es caída |
| `restarting` | `Caido` | La política de reinicio está actuando |
| `exited` con código 0 | `Finalizado` | Típico de tareas puntuales |
| `exited` con código distinto de 0 | `Caido` | |
| `paused` | `Activo (pausado)` | |
| `dead` o eliminado | `Retirado` | |

### 4.4 Semántica de las aristas del lienzo

Este es el punto donde el análisis previo del proyecto identificó el mayor riesgo de modelar mal la
abstracción, y conviene fijarlo aquí sin ambigüedad. Conviven **dos vínculos distintos** entre
servicios:

| Vínculo | Cómo se establece | Visibilidad en el lienzo |
|---|---|---|
| **Conectividad de red** | Automática: los servicios del mismo proyecto comparten red | **Implícita** — no se dibuja; es el fondo sobre el que viven los nodos |
| **Dependencia de configuración** | El usuario declara que un servicio consume la dirección y el puerto de otro mediante una variable de entorno | **Explícita** — es la arista que se dibuja |

**[D] Definición operativa adoptada:** *una arista del lienzo representa que el servicio origen
consume, vía variable de entorno, la dirección interna y el puerto del servicio destino.* De ahí se
derivan tres comportamientos concretos:

1. **Generación automática de la variable.** Al trazar la arista, la interfaz propone el valor
   correcto según el modo de red (nombre de contenedor y puerto en bridge; IP y puerto en macvlan).
2. **Orden de arranque.** El grafo de aristas define el orden topológico de arranque del proyecto y
   permite detectar ciclos.
3. **Propagación de cambios.** Si cambia la IP o el puerto del destino, todos los servicios origen
   de aristas entrantes quedan marcados como "requieren redespliegue".

### 4.5 Invariantes del modelo

| # | Invariante | Origen |
|---|---|---|
| I1 | Un proyecto contiene N servicios; un servicio pertenece a exactamente un proyecto | Definición del alcance 1 |
| I2 | Un servicio es, siempre, exactamente un contenedor Docker | Definición del alcance 1 |
| I3 | Un servicio no tiene estado encendido/apagado: su configuración existe siempre | [§4.2](#42-la-separación-configuración--ejecución) **[D]** |
| I4 | El ciclo de vida vive en el despliegue, nunca en el servicio | [§4.3](#43-ciclo-de-vida-del-despliegue) **[D]** |
| I5 | Un servicio tiene como máximo un despliegue activo por réplica | **[D]** |
| I6 | Los datos persistentes viven en el volumen o montaje, adjunto al servicio, y sobreviven a la parada | **[D]** |
| I7 | Dos servicios **activos** de proyectos distintos no pueden ocupar la misma IP; dos servicios **configurados** sí | Regla declarada en el alcance 1 |
| I8 | El nombre de servicio es único dentro del proyecto y es también su nombre DNS interno | **[D]** |
| I9 | Los cambios de arquitectura se acumulan en un changeset y se aplican en lote | **[D]**, ver [§5.5](#55-changeset-de-cambios-pendientes) |
| I10 | Un contenedor adoptado pertenece a un solo proyecto: no puede adoptarse dos veces | Módulo de adopción, alcance 1 |

### 4.6 Diagrama entidad-relación

```mermaid
erDiagram
    PROYECTO ||--o{ SERVICIO : contiene
    PROYECTO ||--o| RED : define
    PROYECTO ||--o{ CHANGESET : acumula
    SERVICIO ||--o{ DESPLIEGUE : historial
    SERVICIO ||--o{ VARIABLE : declara
    SERVICIO ||--o{ MONTAJE : monta
    SERVICIO ||--o{ PUERTO : publica
    SERVICIO ||--o| RESERVA_IP : reserva
    SERVICIO ||--o{ ENLACE : participa
    CATALOGO_ITEM ||--o{ SERVICIO : instancia
    ADMINISTRADOR ||--o{ TOKEN_API : emite
    DESPLIEGUE ||--o{ EVENTO : registra

    PROYECTO {
        int Id PK
        string Nombre
        string Slug
        bool AutoArranque
        string EstadoCalculado
        datetime CreadoEn
    }
    SERVICIO {
        int Id PK
        int ProyectoId FK
        string Nombre
        string TipoOrigen
        string Imagen
        string ModoRed
        string IpFija
        int Replicas
        bool AutoArranque
        string PoliticaReinicio
        float PosX
        float PosY
    }
    DESPLIEGUE {
        int Id PK
        int ServicioId FK
        int NumeroReplica
        string ContenedorId
        string Estado
        int CodigoSalida
        datetime IniciadoEn
        datetime FinalizadoEn
    }
    ENLACE {
        int Id PK
        int OrigenServicioId FK
        int DestinoServicioId FK
        string VariableGenerada
        int PuertoDestino
    }
    RESERVA_IP {
        int Id PK
        int ServicioId FK
        string Direccion
        string Interfaz
    }
```

---

## 5. Modelos de datos

Todos los modelos de esta sección son **[D] propuestas de diseño** de este análisis, construidas
para cubrir los requisitos declarados y los patrones observados en el parque real
([§3.3](#33-parque-de-contenedores-de-referencia)). Están pensados para servir de contrato tanto
del maquetado de la interfaz como del esquema de datos y de la API REST.

Convención: `snake_case` en la base de datos, `camelCase` en el JSON de la API, `PascalCase` en las
entidades de C#.

### 5.1 Proyecto con layout de canvas

```json
{
  "id": 12,
  "nombre": "Portal Interno",
  "slug": "portal-interno",
  "descripcion": "Sitio web interno con su base de datos y su cache",
  "autoArranque": true,
  "estado": "parcialmente-activo",
  "creadoEn": "2026-07-26T10:15:00-03:00",
  "red": {
    "modo": "bridge",
    "nombre": "portal-interno-net",
    "subred": "172.20.0.0/24",
    "gateway": "172.20.0.1",
    "creadaPorElServicio": true
  },
  "canvas": {
    "version": 1,
    "zoom": 0.9,
    "pan": { "x": -120, "y": 40 },
    "nodos": [
      { "servicioId": 101, "x": 160, "y": 120, "ancho": 260, "alto": 132, "grupo": null },
      { "servicioId": 102, "x": 560, "y": 120, "ancho": 260, "alto": 132, "grupo": null },
      { "servicioId": 103, "x": 560, "y": 320, "ancho": 260, "alto": 132, "grupo": "datos" }
    ],
    "grupos": [
      { "id": "datos", "titulo": "Persistencia", "color": "#7E57C2" }
    ],
    "enlaces": [
      { "id": 9001, "origen": 101, "destino": 102, "puertoOrigen": "salida", "puertoDestino": "entrada" },
      { "id": 9002, "origen": 101, "destino": 103, "puertoOrigen": "salida", "puertoDestino": "entrada" }
    ]
  },
  "servicios": [101, 102, 103],
  "cambiosPendientes": 0
}
```

**[D] Decisión de modelado:** el layout del lienzo se guarda **junto al proyecto**, en una única
columna JSON, no repartido en columnas por nodo. Motivo: se lee y se escribe siempre completo (al
abrir el lienzo y al soltar el arrastre), nunca se consulta por partes, y así una reorganización
visual es una sola escritura. SQLite y EF Core soportan bien esta forma con una conversión de
valor.

### 5.2 Servicio

Modelo completo, con las tres variantes de origen. Los campos ausentes se omiten en la respuesta de
la API.

```json
{
  "id": 101,
  "proyectoId": 12,
  "nombre": "api",
  "descripcion": "API REST del portal",
  "origen": {
    "tipo": "imagen",
    "imagen": "registro-privado/portal-api",
    "etiqueta": "1.4.2",
    "politicaActualizacion": "fijada",
    "registro": { "url": "registry.interno.lan", "requiereCredenciales": true, "credencialId": 3 }
  },
  "red": {
    "modo": "bridge",
    "aliasDns": "api",
    "ipFija": null,
    "interfazPadre": null
  },
  "puertos": [
    { "contenedor": 8080, "host": 8080, "protocolo": "tcp", "publicar": true }
  ],
  "variables": [
    { "clave": "ASPNETCORE_ENVIRONMENT", "valor": "Production", "secreta": false, "origen": "manual" },
    { "clave": "ConnectionStrings__Default", "valor": "Host=db;Port=5432;Database=portal", "secreta": false, "origen": "enlace", "enlaceId": 9002 },
    { "clave": "REDIS_URL", "valor": "cache:6379", "secreta": false, "origen": "enlace", "enlaceId": 9001 },
    { "clave": "API_KEY_EXTERNA", "valor": null, "secreta": true, "referenciaSecreto": "sec-004", "origen": "manual" }
  ],
  "montajes": [
    { "tipo": "volumen", "nombre": "portal-api-datos", "destino": "/app/data", "soloLectura": false }
  ],
  "dispositivos": [],
  "capacidades": [],
  "recursos": {
    "limiteMemoriaMb": 512,
    "reservaMemoriaMb": 128,
    "limiteCpus": 1.0
  },
  "replicas": 1,
  "politicaReinicio": "unless-stopped",
  "autoArranque": true,
  "efimero": false,
  "healthcheck": {
    "modo": "heredado-de-la-imagen",
    "comando": null,
    "intervaloSegundos": null
  },
  "adopcion": null,
  "posicionCanvas": { "x": 160, "y": 120 },
  "estadoActual": {
    "estado": "activo",
    "despliegueId": 5471,
    "desde": "2026-07-26T09:02:11-03:00",
    "requiereRedespliegue": false
  }
}
```

**Variante de origen por repositorio GitHub:**

```json
{
  "origen": {
    "tipo": "repositorio",
    "proveedor": "github",
    "url": "https://github.com/usuario/portal-api",
    "rama": "main",
    "rutaDockerfile": "src/Api/Dockerfile",
    "contextoBuild": ".",
    "argumentosBuild": { "CONFIGURATION": "Release" },
    "credencialId": 2,
    "reconstruirEnDespliegue": true
  }
}
```

**Variante de origen por Dockerfile local:**

```json
{
  "origen": {
    "tipo": "dockerfile",
    "rutaDockerfile": "/srv/proyectos/portal/Dockerfile",
    "contextoBuild": "/srv/proyectos/portal",
    "argumentosBuild": {},
    "reconstruirEnDespliegue": true
  }
}
```

**Variante macvlan con IP fija y dispositivo anclado** — el caso del parque real:

```json
{
  "nombre": "print-server",
  "red": {
    "modo": "macvlan",
    "aliasDns": "print-server",
    "ipFija": "192.168.1.139",
    "interfazPadre": "enp1s0",
    "subred": "192.168.1.0/24",
    "gateway": "192.168.1.1"
  },
  "puertos": [
    { "contenedor": 3344, "host": null, "protocolo": "tcp", "publicar": false }
  ],
  "dispositivos": [
    { "host": "/dev/serial/by-id/usb-FTDI-if00-port0", "contenedor": "/dev/ttyUSB0", "permisos": "rwm" }
  ],
  "recursos": { "limiteMemoriaMb": 512 },
  "politicaReinicio": "always"
}
```

**[D] Nota sobre `publicar` en macvlan:** cuando el modo es macvlan el contenedor tiene IP propia y
la publicación de puertos en el host **no aplica**; el formulario debe deshabilitar ese campo, no
sólo ignorarlo. Es un error de configuración frecuente y silencioso.

### 5.3 Despliegue

```json
{
  "id": 5471,
  "servicioId": 101,
  "numeroReplica": 1,
  "contenedorId": "3f9a1c7b2e4d",
  "nombreContenedor": "portal-interno_api_1",
  "imagenResuelta": "registro-privado/portal-api:1.4.2",
  "digestImagen": "sha256:a1b2c3...",
  "estado": "activo",
  "codigoSalida": null,
  "solicitadoPor": "ui",
  "changesetId": 331,
  "iniciadoEn": "2026-07-26T09:02:11-03:00",
  "finalizadoEn": null,
  "eventos": [
    { "en": "2026-07-26T09:01:40-03:00", "tipo": "pendiente", "detalle": "Despliegue encolado" },
    { "en": "2026-07-26T09:01:44-03:00", "tipo": "construyendo", "detalle": "build de imagen · 38 s" },
    { "en": "2026-07-26T09:02:09-03:00", "tipo": "creando", "detalle": "Contenedor creado" },
    { "en": "2026-07-26T09:02:11-03:00", "tipo": "activo", "detalle": "Healthcheck OK" }
  ],
  "metricas": {
    "cpuPorcentaje": 3.4,
    "memoriaUsadaMb": 186,
    "memoriaLimiteMb": 512,
    "tomadoEn": "2026-07-26T10:14:58-03:00"
  }
}
```

### 5.4 Enlace del lienzo

```json
{
  "id": 9002,
  "proyectoId": 12,
  "origenServicioId": 101,
  "destinoServicioId": 103,
  "puertoDestino": 5432,
  "protocolo": "tcp",
  "variableGenerada": {
    "clave": "ConnectionStrings__Default",
    "plantilla": "Host={destino.host};Port={destino.puerto};Database=portal",
    "valorResuelto": "Host=db;Port=5432;Database=portal"
  },
  "estado": "aplicado",
  "creadoEn": "2026-07-20T18:22:00-03:00"
}
```

**[D] Resolución de `{destino.host}` según el modo de red del destino:**

| Modo del destino | `{destino.host}` resuelve a | Requisito |
|---|---|---|
| `bridge` en la misma red del proyecto | El **alias DNS** del servicio (`db`) | Ambos servicios en la misma red |
| `macvlan` | La **IP fija** del servicio (`192.168.1.139`) | La IP debe estar reservada y sin conflicto |
| `bridge` con puerto publicado, consumido desde otra red | La IP del host más el puerto publicado | Requiere puerto publicado |

Si origen y destino no comparten un canal alcanzable, el enlace se marca **inválido** y el proyecto
no arranca. Ver [regla RN-04](#11-reglas-de-negocio-y-validaciones).

### 5.5 Changeset de cambios pendientes

**[D]** Patrón de edición transaccional: el lienzo se edita como borrador y los cambios se aplican
en lote. Aporta tres cosas que un guardado inmediato no da: revisión antes de aplicar, descarte
granular y un único redespliegue en vez de uno por clic.

```json
{
  "id": 331,
  "proyectoId": 12,
  "estado": "pendiente",
  "creadoEn": "2026-07-26T10:02:00-03:00",
  "mensaje": null,
  "cambios": [
    {
      "id": 1,
      "tipo": "servicio-agregado",
      "entidad": "servicio",
      "entidadId": null,
      "resumen": "Nuevo servicio 'cache' (imagen-oficial/redis:7.4)",
      "antes": null,
      "despues": { "nombre": "cache", "imagen": "imagen-oficial/redis", "etiqueta": "7.4" },
      "requiereRedespliegueDe": ["cache"]
    },
    {
      "id": 2,
      "tipo": "variable-modificada",
      "entidad": "servicio",
      "entidadId": 101,
      "resumen": "api · REDIS_URL: (sin valor) -> cache:6379",
      "antes": { "clave": "REDIS_URL", "valor": null },
      "despues": { "clave": "REDIS_URL", "valor": "cache:6379" },
      "requiereRedespliegueDe": ["api"]
    },
    {
      "id": 3,
      "tipo": "nodo-movido",
      "entidad": "canvas",
      "entidadId": 103,
      "resumen": "db movido a (560, 320)",
      "antes": { "x": 520, "y": 300 },
      "despues": { "x": 560, "y": 320 },
      "requiereRedespliegueDe": []
    }
  ],
  "impacto": {
    "serviciosARedesplegar": ["api", "cache"],
    "serviciosSinImpacto": ["db"],
    "conflictosIp": []
  }
}
```

**[D] Regla clave del changeset:** los cambios **puramente visuales** (mover un nodo, agrupar) se
guardan al instante y **no** generan redespliegue; sólo los cambios de configuración entran al
changeset. De lo contrario el usuario acumularía "cambios pendientes" por el mero hecho de ordenar
el dibujo, y el patrón perdería sentido.

### 5.6 Catálogo de servicios reutilizables

**[D]** Tercer alcance. Un ítem de catálogo es una **plantilla de servicio** con huecos
parametrizables.

```json
{
  "id": "cat-postgres-16",
  "nombre": "PostgreSQL 16",
  "categoria": "base-de-datos",
  "icono": "database",
  "version": 3,
  "plantilla": {
    "origen": { "tipo": "imagen", "imagen": "imagen-oficial/postgres", "etiqueta": "16-alpine", "politicaActualizacion": "fijada" },
    "puertos": [ { "contenedor": 5432, "protocolo": "tcp", "publicar": false } ],
    "variables": [
      { "clave": "POSTGRES_DB", "valor": "{{ nombreBase }}", "secreta": false },
      { "clave": "POSTGRES_USER", "valor": "{{ usuario }}", "secreta": false },
      { "clave": "POSTGRES_PASSWORD", "valor": "{{ password }}", "secreta": true }
    ],
    "montajes": [ { "tipo": "volumen", "nombre": "{{ slug }}-datos", "destino": "/var/lib/postgresql/data" } ],
    "recursos": { "limiteMemoriaMb": 1024 },
    "politicaReinicio": "unless-stopped",
    "healthcheck": { "modo": "propio", "comando": "pg_isready -U {{ usuario }}", "intervaloSegundos": 30 }
  },
  "parametros": [
    { "clave": "nombreBase", "etiqueta": "Nombre de la base", "tipo": "texto", "requerido": true, "porDefecto": "app" },
    { "clave": "usuario", "etiqueta": "Usuario", "tipo": "texto", "requerido": true, "porDefecto": "app" },
    { "clave": "password", "etiqueta": "Contraseña", "tipo": "secreto", "requerido": true, "generar": true },
    { "clave": "slug", "etiqueta": "Prefijo de recursos", "tipo": "texto", "requerido": true }
  ],
  "exportadoEn": "2026-07-26T10:00:00-03:00"
}
```

El archivo de exportación del catálogo completo es un envoltorio con versión de formato:

```json
{
  "formato": "selfhosted-catalogo",
  "version": 1,
  "exportadoEn": "2026-07-26T10:00:00-03:00",
  "items": [ "...items de catalogo..." ]
}
```

### 5.7 Adopción de contenedores existentes

**[D]** Salida del módulo de descubrimiento: lo que la interfaz muestra al preguntar "¿qué hay
corriendo en este servidor que quiera incorporar al proyecto?".

```json
{
  "descubiertoEn": "2026-07-26T10:20:00-03:00",
  "candidatos": [
    {
      "contenedorId": "b71c9d4a2f10",
      "nombre": "print-server",
      "imagen": "registro-privado/print-server:1.4.18",
      "estado": "running",
      "creadoEn": "2026-05-02T11:00:00-03:00",
      "redes": [ { "nombre": "infra_vlan", "modo": "macvlan", "ip": "192.168.1.139" } ],
      "montajes": [ { "tipo": "bind", "origen": "/srv/print3d/data", "destino": "/data" } ],
      "variablesDetectadas": 4,
      "etiquetasCompose": { "proyecto": "print3d-server", "servicio": "print3d-server" },
      "adoptable": true,
      "motivoNoAdoptable": null,
      "yaAdoptadoPor": null
    },
    {
      "contenedorId": "1a2b3c4d5e6f",
      "nombre": "panel-admin",
      "imagen": "imagen-oficial/panel-ce:latest",
      "estado": "running",
      "redes": [ { "nombre": "infra_vlan", "modo": "macvlan", "ip": "192.168.1.130" } ],
      "montajes": [ { "tipo": "socket", "origen": "/var/run/docker.sock", "destino": "/var/run/docker.sock" } ],
      "adoptable": false,
      "motivoNoAdoptable": "monta-el-socket-de-docker",
      "yaAdoptadoPor": null
    }
  ]
}
```

**[D] Reglas de adopción:**

| Regla | Comportamiento |
|---|---|
| RA-01 | Un contenedor ya adoptado por otro proyecto **no** vuelve a ofrecerse; se muestra en gris con el proyecto que lo tomó |
| RA-02 | La adopción **importa** la configuración observada (imagen, red, IP, montajes, variables no secretas) y crea el servicio **sin** recrear el contenedor |
| RA-03 | El contenedor adoptado queda vinculado por su identificador; si desaparece del motor, el servicio queda "huérfano" y se ofrece redesplegarlo desde la configuración importada |
| RA-04 | Un contenedor que monta el socket de Docker se marca **no adoptable por defecto**: gobernarlo desde el administrador crearía una dependencia circular de control. Puede forzarse con confirmación explícita |
| RA-05 | Las variables marcadas como sensibles por heurística (claves cuyo nombre contiene `PASSWORD`, `TOKEN`, `SECRET`, `KEY`, `PAT`) se importan **enmascaradas** y requieren recarga manual |

### 5.8 Reserva y conflicto de direcciones IP

```json
{
  "rangoGestionado": {
    "subred": "192.168.1.128/26",
    "desde": "192.168.1.129",
    "hasta": "192.168.1.190",
    "gateway": "192.168.1.1",
    "interfazPadre": "enp1s0",
    "excluidas": ["192.168.1.129"],
    "nota": "Debe estar excluido del rango que reparte el servidor DHCP de la red"
  },
  "reservas": [
    { "direccion": "192.168.1.130", "servicioId": 201, "proyectoId": 5, "servicio": "panel-admin", "activa": true },
    { "direccion": "192.168.1.139", "servicioId": 305, "proyectoId": 7, "servicio": "print-server", "activa": true },
    { "direccion": "192.168.1.139", "servicioId": 412, "proyectoId": 9, "servicio": "print-server-pruebas", "activa": false }
  ]
}
```

**Informe de conflicto** que devuelve el intento de arrancar el proyecto 9:

```json
{
  "proyectoId": 9,
  "puedeArrancar": false,
  "verificadoEn": "2026-07-26T10:31:00-03:00",
  "conflictos": [
    {
      "direccion": "192.168.1.139",
      "servicioSolicitante": { "id": 412, "nombre": "print-server-pruebas", "proyectoId": 9 },
      "ocupadaPor": { "id": 305, "nombre": "print-server", "proyectoId": 7, "proyecto": "Impresion 3D", "despliegueId": 5310, "estado": "activo" },
      "resolucionesPosibles": [
        { "accion": "detener-proyecto-en-conflicto", "objetivoId": 7, "etiqueta": "Detener el proyecto 'Impresion 3D'" },
        { "accion": "reasignar-ip", "sugerencia": "192.168.1.141", "etiqueta": "Asignar la siguiente IP libre del rango" },
        { "accion": "arrancar-parcial", "excluye": [412], "etiqueta": "Arrancar los demas servicios del proyecto" }
      ]
    }
  ],
  "serviciosSinConflicto": [410, 411]
}
```

### 5.9 Esquema relacional SQLite

**[D]** DDL de referencia para el maquetado de datos. Se muestra el subconjunto núcleo; los campos
de configuración de baja cardinalidad viajan como JSON en columnas `TEXT`, lo que en SQLite es una
forma idiomática y permite consultarlos con `json_extract` si hiciera falta.

```sql
CREATE TABLE proyectos (
    id                INTEGER PRIMARY KEY AUTOINCREMENT,
    nombre            TEXT    NOT NULL,
    slug              TEXT    NOT NULL UNIQUE,
    descripcion       TEXT,
    auto_arranque     INTEGER NOT NULL DEFAULT 0,   -- 0/1
    red_json          TEXT    NOT NULL DEFAULT '{}',
    canvas_json       TEXT    NOT NULL DEFAULT '{}',
    creado_en         TEXT    NOT NULL,
    modificado_en     TEXT    NOT NULL
);

-- Se crea antes que `servicios` porque `variables` referencia a ambas.
-- Las claves foraneas hacia `servicios` las agrega EF Core en la migracion
-- (SQLite las valida en tiempo de ejecucion, no de definicion).
CREATE TABLE enlaces (
    id                  INTEGER PRIMARY KEY AUTOINCREMENT,
    proyecto_id         INTEGER NOT NULL REFERENCES proyectos(id) ON DELETE CASCADE,
    origen_servicio_id  INTEGER NOT NULL,
    destino_servicio_id INTEGER NOT NULL,
    puerto_destino      INTEGER NOT NULL,
    protocolo           TEXT    NOT NULL DEFAULT 'tcp',
    plantilla_variable  TEXT    NOT NULL,
    clave_variable      TEXT    NOT NULL,
    estado              TEXT    NOT NULL DEFAULT 'pendiente',
    creado_en           TEXT    NOT NULL,
    CHECK (origen_servicio_id <> destino_servicio_id)
);

CREATE TABLE servicios (
    id                INTEGER PRIMARY KEY AUTOINCREMENT,
    proyecto_id       INTEGER NOT NULL REFERENCES proyectos(id) ON DELETE CASCADE,
    nombre            TEXT    NOT NULL,
    descripcion       TEXT,
    origen_json       TEXT    NOT NULL,             -- imagen | repositorio | dockerfile
    red_json          TEXT    NOT NULL,             -- modo, alias, ip fija, interfaz padre
    puertos_json      TEXT    NOT NULL DEFAULT '[]',
    montajes_json     TEXT    NOT NULL DEFAULT '[]',
    dispositivos_json TEXT    NOT NULL DEFAULT '[]',
    recursos_json     TEXT    NOT NULL DEFAULT '{}',
    healthcheck_json  TEXT    NOT NULL DEFAULT '{}',
    replicas          INTEGER NOT NULL DEFAULT 1,
    politica_reinicio TEXT    NOT NULL DEFAULT 'unless-stopped',
    auto_arranque     INTEGER NOT NULL DEFAULT 1,
    efimero           INTEGER NOT NULL DEFAULT 0,
    adopcion_json     TEXT,                          -- NULL si no fue adoptado
    pos_x             REAL    NOT NULL DEFAULT 0,
    pos_y             REAL    NOT NULL DEFAULT 0,
    creado_en         TEXT    NOT NULL,
    modificado_en     TEXT    NOT NULL,
    UNIQUE (proyecto_id, nombre)
);

CREATE TABLE variables (
    id                 INTEGER PRIMARY KEY AUTOINCREMENT,
    servicio_id        INTEGER NOT NULL REFERENCES servicios(id) ON DELETE CASCADE,
    clave              TEXT    NOT NULL,
    valor              TEXT,                         -- NULL si es secreta
    secreta            INTEGER NOT NULL DEFAULT 0,
    referencia_secreto TEXT,
    origen             TEXT    NOT NULL DEFAULT 'manual', -- manual | enlace | catalogo | adopcion
    enlace_id          INTEGER REFERENCES enlaces(id) ON DELETE SET NULL,
    UNIQUE (servicio_id, clave)
);

CREATE TABLE changesets (
    id                INTEGER PRIMARY KEY AUTOINCREMENT,
    proyecto_id       INTEGER NOT NULL REFERENCES proyectos(id) ON DELETE CASCADE,
    estado            TEXT    NOT NULL DEFAULT 'pendiente', -- pendiente | aplicado | descartado
    mensaje           TEXT,
    cambios_json      TEXT    NOT NULL DEFAULT '[]',
    creado_en         TEXT    NOT NULL,
    aplicado_en       TEXT
);

CREATE TABLE despliegues (
    id                INTEGER PRIMARY KEY AUTOINCREMENT,
    servicio_id       INTEGER NOT NULL REFERENCES servicios(id) ON DELETE CASCADE,
    numero_replica    INTEGER NOT NULL DEFAULT 1,
    contenedor_id     TEXT,
    nombre_contenedor TEXT,
    imagen_resuelta   TEXT,
    digest_imagen     TEXT,
    estado            TEXT    NOT NULL,
    codigo_salida     INTEGER,
    solicitado_por    TEXT    NOT NULL DEFAULT 'ui', -- ui | api | autoarranque | politica
    changeset_id      INTEGER REFERENCES changesets(id) ON DELETE SET NULL,
    iniciado_en       TEXT    NOT NULL,
    finalizado_en     TEXT
);

CREATE TABLE reservas_ip (
    id                INTEGER PRIMARY KEY AUTOINCREMENT,
    servicio_id       INTEGER NOT NULL REFERENCES servicios(id) ON DELETE CASCADE,
    numero_replica    INTEGER NOT NULL DEFAULT 1,
    direccion         TEXT    NOT NULL,
    interfaz_padre    TEXT    NOT NULL,
    UNIQUE (servicio_id, numero_replica)
);

CREATE TABLE catalogo_items (
    id                TEXT    PRIMARY KEY,
    nombre            TEXT    NOT NULL,
    categoria         TEXT    NOT NULL,
    icono             TEXT,
    version           INTEGER NOT NULL DEFAULT 1,
    plantilla_json    TEXT    NOT NULL,
    parametros_json   TEXT    NOT NULL DEFAULT '[]',
    modificado_en     TEXT    NOT NULL
);

CREATE TABLE tokens_api (
    id                INTEGER PRIMARY KEY AUTOINCREMENT,
    nombre            TEXT    NOT NULL,
    hash_token        TEXT    NOT NULL UNIQUE,
    prefijo           TEXT    NOT NULL,
    ambitos           TEXT    NOT NULL DEFAULT '',   -- lista separada por espacios
    creado_en         TEXT    NOT NULL,
    expira_en         TEXT,
    ultimo_uso_en     TEXT,
    revocado_en       TEXT
);

CREATE TABLE eventos_auditoria (
    id                INTEGER PRIMARY KEY AUTOINCREMENT,
    en                TEXT    NOT NULL,
    actor             TEXT    NOT NULL,              -- admin | token:<prefijo>
    accion            TEXT    NOT NULL,
    entidad           TEXT,
    entidad_id        TEXT,
    detalle_json      TEXT,
    resultado         TEXT    NOT NULL               -- ok | error | rechazado
);

-- Indices de consulta habitual
CREATE INDEX ix_servicios_proyecto      ON servicios(proyecto_id);
CREATE INDEX ix_despliegues_servicio    ON despliegues(servicio_id, estado);
CREATE INDEX ix_despliegues_contenedor  ON despliegues(contenedor_id);
CREATE INDEX ix_enlaces_proyecto        ON enlaces(proyecto_id);
CREATE INDEX ix_reservas_direccion      ON reservas_ip(direccion);
CREATE INDEX ix_auditoria_en            ON eventos_auditoria(en DESC);
```

**[D] Tres decisiones de esquema que conviene sostener:**

1. **La IP se guarda en `reservas_ip`, no sólo dentro de `red_json`.** Es el único dato que se
   consulta *entre proyectos* para detectar conflictos: necesita ser una columna indexada. La clave
   única por `(servicio_id, numero_replica)` es la que permite escalar un servicio macvlan dando una
   IP por réplica.
2. **`despliegues` no se borra nunca**; es el historial que alimenta la línea de tiempo del panel de
   servicio. Requiere una política de retención (por ejemplo, conservar los últimos 50 por servicio).
3. **Un único administrador no significa "sin auditoría"**: `eventos_auditoria` es lo que permite
   entender qué disparó un despliegue cuando lo hizo un workflow de CI y no una persona.

---

## 6. Decisión 1 · Canvas de arquitectura con Blazor.Diagrams

### 6.1 Requisitos que debe cubrir el lienzo

| # | Requisito | Justificación en el alcance del proyecto |
|---|---|---|
| R1 | Nodos totalmente personalizados: icono, nombre, insignia de estado, métricas, menú contextual | El nodo es la ficha del servicio, no una caja con texto |
| R2 | Aristas entre nodos, con puertos, para representar dependencias por variables | [§4.4](#44-semántica-de-las-aristas-del-lienzo) |
| R3 | Desplazamiento, zoom y ajuste a la vista sobre lienzo infinito | Metáfora de espacio visual declarada en el alcance 1 |
| R4 | Arrastre con posición persistente por proyecto | El layout es parte del proyecto |
| R5 | Agrupación de nodos | Agrupar servicios afines dentro de un proyecto |
| R6 | Actualización del estado del nodo en vivo, sin redibujar el grafo | Estados de despliegue cambiantes |
| R7 | Selección de nodo con panel lateral contextual | Configurar el servicio sin salir del lienzo |
| R8 | Serialización del grafo y del layout | Persistencia en SQLite y exportación a Compose |
| R9 | Tercer estilo visual para "pendiente de aplicar" | [Changeset](#55-changeset-de-cambios-pendientes) |
| R10 | Soltar un archivo sobre el lienzo (`docker-compose.yml`) | Importación del tercer alcance |
| R11 | Minimapa o navegador para proyectos grandes | Escalabilidad visual |
| R12 | Fluidez aceptable bajo Interactive Server | [§6.3](#63-el-condicionante-real-interactive-server) |

### 6.2 Qué ofrece la librería propuesta

**[E] Datos verificados del paquete `Z.Blazor.Diagrams`** (consulta del 2026-07-26):

| Atributo | Valor |
|---|---|
| Versión publicada | **3.0.4.1**, del **2 de marzo de 2026** |
| Licencia | **MIT** |
| Marcos objetivo | `net6.0`, `net7.0`, `net8.0`, `net9.0`, **`net10.0`** |
| Descargas | ~1,7 millones acumuladas (145 K de la versión actual) |
| Dependencias | `Microsoft.AspNetCore.Components`, `Microsoft.AspNetCore.Components.Web`, `Z.Blazor.Diagrams.Core` |
| Comunidad | ~1,4 K estrellas; 108 incidencias abiertas y 17 pull requests al momento de la consulta |

**[E] Capacidades declaradas en el repositorio oficial**, transcriptas de su lista de
características: *multi purpose*; *touch support*; *SVG layer for links/nodes and HTML layer for
nodes for maximum customizability*; *Links between nodes, ports and even other links*; *Link
routers, path generators, markers and labels*; *Panning, Zooming and Zooming to fit a set of
nodes*; *Multi selection, deletion and region selection*; *Groups as first class citizen, with all
the features of nodes*; *Custom nodes, links and groups*; *Replaceable ("Hackable") behaviors*;
*Customizable Diagram overview/navigator for large diagrams*; *Snap to Grid*; *Virtualization, only
draw nodes that are visible to the users*; *Locking mechanism (read-only)*; *Algorithms*.

**[E]** El proyecto se describe a sí mismo como librería para Blazor *"both Server Side and WASM"* y
afirma que *"95% of ZBD is made using C#/Blazor, JS is only used when absolutely necessary"*.

**Cobertura de los requisitos:**

| Requisito | Cubierto por | Estado |
|---|---|---|
| R1 | *Custom nodes* más capa HTML para nodos: el nodo es un componente Razor, construible con MudBlazor | ✔ **[E]** |
| R2 | *Links between nodes, ports and even other links* | ✔ **[E]** |
| R3 | *Panning, Zooming and Zooming to fit a set of nodes* | ✔ **[E]** |
| R4 | Modelo de nodo con posición, enlazable a la entidad de C# | ✔ **[E]** |
| R5 | *Groups as first class citizen* | ✔ **[E]** |
| R6 | Separación entre capa de datos (modelos) y capa visual: cambiar una propiedad refresca el nodo | ✔ **[E]** |
| R7 | Eventos de selección del diagrama | ✔ **[E]** |
| R8 | Los modelos son objetos C#: la serialización es propia de la aplicación | ✔ **[D]** |
| R9 | Estilo propio en el componente de nodo (CSS y MudBlazor) | ✔ **[D]** |
| R10 | La librería no lo provee: se resuelve con una zona de *drop* HTML sobre el contenedor del lienzo | ◐ **[D]** |
| R11 | *Customizable Diagram overview/navigator for large diagrams* más *Virtualization* | ✔ **[E]** |
| R12 | Depende del modelo de hospedaje, no de la librería | ◐ — ver [§6.3](#63-el-condicionante-real-interactive-server) |

### 6.3 El condicionante real: Interactive Server

**[E]** La documentación de Microsoft sobre modelos de hospedaje de Blazor enumera entre las
limitaciones de Blazor Server: *"Higher latency usually exists. Every user interaction involves a
network hop."*, y describe el mecanismo: *"UI updates, event handling, and JavaScript calls are
handled over a SignalR connection using the WebSockets protocol."*

**[D] Implicancia concreta para un lienzo.** Un arrastre genera decenas de eventos de puntero por
segundo. Si esos eventos se manejan en C#, **cada uno es un viaje de ida y vuelta al servidor**: el
arrastre se percibe con un retraso proporcional a la latencia. En una red local el efecto es
pequeño; a través de internet con 100–200 ms de ida y vuelta, es inutilizable.

**Mitigaciones, ordenadas por relación costo/beneficio [D]:**

| # | Mitigación | Costo |
|---|---|---|
| M1 | Manejar arrastre y zoom **en JavaScript** y notificar a C# **sólo al soltar**: la posición final es lo único que hay que persistir | Bajo — una función JS y una llamada de interoperabilidad |
| M2 | Mover con `transform` de CSS, sin volver a renderizar el árbol Razor durante el gesto | Bajo |
| M3 | Activar la **virtualización** de nodos fuera de la vista (ya provista por la librería) | Nulo |
| M4 | Garantizar **WebSockets** (no sondeo largo) en la publicación del contenedor y en cualquier proxy delante | Bajo, pero olvidarlo degrada todo |
| M5 | Enviar el estado de los nodos por lotes y con antirrebote (por ejemplo, cada 2 s) en vez de un mensaje por cambio | Bajo |
| M6 | Si el retraso resultara bloqueante, aislar **sólo la página del lienzo** en modo `InteractiveAuto` o WebAssembly | Alto — cambia un requisito del proyecto |

**[D] Precisión importante:** la librería **ya maneja internamente** el arrastre y el zoom, y
declara minimizar el uso de JavaScript. Eso significa que M1 no es "activar una opción", sino
sustituir o interceptar el comportamiento de arrastre — algo previsto por su diseño de
comportamientos reemplazables (*"Replaceable (\"Hackable\") behaviors"* **[E]**), pero que hay que
implementar. Por eso la prueba de concepto debe hacerse **antes** de comprometer la arquitectura, no
después.

### 6.4 Veredicto y condición de aceptación

**Se confirma `Z.Blazor.Diagrams` como primera opción.** Fundamentos:

1. Cumple el requisito no negociable: publica para `net10.0` y declara soporte de Blazor Server **[E]**.
2. Licencia **MIT**, sin costo ni fricción legal para un servicio autoalojado **[E]**.
3. Cubre R1–R7, R9 y R11 sin desarrollo propio **[E]**.
4. Los nodos son componentes Razor: el nodo de servicio se construye con **MudBlazor**, que ya es
   la librería de interfaz elegida, y se enlaza directamente al estado del despliegue **[D]**.
5. Publicación reciente (marzo de 2026) y comunidad amplia: riesgo de abandono bajo **[E]**.

**Condición de aceptación [D]** — prueba de concepto medida antes de comprometer la arquitectura:

| Criterio | Umbral |
|---|---|
| Escala de prueba | 30 nodos y 40 aristas, con insignia de estado y métricas por nodo |
| Fluidez de arrastre | Sin retraso perceptible en red local; medir el tiempo entre el evento del puntero y la actualización visual |
| Actualización de estado | 30 nodos actualizando estado cada 2 s, sin degradar el arrastre |
| Consumo del circuito | Memoria por circuito estable tras 15 minutos de uso continuo |
| Salida | Si falla, aplicar M1 y M2 y volver a medir **antes** de descartar la librería |

### 6.5 Maqueta de integración

**[D]** Esqueleto de referencia para el maquetado. El objetivo es mostrar los puntos de contacto:
modelo de nodo propio, componente Razor del nodo, y persistencia del layout al soltar.

```csharp
// Modelo de nodo: envuelve la entidad de dominio, no la reemplaza.
public sealed class ServicioNodo : NodeModel
{
    public ServicioNodo(ServicioDto servicio, Point posicion) : base(posicion)
    {
        Servicio = servicio;
        Title = servicio.Nombre;
    }

    public ServicioDto Servicio { get; }
    public EstadoDespliegue Estado => Servicio.EstadoActual.Estado;
    public bool TienePendientes { get; set; }
}
```

```razor
@* Nodo de servicio: componente Razor construido con MudBlazor *@
<MudPaper Class="@ClaseNodo" Elevation="2">
    <div class="nodo-cabecera">
        <MudIcon Icon="@IconoPorCategoria(Nodo.Servicio)" Size="Size.Small" />
        <MudText Typo="Typo.subtitle2">@Nodo.Servicio.Nombre</MudText>
        <MudSpacer />
        <EstadoChip Estado="@Nodo.Estado" />
    </div>
    <MudText Typo="Typo.caption" Class="nodo-imagen">@Nodo.Servicio.Origen.Referencia</MudText>
    <div class="nodo-metricas">
        <MudProgressLinear Value="@Nodo.Servicio.EstadoActual.CpuPorcentaje" Class="mini" />
        <MudText Typo="Typo.caption">@Nodo.Servicio.EstadoActual.MemoriaUsadaMb MB</MudText>
    </div>
</MudPaper>

@code {
    [Parameter] public ServicioNodo Nodo { get; set; } = default!;

    private string ClaseNodo => Nodo.TienePendientes
        ? "nodo-servicio nodo-pendiente"
        : $"nodo-servicio nodo-{Nodo.Estado.ToString().ToLowerInvariant()}";
}
```

```csharp
// Persistencia del layout: una sola escritura al soltar, nunca durante el arrastre.
private void ConfigurarDiagrama(Diagram diagrama)
{
    diagrama.Nodes.Added   += _ => MarcarLayoutSucio();
    diagrama.Nodes.Removed += _ => MarcarLayoutSucio();

    foreach (var nodo in diagrama.Nodes)
        nodo.Moved += _ => MarcarLayoutSucio();   // se dispara al terminar el movimiento
}

private void MarcarLayoutSucio() =>
    _debounce.Reiniciar(TimeSpan.FromMilliseconds(400), GuardarLayoutAsync);

private async Task GuardarLayoutAsync()
{
    var layout = new CanvasLayoutDto(
        Zoom: _diagrama.Zoom,
        Pan: new(_diagrama.Pan.X, _diagrama.Pan.Y),
        Nodos: _diagrama.Nodes.OfType<ServicioNodo>()
            .Select(n => new NodoLayoutDto(n.Servicio.Id, n.Position.X, n.Position.Y))
            .ToList());

    await _proyectos.GuardarLayoutAsync(_proyectoId, layout);   // una escritura sobre canvas_json
}
```

**[D] Regla de oro del lienzo:** durante el gesto no se persiste nada y no se notifica al servidor;
al finalizar, una única escritura con antirrebote. Es la diferencia entre un lienzo usable y uno
que satura el circuito.

---

## 7. Investigación de alternativas de canvas

Criterios de admisión, derivados del enunciado: **licencia abierta y permisiva** (preferentemente
MIT), **buena compatibilidad con Blazor y páginas Interactive Server de .NET**, y encaje con la
funcionalidad completa de la aplicación.

### 7.1 Candidatas nativas .NET

| Librería | Versión y fecha verificadas | Licencia | Marcos | Perfil |
|---|---|---|---|---|
| **`Z.Blazor.Diagrams`** (Blazor.Diagrams) | **3.0.4.1** · 2026-03-02 **[E]** | **MIT** **[E]** | `net6.0`–**`net10.0`** **[E]** | La más completa del ecosistema nativo: nodos, enlaces y grupos personalizados, puertos, minimapa, virtualización, comportamientos reemplazables. ~1,7 M de descargas **[E]** |
| **`Excubo.Blazor.Diagrams`** | 4.1.136 · 2025-11-15 | **MIT** **[E]** | `net6.0`+ **[E]** | Componente nativo con manipulación de nodos, selección múltiple, enlaces con curvas, **deshacer y rehacer por atajos**, biblioteca de nodos con arrastrar y soltar, pantalla de vista general y fondos configurables **[E]**. Comunidad bastante menor (~138 K descargas) |
| **Syncfusion Blazor Diagram** | Suite 34.x | **Comercial** | Blazor Server y WASM | Muy completo (rutas ortogonales, anotaciones, formas de diagrama de flujo) y con soporte pagado. Introduce dependencia de licencia en un proyecto autoalojado personal |
| **MindFusion Diagramming for Blazor** | — | **Comercial** | Blazor | Alternativa comercial equivalente; mismas objeciones |
| **`Qkmaxware.Blazor.NodeEditor`** | Librería de la era .NET 5 **[E]** | Ver paquete | .NET 5 | Editor de nodos reutilizable, pero desactualizado frente a `net10.0`: **descartado** por riesgo de mantenimiento |

**Lectura [D]:** dentro de .NET, la comparación real es `Z.Blazor.Diagrams` frente a
`Excubo.Blazor.Diagrams`. Ambas son MIT y nativas. La primera gana por cobertura funcional
(grupos como ciudadanos de primera clase, minimapa, virtualización, comportamientos reemplazables)
y por tamaño de comunidad, que es el mejor indicador disponible de continuidad. La segunda aporta
una función que la primera no declara —**deshacer y rehacer integrados**— que en un editor de
arquitectura es valiosa; si se elige la primera, ese comportamiento debe implementarse sobre el
changeset, que de todos modos ya lo hace posible.

### 7.2 Candidatas JavaScript vía interoperabilidad

Sólo se justifican si el requisito de fluidez del gesto resulta incompatible con manejarlo en C#.
El componente Blazor hospeda la librería JS y sólo intercambia el grafo serializado y eventos de
alto nivel ("nodo movido", "arista creada", "nodo seleccionado").

| Librería | Licencia verificada | Estado | Consideración para este proyecto |
|---|---|---|---|
| **React Flow / Svelte Flow (xyflow)** | **MIT** **[E]** — la suscripción "Pro" es patrocinio opcional, no un cambio de licencia **[E]** | Estándar de facto de editores de nodos | Excelente, pero exige meter React o Svelte dentro de una página Blazor: complejidad de compilación desproporcionada en un proyecto .NET |
| **maxGraph** | **Apache-2.0** **[E]** | 0.24.0 · 2026-07-08 **[E]**; sucesor mantenido de mxGraph, TypeScript nativo, **agnóstico de framework** **[E]** | La mejor candidata JS para envolver: sin dependencia de framework, tipada, sin dependencias externas. En contra: *"currently under active development, with a few adjustments still required to match the behavior of mxGraph"* **[E]** |
| **JointJS (núcleo)** | **MPL-2.0** **[E]** | Maduro; `JointJS+` es la extensión comercial **[E]** | MPL-2.0 es permisiva con copyleft por archivo: aceptable si no se modifican sus archivos. Las funciones avanzadas están del lado pago |
| **Drawflow** | MIT según su repositorio | Vanilla, liviana | La más fácil de envolver en un componente Blazor; menos funciones avanzadas (sin grupos ni minimapa) |
| **litegraph.js** | MIT según su repositorio | Motor y editor sobre lienzo HTML5 | Estética de "editor de nodos"; más lejos del aspecto de ficha de servicio que se busca |
| **Rete.js** | — | Framework de flujos visuales | Requiere framework JS: misma objeción que React Flow |
| **jsPlumb Community Edition** | Doble MIT/GPLv2 | Repositorio **sin actualizaciones** | **Descartada** para un proyecto nuevo |

### 7.3 Matriz comparativa

Escala: ✔ cumple · ◐ parcial o requiere trabajo · ✖ no cumple. **†** inferido del diseño de la
librería, no medido en una prueba.

| Criterio | Z.Blazor.Diagrams | Excubo.Blazor.Diagrams | Syncfusion | maxGraph (interop) | React Flow (interop) | Drawflow (interop) |
|---|---|---|---|---|---|---|
| R1 Nodos custom con Razor y MudBlazor | ✔ | ◐ | ✔ | ✖ | ✖ | ✖ |
| R2 Aristas con puertos | ✔ | ✔ | ✔ | ✔ | ✔ | ◐ |
| R3 Desplazamiento, zoom y ajuste | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| R4 Arrastre con posición persistente | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| R5 Grupos | ✔ | ◐ † | ✔ | ✔ | ✔ | ✖ |
| R6 Estado en vivo por nodo | ✔ (enlace C#) | ✔ | ✔ | ◐ (interop) | ◐ (interop) | ◐ (interop) |
| R7 Selección con panel lateral | ✔ | ✔ | ✔ | ◐ | ◐ | ◐ |
| R8 Serialización del grafo | ✔ (modelos C#) | ✔ | ✔ | ✔ (XML/JSON) | ◐ | ◐ |
| R9 Estilo "pendiente de aplicar" | ✔ (CSS propio) | ✔ | ✔ | ✔ | ✔ | ◐ |
| R10 Soltar archivo en el lienzo | ◐ (JS del contenedor) | ◐ | ◐ | ◐ | ◐ | ◐ |
| R11 Minimapa o navegador | ✔ | ◐ † | ✔ | ✔ | ✔ | ✖ |
| R12 Fluidez bajo Interactive Server | ◐ (requiere M1–M4) † | ◐ † | ◐ † | ✔ (todo en cliente) | ✔ | ✔ |
| Deshacer y rehacer integrados | ✖ (se implementa sobre el changeset) | ✔ **[E]** | ✔ | ◐ | ◐ | ✖ |
| Licencia | **MIT** | **MIT** | Comercial | **Apache-2.0** | **MIT** | MIT |
| Soporte `net10.0` | ✔ | ✔ | ✔ | n/a | n/a | n/a |
| Sin JavaScript propio que mantener | ✔ | ✔ | ✔ | ✖ | ✖ | ✖ |
| Continuidad (indicador de comunidad) | ~1,7 M descargas · 1,4 K ★ | ~138 K descargas | Producto comercial | Activo, aún no equiparado a mxGraph | Muy grande | Media |

### 7.4 Recomendación y plan de contingencia

**Primera opción: `Z.Blazor.Diagrams`.** Es la única que combina las tres condiciones del
enunciado —licencia MIT, compatibilidad declarada con Blazor Server sobre `net10.0`, y cobertura
funcional completa— sin introducir una capa de interoperabilidad que la elección de Blazor
justamente busca evitar.

**Segunda opción nativa: `Excubo.Blazor.Diagrams`**, también MIT y nativa, si se prioriza el
deshacer y rehacer integrados o si aparece un bloqueo con la primera.

**Plan de contingencia si la prueba de fluidez falla y M1–M4 no alcanzan [D]:** envolver
**maxGraph** en un componente Blazor con interoperabilidad **de grano grueso** — el grafo viaja como
JSON al inicializar y sólo se notifican eventos semánticos. Se elige maxGraph por encima de React
Flow porque es agnóstico de framework y no obliga a introducir React en el proyecto; se acepta a
cambio su madurez aún en curso.

**Descartes explícitos:**

| Descartada | Motivo |
|---|---|
| Syncfusion, MindFusion | Licencia comercial en un proyecto autoalojado personal, sin necesidad funcional que lo justifique |
| jsPlumb Community | Repositorio sin actualizaciones |
| React Flow, Rete.js | Obligan a introducir un framework JS completo en una aplicación .NET |
| `Qkmaxware.Blazor.NodeEditor` | Librería de la era .NET 5, sin evidencia de soporte `net10.0` |

---

## 8. Decisión 2 · Autenticación de la REST API

### 8.1 Qué se decidió y qué problema tiene

Lo declarado: *"Las Rest APIs deben ser con autentificación ROPC, jwt bearer token, e
implementación mediante Controladores."*

Hay tres decisiones distintas en esa frase y **conviene evaluarlas por separado**, porque no
comparten veredicto:

| Decisión | Veredicto | Sección |
|---|---|---|
| **ROPC** como flujo de obtención de token | **Desaconsejada** — contradice la práctica recomendada vigente | [§8.2](#82-evidencia-normativa-sobre-ropc) |
| **JWT bearer** como forma de presentar el token | **Correcta** y se mantiene | [§8.5](#85-arquitectura-de-autenticación-propuesta) |
| **Controladores** como estilo de implementación | **Correcta** para este proyecto | [§8.7](#87-controladores-frente-a-minimal-apis) |

ROPC (*Resource Owner Password Credentials*) es el flujo en el que el cliente recibe el usuario y la
contraseña del dueño de los datos y los cambia por un token contra el servidor de autorización.

### 8.2 Evidencia normativa sobre ROPC

**[E] Práctica recomendada del IETF — RFC 9700, "Best Current Practice for OAuth 2.0 Security"
(BCP 240), enero de 2025.** Su sección 2.4 dice textualmente:

> *"The resource owner password credentials grant MUST NOT be used."*

Y da dos motivos, también textuales:

> *"This grant type insecurely exposes the credentials of the resource owner to the client. Even if
> the client is benign, usage of this grant results in an increased attack surface (i.e.,
> credentials can leak in more places than just the authorization server) and in training users to
> enter their credentials in places other than the authorization server."*

> *"[the grant] is not designed to work with two-factor authentication and authentication processes
> that require multiple user interaction steps. Authentication with cryptographic credentials may
> be impossible to implement with this grant type, as it is usually bound to a specific web
> origin."*

**[E] OAuth 2.1 — borrador 15 del grupo de trabajo, marzo de 2026.** Enumera los flujos que
define: *"This specification defines the following authorization grant types: authorization code,
client credentials, and refresh token"*. Y en su sección de compatibilidad aclara que *"some
features available in OAuth 2.0, such as the Implicit or Resource Owner Credentials grant types,
are not specified in OAuth 2.1"*. Es decir: **ROPC deja de existir en la próxima versión del
estándar.**

**[E] Microsoft, documentación de la plataforma de identidad (actualizada el 15 de junio de 2026).**
Encabeza su página sobre ROPC con una advertencia:

> *"Microsoft recommends you do not use the ROPC flow; it's incompatible with multifactor
> authentication (MFA). In most scenarios, more secure alternatives are available and recommended.
> This flow requires a very high degree of trust in the application, and carries risks that aren't
> present in other flows. You should only use this flow when more secure flows aren't viable."*

Y añade restricciones operativas concretas: no funciona con cuentas sin contraseña (inicio por SMS,
FIDO, aplicación autenticadora), bloquea a los usuarios que requieran segundo factor, y no se
soporta en escenarios de identidad federada. Su sección "How to migrate away from ROPC" recomienda
autenticación interactiva cuando hay una persona involucrada, y autenticación de **entidad de
servicio** cuando no la hay — el caso de un script en una tubería de integración continua.

**[D] Lectura para este proyecto en particular.** Parte de la objeción del IETF se atenúa aquí: el
cliente y el servidor de autorización son **la misma aplicación** de primera parte, hay **un solo
usuario**, y el servicio corre en una red local. No hay terceros a quienes se entrene a tipear la
contraseña en el lugar equivocado. Pero dos objeciones se mantienen enteras y no dependen del
contexto:

1. **Superficie de exposición.** La contraseña del administrador —la misma que gobierna el motor de
   contenedores del servidor— viaja y se maneja en más lugares de los necesarios: cuerpo de la
   petición, registros de acceso si alguien registra los cuerpos, historial de la herramienta que
   la invoque, y variables de un workflow de CI si se usa desde ahí.
2. **Techo de evolución.** Un segundo factor, una clave de paso o cualquier mecanismo moderno
   quedan bloqueados desde el diseño. Para un panel que controla el servidor entero, cerrarse esa
   puerta el primer día es caro.

Y hay una tercera, específica de la arquitectura elegida **[D]**: **la interfaz web no necesita
ROPC**. Ver [§8.3](#83-los-tres-consumidores-reales-de-la-api).

### 8.3 Los tres consumidores reales de la API

```mermaid
graph LR
    U["Administrador<br/>navegador"] -->|"cookie de sesion<br/>sobre el circuito"| UI["Paginas Blazor<br/>Interactive Server"]
    UI -->|"llamada en proceso<br/>sin HTTP"| APP["Capa de aplicacion"]
    CI["GitHub Actions<br/>u otro automatismo"] -->|"Authorization: Bearer"| API["Controladores REST"]
    CLI["Script o herramienta<br/>del administrador"] -->|"Authorization: Bearer"| API
    API --> APP
    APP --> DOCKER[("Motor de contenedores")]
```

| Consumidor | ¿Necesita token? | Mecanismo adecuado |
|---|---|---|
| **La interfaz web** (páginas Interactive Server) | **No** | **[E]** En un Blazor Web App interactivo *"the authentication context is only established when the app starts... Authentication can be based on a cookie or some other bearer token, but authentication is managed via the SignalR hub and entirely within the circuit."* Las páginas invocan la capa de aplicación **en proceso**: no hay una llamada HTTP que autenticar |
| **GitHub Actions** (cuarto alcance) | **Sí** | No hay una persona presente: corresponde una credencial de **máquina**, no la contraseña del administrador |
| **Scripts propios del administrador** | **Sí** | Igual que el anterior: credencial revocable e independiente de la contraseña |

**[D] Conclusión estructural:** ROPC se propuso para un problema que no existe. El único consumidor
que necesita token es **automatizado**, y para automatismos el estándar y Microsoft coinciden en
recomendar credenciales de servicio, no las de la persona.

### 8.4 Alternativas evaluadas

| Alternativa | Cómo funciona | Ventajas | Costos | Encaje |
|---|---|---|---|---|
| **A. Cookie de ASP.NET Core Identity para la interfaz más tokens de API para automatismos** | Identity con cookie para la persona; tokens con ámbitos, guardados **hasheados**, para máquinas | Mínima superficie; revocación individual; auditoría por token; sin contraseña en workflows; abre la puerta a segundo factor | Hay que implementar el alta, listado y revocación de tokens: una pantalla y una tabla | **Recomendada** |
| **B. Cookie para la interfaz más `client_credentials` con JWT firmado por la propia aplicación** | El automatismo presenta identificador y secreto de cliente y recibe un JWT de vida corta | Estándar; JWT verificable sin consultar la base; alineado con OAuth 2.1 | Más piezas: emisión, firma, rotación de claves; y un JWT no se revoca antes de expirar | Buena si se prevé más de un automatismo con permisos distintos |
| **C. Endpoints de Identity de ASP.NET Core (`MapIdentityApi`)** | `POST /login` devuelve un token de portador propio de la plataforma más un token de refresco | Listo en pocas líneas, con segundo factor y refresco incluidos | **[E]** *"The tokens aren't standard JSON Web Tokens (JWTs). The use of custom tokens is intentional, as the built-in Identity API is meant primarily for simple scenarios."* Además: *"We recommend using cookies in browser-based applications"* | Aceptable como atajo; **contradice** el requisito de que el token sea JWT |
| **D. Servidor OpenID Connect propio (OpenIddict)** | La aplicación hospeda un servidor OIDC completo | Totalmente estándar; escala a múltiples clientes y usuarios | **[E]** OpenIddict 7.6.0, publicado 2026-07-15, Apache-2.0, con soporte `net8.0`/`net9.0`/`net10.0`: técnicamente apto, pero desproporcionado para un administrador de un solo usuario | Reservar para el día en que haya varios usuarios o clientes de terceros |
| **E. ROPC tal como fue especificado** | La API recibe usuario y contraseña y devuelve un JWT | Trivial de invocar desde cualquier cliente | Contradice BCP 240, eliminado en OAuth 2.1, desaconsejado por Microsoft; expone la contraseña del administrador; bloquea el segundo factor | **Desaconsejada**; si se mantiene, ver [§8.6](#86-si-se-mantiene-ropc-mitigaciones-obligatorias) |

### 8.5 Arquitectura de autenticación propuesta

**[D] Propuesta: alternativa A, con JWT bearer conservado como formato de presentación.**

```mermaid
sequenceDiagram
    actor ADM as Administrador
    participant UI as Paginas Blazor
    participant ID as ASP.NET Core Identity
    participant API as Controladores REST
    participant CI as GitHub Actions

    Note over ADM,ID: Primer arranque
    ADM->>UI: Alta del administrador con usuario y contrasena
    UI->>ID: Crear usuario y hash de contrasena
    ID-->>UI: Sesion iniciada, cookie emitida

    Note over ADM,API: Uso diario de la interfaz
    ADM->>UI: Operar el lienzo
    UI->>UI: Llamada en proceso a la capa de aplicacion

    Note over ADM,CI: Habilitar la automatizacion
    ADM->>UI: Crear token de API con ambitos
    UI-->>ADM: Token mostrado una unica vez
    ADM->>CI: Guardar como secreto del repositorio

    Note over CI,API: Despliegue automatizado
    CI->>API: POST /api/v1/servicios/101/desplegar con Authorization Bearer
    API->>API: Validar token, ambitos y vigencia
    API-->>CI: 202 Accepted con id de operacion
```

**Esquema de autenticación resultante:**

| Aspecto | Definición **[D]** |
|---|---|
| Interfaz web | Cookie de ASP.NET Core Identity, `HttpOnly`, `Secure`, `SameSite=Strict`; sin token en el navegador |
| API para automatismos | Encabezado `Authorization: Bearer <token>` |
| Formato del token | **JWT firmado** con clave simétrica de la instancia (HS256), emitido al crear el token de API; se guarda su **hash**, nunca el token |
| Vigencia | Configurable por token; por defecto 90 días, con opción "sin vencimiento" desaconsejada en la interfaz |
| Ámbitos | `proyectos:leer`, `proyectos:escribir`, `despliegues:ejecutar`, `catalogo:leer`, `catalogo:escribir`, `sistema:leer` |
| Revocación | Inmediata: el identificador del token (`jti`) se contrasta contra la tabla `tokens_api`, que marca `revocado_en` |
| Almacenamiento del secreto de firma | Fuera del repositorio y fuera de la imagen: variable de entorno o archivo montado, generado en el primer arranque |
| Auditoría | Toda operación de escritura registra el actor (`admin` o `token:<prefijo>`) en `eventos_auditoria` |
| Segundo factor | No requerido en el primer alcance, pero **posible** porque Identity lo soporta: la elección no lo bloquea |

Ejemplo del contenido de un token de API emitido (carga útil del JWT):

```json
{
  "iss": "selfhosted-service-core",
  "aud": "selfhosted-api",
  "sub": "admin",
  "jti": "tk_7f3c9a12",
  "scope": "proyectos:leer despliegues:ejecutar",
  "nombre": "github-actions-portal",
  "iat": 1785000000,
  "exp": 1792776000
}
```

**Nota de seguridad transversal [D]:** el servicio necesita acceso al socket del motor de
contenedores, lo que equivale a control total del host. Por eso la puerta de entrada de la API es
tan sensible como la del propio servidor. Dos consecuencias prácticas: (1) el servicio **no debe
publicarse a internet** sin una capa adicional de protección —y el proxy inverso está fuera de
alcance—; (2) el token de la automatización debe tener **el mínimo ámbito** que necesite,
típicamente sólo `despliegues:ejecutar`.

### 8.6 Si se mantiene ROPC: mitigaciones obligatorias

**[D]** Si por decisión del proyecto se conserva el flujo ROPC —es una decisión legítima del dueño
del sistema, que este análisis registra sin bloquear—, estas mitigaciones dejan de ser opcionales:

| # | Mitigación | Motivo |
|---|---|---|
| MT-1 | **TLS obligatorio** en el endpoint de token; rechazar HTTP simple incluso en la red local | La contraseña viaja en el cuerpo de la petición |
| MT-2 | **Bloqueo por intentos fallidos** con la política de Identity (por ejemplo, 5 intentos y 15 minutos de bloqueo) y límite de tasa por dirección de origen | El endpoint es un objetivo directo de fuerza bruta |
| MT-3 | **Nunca registrar el cuerpo** de la petición de token; enmascarar el parámetro `password` en cualquier traza | Evita filtrar la contraseña a los registros |
| MT-4 | **Token de acceso de vida corta** (15 minutos o menos) más token de refresco **rotativo** y revocable | Reduce la ventana de un token filtrado |
| MT-5 | Descartar la contraseña de memoria inmediatamente tras validarla; **jamás** persistirla | Es la indicación explícita de Microsoft para este flujo **[E]** |
| MT-6 | **No exponer el endpoint fuera de la red local** | El servicio controla el motor de contenedores |
| MT-7 | **Ofrecer igualmente tokens de API** para la automatización, de modo que ningún workflow guarde la contraseña del administrador | Es el escenario que Microsoft señala explícitamente para migrar fuera de ROPC **[E]** |
| MT-8 | Registrar en auditoría cada emisión de token con origen y resultado | Detección de abuso |

Aun con todas ellas, la evaluación no cambia: MT-7 resuelve el único caso de uso real, y una vez
implementado, ROPC queda sin consumidor.

### 8.7 Controladores frente a minimal APIs

| Criterio | Controladores | Minimal APIs |
|---|---|---|
| Encaje con Clean Architecture y carpetas por módulo | Alto: un controlador por recurso, dentro de la carpeta de su módulo | Requiere una convención propia de agrupación de endpoints |
| Filtros, convenciones, enlace de modelos y validación | Maduro y declarativo por atributos | Disponible, con una ergonomía distinta |
| Volumen de código repetitivo | Mayor | Menor |
| Rendimiento | Suficiente para este caso | Ligeramente mejor |
| Familiaridad y documentación | Muy amplia | Amplia |

**[D] Veredicto: se mantiene la decisión de usar controladores.** En una API de administración con
pocas decenas de endpoints, agrupados por módulo y con validación por atributos, los controladores
son la opción más legible y la que mejor acompaña la organización por carpetas ya definida. La
diferencia de rendimiento es irrelevante frente al costo de las operaciones sobre el motor de
contenedores. Excepción razonable: los endpoints de sondeo de estado y métricas, de altísima
frecuencia y cuerpo mínimo, pueden implementarse como minimal APIs sin romper la coherencia.

### 8.8 Contratos de la API

**[D]** Superficie mínima que sostiene los cuatro alcances. Todos los endpoints bajo `/api/v1`,
todos autenticados, todos con ámbito declarado.

| Método y ruta | Ámbito | Descripción |
|---|---|---|
| `GET /api/v1/proyectos` | `proyectos:leer` | Lista de proyectos con estado agregado |
| `POST /api/v1/proyectos` | `proyectos:escribir` | Alta de proyecto |
| `GET /api/v1/proyectos/{id}` | `proyectos:leer` | Proyecto con servicios, enlaces y layout |
| `PUT /api/v1/proyectos/{id}/canvas` | `proyectos:escribir` | Guardado del layout del lienzo |
| `POST /api/v1/proyectos/{id}/servicios` | `proyectos:escribir` | Alta de servicio |
| `PUT /api/v1/servicios/{id}` | `proyectos:escribir` | Edición de servicio; entra al changeset |
| `POST /api/v1/proyectos/{id}/changeset/aplicar` | `despliegues:ejecutar` | Aplica los cambios pendientes y redespliega lo afectado |
| `POST /api/v1/proyectos/{id}/arrancar` | `despliegues:ejecutar` | Arranca el proyecto completo; valida conflictos de IP |
| `POST /api/v1/proyectos/{id}/detener` | `despliegues:ejecutar` | Detiene el proyecto completo |
| `POST /api/v1/servicios/{id}/desplegar` | `despliegues:ejecutar` | Despliega o redespliega un servicio |
| `POST /api/v1/servicios/{id}/detener` | `despliegues:ejecutar` | Detiene el servicio, conservando su configuración |
| `POST /api/v1/servicios/{id}/reiniciar` | `despliegues:ejecutar` | Reinicia el contenedor sin reconstruir |
| `PUT /api/v1/servicios/{id}/replicas` | `despliegues:ejecutar` | Escalado horizontal manual |
| `PUT /api/v1/servicios/{id}/recursos` | `proyectos:escribir` | Escalado vertical manual: límites de CPU y memoria |
| `GET /api/v1/servicios/{id}/logs` | `proyectos:leer` | Registro del contenedor, con opción de flujo continuo |
| `GET /api/v1/descubrimiento/contenedores` | `proyectos:leer` | Candidatos a adopción |
| `POST /api/v1/proyectos/{id}/adoptar` | `proyectos:escribir` | Adopta contenedores existentes |
| `GET /api/v1/proyectos/{id}/exportar/compose` | `proyectos:leer` | Exporta la arquitectura como Docker Compose |
| `POST /api/v1/proyectos/importar/compose` | `proyectos:escribir` | Importa un Compose como proyecto nuevo |
| `GET /api/v1/catalogo` · `POST /api/v1/catalogo` | `catalogo:leer` / `catalogo:escribir` | Catálogo de servicios reutilizables |
| `GET /api/v1/sistema/estado` | `sistema:leer` | CPU, memoria, swap y disco del host |
| `GET /api/v1/red/conflictos` | `sistema:leer` | Estado de reservas y conflictos de IP |

Ejemplo de petición y respuesta del endpoint que usará el workflow de CI:

```http
POST /api/v1/servicios/101/desplegar HTTP/1.1
Host: admin.interno.lan
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "etiquetaImagen": "1.4.3",
  "esperarActivo": true,
  "tiempoLimiteSegundos": 180,
  "mensaje": "Despliegue automatico desde workflow build-and-deploy 482"
}
```

```json
{
  "operacionId": "op-9f21c",
  "servicioId": 101,
  "despliegueId": 5480,
  "estado": "creando",
  "iniciadoEn": "2026-07-26T11:02:00-03:00",
  "seguimiento": "/api/v1/operaciones/op-9f21c"
}
```

Respuesta de error cuando el arranque se bloquea por conflicto de IP, en formato `ProblemDetails`,
el estándar de ASP.NET Core:

```json
{
  "type": "https://selfhosted.local/errores/conflicto-ip",
  "title": "Conflicto de direcciones IP",
  "status": 409,
  "detail": "El servicio 'print-server-pruebas' solicita 192.168.1.139, ocupada por un servicio activo de otro proyecto.",
  "instance": "/api/v1/proyectos/9/arrancar",
  "conflictos": [
    {
      "direccion": "192.168.1.139",
      "servicioSolicitante": "print-server-pruebas",
      "proyectoEnConflicto": "Impresion 3D",
      "servicioEnConflicto": "print-server"
    }
  ]
}
```

---

## 9. Maquetado de la solución web

### 9.1 Mapa de navegación

```mermaid
graph TD
    L["/login<br/>o alta inicial del administrador"] --> P["/proyectos<br/>listado"]
    P --> C["/proyectos/id<br/>LIENZO · vista por defecto"]
    C --> SP["Panel lateral del servicio<br/>configuracion, variables, red, recursos"]
    C --> CH["Cajon de cambios pendientes"]
    C --> LG["/proyectos/id/servicios/sid/logs"]
    P --> D["/dashboard<br/>alcance 2"]
    P --> CAT["/catalogo<br/>alcance 3"]
    P --> DESC["/descubrimiento<br/>adopcion de contenedores"]
    P --> CFG["/configuracion<br/>rango de IP, tokens de API, respaldos"]
```

### 9.2 Pantalla del lienzo

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│  ☰  SelfHosted · Portal Interno            [▶ Arrancar] [■ Detener]   admin ▾    │
├───────┬──────────────────────────────────────────────────────┬───────────────────┤
│       │  ⚠ 3 cambios pendientes    [Ver detalle]  [Aplicar]  │  Actividad        │
│  ▣    │ ─────────────────────────────────────────────────────│ ───────────────── │
│ Lienzo│                                                      │ ✓ api desplegado  │
│       │      ┌──────────────┐          ┌──────────────┐      │   hace 2 min      │
│  ▤    │      │ ● api        │─────────▶│ ● db         │      │ ✓ cache agregado  │
│ Logs  │      │ portal-api   │          │ postgres:16  │      │   hace 5 min      │
│       │      │ ▮▮▮▯ 186 MB  │          │ ▮▮▯▯ 410 MB  │      │ ⚠ conflicto IP    │
│  ▦    │      └──────┬───────┘          └──────────────┘      │   resuelto        │
│ Métr. │             │                                        │                   │
│       │             ▼                                        │ ───────────────── │
│  ⚙    │      ┌──────────────┐                                │ Proyecto          │
│ Ajus. │      │ ○ cache      │  ← nodo pendiente (violeta)    │ 3 servicios       │
│       │      │ redis:7.4    │                                │ 2 activos         │
│       │      └──────────────┘                                │ red: bridge       │
│       │                                                      │ autoarranque: si  │
│       │   [+ Nuevo servicio]   [⤢ Ajustar]  [🗗 Minimapa]     │                   │
└───────┴──────────────────────────────────────────────────────┴───────────────────┘
```

**[D] Decisiones de la pantalla:**

| Elemento | Decisión | Motivo |
|---|---|---|
| El lienzo es la vista por defecto del proyecto | Se entra al proyecto y se ve su arquitectura, no una lista | La arquitectura *es* el proyecto |
| Banner de cambios pendientes | Fijo arriba del lienzo, con contador, detalle y aplicar | Hace visible el estado transaccional del borrador |
| Panel derecho contextual | Actividad cuando no hay selección; configuración del servicio cuando hay un nodo seleccionado | Evita navegar fuera del lienzo para configurar |
| Acciones de proyecto en la barra superior | Arrancar y detener el proyecto completo, siempre visibles | Son las dos operaciones más frecuentes |
| Botón primario único | "Nuevo servicio" | Una sola acción primaria por pantalla |

### 9.3 Anatomía del nodo de servicio

```
        ┌────────────────────────────────────┐
   ○────┤ 🐳  api                    ● Activo├────○      ○ = puerto de enlace
(entrada)│    registro/portal-api:1.4.2      │ (salida)
        │    ▮▮▮▯▯ CPU 3.4%   186 / 512 MB  │
        │    192.168.1.130 · macvlan         │
        │    ⟳ unless-stopped   ×2 replicas  │
        └────────────────────────────────────┘
              ▲ borde por estado · violeta si esta pendiente de aplicar
```

| Zona | Contenido | Origen del dato |
|---|---|---|
| Cabecera | Icono por categoría, nombre, insignia de estado | `servicio.nombre`, `estadoActual.estado` |
| Subtítulo | Imagen resuelta con etiqueta | `origen.imagen` y `origen.etiqueta` |
| Métricas | Barra de CPU y memoria usada sobre el límite | `despliegue.metricas` |
| Red | IP y modo, o alias DNS si es bridge | `red` |
| Pie | Política de reinicio y número de réplicas | `politicaReinicio`, `replicas` |
| Puertos laterales | Anclas de las aristas: entrada a la izquierda, salida a la derecha | Modelo de puertos de la librería |

### 9.4 Panel lateral de servicio

```
┌─ api ────────────────────────────── ✕ ─┐
│ ● Activo · desde hace 1 h 12 min       │
│ [⟳ Reiniciar] [↻ Redesplegar] [■ Parar]│
├────────────────────────────────────────┤
│ General │ Variables │ Red │ Recursos   │
│ Montajes│ Despliegues │ Logs           │
├────────────────────────────────────────┤
│ Origen        ▸ Imagen de registro     │
│ Imagen          registro/portal-api    │
│ Etiqueta        1.4.2   [fijada ▾]     │
│ Reinicio        unless-stopped ▾       │
│ Autoarranque    [x]                    │
│ Replicas        [– 2 +]                │
│ Efimero         [ ]                    │
├────────────────────────────────────────┤
│         [Cancelar]  [Guardar cambio]   │
└────────────────────────────────────────┘
```

**[D]** "Guardar cambio" **no despliega**: agrega la modificación al changeset del proyecto. El
despliegue ocurre al aplicar el changeset o al pulsar explícitamente "Redesplegar". Esta distinción
debe quedar clara en las etiquetas de los botones, porque es la fuente más probable de confusión
del modelo.

### 9.5 Dashboard del segundo alcance

Tres capas, tal como fue definido: servidor → proyecto → contenedor.

```
┌─ Servidor ───────────────────────────────────────────────────────────────┐
│  CPU  ▮▮▮▯▯▯▯▯ 34%    RAM ▮▮▮▮▮▯▯▯ 16.2/32 GB    SWAP ▮▮▯▯▯▯▯▯ 6.5/32 GB │
│  Disco / ▮▮▯▯▯▯▯▯ 115/884 GB     Contenedores 8 activos / 8 · 18 imagenes │
└──────────────────────────────────────────────────────────────────────────┘
┌─ Proyectos ──────────────────────────────────────────────────────────────┐
│  ● Portal Interno    3/3 activos   CPU 6%   RAM 1.1 GB   [abrir lienzo]  │
│  ◐ Impresion 3D      1/2 activos   CPU 1%   RAM 0.4 GB   [abrir lienzo]  │
│  ○ Laboratorio IA    0/3 activos   —        —            [abrir lienzo]  │
└──────────────────────────────────────────────────────────────────────────┘
┌─ Contenedores de "Portal Interno" ───────────────────────────────────────┐
│  api    ● Activo   3.4%  186/512 MB   1h12m   [logs] [reiniciar]         │
│  db     ● Activo   1.2%  410/1024 MB  2d 4h   [logs] [reiniciar]         │
│  cache  ● Activo   0.3%   24/256 MB   1h12m   [logs] [reiniciar]         │
└──────────────────────────────────────────────────────────────────────────┘
```

**[D] Restricciones de implementación del dashboard:**

1. **Origen de los datos: el motor de contenedores**, no peticiones HTTP a los servicios
   ([§3.2](#32-topología-de-red-y-su-consecuencia-de-diseño)).
2. **Frecuencia moderada.** El flujo de estadísticas del motor es costoso en CPU; con un servidor
   de gama modesta, un sondeo cada 3–5 segundos para la vista abierta, y ningún sondeo para las
   vistas cerradas.
3. **Un solo recolector.** Un servicio en segundo plano recolecta y publica a los circuitos
   conectados; no un flujo por pestaña abierta.
4. Los datos del **host** (CPU, RAM, swap, disco) se leen del sistema de archivos virtual del
   sistema operativo montado en el contenedor, en modo sólo lectura.

### 9.6 Lenguaje visual de estados

| Estado | Color | Insignia | Borde del nodo |
|---|---|---|---|
| Activo | Verde | ● | Sólido tenue |
| Activo degradado (healthcheck fallando) | Ámbar | ◐ | Sólido ámbar |
| Creando o construyendo | Azul | ◔ animado | Punteado animado |
| Detenido o retirado | Gris | ○ | Sólido gris |
| Caído o fallido | Rojo | ✕ | Sólido rojo |
| **Pendiente de aplicar** | **Violeta** | ◇ | **Punteado violeta** |
| Huérfano (contenedor adoptado desaparecido) | Gris con contorno rojo | ⚠ | Rayado |

**[D]** El violeta se reserva **exclusivamente** para "pendiente de aplicar" y no se usa en ningún
otro elemento de la interfaz. Un tercer estado visual sólo funciona si es inequívoco.

---

## 10. Ejemplos prácticos de extremo a extremo

### 10.1 E1 · Alta de proyecto con API y base de datos

**Escenario:** publicar una API .NET con su base PostgreSQL, conectadas de forma privada, sobre una
red bridge propia del proyecto.

**Pasos en la interfaz:**

1. `Nuevo proyecto` → nombre "Portal Interno" → se elige modo de red **bridge**, y el sistema
   propone la subred `172.20.0.0/24`. Se aterriza en el lienzo vacío.
2. `+ Nuevo servicio` → **Desde catálogo** → *PostgreSQL 16* → se completan los parámetros
   (`nombreBase=portal`, `usuario=portal`, contraseña generada). El nodo `db` aparece en **violeta**
   (pendiente).
3. `+ Nuevo servicio` → **Imagen de registro** → `registro-privado/portal-api:1.4.2` → nodo `api`,
   también pendiente.
4. Se arrastra una arista de `api` a `db`. El sistema propone la variable:

   ```json
   {
     "clave": "ConnectionStrings__Default",
     "plantilla": "Host={destino.host};Port={destino.puerto};Database=portal;Username=portal;Password={secreto:db.password}",
     "valorResuelto": "Host=db;Port=5432;Database=portal;Username=portal;Password=***"
   }
   ```

5. En `api` → pestaña **Red** → se publica el puerto `8080` en el host.
6. `Aplicar cambios` con el mensaje "Alta inicial del portal". El sistema crea la red, despliega
   `db`, espera su healthcheck y luego despliega `api`, respetando el orden topológico del grafo.

**Topología resultante:**

```mermaid
graph LR
    LAN["Equipos de la LAN"] -->|"http://host:8080"| API["Servicio: api<br/>Despliegue activo"]
    API -->|"db:5432 por DNS interno"| DB["Servicio: db<br/>Despliegue activo"]
    DB --- VOL[("Volumen<br/>portal-interno-db-datos")]
    subgraph NET["Red bridge portal-interno-net · 172.20.0.0/24"]
        API
        DB
        VOL
    end
```

**Qué hay que entender del ejemplo [D]:**

- `db` **no publica** ningún puerto en el host: `api` la alcanza por nombre dentro de la red del
  proyecto. Publicar el puerto de la base sería un error de seguridad que la interfaz debe
  desalentar.
- El orden de arranque **no se configura a mano**: se deduce del grafo de aristas.
- La contraseña de la base vive como **secreto referenciado**, nunca en texto plano en la
  exportación ni en la interfaz.
- El volumen sobrevive a detener y redesplegar `db`; sólo se borra al eliminar el servicio con
  confirmación explícita.

### 10.2 E2 · Adopción de un contenedor ya existente

**Escenario:** el servidor ya tiene corriendo un servidor de impresión 3D, con un dispositivo USB
anclado y una IP fija de la LAN. Se quiere incorporarlo a un proyecto sin reinstanciarlo.

```mermaid
sequenceDiagram
    actor ADM as Administrador
    participant UI as Interfaz
    participant SVC as Modulo de adopcion
    participant DOCKER as Motor de contenedores

    ADM->>UI: Proyecto "Impresion 3D" y Adoptar contenedor
    UI->>SVC: Descubrir candidatos
    SVC->>DOCKER: Listar contenedores e inspeccionar
    DOCKER-->>SVC: 8 contenedores con su configuracion
    SVC->>SVC: Filtrar ya adoptados y no adoptables
    SVC-->>UI: 5 candidatos
    ADM->>UI: Selecciona "print-server"
    UI->>SVC: Adoptar sin recrear
    SVC->>SVC: Importar imagen, red, IP, montajes, dispositivos y variables
    SVC->>SVC: Enmascarar variables sensibles por heuristica
    SVC-->>UI: Servicio creado, estado Activo, vinculado al contenedor existente
    UI-->>ADM: Nodo en el lienzo, sin corte de servicio
```

**Servicio resultante de la importación:**

```json
{
  "id": 305,
  "proyectoId": 7,
  "nombre": "print-server",
  "origen": { "tipo": "imagen", "imagen": "registro-privado/print-server", "etiqueta": "1.4.18", "politicaActualizacion": "fijada" },
  "red": { "modo": "macvlan", "ipFija": "192.168.1.139", "interfazPadre": "enp1s0" },
  "montajes": [ { "tipo": "bind", "origen": "/srv/print3d/data", "destino": "/data", "soloLectura": false } ],
  "dispositivos": [ { "host": "/dev/serial/by-id/usb-FTDI-if00-port0", "contenedor": "/dev/ttyUSB0", "permisos": "rwm" } ],
  "recursos": { "limiteMemoriaMb": 512 },
  "politicaReinicio": "always",
  "adopcion": {
    "adoptadoEn": "2026-07-26T10:25:00-03:00",
    "contenedorId": "b71c9d4a2f10",
    "recreado": false,
    "variablesEnmascaradas": ["ADMIN_TOKEN"]
  },
  "estadoActual": { "estado": "activo", "despliegueId": 5310, "requiereRedespliegue": false }
}
```

**Punto crítico de la adopción [D]:** el contenedor **no se recrea**. El servicio queda vinculado al
contenedor existente por su identificador, y el primer redespliegue —cuando el administrador lo
pida— será el momento en que la configuración importada se materialice de nuevo. Ese primer
redespliegue **sí** implica corte, y la interfaz debe advertirlo con esas palabras.

### 10.3 E3 · Arranque bloqueado por conflicto de IP

**Escenario:** un proyecto de pruebas define un servicio con la misma IP de LAN que un servicio ya
activo de otro proyecto.

```mermaid
sequenceDiagram
    actor ADM as Administrador
    participant UI as Interfaz
    participant VAL as Validador de red
    participant DB as Base de datos
    participant DOCKER as Motor de contenedores

    ADM->>UI: Arrancar proyecto "Impresion 3D - Pruebas"
    UI->>VAL: Validar reservas del proyecto 9
    VAL->>DB: Direcciones ocupadas por servicios activos
    DB-->>VAL: 192.168.1.139 ocupada por servicio 305 del proyecto 7
    VAL-->>UI: 409 Conflicto con 3 resoluciones posibles
    UI-->>ADM: Dialogo: detener el otro proyecto, reasignar IP o arrancar parcial
    ADM->>UI: Reasignar a la siguiente IP libre
    UI->>VAL: Reservar 192.168.1.141 para el servicio 412
    VAL->>DB: Actualizar reserva y marcar enlaces afectados
    UI->>DOCKER: Crear y arrancar los 3 servicios
    DOCKER-->>UI: Todos activos
```

**Algoritmo de validación [D]:**

```text
funcion validarArranque(proyectoId):
    conflictos = []
    servicios = serviciosConIpFija(proyectoId)

    para cada s en servicios:
        ocupante = buscarServicioActivoConIp(s.ip)     # indice sobre reservas_ip y despliegues activos
        si ocupante existe y ocupante.proyectoId != proyectoId:
            conflictos.agregar({ ip: s.ip, solicitante: s, ocupante: ocupante })

    # Colision dentro del propio proyecto: siempre invalida
    para cada par (a, b) en servicios con a.ip == b.ip:
        conflictos.agregar({ ip: a.ip, tipo: "duplicado-interno", servicios: [a, b] })

    # La IP debe pertenecer al rango gestionado y no estar excluida
    para cada s en servicios:
        si no perteneceAlRangoGestionado(s.ip) o estaExcluida(s.ip):
            conflictos.agregar({ ip: s.ip, tipo: "fuera-de-rango", servicio: s })

    si conflictos esta vacio: devolver PERMITIDO
    devolver BLOQUEADO con conflictos y resoluciones posibles
```

**Puntos finos que el algoritmo debe respetar [D]:**

- Se compara contra **servicios activos**, no contra servicios configurados: es exactamente la regla
  declarada en el alcance.
- La verificación es **transaccional**: entre validar y crear el contenedor no puede colarse otro
  arranque. Con SQLite, la validación y el registro de la reserva activa van en la misma
  transacción de escritura.
- El **arranque parcial** es una resolución legítima: el proyecto puede levantar sus servicios sin
  conflicto y dejar el conflictivo detenido, con el proyecto marcado "parcialmente activo".
- Al reasignar una IP, todos los **enlaces entrantes** al servicio quedan marcados para
  redespliegue, porque su variable de entorno cambió de valor.

### 10.4 E4 · Exportación a Docker Compose

**Escenario:** llevarse la arquitectura del proyecto "Portal Interno" a otro servidor.

`GET /api/v1/proyectos/12/exportar/compose` produce:

```yaml
name: portal-interno

services:
  api:
    image: registro-privado/portal-api:1.4.2
    container_name: portal-interno_api_1
    restart: unless-stopped
    environment:
      ASPNETCORE_ENVIRONMENT: Production
      ConnectionStrings__Default: Host=db;Port=5432;Database=portal;Username=portal;Password=${DB_PASSWORD}
      REDIS_URL: cache:6379
    ports:
      - "8080:8080"
    volumes:
      - portal-api-datos:/app/data
    depends_on:
      db:
        condition: service_healthy
    networks: [portal-interno-net]
    deploy:
      resources:
        limits: { memory: 512M, cpus: "1.0" }

  db:
    image: imagen-oficial/postgres:16-alpine
    container_name: portal-interno_db_1
    restart: unless-stopped
    environment:
      POSTGRES_DB: portal
      POSTGRES_USER: portal
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - portal-interno-db-datos:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U portal"]
      interval: 30s
    networks: [portal-interno-net]
    deploy:
      resources:
        limits: { memory: 1024M }

  cache:
    image: imagen-oficial/redis:7.4
    container_name: portal-interno_cache_1
    restart: unless-stopped
    networks: [portal-interno-net]
    deploy:
      resources:
        limits: { memory: 256M }

volumes:
  portal-api-datos:
  portal-interno-db-datos:

networks:
  portal-interno-net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/24
          gateway: 172.20.0.1
```

Acompañado de un archivo de variables **con los secretos vacíos**:

```bash
# portal-interno.env — completar antes de levantar
DB_PASSWORD=
```

Y del manifiesto propio que preserva lo que Compose no representa: el layout del lienzo.

```json
{
  "formato": "selfhosted-proyecto",
  "version": 1,
  "proyecto": { "nombre": "Portal Interno", "slug": "portal-interno", "autoArranque": true },
  "canvas": { "zoom": 0.9, "pan": { "x": -120, "y": 40 }, "nodos": [ { "servicio": "api", "x": 160, "y": 120 } ] },
  "enlaces": [ { "origen": "api", "destino": "db", "puerto": 5432, "clave": "ConnectionStrings__Default" } ],
  "secretosRequeridos": ["DB_PASSWORD"]
}
```

**[D] Correspondencia entre el modelo propio y Compose:**

| Concepto propio | Equivalente en Compose | Pérdida |
|---|---|---|
| Proyecto | `name` del archivo | Ninguna |
| Servicio | Entrada de `services` | Ninguna |
| Enlace por variable | Variable de entorno más `depends_on` | Se pierde la **intención** del enlace: se recupera del manifiesto propio |
| Reserva de IP macvlan | `networks.<red>.ipv4_address` | Ninguna |
| Layout del lienzo | **No existe** | Se preserva en el manifiesto propio |
| Changeset | **No existe** | Se exporta el estado aplicado, no el borrador |
| Secreto | Referencia `${VAR}` | El valor **nunca** se exporta: es deliberado |

**Regla de la importación inversa [D]:** al importar un Compose sin manifiesto propio, los nodos se
disponen automáticamente por capas según el grafo de `depends_on`, para que el lienzo resultante sea
legible desde el primer momento.

### 10.5 E5 · Despliegue disparado desde GitHub Actions

**Escenario del cuarto alcance:** al integrar cambios en la rama principal, el workflow construye la
imagen, la publica y le pide al administrador que despliegue la nueva versión.

**Preparación, una sola vez:**

1. En `/configuracion` → **Tokens de API** → crear el token `github-actions-portal` con el ámbito
   mínimo `despliegues:ejecutar` y vigencia de 90 días.
2. El token se muestra **una única vez** y se guarda como secreto del repositorio.

```yaml
# .github/workflows/deploy.yml
name: build-and-deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: self-hosted          # el runner del propio servidor, ya presente en el parque
    steps:
      - uses: actions/checkout@v4

      - name: Construir y publicar la imagen
        run: |
          docker build -t "$REGISTRO/portal-api:${GITHUB_SHA::7}" .
          docker push "$REGISTRO/portal-api:${GITHUB_SHA::7}"
        env:
          REGISTRO: ${{ vars.REGISTRO_PRIVADO }}

      - name: Solicitar el despliegue
        run: |
          curl --fail --silent --show-error \
            -X POST "$ADMIN_URL/api/v1/servicios/101/desplegar" \
            -H "Authorization: Bearer $ADMIN_TOKEN" \
            -H "Content-Type: application/json" \
            -d "{\"etiquetaImagen\":\"${GITHUB_SHA::7}\",\"esperarActivo\":true,\"tiempoLimiteSegundos\":180}"
        env:
          ADMIN_URL:   ${{ vars.ADMIN_URL }}
          ADMIN_TOKEN: ${{ secrets.ADMIN_API_TOKEN }}
```

**[D] Lo que este ejemplo demuestra sobre la decisión de autenticación:** el workflow nunca ve la
contraseña del administrador. Si el token se filtra, se revoca desde la interfaz y sólo se pierde
la capacidad de desplegar ese servicio; la sesión del administrador y el resto del sistema no se
ven afectados. Con ROPC, el mismo incidente comprometería la credencial que gobierna el servidor
entero. Esa diferencia es, en concreto, todo el argumento de
[§8](#8-decisión-2--autenticación-de-la-rest-api).

**Comportamiento esperado del endpoint [D]:**

| Situación | Respuesta |
|---|---|
| Despliegue aceptado | `202 Accepted` con `operacionId` y ruta de seguimiento |
| `esperarActivo=true` y el servicio queda activo | `200 OK` con el despliegue final |
| Tiempo límite superado | `504` con el último estado conocido y las últimas líneas del registro |
| Imagen inexistente en el registro | `422` con detalle del error de descarga |
| Token sin el ámbito requerido | `403` indicando el ámbito faltante |
| Conflicto de IP al recrear | `409` con el informe de conflicto |

---

## 11. Reglas de negocio y validaciones

**[D]** Catálogo de reglas verificables, pensado para que cada una se traduzca en una prueba
automatizada.

| # | Regla | Momento de validación | Respuesta ante incumplimiento |
|---|---|---|---|
| RN-01 | El nombre de servicio es único dentro del proyecto, en minúsculas, con guiones, de 1 a 32 caracteres | Alta y edición | `422` con el campo señalado |
| RN-02 | Un servicio pertenece a un único proyecto | Alta y adopción | `409` |
| RN-03 | Dos servicios **activos** de proyectos distintos no pueden compartir IP | Arranque de proyecto o servicio | `409` con informe y resoluciones |
| RN-04 | Todo enlace debe tener un canal alcanzable entre origen y destino según sus modos de red | Aplicación del changeset | Enlace marcado inválido; bloquea el arranque |
| RN-05 | El grafo de enlaces no puede tener ciclos | Creación de enlace | `422` señalando el ciclo |
| RN-06 | Toda IP fija debe pertenecer al rango gestionado y no estar excluida | Alta y edición | `422` con la siguiente IP libre sugerida |
| RN-07 | Un servicio en macvlan no puede publicar puertos en el host | Alta y edición | Campo deshabilitado en la interfaz; `422` desde la API |
| RN-08 | El servicio con origen "repositorio" requiere ruta de Dockerfile y rama | Alta | `422` |
| RN-09 | Al detener un servicio, sus volúmenes y montajes **no** se tocan | Detención | Invariante, verificable por prueba |
| RN-10 | Al eliminar un servicio se pide confirmación escribiendo su nombre, y se ofrece conservar los volúmenes | Eliminación | Interacción obligatoria |
| RN-11 | Un contenedor adoptado no puede adoptarse dos veces | Descubrimiento y adopción | Aparece deshabilitado con el proyecto que lo tomó |
| RN-12 | Los cambios visuales no entran al changeset ni disparan redespliegue | Edición del lienzo | Invariante |
| RN-13 | Aplicar el changeset redespliega **sólo** los servicios afectados | Aplicación | El informe de impacto lo declara antes de ejecutar |
| RN-14 | El arranque del proyecto respeta el orden topológico del grafo | Arranque | Invariante |
| RN-15 | Un secreto nunca se devuelve en texto plano por la API ni se escribe en una exportación | Toda respuesta y exportación | Enmascarado con `***` |
| RN-16 | El token de API se muestra una única vez y sólo se persiste su hash | Creación de token | Invariante |
| RN-17 | Toda operación de escritura queda registrada en auditoría con su actor | Cada operación | Invariante |
| RN-18 | El escalado horizontal crea réplicas con nombres sufijados y sin IP fija duplicada | Cambio de réplicas | `422` si el servicio tiene una sola IP fija y se piden más réplicas |
| RN-19 | El escalado vertical no puede exceder los recursos declarados del host | Cambio de límites | `422` con el máximo admisible |
| RN-20 | Un proyecto con al menos un conflicto puede arrancar parcialmente, quedando "parcialmente activo" | Arranque | Estado explícito, no error silencioso |

**RN-18, detalle importante [D]:** el escalado horizontal y la IP fija de macvlan son
**incompatibles** entre sí — dos réplicas no pueden compartir dirección. Un servicio en macvlan que
quiera escalar necesita una IP por réplica; el modelo lo admite (clave única
`(servicio_id, numero_replica)` en `reservas_ip`) pero la interfaz debe pedirlas explícitamente en
lugar de fallar en el arranque.

---

## 12. Arquitectura técnica de la solución

**[D]** Clean Architecture con organización por módulos, dentro de un despliegue monolítico: un
único proceso sirve la interfaz Blazor, la API REST y los servicios en segundo plano.

```mermaid
graph TD
    subgraph WEB["Capa de presentacion — un solo proceso"]
        BLAZOR["Paginas Blazor Interactive Server<br/>lienzo, dashboard, catalogo"]
        CTRL["Controladores REST /api/v1"]
        BG["Servicios en segundo plano<br/>sincronizacion de estado y metricas"]
    end
    subgraph APP["Capa de aplicacion — casos de uso por modulo"]
        M1["Proyectos"]
        M2["Servicios y despliegues"]
        M3["Descubrimiento y adopcion"]
        M4["Red y conflictos de IP"]
        M5["Catalogo"]
        M6["Observabilidad"]
        M7["Identidad y tokens"]
    end
    subgraph DOM["Capa de dominio"]
        E["Entidades, invariantes y reglas<br/>sin dependencias externas"]
    end
    subgraph INF["Capa de infraestructura"]
        EF["EF Core y SQLite"]
        DKR["Cliente del motor de contenedores"]
        FS["Sistema de archivos<br/>exportaciones y respaldos"]
        SYS["Metricas del host"]
    end
    BLAZOR --> APP
    CTRL --> APP
    BG --> APP
    APP --> DOM
    APP --> INF
    INF --> DOM
```

**Estructura de carpetas propuesta:**

```
SelfHosted.Service.Core/
├── src/
│   ├── SelfHosted.Domain/
│   │   ├── Proyectos/            (Proyecto, Red, CanvasLayout)
│   │   ├── Servicios/            (Servicio, Origen, Variable, Montaje, Recursos)
│   │   ├── Despliegues/          (Despliegue, EstadoDespliegue, Evento)
│   │   ├── Red/                  (ReservaIp, RangoGestionado, Conflicto)
│   │   ├── Catalogo/             (CatalogoItem, Parametro)
│   │   └── Identidad/            (TokenApi, Ambito)
│   ├── SelfHosted.Application/
│   │   ├── Proyectos/            (casos de uso, DTO, validadores)
│   │   ├── Servicios/
│   │   ├── Despliegues/
│   │   ├── Descubrimiento/
│   │   ├── Red/
│   │   ├── Catalogo/
│   │   ├── Observabilidad/
│   │   └── Abstracciones/        (IContenedorEngine, IProyectoRepository, IRelojSistema)
│   ├── SelfHosted.Infrastructure/
│   │   ├── Persistencia/         (DbContext, configuraciones, migraciones)
│   │   ├── Contenedores/         (adaptador del motor de contenedores)
│   │   ├── Sistema/              (metricas del host)
│   │   └── Exportacion/          (Compose, catalogo, respaldos)
│   └── SelfHosted.Web/
│       ├── Components/
│       │   ├── Canvas/           (lienzo, nodo de servicio, aristas, minimapa)
│       │   ├── Paneles/          (panel de servicio, changeset, actividad)
│       │   ├── Dashboard/
│       │   └── Layout/           (barra superior, menu lateral)
│       ├── Controllers/          (un controlador por recurso)
│       ├── BackgroundServices/   (sincronizador de estado, recolector de metricas)
│       └── wwwroot/js/           (canvas-interop.js — unico JS propio)
├── tests/
│   ├── SelfHosted.Domain.Tests/
│   ├── SelfHosted.Application.Tests/
│   └── SelfHosted.Integration.Tests/
└── scripts/                      (build, run local, migraciones)
```

**Decisión de infraestructura pendiente de cerrar — cliente del motor de contenedores [E]:**

| Paquete | Última versión verificada | Marcos | Licencia | Observación |
|---|---|---|---|---|
| `Docker.DotNet` | **3.125.15**, del **18 de mayo de 2023** | `netstandard2.0`+ | MIT | Cliente histórico de la Fundación .NET; 73,1 M de descargas, pero **más de tres años sin publicar** |
| `Docker.DotNet.Enhanced` | **4.3.3**, del **28 de junio de 2026** | `netstandard2.0`, `net8.0`, `net9.0`, `net10.0` | MIT | Fork mantenido por el equipo de Testcontainers; declara soporte de la API del motor **v29.4.1** |

**[D] Recomendación: `Docker.DotNet.Enhanced`.** El servidor de referencia corre un motor moderno
y el proyecto apunta a `net10.0`; un cliente sin publicaciones desde 2023 es una deuda técnica que
se paga en el primer endpoint no soportado. La abstracción `IContenedorEngine` de la capa de
aplicación deja la elección aislada: si hiciera falta volver atrás, el cambio queda confinado a un
adaptador.

**Servicios en segundo plano necesarios [D]:**

| Servicio | Frecuencia | Función |
|---|---|---|
| Sincronizador de estado | Suscripción a eventos del motor, más reconciliación cada 30 s | Mantiene `despliegues` alineado con la realidad del motor |
| Recolector de métricas | 3–5 s, sólo con vistas abiertas | Alimenta nodos y dashboard |
| Autoarranque | Al iniciar la aplicación | Levanta los proyectos marcados, en orden topológico y validando conflictos de IP |
| Retención de historial | Diaria | Poda los despliegues antiguos y los eventos de auditoría según política |

---

## 13. Evaluación general

### 13.1 Matriz de riesgos

| # | Riesgo | Probabilidad | Impacto | Mitigación |
|---|---|---|---|---|
| RG-01 | **Latencia del lienzo bajo Interactive Server** con arrastre manejado en C# | Media | Alto — es la pantalla principal | Prueba de concepto medida ([§6.4](#64-veredicto-y-condición-de-aceptación)) y mitigaciones M1–M4 |
| RG-02 | **ROPC** como puerta de entrada a un servicio que controla el host | Media | Alto | Adoptar la arquitectura de [§8.5](#85-arquitectura-de-autenticación-propuesta); si se mantiene, aplicar MT-1 a MT-8 |
| RG-03 | **Acceso al socket del motor de contenedores** equivale a control total del host | Alta (es inherente al diseño) | Muy alto | No exponer el servicio fuera de la red local; tokens de ámbito mínimo; auditoría de toda escritura |
| RG-04 | **Monitoreo inviable por red** con contenedores en macvlan | Alta | Medio | Observar por el motor de contenedores, no por HTTP ([§3.2](#32-topología-de-red-y-su-consecuencia-de-diseño)) |
| RG-05 | **Cliente de Docker desactualizado** frente al motor instalado | Media | Medio | Usar el fork mantenido y aislar tras `IContenedorEngine` |
| RG-06 | **Concurrencia de escritura en SQLite** entre la interfaz, la API y los servicios en segundo plano | Media | Medio | Activar el modo WAL, fijar tiempo de espera de bloqueo y serializar las operaciones de despliegue por proyecto |
| RG-07 | **Sin redundancia de disco** en el servidor de referencia | Alta | Alto (para el usuario, no para el software) | Exportación periódica de proyectos y catálogo a un destino externo; el propio servicio debe facilitarla |
| RG-08 | **Deriva entre el estado registrado y el motor** cuando alguien opera contenedores por fuera | Alta | Medio | Reconciliación periódica y estado "huérfano" explícito en el nodo |
| RG-09 | **Secretos importados en la adopción** que terminen visibles | Media | Alto | Enmascarado por heurística (RA-05) y regla RN-15 |
| RG-10 | **Alcance creciente** del lienzo (autolayout, rutas ortogonales, deshacer y rehacer) | Media | Medio | Fijar el alcance visual del primer incremento; deshacer y rehacer se apoyan en el changeset |

### 13.2 Inconsistencias detectadas en los insumos

Señalarlas es parte del análisis; ninguna se resolvió unilateralmente.

| # | Inconsistencia | Evidencia | Efecto |
|---|---|---|---|
| IC-01 | El prompt referencia `Analisis/Analisis-Rayway/Definicion-Idea.md`, pero en el repositorio la carpeta se llama `Analisis/Analisis-SaaS-Service/` | Estructura real del repositorio documental | Se usó el documento existente. **Conviene unificar la ruta** antes de ejecutar el prompt integrador siguiente, que referencia la misma ruta inexistente |
| IC-02 | Los archivos `Requerimientos-Funcionales.md` y `Requerimientos-Tecnicos.md` de la carpeta de entradas describen **otro proyecto**: una solución de monitoreo de SAI/UPS, con flujos UF-1 a UF-10 sobre baterías y con el nombre de solución `SAI.Service.Core` | Contenido de esos archivos | **Alto**: si el prompt integrador los toma como entrada, el documento Intake mezclará dos proyectos. Deben reemplazarse por los requerimientos de este servicio antes de continuar |
| IC-03 | La definición llama al servicio indistintamente "SaaS", "Selfhosted" y "PaaS" | Texto de la definición de idea | Menor, pero conviene fijar el término: es un **servicio autoalojado de un solo administrador**, no un SaaS multiinquilino |
| IC-04 | Se pide despliegue automático y escalado horizontal, pero el balanceo de carga y los proxies inversos están fuera de alcance | Secciones de alcance de la definición | Las réplicas creadas por escalado horizontal **no tendrán quién distribuya el tráfico** entre ellas. Debe explicitarse que el escalado horizontal en este alcance sirve para procesos sin tráfico entrante, o aceptar un proxy mínimo |
| IC-05 | La sección de alcance abre con una frase cortada: *"Debería verificar que otro"* | Texto de la definición de idea | Queda un requisito sin enunciar en el módulo de adopción. **[S] Supuesto adoptado:** se refiere a verificar que el contenedor no esté ya adoptado por otro proyecto, formalizado en RA-01 e I10. Requiere confirmación |

### 13.3 Complejidad relativa por alcance

**[D]** Estimación relativa, no en horas: sirve para ordenar el trabajo, no para comprometer fechas.

| Alcance | Complejidad | Componentes más costosos | Riesgo dominante |
|---|---|---|---|
| **1 — Núcleo** | Alta | Lienzo con nodos custom y persistencia de layout; adaptador del motor de contenedores; validador de conflictos de IP; changeset | RG-01 |
| **2 — Dashboard** | Media | Recolección eficiente de métricas; agregación por capas | RG-04 |
| **3 — Compose y catálogo** | Media | Correspondencia bidireccional con Compose; parametrización de plantillas | Pérdida de información en la ida y vuelta |
| **4 — GitHub Actions** | Baja | Tokens de API con ámbitos; endpoint de despliegue asíncrono con seguimiento | RG-02, RG-03 |

**[D] Observación sobre el orden:** el alcance 4 es el **menos costoso** y el que valida antes la
decisión de autenticación. Adelantar la emisión de tokens de API al alcance 1 —aunque el endpoint de
despliegue automatizado llegue después— cierra la discusión de ROPC con evidencia funcionando, y no
agrega esfuerzo apreciable.

### 13.4 Decisiones que conviene cerrar antes de codificar

| # | Decisión abierta | Opciones | Recomendación de este análisis |
|---|---|---|---|
| DA-01 | Flujo de autenticación de la API | ROPC / cookie más tokens de API / OIDC propio | **Cookie más tokens de API** ([§8.5](#85-arquitectura-de-autenticación-propuesta)) |
| DA-02 | Cliente del motor de contenedores | `Docker.DotNet` / `Docker.DotNet.Enhanced` | **El fork mantenido**, detrás de `IContenedorEngine` |
| DA-03 | Modo de red por defecto de un proyecto nuevo | bridge / macvlan | **bridge**: aislado, con DNS por nombre y sin consumir IP de la LAN. macvlan como opción explícita por servicio |
| DA-04 | Rango de IP gestionado | A definir | Un bloque **fuera del rango que reparte el DHCP** de la red; el sistema debe advertirlo en la configuración inicial |
| DA-05 | Alcance del deshacer y rehacer | Ninguno / sobre el changeset / integrado en la librería | **Sobre el changeset**: descartar un cambio individual ya es la mitad del deshacer |
| DA-06 | Manejo del arrastre | C# puro / interoperabilidad con JS al soltar | **Medir primero** (prueba de [§6.4](#64-veredicto-y-condición-de-aceptación)); implementar M1 sólo si hace falta |
| DA-07 | Política de retención del historial de despliegues | A definir | Últimos 50 por servicio y 90 días de auditoría, configurables |
| DA-08 | Estrategia de respaldo | A definir | Exportación programada de proyectos y catálogo a un destino externo, dado RG-07 |

---

## 14. Glosario

| Término | Definición |
|---|---|
| **Adopción** | Incorporación de un contenedor ya existente en el servidor a un proyecto, **sin recrearlo**. Sólo importa su configuración y lo vincula |
| **Alias DNS** | Nombre por el que un contenedor es resoluble dentro de una red definida por el usuario; suele coincidir con el nombre del servicio |
| **Arista o enlace** | Conexión dibujada en el lienzo. Representa que un servicio consume, por variable de entorno, la dirección y el puerto de otro |
| **Autoarranque** | Marca que indica que un proyecto o servicio debe levantarse al iniciar el sistema administrador |
| **Bridge** | Red virtual del motor de contenedores con su propia subred privada; sus miembros se resuelven por nombre y publican puertos en el host |
| **Canvas o lienzo** | Vista por defecto de un proyecto: espacio visual infinito donde cada bloque es un servicio y cada arista una dependencia |
| **Changeset** | Conjunto de cambios de configuración acumulados y pendientes de aplicar en lote sobre un proyecto |
| **Cliente confidencial o público** | En OAuth, cliente capaz o no de custodiar un secreto. Una aplicación de servidor es confidencial; una que corre en el navegador, no |
| **Cookie de sesión** | Credencial que el navegador envía automáticamente y que sostiene la sesión del administrador en la interfaz web |
| **Despliegue** | Intento concreto de materializar la configuración de un servicio: el contenedor creado, con su ciclo de vida |
| **Efímero** | Servicio pensado para reconstruirse en cada uso, sin estado persistente propio |
| **Escalado horizontal** | Agregar réplicas del mismo servicio. En este proyecto, manual |
| **Escalado vertical** | Aumentar los recursos (CPU, memoria) asignados a un servicio. En este proyecto, manual |
| **Healthcheck** | Comprobación periódica declarada en la imagen o en el servicio que determina si el contenedor está sano |
| **Huérfano** | Servicio cuyo contenedor vinculado ya no existe en el motor |
| **Interactive Server** | Modo de renderizado de Blazor en el que la interfaz se ejecuta en el servidor y se sincroniza con el navegador por una conexión SignalR sobre WebSockets |
| **JWT (JSON Web Token)** | Token firmado y autodescriptivo que se presenta en el encabezado `Authorization: Bearer` |
| **Macvlan** | Modo de red en el que el contenedor obtiene una dirección propia de la LAN y aparece como un equipo más de la red. El host no lo alcanza por la misma placa |
| **Minimapa o navegador** | Vista reducida del lienzo completo que facilita orientarse en grafos grandes |
| **Modo pendiente** | Estado visual (violeta) de un nodo o arista que existe en el changeset pero aún no se aplicó |
| **Política de reinicio** | Regla que indica si el contenedor debe reiniciarse solo: `no`, `on-failure`, `always`, `unless-stopped` |
| **Proyecto** | Unidad de agrupación: la arquitectura completa de servicios contenedorizados, con su red y su lienzo |
| **Réplica** | Cada instancia paralela de un mismo servicio |
| **ROPC** | *Resource Owner Password Credentials*: flujo de OAuth 2.0 en el que el cliente recibe usuario y contraseña y los cambia por un token. Prohibido por la práctica recomendada vigente y eliminado en OAuth 2.1 |
| **Servicio** | La **configuración** de un contenedor dentro de un proyecto: origen, variables, red, montajes, límites. No tiene estado de encendido |
| **Socket del motor de contenedores** | Punto de acceso local a la API del demonio de contenedores. Acceder a él equivale a control administrativo del host |
| **Token de API** | Credencial de máquina, con ámbitos y vigencia, revocable individualmente, usada por automatismos |
| **Variable de enlace** | Variable de entorno generada automáticamente a partir de una arista del lienzo |
| **Volumen o montaje** | Almacenamiento persistente adjunto a un servicio; sobrevive a la detención y al redespliegue |
| **Ámbito (scope)** | Permiso concreto asociado a un token de API, por ejemplo `despliegues:ejecutar` |

---

## Anexo A · Evidencias y fuentes

Este documento es autocontenido: no requiere abrir ninguna de estas fuentes para ser comprendido ni
para maquetar a partir de él. Se listan **únicamente** para permitir verificar cada afirmación
marcada **[E]**, como exige la restricción de trabajar sólo con información respaldada. Todas fueron
consultadas el **2026-07-26**.

**Sobre el lienzo y las librerías**

| Afirmación verificada | Fuente |
|---|---|
| `Z.Blazor.Diagrams` 3.0.4.1 (2026-03-02), MIT, `net6.0` a `net10.0`, ~1,7 M de descargas | Ficha del paquete en `nuget.org/packages/Z.Blazor.Diagrams/` |
| Lista de características, soporte de Blazor Server y WASM, *"95% of ZBD is made using C#/Blazor"*, ~1,4 K estrellas | Repositorio `github.com/Blazor-Diagrams/Blazor.Diagrams` |
| `Excubo.Blazor.Diagrams`: licencia MIT, `net6.0`+, deshacer y rehacer, biblioteca de nodos, vista general | Repositorio `github.com/excubo-ag/Blazor.Diagrams` y ficha del paquete en `nuget.org` |
| xyflow (React Flow y Svelte Flow): licencia MIT; la suscripción Pro es patrocinio, no cambio de licencia | Repositorio `github.com/xyflow/xyflow` |
| maxGraph: Apache-2.0, TypeScript nativo, agnóstico de framework, sucesor mantenido de mxGraph, 0.24.0 (2026-07-08), *"a few adjustments still required to match the behavior of mxGraph"* | Repositorio `github.com/maxGraph/maxGraph` y sus notas de publicación |
| JointJS núcleo bajo MPL-2.0; `JointJS+` es la extensión comercial | Página de licenciamiento en `jointjs.com/license` |
| Blazor Server: *"Higher latency usually exists. Every user interaction involves a network hop."* y *"UI updates, event handling, and JavaScript calls are handled over a SignalR connection using the WebSockets protocol."* | Microsoft Learn — *ASP.NET Core Blazor hosting models* (.NET 10) |

**Sobre autenticación**

| Afirmación verificada | Fuente |
|---|---|
| *"The resource owner password credentials grant MUST NOT be used."* y sus dos justificaciones textuales | RFC 9700 (BCP 240), *Best Current Practice for OAuth 2.0 Security*, enero de 2025, sección 2.4 |
| OAuth 2.1 define sólo *"authorization code, client credentials, and refresh token"*; *"some features available in OAuth 2.0, such as the Implicit or Resource Owner Credentials grant types, are not specified in OAuth 2.1"* | `draft-ietf-oauth-v2-1-15`, marzo de 2026 |
| *"Microsoft recommends you do not use the ROPC flow; it's incompatible with multifactor authentication (MFA)..."*, incompatibilidades con cuentas sin contraseña y con federación, y la guía de migración hacia autenticación interactiva o de entidad de servicio | Microsoft Learn — *Microsoft identity platform and OAuth 2.0 Resource Owner Password Credentials*, actualizado 2026-06-15 |
| *"The tokens aren't standard JSON Web Tokens (JWTs). The use of custom tokens is intentional, as the built-in Identity API is meant primarily for simple scenarios."* y *"We recommend using cookies in browser-based applications"*; lista de endpoints de `MapIdentityApi` | Microsoft Learn — *Use Identity to secure a Web API backend for SPAs* (.NET 10), actualizado 2026-03-23 |
| *"The authentication context is only established when the app starts... Authentication can be based on a cookie or some other bearer token, but authentication is managed via the SignalR hub and entirely within the circuit."* y *"If authorization rule enforcement must be guaranteed, don't implement authorization checks in client-side code."* | Microsoft Learn — *ASP.NET Core Blazor authentication and authorization* (.NET 10) |
| OpenIddict.AspNetCore 7.6.0 (2026-07-15), Apache-2.0, `net8.0`/`net9.0`/`net10.0` | Ficha del paquete en `nuget.org/packages/OpenIddict.AspNetCore` |

**Sobre la plataforma y la infraestructura**

| Afirmación verificada | Fuente |
|---|---|
| MudBlazor 9.7.0 (2026-07-09), MIT, `net8.0`/`net9.0`/`net10.0` | Ficha del paquete en `nuget.org/packages/MudBlazor` |
| `Docker.DotNet` 3.125.15 (2023-05-18), MIT, `netstandard2.0`+, 73,1 M de descargas | Ficha del paquete en `nuget.org/packages/Docker.DotNet` |
| `Docker.DotNet.Enhanced` 4.3.3 (2026-06-28), MIT, `netstandard2.0`/`net8.0`/`net9.0`/`net10.0`, API del motor v29.4.1, mantenido por el equipo de Testcontainers | Ficha del paquete en `nuget.org/packages/Docker.DotNet.Enhanced` y repositorio `github.com/testcontainers/Docker.DotNet` |
| Perfil de capacidad, topología de red, limitación del modo macvlan y parque de contenedores del servidor de referencia | Base de conocimiento documental del servidor propio, estado verificado el 2026-07-18 (valores **ofuscados** en este documento según el Anexo C) |
| Definición del servicio, alcances 1 a 4, requisitos técnicos y exclusiones | Documento de definición de idea del proyecto |
| Modelo de abstracción, semántica de las aristas, patrón de cambios en lote y evaluación previa de librerías | Análisis funcional previo del proyecto sobre una plataforma comercial equivalente |

---

## Anexo B · Limitaciones y puntos no verificados

| # | Punto | Estado |
|---|---|---|
| L1 | **Rendimiento real de `Z.Blazor.Diagrams` bajo Interactive Server.** No se encontró documentación ni medición pública. Las marcas **†** de la matriz de [§7.3](#73-matriz-comparativa) son inferencias de diseño | Requiere la prueba empírica de [§6.4](#64-veredicto-y-condición-de-aceptación) |
| L2 | **Soporte de `net10.0` en `Excubo.Blazor.Diagrams`.** El repositorio declara `net6.0`+; la ficha del paquete debe revisarse antes de adoptarlo | Pendiente de confirmación |
| L3 | **Licencias de Drawflow y litegraph.js.** Se reportan como MIT en sus repositorios, pero no fueron reverificadas en esta ejecución | No reverificado |
| L4 | **Condiciones de la licencia comunitaria de Syncfusion.** No se relevaron; sólo se constató que es un producto comercial | Fuera de alcance |
| L5 | **Comportamiento exacto de la librería al reemplazar el comportamiento de arrastre** (mitigación M1). Está declarado como posible por su diseño de comportamientos reemplazables, pero no se verificó una implementación concreta | Inferencia fundada |
| L6 | **Frase incompleta en la definición de idea** (*"Debería verificar que otro"*). El supuesto adoptado se documenta en IC-05 | Requiere confirmación del autor |
| L7 | **Requerimientos funcionales y técnicos de la carpeta de entradas**: pertenecen a otro proyecto (IC-02). No se usaron como fuente | Señalado, no resuelto |
| L8 | **Estado del servidor de referencia**: refleja el relevamiento del 2026-07-18. IP, versiones y contenedores cambian | Verificar vigencia antes de decisiones operativas |
| L9 | **Estimaciones de complejidad de [§13.3](#133-complejidad-relativa-por-alcance)**: son relativas y de diseño, no mediciones | Interpretación explícita |
| L10 | **Límites de concurrencia de SQLite** con tres escritores lógicos (interfaz, API y servicios en segundo plano): la mitigación propuesta (WAL y serialización por proyecto) no fue probada en este contexto | Requiere validación en la etapa de codificación |

---

## Anexo C · Política de ofuscación aplicada

Se ofuscó la información que podría comprometer la seguridad del servidor de referencia,
**conservando** todos los valores, modelos y ejemplos necesarios para maquetar la solución.

| Categoría | Tratamiento | Motivo |
|---|---|---|
| Nombre del host y su FQDN o dominio | **Eliminados**; se usa "servidor de referencia" o "host de contenedores" | Identifican el equipo real |
| Direcciones IP del host y de los contenedores | **Reasignadas** dentro de un rango privado genérico; se conservan la estructura y el modo de red | La topología es necesaria para maquetar; el mapa exacto del parque, no |
| Nombres de imágenes propias y del registro privado | **Genéricos** (`registro-privado/...`, `imagen-oficial/...`) | Revelan cuentas y artefactos del propietario |
| Nombres de contenedores y proyectos reales | **Normalizados** a equivalentes funcionales | Reducen la identificabilidad conservando el caso de uso |
| Puertos en escucha del host y su alcance de exposición | **Omitidos** | Es información directamente aprovechable por un atacante |
| Hallazgos de seguridad, riesgos abiertos y servicios sin autenticar del servidor | **Omitidos por completo** | Es el dato más sensible del relevamiento |
| Rutas absolutas del sistema de archivos del propietario | **Reemplazadas** por rutas de ejemplo (`/srv/...`) | Revelan estructura y usuario del equipo |
| Ubicación de secretos, tokens y archivos de variables no versionados | **Omitida** | Señala directamente dónde buscar credenciales |
| Configuración de acceso remoto, cortafuegos y usuarios | **Omitida** | Superficie de ataque |
| Capacidad de hardware (núcleos, RAM, disco, ausencia de GPU) | **Conservada, aproximada** | Necesaria para dimensionar; no es explotable por sí sola |
| Versiones del motor de contenedores y del sistema operativo | **Conservadas a nivel de versión mayor** | Necesarias para elegir el cliente de la API; sin detalle de parche |
| Patrones de configuración observados (dispositivos, capacidades, límites, políticas) | **Conservados** | Son el insumo que justifica el modelo de datos |

---

> **Estado:** `draft` · generado el 2026-07-26 bajo `RuleSet-Lean`, a partir del prompt
> `PROMPTs/01-Crear-Analisis/Crear-Analisis-Final.md`.
> Antes de tomar decisiones de implementación, verificar la vigencia de las versiones de paquetes
> citadas en el [Anexo A](#anexo-a--evidencias-y-fuentes) y resolver las inconsistencias
> IC-01 e IC-02 de [§13.2](#132-inconsistencias-detectadas-en-los-insumos), que afectan al
> prompt integrador siguiente.
