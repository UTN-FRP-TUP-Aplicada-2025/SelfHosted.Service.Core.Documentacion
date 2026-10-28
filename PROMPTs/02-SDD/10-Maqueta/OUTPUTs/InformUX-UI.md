# Informe UX-UI · El lenguaje de la interfaz y los ejes que no se ven

**Documento:** `InformUX-UI.md`
**Versión:** 1.0
**Estado:** Emitido — insumo de dos destinatarios
**Fecha:** 2026-08-04
**Autor:** Orquestador SDD
**Origen:** Observación del agente humano del proyecto sobre `SUP-17` (alta de servicio) durante el paso 5 de la Fase B2, el 2026-08-04, mirando `http://127.0.0.1:8137/Alta-De-Servicio.html`

**Dos lectores, y conviene no mezclarlos.**

1. **El producto SelfHosted Service.** Qué hay que corregir en `03-UX-UI-DX` y qué tiene que decidir el agente humano del proyecto. Las decisiones concretas viven en `Validacion-Maqueta.md`, punto K; acá está el análisis que las funda.
2. **El Framework SDD.** Qué de esto **no es un error de este producto sino un hueco de las reglas que generan la maqueta y la especificación de experiencia**, y qué cambio concreto se propone en cada archivo. Es la parte pensada para realimentar el framework en una corrida posterior, y para servir de ejemplo de análisis.

La distinción importa porque casi todo lo que sigue **pasó el audit de la Fase B sin un solo hallazgo**. Si algo que salta a la vista en cuanto un humano mira la pantalla no lo levanta ninguna regla, el problema no está en quien escribió la pantalla.

---

## Tabla de contenido

- [1. El caso, en una página](#1-el-caso-en-una-página)
- [2. Hallazgo 1 · El vocabulario del modelo se filtra a la interfaz](#2-hallazgo-1--el-vocabulario-del-modelo-se-filtra-a-la-interfaz)
- [3. Hallazgo 2 · La especificación declara tres ejes y la interfaz muestra uno](#3-hallazgo-2--la-especificación-declara-tres-ejes-y-la-interfaz-muestra-uno)
- [4. Hallazgo 3 · Una capacidad se mudó de superficie y no dejó puntero](#4-hallazgo-3--una-capacidad-se-mudó-de-superficie-y-no-dejó-puntero)
- [5. Hallazgo 4 · El instrumento de la maqueta compite con el texto del producto](#5-hallazgo-4--el-instrumento-de-la-maqueta-compite-con-el-texto-del-producto)
- [6. Hallazgo 5 · La fase no pide la lectura que produjo todo esto](#6-hallazgo-5--la-fase-no-pide-la-lectura-que-produjo-todo-esto)
- [7. Propuesta para el producto](#7-propuesta-para-el-producto)
- [8. Propuesta para el Framework SDD](#8-propuesta-para-el-framework-sdd)
- [9. Cómo usar este documento en el prompt posterior](#9-cómo-usar-este-documento-en-el-prompt-posterior)
- [Control de cambios](#control-de-cambios)

---

## 1. El caso, en una página

El agente humano del proyecto abrió el alta de servicio en el estado «formulario abierto, sin nombre» y encontró un apartado **Origen · De dónde sale la imagen** con tres opciones:

| Rótulo | Texto de ayuda |
|---|---|
| Imagen de un registro | La imagen ya está publicada en un registro |
| Imagen del almacén local | La imagen ya está en este servidor |
| Definición local | Quiero tomar una imagen publicada y ajustarla con un archivo de construcción declarado acá |

Sus tres observaciones, textuales en lo sustantivo:

- **«decís *Imagen de un registro* y me pregunto registro de qué».**
- **«si elijo una opción que me permita preparar el servicio para desplegar desde GitHub debería reflejarme esa opción, e incluso darme parte de las reglas a modo de ejemplo para pegar con el identificador del servicio».**
- **«si tomo una imagen del catálogo, debería reflejármelo. O el caso de que tome la imagen desde Docker Hub».**

Las tres son correctas y ninguna es la misma cosa. La primera es de redacción. La segunda es de arquitectura de información: busca un escenario donde la interfaz expone un eje del modelo. La tercera choca con una decisión que él mismo tomó cinco días antes y que la interfaz no le recuerda.

**Dato que enmarca todo lo demás.** Los tres rótulos **no los inventó la maqueta**: están transcriptos del wireframe `Wireframes-Alta-De-Servicio.md`, que los declara en su tabla de valores del campo de origen. Son especificación aprobada, emitida por `03-UX-UI-DX` y auditada.

---

## 2. Hallazgo 1 · El vocabulario del modelo se filtra a la interfaz

**Qué pasa.** «Registro», «almacén local» y «definición local» son nombres del modelo de datos, no de lo que la persona controla. El usuario ve una etiqueta que nombra la categoría interna y tiene que inferir el caso de uso. «Docker Hub» no aparece en ninguna parte, y Docker Hub **es** el caso central de «un registro».

Peor es «Definición local», que no dice qué hace: no nombra ni la acción —construir— ni el resultado —una imagen propia—. Un usuario que quiere construir su imagen no tiene forma de saber que ésa es su opción.

**Lo que hace que esto sea un hallazgo del framework y no del producto.** El catálogo de diseño **ya tiene la regla**. `Design-Rules-Web-Generico.md` §8, último renglón del párrafo de reglas de redacción:

> «Nombrar las cosas por lo que la persona controla, no por cómo está construido el sistema.»

Es exactamente el criterio que los tres rótulos violan. Existía, estaba cargada en el despacho, y no se aplicó. La causa es verificable: **esa regla no está en ninguna de las dos listas de criterios de aceptación**.

- `Design-Rules-Web-Generico.md` §9 enumera seis condiciones para que una superficie cumpla el catálogo. Sobre textos dice: «sus textos están en voz activa y **nombran acciones de forma consistente extremo a extremo**». Consistencia, no comprensibilidad. Tres rótulos consistentemente escritos en jerga cumplen ese criterio.
- `Rules-UX-UI-DX.md` §6 tiene veinte criterios. Ninguno menciona redacción, microcopy ni vocabulario del usuario. Los que hablan de vocabulario —cuatro— son de **gobierno del glosario**: que los términos estén declarados, que no se dupliquen con otra semántica, que las polisemias estén gobernadas. Todos verifican coherencia interna del corpus. **Ninguno verifica que el lector entienda.**

De ahí que el audit de la Fase B haya aprobado la superficie sin observaciones: hizo lo que las reglas le pedían.

**El patrón, enunciado en general.** Una regla que vive como prosa en un documento normativo pero no aparece en la lista de criterios de aceptación **no rige**. El audit evalúa contra §6; lo que no está en §6 no se mira. Es un modo de falla del framework entero, no de esta categoría.

---

## 3. Hallazgo 2 · La especificación declara tres ejes y la interfaz muestra uno

**Qué pasa.** El intake declara tres ejes **independientes** del alta de un servicio:

| Eje | Qué es |
|---|---|
| Vía de alta | Cómo llega el usuario a crear el servicio |
| **Origen** | De dónde sale la imagen |
| **Disparador** | Quién inicia el despliegue: el usuario, el proyecto, o un automatismo externo |

El alta de servicio expone el **segundo**. El agente humano vino con un escenario —«desplegar desde GitHub»— que vive en el **tercero**, lo buscó en el único que la pantalla ofrece, y no lo encontró.

**Por qué es un hallazgo fuerte y no una confusión aislada.** La propia decisión que creó el tercer eje, del 2026-07-31, dice textualmente que **confundir origen con disparador** era lo que hacía que el menú anterior se leyera mal. Es decir: el modelo identificó la confusión, la resolvió separando los ejes, y **la interfaz no heredó ninguna defensa contra ella**. Separar los ejes en el modelo arregla el modelo. La persona sigue llegando con un escenario que los cruza.

Y quien se confundió no es un usuario novel: es el dueño del dominio, que tomó la decisión de separar los ejes.

**Qué falta, concretamente.** Que la superficie que materializa un eje **declare la existencia de los otros y hacia dónde van**. No se trata de meter el disparador dentro del origen —eso sería volver al error—, sino de que la pantalla diga «esto no cambia quién dispara el despliegue; eso se configura acá».

**Lo que el producto además ya tenía anotado.** Que la pantalla entregue el fragmento listo para pegar en el automatismo, con el identificador del servicio, es la pendiente `Q-10` del intake: «si el panel genera el fragmento de configuración del automatismo de integración continua», con la nota «es lo que vuelve usable la capacidad para quien no conoce esa herramienta». Estaba declarada, abierta, y con el agente humano como destinatario. Su observación la cierra en positivo si la confirma.

**El patrón, enunciado en general.** Cuando la especificación declara **N ejes independientes** y una superficie expone uno, el riesgo de que el usuario busque los otros ahí es estructural, no accidental. Ninguna regla del framework obliga hoy a tratarlo.

---

## 4. Hallazgo 3 · Una capacidad se mudó de superficie y no dejó puntero

**Qué pasa.** El 2026-07-31 se decidió que **adoptar un contenedor** y **usar el catálogo de plantillas** salen del alta, porque no son formularios sino listados donde se elige, y ya tienen superficie propia. La decisión es razonable y está fundada.

Lo que no ocurrió es lo otro: **el alta no dice a dónde se fueron**. Un usuario que aprendió que ahí se daban de alta servicios de siete maneras, hoy encuentra tres y ninguna explicación. El agente humano preguntó exactamente eso.

Es asimétrico, además: el catálogo de plantillas **sí** declara en su estado vacío las otras formas de tener un servicio. El puntero existe en una dirección y no en la otra.

**El patrón, enunciado en general.** Una decisión que **retira** una capacidad de una superficie deja un hueco que la superficie no declara. La documentación registra el movimiento en el control de cambios, que el usuario final no lee. Nada obliga a que la superficie de origen conserve el puntero.

---

## 5. Hallazgo 4 · El instrumento de la maqueta compite con el texto del producto

**Qué pasa.** Debajo de las tres opciones, la maqueta imprime notas que explican decisiones de especificación: que la variante se deriva y no se elige, que el servicio sin origen no es un cuarto valor, que el campo todavía no tiene valor. Son **anotaciones para el validador**, no microcopy del producto. El agente humano las leyó como parte de la pantalla.

**Por qué es un hueco de la regla y no un descuido del maquetador.** `Maqueta-Rules.md` §4.3 obliga a rotular la **barra de validación** como instrumento —«Barra de validación de maqueta — no forma parte del producto»— y prohíbe trasladarla al producto. Pero la maqueta emite además otras dos clases de texto de instrumento que la regla no nombra:

- **Propuestas a validar**, cuando la especificación no cubre algo y la maqueta dibuja una opción para que el humano la apruebe.
- **Brechas abiertas declaradas**, cuando la maqueta deliberadamente no dibuja algo y explica por qué.

Las dos son buenas prácticas y las dos aparecieron en esta maqueta. Ninguna tiene regla de rotulado, ubicación ni tratamiento visual, así que quedan a criterio de cada corrida y compiten visualmente con el producto.

---

## 6. Hallazgo 5 · La fase no pide la lectura que produjo todo esto

**Qué pasa.** Los siete pasos de la Fase B2 y los criterios de aceptación de `Maqueta-Rules.md` §8 hacen que el humano valide **composición, estados, datos y navegación**. La lectura que produjo los cuatro hallazgos anteriores fue otra: leer la pantalla como la leería alguien que no conoce el sistema.

Esa lectura no está pedida en ninguna parte. Ocurrió porque el agente humano la hizo por su cuenta.

**Por qué conviene pedirla explícitamente.** Es la única verificación del cuerpo documental que **no puede hacer el agente que escribió la especificación**, por la misma razón por la que el ensayo de entrega de la Fase J es un gate humano: quien acaba de escribir la pantalla conoce el sistema y no puede simular no conocerlo. Es contaminación, no falta de rigor.

---

## 7. Propuesta para el producto

Las decisiones concretas están en `Validacion-Maqueta.md` punto K, con su línea para completar. Acá va el contenido de la propuesta.

**7.1 Rótulos del campo de origen.**

| Hoy | Propuesta |
|---|---|
| **Imagen de un registro** · «La imagen ya está publicada en un registro» | **Descargar una imagen ya publicada** · «Está en Docker Hub, en GitHub Container Registry o en un registro privado tuyo. El panel la descarga.» |
| **Imagen del almacén local** · «La imagen ya está en este servidor» | **Usar una imagen que ya está en este servidor** · «Alguien la descargó o la construyó antes. No se vuelve a descargar.» |
| **Definición local** · «Quiero tomar una imagen publicada y ajustarla con un archivo de construcción declarado acá» | **Construir la imagen acá** · «Partís de una imagen publicada y le agregás lo tuyo con un archivo de construcción que escribís en el panel.» |

El criterio aplicado es el del catálogo: nombrar por lo que la persona controla, y nombrar los ejemplos que la persona reconoce. «Docker Hub» tiene que aparecer, porque es el caso central y es la palabra que el usuario tiene en la cabeza.

**7.2 Bloque del disparador.** Que el alta declare el tercer eje sin absorberlo: un bloque informativo que diga que de dónde sale la imagen no determina quién dispara el despliegue, con el puntero a dónde se configura el disparo externo. Es la defensa contra la confusión que el propio modelo identificó.

**7.3 Fragmento para el automatismo.** Confirmar `Q-10` en positivo: que la superficie entregue el fragmento de configuración con el identificador del servicio ya puesto, listo para pegar. Es lo que la propia pendiente declara como «lo que vuelve usable la capacidad para quien no conoce esa herramienta».

**7.4 Puntero a las otras dos formas.** Que el alta declare que adoptar un contenedor existente y usar una plantilla del catálogo son las otras dos formas de tener un servicio, y dónde viven. Alternativa, si se prefiere: revertir la decisión y devolver el catálogo al campo de origen. Es decisión del agente humano.

---

## 8. Propuesta para el Framework SDD

Cinco cambios, uno por hallazgo. Cada uno declara el archivo, qué se agrega y por qué el hueco existe. Ninguno inventa una regla nueva de diseño: cuatro de los cinco hacen **verificable** algo que el framework ya dice o ya practica.

**8.1 Promover la regla de nomenclatura a criterio de aceptación.**

- *Archivo:* `References/Design/Design-Rules-Web-Generico.md` §9, y `Rules/Rules-UX-UI-DX.md` §6.
- *Qué se agrega:* un criterio verificable, por ejemplo: «Ningún rótulo, opción o texto de ayuda de una superficie nombra una entidad, tabla, variante o campo del modelo por su nombre interno. Cada opción nombra la acción o el resultado que la persona controla, y cuando el dominio tiene ejemplos reconocibles, los nombra.»
- *Por qué:* la regla ya existe en §8 del catálogo como prosa y no se aplicó, porque el audit evalúa contra las listas de criterios y esa regla no está en ninguna. El criterio de §9 sobre textos verifica **consistencia**, que la jerga cumple trivialmente.
- *Alcance más amplio, que conviene mirar aparte:* **toda regla del framework que viva como prosa y no aparezca en la lista de criterios de aceptación de su archivo, no rige.** Vale la pena un barrido de los diecisiete archivos de reglas con esa pregunta.

**8.2 Obligar a que la superficie declare los ejes que no expone.**

- *Archivo:* `Rules/Rules-UX-UI-DX.md`, sección de estructura del wireframe, y su §6.
- *Qué se agrega:* cuando la especificación funcional declara ejes independientes que se cruzan en la cabeza del usuario, la superficie que expone uno declara la existencia de los otros y a dónde van. Criterio verificable: «Si el modelo declara N ejes independientes y la superficie expone menos de N, el wireframe declara cómo se defiende de la confusión entre ellos, o declara por qué no hace falta.»
- *Por qué:* separar los ejes en el modelo no protege al usuario, que llega con un escenario que los cruza. En este caso la propia decisión de producto había identificado la confusión como causa del defecto anterior, y la interfaz no heredó defensa alguna.

**8.3 Obligar al puntero cuando una capacidad se muda.**

- *Archivo:* `Rules/Rules-UX-UI-DX.md`, y la matriz de propagación de `Rules/Maqueta-Rules.md` §3.6.
- *Qué se agrega:* toda decisión que retire una capacidad de una superficie y la lleve a otra obliga a declarar el puntero **en la superficie de origen**, o a declarar explícitamente por qué no hace falta.
- *Por qué:* la mudanza queda registrada en el control de cambios, que el usuario final no lee. Hoy el puntero aparece por criterio del subagente, y en este producto apareció en una dirección y no en la otra.

**8.4 Rotular las tres clases de texto de instrumento.**

- *Archivo:* `Rules/Maqueta-Rules.md` §4.3, que hoy sólo cubre la barra de validación.
- *Qué se agrega:* declarar las tres clases —barra de validación, **propuesta a validar** y **brecha abierta declarada**—, con su rotulado obligatorio, su tratamiento visual diferenciado del producto y la prohibición de trasladarlas. Las dos últimas son prácticas que las corridas ya inventaron y que la regla no nombra.
- *Por qué:* si el texto del instrumento no se distingue del texto del producto, el validador evalúa como producto lo que es andamio. Ocurrió en esta corrida, con el dueño del dominio.

**8.5 Incorporar la lectura de usuario novel como paso o criterio de la Fase B2.**

- *Archivo:* `Rules/Maqueta-Rules.md` §3.5 (ciclo de corrección) y §8 (criterios de aceptación).
- *Qué se agrega:* que el paso 5 pida explícitamente una pasada leyendo cada superficie como alguien que no conoce el sistema, y que su resultado se registre en la bitácora de validación. Puede formularse como guion: por cada superficie, qué cree el lector que hace cada opción antes de hacer clic.
- *Por qué:* es la lectura que produjo los cuatro hallazgos anteriores y la fase no la pide. Y es estructuralmente **indelegable al agente que escribió la especificación**, por la misma razón por la que el ensayo de entrega de la Fase J es gate humano.

**Nota sobre el alcance de estos cinco cambios.** Cuatro son minor: agregan criterios sin cambiar el flujo plan-then-confirm, el conjunto D8 ni la cardinalidad de generación. El 8.1, si se aplica el barrido general que sugiere, puede alcanzar a varios archivos de reglas a la vez y conviene tratarlo como intervención propia.

---

## 9. Cómo usar este documento en el prompt posterior

Para la corrida de realimentación del framework, lo utilizable es la **§8**, que ya viene con archivo, texto propuesto y fundamento por cambio. Las §2 a §6 son la evidencia que sostiene cada uno: conviene conservarlas, porque un cambio normativo sin el caso que lo motiva se discute en abstracto y termina en preferencia estética.

Como **ejemplo de análisis**, lo que este documento muestra es un método de tres movimientos:

1. **Separar el defecto del hueco.** Los rótulos son un defecto de este producto; que ninguna regla los haya detectado es un hueco del framework. Corregir el primero sin el segundo garantiza que el próximo producto lo repita.
2. **Buscar si la regla ya existía.** En cuatro de los cinco casos existía, o existía la práctica. El framework rara vez necesita reglas nuevas: necesita que las que tiene sean verificables. Una regla que no está en la lista de criterios de aceptación no rige, y eso se comprueba leyendo qué evaluó el audit.
3. **Preguntar quién podía haberlo visto.** El hallazgo apareció porque un humano leyó la pantalla sin el contexto del que la escribió. Si una clase de defecto sólo la puede encontrar alguien sin contexto, la fase tiene que pedir esa lectura explícitamente, no esperar que ocurra.

---

## Control de cambios

| Versión | Fecha | Cambios | Autor |
|---|---|---|---|
| 1.0 | 2026-08-04 | Emisión inicial. Recoge la observación del agente humano del proyecto sobre el campo de origen del alta de servicio durante el paso 5 de la Fase B2, y la separa en cinco hallazgos: el vocabulario del modelo filtrado a la interfaz, la superficie que expone uno de los tres ejes independientes que la especificación declara, la capacidad mudada sin puntero, el texto de instrumento de la maqueta compitiendo con el del producto, y la lectura de usuario novel que la fase no pide. Cada hallazgo declara su evidencia verificable y, cuando corresponde, la regla que ya existía y no se aplicó. §7 propone la corrección para el producto, con los tres rótulos reescritos. §8 propone cinco cambios al framework con archivo, texto y fundamento, cuatro de los cuales hacen verificable algo que el framework ya dice o ya practica. §9 declara el método, para que el documento sirva de ejemplo de análisis en la corrida de realimentación. | Orquestador SDD |
