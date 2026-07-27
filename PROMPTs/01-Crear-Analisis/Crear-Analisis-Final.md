
# Tool-Prompt — Prompt Integrador de documentación final

> **Invocación**:
> - `Lee y ejecuta /DEV/SelfHosted.Service.Core.Documentos/PROMPTs/01-Crear-Analisis/Crear-Analisis-Final.md`
> Overview: Crear un unico documento final

---


# Contexto

  Lee `/DEV/SelfHosted.Service.Core.Documentos/Analisis/Analisis-Rayway/Definicion-Idea.md` es un documento que describe el plan de diseño de un servicio un `Servicio Selfhosted a diseñar`.

  ---

# Objetivo

 Cerrar o definir algunas cuestiones especificas.

---

# Solicitudes

   1. Analizar :
 
 ```
 La arquitectura de un proyecto debe ser modelada utilizando un lienzo o tablero canvas interactivo para la edición de la arquitectura de cada proyecto y su servicio. Utilizar `https://github.com/Blazor-Diagrams/Blazor.Diagrams` 
 Las Rest APIs deben ser con autentificación ROPC, jwt bearer token. e implementación mediante Controladores
 ```

  2. Investiga y propone alternativas en otras librerias con mejores caracteristicas y que se adapten dentro de la funcionalidad que propone toda la aplicación. Tambien se valora que sea lo mejor compatible con blazor y paginas interactive server de .net y que sea de licencia open source MIT.

  3. Hace una evaluación general de acuerdo a todo el cotnexto proporcionado.
  
  Gnerá un único documento con toda la información evaluada, incluyendo ejemplos, modelos de datos con json todo extraido y embebido en un único documento sin citar referencias. `/DEV/SelfHosted.Service.Core.Documentos/PROMPTs/02-Ejecutar-Prompt-Integrador-Documento-Intake/INPUTs/Analisis-Final-Integrado.md`.   Te recomiento luego ofuscar información sobre el servidor mencionado en el que se piensa montar el servicio diseñado SaaS en cuanto a la información  que comprometa la seguridad del server, pero no sacrifiques los ejemplos y valores, modelos de datos, etc porque se van a usar para maquetar la solución .

  
---

# Restricciones

  - El documento markdown debe tener estar organizado en secciones jerarquizadas, y tener una tabla de contenidos, idear ejemplos prácticos concretos con explicaciones y analisis oridentados al maquetado de la solución tanto web como de datos, definiciones y glosario detallado, graficos mermaid.

 - No inventar información.

 - Toda afirmación deberá estar respaldada por evidencias verificables.

---

# Framework

## Rule Set

  - `/IA.Prompting.Templates/PromptFramework/RuleSets/RuleSet-Lean.md`

