
# Requerimientos Técnicos

Este documento hereda todos los requerimientos técnicos de `/DEV/SelfHosted.Service.Core.Documentos/PROMPTs/02-Ejecutar-Prompt-Integrador-Documento-Intake/INPUTs/Analisis-Final-Integrado.md` y se toman las siguientes decisiones.

---

## 1. Datos de la solución

| Dato | Valor |
|---|---|
| Nombre de la solución | **SelfHosted.Service.Core** |
| Cantidad de desarrolladores | 2 |
| Tiempo de desarrollo | No se contempla plazo. El avance se mide por etapas cerradas según el documento de requerimientos funcionales. |
| Modo de trabajo | Etapas **en serie**. El punto de control de cada etapa es un cuello por diseño: no se abre la etapa siguiente sin el OK del agente humano. |

---

## 2. Plataforma y librerías

Versiones ancladas, verificadas en el análisis integrado. Cualquier cambio de versión mayor es una
decisión que se documenta, no un efecto colateral de una actualización de paquetes.

| Componente | Versión | Nota |
|---|---|---|
| Plataforma | **.NET 10** | Blazor con páginas **Interactive Server** |
| Acceso a datos | **Entity Framework Core** | Migraciones aplicadas al arrancar |
| Base de datos | **SQLite** | Ver §6 |
| Componentes de interfaz | **MudBlazor 9.7.0** | MIT · publicada 2026-07-09 |
| Lienzo | **Z.Blazor.Diagrams 3.0.4.1** | MIT · publicada 2026-03-02 · sujeta a la puerta técnica de §9 |
| Cliente del motor de contenedores | **Docker.DotNet.Enhanced 4.3.3** | MIT · publicada 2026-06-28 · declara soporte de la API del motor v29.4.1 |

**Patrón** — Clean Architecture con organización por módulos, en despliegue **monolítico**: un único
proceso sirve la interfaz Blazor, la API REST y los servicios en segundo plano. La estructura de
carpetas es la definida en
[§12 del análisis integrado](Analisis-Final-Integrado.md#12-arquitectura-técnica-de-la-solución).

**Regla de aislamiento** — el cliente del motor de contenedores se consume **exclusivamente** a
través de la abstracción `IContenedorEngine` de la capa de aplicación. Ningún tipo de la librería
de Docker puede aparecer fuera de `SelfHosted.Infrastructure/Contenedores/`. Es lo que permite
cambiar de cliente sin tocar el resto de la solución.

---

## 3. Entorno de desarrollo · devcontainer

### 3.1 Restricción de partida

**El desarrollo se realiza dentro de un Dev Container** (especificación `containers.dev`). **El host
Linux de desarrollo no tiene instalado el SDK de .NET, y no se va a instalar.**

> **Único requisito del host: Docker.** Todo el ciclo de desarrollo —compilar, ejecutar, migrar,
> depurar, probar— ocurre dentro del devcontainer. El host sólo aporta el motor de contenedores y el
> editor.

**Consecuencia directa:** ningún comando ni paso de un guion de demostración puede asumir `dotnet`
disponible en el host.

### 3.2 Reglas del entorno de desarrollo

Estas cinco reglas son de cumplimiento obligatorio. Cada una lleva su regla negativa asociada,
porque el error que previenen es más probable que el acierto que describen.

**R1 · El SDK vive dentro del contenedor, y ahí es "local".**
Dentro del devcontainer, `dotnet` está en el `PATH` como en cualquier máquina con el SDK instalado.
No hay nada especial que compensar.

**R2 · Los scripts son agnósticos del entorno.**
Los scripts de `scripts/` asumen `dotnet` en el `PATH` y nada más. No detectan el entorno, no
envuelven llamadas en `docker exec`, no ramifican por plataforma.
> **No** generar un conjunto de scripts duplicado por entorno. **No** existe una "versión
> devcontainer" de `build` o `run` distinta de la normal. En este proyecto el único lugar donde se
> cumple la premisa del `PATH` es dentro del devcontainer, pero eso es contexto de ejecución, no
> algo que el script deba saber.

**R3 · La orquestación del contenedor es declarativa.**
Se define en `.devcontainer/devcontainer.json` —más `Dockerfile` o `compose` si hacen falta— y se
levanta con "Reopen in Container" de VS Code o con el CLI `devcontainer up`.
> **No** usar scripts que hagan `docker run` a mano para levantar el entorno de desarrollo. Si algo
> del entorno no se puede expresar en `devcontainer.json`, se resuelve en el `Dockerfile` o el
> `compose` que ese archivo referencia, no en un script paralelo.
>
> *Aclaración de alcance:* esta regla gobierna **cómo se levanta el entorno de desarrollo**. No
> alcanza a la aplicación, cuya función es precisamente crear y operar contenedores a través de
> `IContenedorEngine`.

**R4 · La depuración no pasa por los scripts.**
Se resuelve con `.vscode/launch.json` (`coreclr`) y F5, con el depurador instalado en el
devcontainer.
> Los scripts **sólo compilan y ejecutan**. **No** se les agregan modos de depuración, banderas de
> espera de depurador ni variantes "debug". Son dos caminos separados y deben seguir siéndolo.

**R5 · La imagen de desarrollo no es la de producción.**
La imagen del devcontainer lleva SDK y depurador. La de producción es sólo entorno de ejecución y la
construye otro pipeline, con el `Dockerfile` multietapa de §8.
> El devcontainer **no** define, ni deriva, ni condiciona la imagen de producción. Son dos
> artefactos con propósitos distintos que no comparten linaje.

### 3.3 Composición del devcontainer

| Elemento | Definición |
|---|---|
| Imagen base | Imagen oficial de devcontainer con el **SDK de .NET 10** (`mcr.microsoft.com/devcontainers/dotnet`). El tag exacto se fija al crear el devcontainer y queda anclado en `devcontainer.json`. |
| Acceso a Docker | Característica **`docker-outside-of-docker`**: se monta el socket del host y se instala el cliente de línea de comandos dentro del contenedor. Ver §3.4. |
| Depurador | `coreclr`, disponible dentro del contenedor, con `.vscode/launch.json` versionado en el repositorio (R4). |
| Herramientas | `dotnet-ef` como herramienta local del repositorio, no global, para que la versión quede versionada junto al código. |
| Puertos | La aplicación se publica y se reenvía al host para poder abrirla desde el navegador del host. |
| Protocolo en desarrollo | **HTTP**, sin certificado de desarrollo. Evita la fricción del certificado de confianza dentro del contenedor. HTTPS es asunto del despliegue, no del desarrollo. |
| Usuario | No `root`. El usuario del contenedor debe tener permiso efectivo sobre el socket montado. |

### 3.4 Acceso al motor de contenedores · decisión y consecuencias

**Decisión: `docker-outside-of-docker`** — se monta `/var/run/docker.sock` del host dentro del
devcontainer. Se descarta *docker-in-docker*.

**Fundamento:** el producto administra el motor de contenedores. Con el socket del host montado, el
entorno de desarrollo opera **el mismo motor que operará en producción**, donde el contenedor de la
aplicación también recibirá el socket montado. Docker-in-Docker daría un motor distinto al de
producción y agregaría una capa de anidamiento que no aporta nada al caso de uso.

Esta elección arrastra tres consecuencias técnicas que **deben estar resueltas antes de la etapa 4
del Alcance 1** (despliegue desde imagen pública):

**a) Los contenedores creados son hermanos, no hijos.**
La aplicación corre dentro del devcontainer, pero los contenedores que crea nacen **en el host**, al
mismo nivel que el propio devcontainer. No son visibles como contenidos dentro de él. Para que la
aplicación pueda alcanzar por red a un servicio que acaba de desplegar, el devcontainer debe estar
adjunto a la misma red de puente del proyecto, o bien alcanzarlo por el puerto publicado en el host.

**b) Las rutas se resuelven en el host, no dentro del devcontainer.**
Toda ruta que la aplicación le pase al demonio —contexto de construcción de un Dockerfile, montajes
de volumen, directorio de repositorios clonados— **la interpreta el demonio del host**, no el
sistema de archivos del devcontainer. Si el espacio de trabajo está montado en rutas distintas en
uno y otro, cualquier construcción desde Dockerfile falla con un error de ruta inexistente que no
apunta a la causa real.

> **Requisito:** el directorio de datos de trabajo de la aplicación —repositorios clonados,
> contextos de construcción, exportaciones— debe estar montado **en la misma ruta absoluta en el
> host y en el devcontainer**. Se expone como una variable de configuración única y todo el
> adaptador de contenedores la usa como raíz. La alternativa, traducir rutas en el adaptador, se
> descarta por frágil.

**c) El desarrollo opera sobre el motor real del host.**
Los contenedores que ya corren en la máquina de desarrollo son visibles para la aplicación, y el
módulo de descubrimiento y adopción ([§5.7](Analisis-Final-Integrado.md#57-adopción-de-contenedores-existentes),
etapa 10 del Alcance 1) los va a listar como candidatos. Existe riesgo real de adoptar o detener un
contenedor ajeno al proyecto. Salvaguardas obligatorias:

| Salvaguarda | Definición |
|---|---|
| Prefijo de nombre | Todo contenedor creado por la aplicación lleva un prefijo reservado, configurable, distinto en desarrollo y en producción. |
| Etiquetas de pertenencia | Todo contenedor creado lleva etiquetas con el identificador de proyecto y de servicio. Son la fuente de verdad de la pertenencia, no el nombre. |
| Rango de IP de desarrollo | El rango gestionado en desarrollo es distinto del de producción y no se solapa con el de la red real del host. |
| Confirmación explícita | Adoptar o detener un contenedor **sin** etiquetas de la aplicación exige confirmación explícita en la interfaz, con el nombre escrito por el usuario. |
| Modo de sólo lectura | El descubrimiento parte en sólo lectura: listar no habilita operar. |

### 3.5 Scripts del repositorio

Los scripts viven en `scripts/`, son **shell (`.sh`)** y cumplen R2: asumen `dotnet` en el `PATH` y
no saben nada del entorno. No hay scripts `.bat`: el host de desarrollo es Linux, el devcontainer es
Linux y el destino de producción es Linux. Tampoco hay variantes por entorno: **un único conjunto**.

| Script | Función |
|---|---|
| `scripts/build.sh` | Restaura y compila la solución completa. Termina en 0 y sin advertencias. |
| `scripts/run.sh` | Ejecuta el servicio web y anuncia la URL. Punto de partida de todo guion de demostración. |
| `scripts/migrate.sh` | Genera y aplica migraciones de Entity Framework. |
| `scripts/test.sh` | Ejecuta la batería de pruebas completa. |
| `scripts/reset-db.sh` | Elimina la base de datos local para reproducir el estado de primer arranque. |

---

## 4. Decisiones técnicas cerradas

Se cierran acá las decisiones que el análisis integrado dejó abiertas en
[§13.4](Analisis-Final-Integrado.md#134-decisiones-que-conviene-cerrar-antes-de-codificar), en todos
los casos adoptando su recomendación.

| # | Decisión | Resolución |
|---|---|---|
| **DA-01** | Flujo de autenticación de la API | **Cookie de ASP.NET Core Identity para la interfaz web + tokens de API con ámbitos para automatismos.** Se **descarta ROPC**. Ver §5. |
| **DA-02** | Cliente del motor de contenedores | **`Docker.DotNet.Enhanced`**, detrás de `IContenedorEngine`. |
| **DA-03** | Modo de red por defecto de un proyecto nuevo | **bridge**: aislado, con resolución de nombres y sin consumir IP de la red local. `macvlan` como opción explícita por servicio. |
| **DA-04** | Rango de IP gestionado | Un bloque **fuera del rango que reparte el DHCP** de la red. La configuración inicial debe advertirlo y el sistema debe validarlo. |
| **DA-05** | Alcance del deshacer y rehacer | **Sobre el changeset**, no sobre la librería del lienzo. |
| **DA-07** | Retención del historial de despliegues | Últimos 50 por servicio y 90 días de auditoría, **configurables**. |
| **DA-08** | Estrategia de respaldo | Exportación programada de proyectos y catálogo a un destino externo. |

**DA-06** (manejo del arrastre en el lienzo) queda deliberadamente abierta: se resuelve **midiendo**,
en la puerta técnica de §9.

---

## 5. Autenticación, autorización y secretos

| Aspecto | Definición |
|---|---|
| Interfaz web | Cookie de ASP.NET Core Identity: `HttpOnly`, `Secure`, `SameSite=Strict`. **Sin token en el navegador.** |
| API para automatismos | Encabezado `Authorization: Bearer <token>`. |
| Formato del token de API | JWT firmado con clave simétrica de la instancia (HS256). Se almacena el **hash** del token, nunca el token. Se muestra al usuario una única vez. |
| Ámbitos | `proyectos:leer`, `proyectos:escribir`, `despliegues:ejecutar`, `catalogo:leer`, `catalogo:escribir`, `sistema:leer`. |
| Revocación | Inmediata, contrastando el identificador del token contra la tabla de tokens. |
| Clave de firma | Generada en el primer arranque. **Fuera del repositorio y fuera de la imagen**: variable de entorno o archivo montado. |
| Credenciales de terceros | Los tokens de GitHub y las credenciales de registros privados se guardan **cifrados en reposo** en la base de datos, con la clave de la instancia. Nunca se devuelven en claro por la API ni por la interfaz. |
| Auditoría | Toda operación de escritura registra el actor (`admin` o `token:<prefijo>`). |
| Segundo factor | Fuera del primer alcance, pero la elección de Identity no lo bloquea. |

**Nota de seguridad transversal.** El servicio necesita acceso al socket del motor de contenedores,
lo que **equivale a control total del host**. Por lo tanto: el servicio **no debe publicarse a
internet** sin una capa adicional de protección —y el proxy inverso está explícitamente fuera de
alcance—; y todo token de automatización debe emitirse con el **mínimo ámbito** necesario,
típicamente sólo `despliegues:ejecutar`.

---

## 6. Persistencia

| Aspecto | Definición |
|---|---|
| Motor | SQLite, archivo único. |
| Modo de diario | **WAL**. Los servicios en segundo plano escriben concurrentemente con la interfaz; sin WAL, los bloqueos de escritura degradan la interfaz. |
| Concurrencia de escritura | **Escritor único**. SQLite no admite escrituras concurrentes: las escrituras de los servicios en segundo plano se serializan. |
| Alcance del `DbContext` | Uno por operación. Los servicios en segundo plano crean su propio alcance en cada ciclo; nunca comparten el de la interfaz. |
| Migraciones | Se aplican automáticamente al arrancar sobre una base inexistente o desactualizada. |
| Ubicación del archivo | Configurable. En producción reside en un **volumen persistente**, nunca dentro de la imagen. |
| Respaldo | Según DA-08. El respaldo debe ser consistente con WAL activo. |

Los servicios en segundo plano son los definidos en §12 del análisis: sincronizador de estado
(suscripción a eventos del motor más reconciliación cada 30 s), recolector de métricas (3–5 s, sólo
con vistas abiertas), autoarranque (al iniciar) y retención de historial (diaria).

---

## 7. Pruebas

La regla de no-regresión del documento de requerimientos funcionales hace que el costo de verificar
manualmente cada guion crezca linealmente con las etapas. Se contiene con automatización desde la
primera etapa.

| Nivel | Proyecto | Qué cubre |
|---|---|---|
| Unitarias de dominio | `SelfHosted.Domain.Tests` | Invariantes del modelo ([§4.5](Analisis-Final-Integrado.md#45-invariantes-del-modelo)) y reglas de negocio ([§11](Analisis-Final-Integrado.md#11-reglas-de-negocio-y-validaciones)), en particular la regla de conflicto de IP. Sin infraestructura. |
| Unitarias de aplicación | `SelfHosted.Application.Tests` | Casos de uso con `IContenedorEngine` y repositorios simulados. |
| Integración | `SelfHosted.Integration.Tests` | Persistencia real contra SQLite y adaptador real contra el motor de contenedores, mediante **Testcontainers**. |

**Criterio de cierre de etapa:** una etapa no se considera terminada sin pruebas automatizadas de
las reglas de negocio que introdujo. Los guiones de demostración siguen siendo manuales —son la
demostración al cliente—, pero lo que protegen las pruebas no debe depender de ellos.

---

## 8. Despliegue en producción

| Aspecto | Definición |
|---|---|
| Formato | Contenedor Docker sobre Linux. |
| Construcción de la imagen | `Dockerfile` **multietapa** propio del pipeline de producción: una etapa con el SDK de .NET compila y publica, la imagen final lleva **sólo el entorno de ejecución**, sin SDK ni depurador. Coherente con no tener SDK en ningún host. |
| Relación con el devcontainer | **Ninguna** (R5 de §3.2). La imagen de desarrollo y la de producción son artefactos independientes, con propósitos distintos y sin linaje compartido. El `devcontainer.json` no interviene en la construcción de producción. |
| Acceso al motor | Se monta `/var/run/docker.sock` del host. Es un requisito funcional del producto, con la implicancia de seguridad de §5. |
| Volúmenes | Base de datos, directorio de datos de trabajo y exportaciones, en volúmenes persistentes. |
| Ruta del directorio de datos | Debe coincidir entre host y contenedor, por el mismo motivo de §3.4.b. |
| Exposición | Sólo en la red local. Sin publicación a internet. |
| Reemplazo de versión | *Detener y arrancar*, con ventana de indisponibilidad: sin proxy inverso no hay despliegue con solapamiento ([§2.4](Analisis-Final-Integrado.md#24-fuera-de-alcance)). |

---

## 9. Puertas técnicas

Verificaciones medidas que condicionan decisiones de arquitectura. **Una puerta que no pasa detiene
la planificación de las etapas que dependen de ella**, no se arrastra como deuda.

### PT-01 · Fluidez del lienzo bajo Interactive Server

Prueba de concepto exigida por
[§6.4](Analisis-Final-Integrado.md#64-veredicto-y-condición-de-aceptación). Debe ejecutarse **antes
de comprometer la etapa 3 del Alcance 1** (lienzo), porque si falla cambia la librería y se
replanifica esa etapa.

| Criterio | Umbral |
|---|---|
| Escala de prueba | 30 nodos y 40 aristas, con insignia de estado y métricas por nodo |
| Fluidez de arrastre | Sin retraso perceptible en red local |
| Actualización de estado | 30 nodos actualizando cada 2 s, sin degradar el arrastre |
| Consumo del circuito | Memoria por circuito estable tras 15 minutos de uso continuo |
| Salida en caso de falla | Aplicar las mitigaciones M1 y M2 de §6.3 y volver a medir **antes** de descartar la librería |

**Regla de oro del lienzo**, que la implementación debe respetar desde el primer día: durante el
gesto de arrastre no se persiste nada ni se notifica al servidor; al finalizar, una única escritura
con antirrebote.

### PT-02 · Motor de contenedores accesible desde el devcontainer

Debe verificarse **en la etapa `a`**, junto con el esqueleto ejecutable, y no más tarde:

- [ ] Desde dentro del devcontainer se listan los contenedores del host.
- [ ] Se crea, arranca, detiene y elimina un contenedor de prueba desde código .NET, con el cliente
      elegido.
- [ ] Se construye una imagen desde un Dockerfile cuyo contexto está en el directorio de datos, con
      la ruta compartida de §3.4.b.
- [ ] El contenedor creado es alcanzable por red desde el devcontainer.

---

## 10. Flujo de trabajo en GitHub

**Unidad de trabajo: una rama y un pull request por etapa.** El pull request *es* el punto de
control definido en el documento de requerimientos funcionales.

Ciclo por etapa:

1. El agente IA crea la rama de la etapa a partir de la rama principal.
2. Desarrolla la etapa completa: código, pruebas y actualización de `changelog.md`.
3. Ejecuta `scripts/build.sh` y `scripts/test.sh` dentro del devcontainer, y verifica que **todos**
   los guiones de demostración anteriores siguen pasando.
4. Realiza los commits y prepara el pull request.
5. Informa al agente humano la URL del pull request, junto con el guion de demostración de la etapa.
6. El agente humano ejecuta el guion, valida y decide: **OK** o correcciones.
7. Con el OK, el agente humano realiza la fusión y el borrado de la rama en el repositorio remoto.
8. El agente humano avisa al agente IA que la tarea está completa.
9. El agente IA actualiza el repositorio local y queda listo para la etapa siguiente.

**Reglas asociadas:**

- No se abre la rama de una etapa antes de que se haya fusionado la anterior. Las etapas van en
  serie.
- El `changelog.md` se actualiza en la rama de la etapa, no después de la fusión.
- Cada etapa cerrada y fusionada recibe una **etiqueta** en el repositorio, para poder volver a
  cualquier demostración anterior.
- Ningún secreto entra al repositorio: ni claves de firma, ni tokens, ni credenciales de registros.
