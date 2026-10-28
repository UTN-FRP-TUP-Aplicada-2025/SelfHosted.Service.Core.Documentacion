

# Tool-Prompt — Definir bien las funcionalidades

> **Invocación**:
> - `Antes de aprobar la maqueta vamos a analizar, Leer y ejecutar /DEV/SelfHosted.Service.Core.Documentos/PROMPTs/08-Re-Evaluacion/01-Incluir-En-Informe-Final.md`
>
> Overview: Definir bien las funcionalidades

---

# Contexto 

Leer  `/DEV/SelfHosted.Service.Core.Documentos/PROMPTs/08-Re-Evaluacion/OUTPUTs/Caso-De-Estudio-Alta-Servicio-Despliegue-desde-Repositorio.md`, en este, la idea es plantear el caso B, porque construir del lado del servidor es super complejo, en cambio constuir del lado de githbu es simple, se genera la imagen docker y lo único que se necesita es disparar el despliegue desde  el workflow de githubaction. 

---

# Objetivos

  Construir el informe a aplicar luego con la definiciones correcta de las funcionalidades para ajustar las definiciones actualmente establecidas en el SDD 

---

# Solicitudes


Me surgen dudas:
  1. Suponiento que el pipeline construyó la imagen, la tuvo que enviar a duckerhub y luego hacer un post hacia nuestro servicio para disparar el despliegue, luego
  2. en nuestro selfHosted service en servicio creado dispara el despliegue. Lo que puedo llegar a necesitar son las credenciales de duckerhub (desde el lado del workflow y desde el selfHosted service , si es que la imagen es privada), y del lado de github action tambien las credenciales de selfHosted service 

  ¿Qué pasa con las variables de entorno que llegue a necesitar ese despliegue?, son configurables desde el lado del servicio o por api se prefijan desde el pipeline? y junto con los secretos esas variables son objetos o entidades de dominio.
  ¿Es posible que se genere la imagen desde el workflow de github y nuestro servicio se lo descargue como un   fichero release?, desconozco el tema de imagenes en docker, o talvez la única forma es usar dockerhub. - no lo sé.
  - otro punto, eliminaria la necesidad de usar 

```
Imagen de registro privado
«La imagen está en mi registro y necesita credencial»
Origen resultante: Imagen de registro privado
```
  porque es similar a 

```
Imagen de registro público
«Sé qué imagen quiero y está publicada»
Origen resultante: Imagen de registro público
```

y se puede unificar.

Elejido el caso B como definitivo, te recomiendo que vayas apuntando en `/DEV/SelfHosted.Service.Core.Documentos/PROMPTs/08-Re-Evaluacion/OUTPUTs/Casos-Estudios-Re-Definidos.md` el caso definitivo para luego reintroducirlo nueva mente en el flujo del prompt orquestador y así redifini lo que tenga que redefinir. Construir este informe de tal forma que sea pueda seguir añadiendo otros casos de alta de servicio y se pueda colocar como entrada al estado actual de la maqueta. Debe haber ejemplos, yml ejemplos de configuración, definiciones y explciaciones


---

# Reglas

  - No inventar información.
  - Toda afirmación deberá estar respaldada por evidencias verificables.


---

# Framework

## Profile

Aplicar:

- `/IA/IA.Prompts/PromptFramework/Profiles/Study-Guide-Documentation.md`
