Qué cambié
Snapshot previo, con la convención de sufijo para el README: README-v1.2.md, Necesidades-Negocio-v1.2.md y las tres NB en v1.2 quedaron en _legacy/2026-07-28/.

A v1.3 (cinco documentos):

Archivo	Qué cambió
NB-01-…-v1.3.md	Tres cosas. El segundo punto de §3 cambia de objeto: la distinción ya no es por qué dato toma cada dependencia sino por si en ella se declaró que hay que esperar, y se dice que la marca la pone el administrador sobre una propuesta del sistema. El primer punto de §3 admite que una dependencia puede existir sin valor de por medio. Y entra el renombrado: un punto de dolor nuevo en §4 y un séptimo criterio de éxito
NB-05-…-v1.3.md	§1 reescrita: el orden sale de las dependencias marcadas como de espera, con el argumento de por qué el sistema no puede deducirlo —un servicio puede depender de otro y tolerar arrancar antes porque reintenta, y otro puede necesitar esperar sin que nada en su configuración lo delate—. Se ajustan el primer punto de dolor y el cuarto criterio
NB-06-…-v1.3.md	El tercer punto de §3 daba por sentado que toda dependencia toma un valor. Ahora precisa que las que sólo declaran espera no llevan valor y por lo tanto no quedan alcanzadas por un cambio de configuración
Necesidades-Negocio-v1.3.md	Enlaces, conteo de criterios de 42 a 43, y el relato del recorrido en §5
README.md	§1 con el reparto de versiones; la quinta fila de §6 sigue el cambio de objeto de la pendiente visual
Quedan en v1.1 (cinco NB): NB-02, NB-03, NB-04, NB-07 y NB-08. Revisé las cinco contra los tres puntos y ninguna afirma nada que la tercera pasada invalide.

Los criterios que toqué
Criterio	Antes	Ahora
NB-05, orden de arranque	"respetan el orden topológico de las dependencias que declaran consumo por red"	"respetan el orden topológico de las dependencias marcadas como de espera, sin intervención manual en el momento del arranque". El target sigue en 100 %
NB-01, previsibilidad leyendo el lienzo	"cuya clase —si gobierna o no el orden de arranque— se distingue mirando el lienzo"	"cuya clase —si obliga o no a esperar al servicio del que se depende— se distingue mirando el lienzo". Mismo target
NB-01, integridad ante renombrado	—	Nuevo: "dependencias declaradas que siguen siendo válidas después de renombrar un servicio, sobre el total de las que lo apuntaban: 100 %"
Ninguno quedó marcado [D]: los tres se apoyan en propiedades que el intake ahora declara. El catálogo queda en 43 criterios con las mismas 2 derivaciones de siempre, ambas en NB-01.

Mi decisión sobre el punto 1
Lo puse en NB-01, no en NB-04. Entiendo tu lectura y comparte familia con el dolor de sincronizar a mano, pero apliqué el criterio de cuál promesa quedaría falsa sin ello.

NB-04 promete dos cosas: que el alta deje de ser copiar y adaptar, y que un valor compartido se declare una sola vez. Un renombrado que rompe referencias no toca ninguna de las dos: no interviene en el alta, y los valores compartidos viven a nivel proyecto, así que renombrar un servicio no los afecta.

NB-01 promete que la arquitectura queda declarada y por lo tanto verificable. Si renombrar rompe en silencio lo que apuntaba al servicio, esa declaración nunca fue tal: era una coincidencia de cadenas de caracteres que se sostenía mientras nadie tocara el nombre. Eso ataca el núcleo de NB-01. Además la capacidad involucrada —configurar y renombrar un servicio— ya es de NB-01 por F-03, de modo que no hay que mover ninguna capacidad de dueño.

Lo dejé explícito al pie de §5 de NB-01: el criterio mide que la dependencia esté declarada sobre el servicio y no sobre el nombre con el que hoy se lo llama.

Verificación
Comprobación	Resultado
Anclas	100 de 100 resuelven
Enlaces relativos	Cero rotos
Criterios	43, con 2 derivados; los tres conteos del índice alineados
Capacidades	19 de 19, sin faltantes ni duplicadas
CU	35 únicos, CU-01 a CU-35
Citas a 00-Contexto versionadas fuera de cabecera	Cero, la convención se mantiene
Sintaxis, nombres de claves provistas, vocabulario del mecanismo, stack	Cero apariciones
Diez secciones, filenames, UTF-8, LF, sin emojis ni negritas	Limpio
El catálogo quedó en 1250 líneas.

Un problema en el archivo histórico que tenés que saber
Al aplicar la convención del README me di cuenta de que el README v1.0 no está archivado y no es recuperable. En la segunda ronda copié README.md a _legacy/2026-07-28/ sin sufijo, y esa copia pisó la que había dejado en la primera ronda. Cuando alguien renombró la superviviente a README-v1.1.md, lo que quedó fue el contenido v1.1, no el v1.0.

El alcance real es acotado: como nunca reescribí filas históricas del control de cambios —fue justamente el hallazgo N-01—, README-v1.1.md conserva las cuatro filas de la 1.0, así que el registro de qué pasó está completo. Lo que se perdió es el cuerpo de la v1.0, cuya diferencia con la v1.1 era §1 y una §6 de cuatro filas en lugar de ocho. Todo lo demás está intacto: las ocho NB y el índice tienen su v1.0 y su v1.1 archivadas correctamente.

Es exactamente el modo de falla contra el que me advertiste en la ronda anterior, y lo provoqué yo al no versionar el nombre del README al archivarlo. Con la convención que fijaste ahora no puede repetirse.


---

Tercera pasada cerrada
Artefacto	Estado
Intake	v1.2, APROBADO · 3768 líneas · 34 reglas, 58 casos, 16 especificaciones derivadas · §13 intacta verificada 8 veces
Manifiesto	v1.2
00-Contexto/	5 documentos en v1.3 más el README
01-Necesidades-Negocio/	5 en v1.3, 5 en v1.1 · 43 criterios, 2 derivados
Archivado	45 archivos
Integridad	0 enlaces rotos, 0 citas colgadas, 0 CRLF
AG-01 rechazó mi sugerencia con mejor argumento, y tenía razón. Yo había propuesto que la promesa de renombrado sin roturas fuera a la necesidad del alta de servicios, por parecido con el dolor de sincronizar a mano. Él la puso en la de ver la arquitectura, aplicando un criterio más preciso: cuál promesa quedaría falsa sin eso. Si renombrar rompe en silencio lo que apuntaba al servicio, entonces la arquitectura nunca estuvo declarada —era una coincidencia de cadenas de texto que se sostenía mientras nadie tocara el nombre—. Eso ataca el núcleo de esa necesidad, no el del alta.

Y cerró la contabilidad de la pérdida del README. Confirmó que la causó él al archivar sin versionar el nombre, y precisó el alcance real: como nunca reescribió filas históricas del control de cambios —que fue justamente un hallazgo de auditoría anterior—, el registro de qué cambió está completo. Lo que se perdió es el cuerpo de una versión cuya diferencia con la siguiente eran dos secciones. Las ocho necesidades y el índice tienen sus dos versiones archivadas correctamente.

Lo que queda pendiente tuyo
#	Punto	Estado
2A	Colisión de nombre al instanciar una plantilla	Sin decidir. Bloquea el caso de uso de instanciación en la Fase B de SelfHosted-Web
2B	Colisión de clave de variable compartida	Sin decidir. Idem, y es donde se juega un secreto
4	Matriz de navegadores	Sin decidir. Bloquea la categoría de experiencia de uso
6	Fase de las capacidades nuevas	Se aflojó solo: la arista sin variable permite que el corte del lienzo entregue el orden sin el mecanismo completo. Ya no es bloqueo duro
Los tres primeros están en 09.md con sus opciones y espacio para responder.

Fase B
El auditor emitió la lectura de aptitud que había reservado: el intake está en condiciones de sostenerla. Las doce categorías tienen su insumo transcripto, las 34 reglas llevan momento de validación, respuesta y caso concreto, y el esquema quedó verificado restricción por restricción.

Dejó dos precauciones para los subagentes: que consuman las 16 especificaciones derivadas declarándolas revisables y no como requisito cerrado tuyo, y que declaren como brecha cada pendiente en lugar de resolverla por su cuenta —que es el error que esta cadena tuvo que corregir tres veces—.

Arranca por SelfHosted-Domain, nivel 0 del orden topológico. Decime si querés el plan.