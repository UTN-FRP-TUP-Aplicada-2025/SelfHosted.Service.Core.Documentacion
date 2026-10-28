# Tool-Prompt — Evaluar maqueta ante un caso

> **Invocación**:
> - `Antes de aprobar la maqueta vamos a analizar, Leer y ejecutar /DEV/SelfHosted.Service.Core.Documentos/PROMPTs/08-Re-Evaluacion/Crear-Analisis-Caso-De-Estudio-Repositorio-Desde-Repositorio.md`
>
> Overview: Evaluar el caso de un repositorio github

---

# Contexto 

Desde alta de servicio (`http://127.0.0.1:8138/Alta-De-Servicio.html`) Dentro de un proyecto elijo el card tengo:
   
```
**Repositorio remoto**
«Tengo el código en un repositorio y quiero que el panel construya»
Origen resultante: Repositorio remoto
```

Como ejemplo práctico y real elegí tomar el siguiente repositorio:
`https://github.com/UTN-FRP-TUP-Aplicada-2025/SAI.Service.Core`
es un servicio web en .net blazor, quiero que funcione con la ip local `192.168.1.120`, es un servicio web que sale por el puerto 8080. Para este servicio le quiero asignar 1Gb de RAM, dos procesadores. 

Tengo entendido, que doy de alta el servicio y no queda desplegado, necesito dar de alta las credenciales, un token en github, para cuando se corra el workflow desde github action lance la llamada para el deploy.




---

# Objetivos

  Evaluar el modelo y la maqueta si es funcional a un caso práctico.

---

# Solicitudes




Volca el resultado del analisis, pasos, definiciones y descripciones en: `/DEV/SelfHosted.Service.Core.Documentos/PROMPTs/08-Re-Evaluacion/OUTPUTs/Caso-De-Estudio-Repositorio-Desde-Repositorio.md`:
- ¿Que pasos debo seguir, que campos debo llenar, ?
- ¿Tendría que construir un workflow? , 
- ¿como se aplicaría el caso dado en la sección de contexto?

---

# Reglas

  - No inventar información.
  - Toda afirmación deberá estar respaldada por evidencias verificables.



---

# Framework

## Profile

Aplicar:

- `/IA/IA.Prompts/PromptFramework/Profiles/Study-Guide-Documentation.md`
