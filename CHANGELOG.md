# Changelog

Todos los cambios notables de la documentación de este repositorio se registran en este archivo.

El formato se basa en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/).

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
