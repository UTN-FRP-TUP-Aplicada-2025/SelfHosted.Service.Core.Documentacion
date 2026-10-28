# Cierre de la Fase A

Lo generado
Ubicación	Archivos	Líneas
00-Contexto/	6	1104
01-Necesidades-Negocio/	2 + 8 NB	1187
Audit/	4 informes	1385
Intake/	intake v1.1 + manifiesto v1.1, con las 1.0 en _legacy/2026-07-27/	—
Veredictos: 00-Contexto aprobado con observaciones, en condiciones de promover; 01-Necesidades-Negocio aprobado. Cero referencias colgadas, cero emojis, cero CRLF.

# Lo que necesito que confirmes
Seis criterios de éxito derivados. AG-01 los marcó [D] porque la regla exige cuatro criterios SMART por necesidad y el intake aporta cuatro métricas de negocio. Estos seis tienen su número derivado, no declarado:

|NB | Criterio | Target | Base|
|----|-----------|---------|---|
|NB-01 | Autosuficiencia de la consulta de dependencias	| 0 archivos externos que abrir  | El dolor de §1 es abrir archivos dispersos; el target expresa su eliminación |
|----|-----------|---------|---|
| NB-01	| Cobertura de la vista única	| 5 de 5 conjuntos de servicios	| Ninguna métrica declarada fija el target ni el plazo |
|----|-----------|---------|---|
| NB-01	| Estabilidad de la sesión de trabajo	| ≤ 10 % de crecimiento de memoria en 15 min |PT-01 exige "estable, sin crecimiento sostenido"; el porcentaje traduce ese umbral cualitativo|
|----|-----------|---------|---|
NB-04|	Altas resueltas desde el catálogo|	≥ 70 % de las altas frecuentes|	No hay medición previa de qué proporción de altas son frecuentes |
|----|-----------|---------|---|
NB-04|Tiempo de alta de un servicio frecuente|	≤ 5 min hasta el contenedor corriendo|	No hay medición del método manual con la que contrastar|
|----|-----------|---------|---|
|NB-05	|Direcciones fijas fuera del sistema|	0 sobre las 5 de la red local| 	El denominador es verificado; el target 0 deriva del dolor declarado
Confirmalos, corregí los que quieras, o dejalos marcados como derivados y que se resuelvan cuando 08-Calidad-Y-Pruebas los tenga que verificar.|
manual con la que contrastar|
|----|-----------|---------|---|

Lo que queda abierto
Cuatro pendientes, registrados en el intake v1.1 con su consumidor. Los tres primeros se vuelven bloqueantes en la Fase B:

|Pendiente	| Dónde pega |
|------|-----|
| CL-04: caída del circuito con un despliegue  en curso |	Casos de uso alternativos de 02 en SelfHosted-Web, probablemente una ADR
|------|-----|
| CL-15: secreto en variable que la heurística no identifica |	Regla de negocio de la adopción en 02, y su caso de prueba en 08 |
|------|-----|
| IC-05: verificar que un contenedor no esté ya adoptado por otro proyecto |	Fundamento de la invariante I10 en 02 de SelfHosted-Domain |
|------|-----|
|Matriz de navegadores |	03-UX-UI-DX, y la verificación en 08 y 09 |

Tres ambigüedades de AG-00 siguen vivas y no bloquean: la composición de los alcances 2 y 3, la matriz de navegadores y qué evento marca la promoción a la primera versión mayor.

# Lo que aprendí de esta fase, aplicado a las siguientes
El P0 y su reincidencia N-01 tuvieron la misma causa: afirmar en un documento algo que vos decidiste en la conversación pero que no estaba registrado en ningún artefacto. Voy a consolidar en el intake toda decisión tuya antes de que un subagente la consuma, en lugar de descubrirlo por auditoría. Y queda declarada la invariante de versionado que ratificó el auditor: sube de versión lo que ya fue consumido por otro artefacto; lo demás absorbe sus correcciones dentro de su emisión, porque el audit es parte del ciclo de emisión y no una revisión posterior.

# Fase B
Arranca por SelfHosted-Domain, nivel 0 del orden topológico, con library como variante. Genera 02-Especificacion-Funcional y 03-UX-UI-DX en su variante DX; 04 se omite en toda la solución porque usa_llm es false. IC-05 aparece acá.

Los cuatro proyectos van estrictamente en serie —ningún nivel topológico tiene más de uno—, así que la Fase B se ejecuta cuatro veces, y SelfHosted-Web suma además la Fase B2 de validación visual de maqueta.

Decime si confirmás los seis criterios y si querés resolver CL-04, CL-15 e IC-05 ahora o dejar que los subagentes los levanten como ambigüedad cuando los necesiten. Con eso presento el plan de la Fase B para SelfHosted-Domain.

---
> respuesta

sobre el NB-01 , 
-  `Autosuficiencia de la consulta de dependencias`: el instake debio incluir los contenidos de esos archivos, eso hay que resolver, el instake no debe depender de otros archivos.1
- `Cobertura de la vista única`, no hay plazo fijado, no es relevante el plazo.
- ` Estabilidad de la sesión de trabajo` esto no se que es, habria que ampliar y ver que es
sobre el NB-04:
- `Altas resueltas desde el catálogo` , si te referis a la cantidad de servicios catalogados , ninguno cuando inicia el servicio de selfhosted, luego el usuario irá agrengando o no. hay que tener en cuenta que ese catalogo no van a ser servicios corriendo sino plantillas listas para instanciar en un proyecto
- `Tiempo de alta de un servicio frecuente` no se a que te referis con esto, 
Sobre NB-05:
- `Direcciones fijas fuera del sistema`,  el requerimiento decia que el servicio selhosted se configuraba un rango de ip validas, o tenia su rango de ip validas asignadas y sobre eso se manejaba tanto para la alta de servicios como para iniciar un proyecto. 

esto es interesante:
-`CL-04: caída del circuito con un despliegue  en curso` porque  si despliego un proyecto , inicia, calculo que te referis que pasa con los contenedores. creo que el servicio debe verficiar e individualizar el estados de los contenedores, los que tuvo exito en desplegarlos, lo marca y los que no marca el error, y si abro el proyecto deberia verificar el estado de los contenedores. asi el despliegue quede fragmentado , al reabrir el proyecto este deberia mostrar que contenedores estan activos y bien desplegados, 
- `CL-15: secreto en variable que la heurística no identifica` esto es super importante resolverlo, como se van a manejar las variables del contenedor o servicio , porque no investigas acá `/DEV/SelfHosted.Service.Core.Documentos/Analisis/Analisis-SaaS-Service/Analisis-Rayway.md` y extraes un mecanismo prolijo para manejo de varialbes de los contenedores dentro del proyecto
- `IC-05: verificar que un contenedor no esté ya adoptado por otro proyecto`, que un servicio este en dos proyectos por ahora no deberia ser posible, no hay forma!, verificalo, porque?, porque cuando creo el proyecto, los servicios los de alta desde cero, lo que puede pasar es que este ocupando la misma IP, y eso se debe verificar al menos durante la fase de despliegue o ejecución de ese contenedor y ahi bloquea y baja de ejecución a todo el proyecto. el proyecto si hace correr completo no debe empezar y alguns de sus ip estan ocupadas o si algun contenedor tiene problemas o mal el despliegue.

fijate si estas respuesta te sirven y me decis