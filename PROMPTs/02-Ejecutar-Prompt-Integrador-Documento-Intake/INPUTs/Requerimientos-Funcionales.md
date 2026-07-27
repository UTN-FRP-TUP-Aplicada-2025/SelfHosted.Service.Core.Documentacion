


6. **Etapas de desarrollo relativas a partir de las fases de codificación**

  Los sprint relativos a la codificación deben asegurar puntos de validación al agente humano verificando estructuras, validaciones con entregables tangibles. Las primeras etapas deben consistir en:

  6.1. **Etapa 1**

    6.1.a. Crear el scaffolding de la solución y script bat para las tareas del run/build local.
    
    6.1.b. Las solución debe compilar y correr mediante los script but sitados.
    
    6.1.c. Luego, el orquestador debe solicitar validar visualmente la estructura de la solución por el agente humano.

  6.2. **Etapa 2**

    6.2.a. Crear el front, menú lateral, barra superior. 

    6.2.b. El servicio debe compilar correctamente y debe ser lanzado.
    
    6.2.c. Se orquestador debe solicitar validar visualmente en el navegador el panel de cotrol web para que se 
    cumple con el diseño definido en la maqueta dada en la etapa de espcificación UX-UI.

  6.3. **Etapa 3**

    6.3.a. Integrar sqlite, entidades necesarias para la autentificación y autorización.

    6.3.b. Idem Crear la primera intefaz de usuario que solicita usuario y contraseña para dar de alta el administrador. Luego redirecciona a la página principal

    6.3.c. Idem al punto 6.2.c. de la etapa 2.

  6.4. **Etapa 4**

    6.4.a. Integrar las interfaces para login, y para cambio de contraseña, las acciones desde la barra superior del admin como cerrar sesión, y cambio de contraseña.

    6.4.b. Idem al punto 6.2.c. de la etapa 2.


  6.4. Las Siguientes etapas se deben estructurar según todas los flujos de usuarios previstos:


    UF1["UF-1 · Alta del parque<br/>y puesta en marcha"]
    UF2["UF-2 · Configuración<br/>de políticas"]
    UF3["UF-3 · Monitoreo<br/>en vivo"]
    UF4["UF-4 · Históricos<br/>y gráficas"]
    UF5["UF-5 · Prueba de batería<br/>y salud"]
    UF6["UF-6 · Servicio técnico:<br/>recambio de batería"]
    UF7["UF-7 · Reparación o<br/>sustitución del SAI"]
    UF8["UF-8 · Ventana de<br/>mantenimiento"]
    UF9["UF-9 · Informe de período<br/>y comparación de marcas"]
    UF10["UF-10 · Ingesta<br/>automatizada"]


 En cada etapa implementar todo lo necesario y en cada una se debe verificar en el navegador que las pantallas funcionan. 

7. **Entorno de desarrollo**

  7.1. Entorno de ejecución destino:  el entorno final será docker en linux , pero esto queda fuera del alcance de este proyecto.

  7.2. Entorno de ejecución durante el desarrollo: seguir `/DEV/SAI.Service.Core.Documentacion/PROMPTs/01-Ejecutar-Prompt-Integrador-Documento-Intake/INPUTs/Entorno-Desarrollo.md`

8. **Jerarquí de usuarios**

  - Un solo usuario administrador.
  - El sistema cuando inicia debe pedir el nombre de usuario y contraseña para la administración.
