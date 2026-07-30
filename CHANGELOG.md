# Changelog

Todos los cambios notables de la documentación de este repositorio se registran en este archivo.

El formato se basa en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/).

## [Sin publicar] - 2026-07-29

### Añadido

- `Avances/`: informes de cierre de etapa de `SelfHosted.Service.Core`, escritos para quien no vio escribir el código y se sienta a probarlo.
  - `README.md`: la regla (el informe se publica antes de convocar el punto de control y no se reescribe, sólo se actualiza su estado) y el índice de informes por orden, etapa, tipo, fecha de cierre y estado.
  - `_PLANTILLA-Informe-de-Etapa.md`: plantilla obligatoria de trece secciones — identificación, qué se entregó, qué quedó fuera, cómo levantarlo, claves y credenciales, qué probar paso a paso, casos de ejemplo, qué debería ver, cómo está armado el proyecto, criterios de aceptación, no-regresión, problemas conocidos y qué habilita.
- `PROMPTs/02-Ejecutar-Prompt-Integrador-Documento-Intake/INPUTs/Analisis-Final-Integrado.md`: contenido del análisis final integrado, que hasta ahora era un archivo vacío. Documento único y autocontenido de catorce secciones más tres anexos: modelo de dominio y modelos de datos, la decisión del canvas de arquitectura con Blazor.Diagrams y la investigación de alternativas, la decisión de autenticación de la REST API, el maquetado de la solución web, ejemplos de extremo a extremo, reglas de negocio, arquitectura técnica, glosario, evidencias, limitaciones y la política de ofuscación aplicada al entorno destino de referencia.
- `PROMPTs/03-Ejecutar-Prompt-Orquestador/Historial/`: traza de la ejecución del orquestador SDD sobre el repositorio de código, en doce archivos. Cubre la validación del documento intake, el plan y los sucesivos cierres de la Fase A (intake v1.0 → v1.2, documentos de `00-Contexto` y `01-Necesidades-Negocio` hasta v1.3), las respuestas del agente humano y las decisiones acordadas.
  - `09.md`: revisión de los puntos abiertos antes de la Fase B, con cinco de seis resueltos y la matriz de navegadores como única pendiente.
  - `11-blazor-interactive-server.md`: por qué Blazor Interactive Server obliga a declarar una matriz de navegadores, pendiente que bloquea `03-UX-UI-DX` de `SelfHosted-Web`.
- `PROMPTs/04-Reformular-Solucion-Debido-Framework-Readecuado/Crear-Reajuste-SDD-Refactorizacion.md`: prompt de migración a la nueva versión del Framework SDD sin descartar los avances, trasladando las especificaciones ya generadas al documento intake antes de reejecutar el prompt orquestador.
- `PROMPTs/05-Fix-SDD-Redefinir-Servicios/Ejecutar-Fix-SDD-Definiciones-Servicios.md`: prompt de fix sobre el producto SDD para completar las definiciones de servicios a partir de `SDD/Estado/Redefinicion-Servicio.md` del repositorio destino.

### Cambiado

- `PROMPTs/02-.../INPUTs/Requerimientos-Funcionales.md`: reescrito por completo. Antes eran notas heredadas del proyecto SAI (flujos `UF-1` a `UF-10` sobre SAIs y baterías, y `SAI.Service.Core` como entorno de desarrollo). Ahora define el principio rector de las etapas de desarrollo, los dos tipos de hito (`HI`, valida el agente humano; `HD`, se demuestra al cliente), la plantilla obligatoria de etapa, las reglas transversales, el informe de cierre de etapa (§2.5, que gobierna `Avances/`), las tres etapas iniciales —esqueleto ejecutable, cáscara del panel de control y administrador con sesión, primera demostración al cliente— y los cortes propuestos para los alcances siguientes.
- `PROMPTs/02-.../INPUTs/Requerimientos-Tecnicos.md`: reescrito por completo, también partiendo de notas del proyecto SAI. Ahora fija plataforma y librerías, el entorno de desarrollo en devcontainer (restricción de partida, reglas, composición, acceso al motor de contenedores y sus consecuencias, scripts del repositorio), las decisiones técnicas cerradas, autenticación/autorización y manejo de secretos, persistencia, pruebas, despliegue en producción, las puertas técnicas `PT-01` (fluidez del lienzo bajo Interactive Server) y `PT-02` (motor de contenedores accesible desde el devcontainer), y el flujo de trabajo en GitHub.
- `PROMPTs/02-.../Ejecutar-Prompt-Integrador-Documento-Intake.md`: el contexto pasa a apuntar a `/IA/IA.SDD/README.md` como entrada al Framework SDD y declara los tres documentos de `INPUTs/` como insumos explícitos, en lugar de la guía de inicio y la definición de idea. Se agrega la solicitud de verificar que el documento intake construido respeta la plantilla.
- `PROMPTs/01-Crear-Analisis/Crear-Analisis-Final.md` → `Crear-Analisis-Final-Integrado.md`: renombrado; sólo cambia la ruta de invocación.
- `PROMPTs/01-Crear-Analisis/Crear-Analisis-RailWay/README.md`: marcador de carpeta, todavía sin contenido.

## [Sin publicar] - 2026-07-26

### Añadido

- `CHANGELOG.md`: registro de cambios del repositorio de documentación.
- `Analisis/`: carpeta de análisis previos al diseño del servicio.
  - `Analisis-SaaS-Service/Definicion-Idea.md`: plan del proyecto «Servicio de Administración Self Hosting» — objetivos, alcance por incrementos (proyectos/servicios sobre canvas, despliegue automático, escalado manual, gestión de IPs de LAN con detección de conflictos; luego dashboard; luego exportación a Docker Compose) y módulo de descubrimiento y adopción de contenedores ya existentes en el demonio Docker.
  - `Analisis-SaaS-Service/Analisis-Rayway.md`: análisis funcional y de UX/UI de Railway (`draft`) centrado en el modelo de abstracción (Project / Service / Deployment y sus invariantes), el canvas de proyecto, el patrón de *Staged Changes*, la administración de deployments, y la evaluación de librerías de canvas equivalentes para .NET 10 + Blazor Interactive Server.
- `PROMPTs/`: prompts numerados por etapa del proceso de documentación.
  - `01-Crear-Analisis/Crear-Analisis-RailWay.md`: prompt integrador para documentar Railway, con `INPUTs/Captura-01.png` y `INPUTs/Captura-02.png` como evidencia visual del canvas.
  - `01-Crear-Analisis/Crear-Analisis-Final.md`: prompt integrador para cerrar las cuestiones abiertas en un único documento final.
  - `02-Ejecutar-Prompt-Integrador-Documento-Intake/`: prompt que integra los documentos de entrada en el Documento Intake del Framework SDD sobre el repositorio destino `/DEV/SelfHosted.Service.Core`, con `INPUTs/` (`Analisis-Final-Integrado.md`, `Requerimientos-Funcionales.md`, `Requerimientos-Tecnicos.md`).
  - `03-Ejecutar-Prompt-Orquestador/Ejecutar-Prompt-Orquestador.md`: invocación del agente Bootstrap SDD sobre el repositorio de código.
