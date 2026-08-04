# Tool-Prompt — Prompt Integrador - documentar el servicio rayway

> **Invocación**:
> - `Lee y ejecuta /DEV/SelfHosted.Service.Core.Documentos/PROMPTs/01-Crear-Analisis/Crear-Analisis-RailWay.md`
> Overview: Documentar el servicio Rayway

---


# Contexto

  Railway (railway.com) es una plataforma de infraestructura tipo PaaS: vos das el código (o un template) y ella se encarga de construir la imagen, correr el contenedor, la red, los dominios y la observabilidad. Es competidora directa de Heroku / Render / Fly.io.
  
  El Canvas es justamente lo que tu prompt quiere modelar: un lienzo visual donde cada service es un nodo dentro del project, y los services del mismo project quedan conectados automáticamente por una red privada (IPv6 con DNS interno). Es decir, el canvas no es solo diagrama decorativo — la topología que ves refleja la red privada real y las referencias entre variables. (`./INPUTs/Captura-01.png`, `./INPUTs/Captura-02.png`).

  ---

# Objetivo

  Documentar las caracteristicas funcionalidades, experiencia de usuario e interfaz de usuario (UIX,UI) del servicio provisto por [railway](https://railway.com/). El documento debe permitir a un lector conocer la nomenclatura y significado del procesado de abstracción que modela una arquitectura basada en contenedores. El enfoque debe estar centrado en el UX/UI mediante el canvas y despliegue de contenedores docker.

---

# Solicitudes

 Documentar la investigación tu investigación en en un documento markdown acorde a los objetivos propuestos en: `/DEV/SelfHosted.Service.Core.Documentos/Analisis/Analisis-Rayway/Analisis-Rayway.md` 

  1- Relevar las carácteristicas funcionales enfocado a la creación de proyectos con contenedores basados en un entorno canvas. Extraer y definir su Modelo de abstracción 

  2- Basado en relavamiento del punto 1 de esta sección evaluar librerias que tengan el mejor acercamiento a lo que ofrece el canvas de  [railway](https://railway.com/) dentro de un entorno .net 10 con con paginas blazor interactive server.

  3. Temas a tratar: Project contiene N Services, un Service = un contenedor, Remove del deployment active, Serverless / App Sleeping, Restart, Despliegue (deployment) , Administración / operación , Service = la configuración (existe siempre, no tiene estado on/off),Deployment = la instancia corriendo (es lo que tiene ciclo de vida)

  3- Crea un glosario de terminos de consulta.
  
---

# Restricciones

  - El documento markdown debe tener estar organizado en secciones jerarquizadas, y tener una tabla de contenidos, idear ejemplos prácticos concretos con explicaciones y analisis, definiciones. graficos mermaid 

 - No inventar información.

 - Toda afirmación deberá estar respaldada por evidencias verificables.

---

# Framework

## Rule Set

  - Aplicar: `/IA/IA.Prompts/PromptFramework/RuleSets/RuleSet-Lean.md`

