# Validación de la maqueta · Fase B2, paso 5

**Documento:** `Validacion-Maqueta.md`
**Versión:** 1.0
**Estado:** En elaboración — insumo de la ronda de validación
**Fecha:** 2026-08-03
**Autor:** Orquestador SDD, a pedido del agente humano del proyecto
**Naturaleza:** Documento de trabajo del repositorio de documentación. No es artefacto de ninguna de las doce categorías del SDD y ningún subagente lo consume como insumo. Existe para que el agente humano del proyecto decida sobre lo que la reconstrucción de la maqueta dejó abierto, y para que de esas decisiones salga el informe que después se ejecuta.

**Alcance.** Recoge el análisis de la reconstrucción de la maqueta del 2026-08-02, que materializó sobre `SDD/Maquetas/SelfHosted-Service/` la ronda de decisiones del 2026-07-31 y del 2026-08-01. No recoge el estado de las fases anteriores, que vive en `SDD/Estado/Informe-Avance.md` del repositorio de código.

**Cómo se usa.** Cada punto trae qué se observó, dónde está la evidencia, por qué importa aguas abajo y qué opciones hay. Debajo de cada uno hay una línea `Decisión:` sin completar. A medida que las completes, este documento se vuelve el informe que el orquestador ejecuta: cada decisión se consolida en el `PRODUCT-INTAKE` o en el wireframe que corresponda y se propaga según la matriz de propagación de `Maqueta-Rules.md` §3.6.

---

## Tabla de contenido

- [1. Dónde estamos](#1-dónde-estamos)
- [2. Qué mirar, y en qué orden](#2-qué-mirar-y-en-qué-orden)
- [3. Las cuatro ausencias deliberadas](#3-las-cuatro-ausencias-deliberadas)
- [4. Punto A · Contradicción sobre el grupo de acciones de ejecución](#4-punto-a--contradicción-sobre-el-grupo-de-acciones-de-ejecución)
- [5. Punto B · No hay servicio de ejemplo que instancie la variante nueva ni la cuarentena](#5-punto-b--no-hay-servicio-de-ejemplo-que-instancie-la-variante-nueva-ni-la-cuarentena)
- [6. Punto C · Tres rótulos de interfaz sin cadena literal declarada](#6-punto-c--tres-rótulos-de-interfaz-sin-cadena-literal-declarada)
- [7. Punto D · Tres desfases de conteo dentro de los wireframes](#7-punto-d--tres-desfases-de-conteo-dentro-de-los-wireframes)
- [8. Punto E · Dos categorías nombran distinto la misma variante](#8-punto-e--dos-categorías-nombran-distinto-la-misma-variante)
- [9. Punto F · La celda «Vacío» de SUP-11 se contradice con su propia sección](#9-punto-f--la-celda-vacío-de-sup-11-se-contradice-con-su-propia-sección)
- [10. Punto G · El modelo de estados no distingue el contenedor en reposo](#10-punto-g--el-modelo-de-estados-no-distingue-el-contenedor-en-reposo)
- [11. Punto H · Vocabulario obsoleto, ya corregido](#11-punto-h--vocabulario-obsoleto-ya-corregido)
- [11 bis. Punto I · La propuesta de distinción visual de las aristas](#11-bis-punto-i--la-propuesta-de-distinción-visual-de-las-aristas)
- [11 ter. Punto J · Los cuatro puntos abiertos de la política de cuarentena](#11-ter-punto-j--los-cuatro-puntos-abiertos-de-la-política-de-cuarentena)
- [11 quater. Punto K · El lenguaje del alta y el eje que no se ve](#11-quater-punto-k--el-lenguaje-del-alta-y-el-eje-que-no-se-ve)
- [11 quinquies. Punto L · El origen es una composición de dos ejes, no un campo de tres valores](#11-quinquies-punto-l--el-origen-es-una-composición-de-dos-ejes-no-un-campo-de-tres-valores)
- [12. Tabla de decisiones](#12-tabla-de-decisiones)
- [13. Qué pasa después de que decidas](#13-qué-pasa-después-de-que-decidas)

---

## 1. Dónde estamos

La Fase B2 tiene siete pasos más la emisión de la línea de base. Los pasos 1, 2 y 5 se detienen a esperar tu confirmación.

| Paso | Estado |
|---|---|
| 1 · Oferta de maqueta y elección del modelo UX-UI | Cerrado. El catálogo `Modelos-UX-UI/` está vacío, de modo que se aplica el catálogo base: `Design-Rules-Web-Generico` 1.2 más `Design-Rules-Blazor-Mudblazor` 1.2 más las cuatro extensiones por capacidad |
| 2 · Plan de maqueta | Aprobado por vos el 2026-08-02 |
| 3 · Construcción | Ejecutado el 2026-08-02. Nueve superficies rehechas o ajustadas, diez conservadas |
| 4 · Lanzamiento | Servida en `http://127.0.0.1:8137/` |
| **5 · Ciclo de corrección y validación** | **Acá estamos.** No cierra sin tu aprobación explícita |
| 6 · Retroalimentación de la documentación | Pendiente |
| 7 · Captura del modelo UX-UI | Pendiente, opcional |
| 8 · Línea de base del sensado de deriva | Pendiente |

**Qué se rehizo.** Nueve superficies, verificadas en disco por marca de tiempo, todas con fecha 2026-08-02:

| Superficie | Alcance del cambio |
|---|---|
| `SUP-17` Alta de servicio | Rehecha entera. La grilla de siete tarjetas se retiró; el origen pasó a un grupo de radios de tres valores nunca colapsado, con la variante pública o privada derivada de la credencial |
| `SUP-06` Panel lateral del servicio | Cabecera rehecha, con la fila condicional de distintivos y el conjunto nuevo de acciones de ejecución |
| `SUP-05` Lienzo del proyecto | La acción primaria abre el alta directamente; tercera acción en la barra; nodo y resumen con los dos distintivos |
| `SUP-09` Tablero de estado | Bloque de servicios en cuarentena, dentro del bloque del servidor |
| `SUP-18` Imágenes | Crear servicio desde imagen del almacén local en el grupo administrado; en el grupo ajeno la acción no existe |
| `SUP-15` Exportación e importación | Advertencia previa al modo exportación y tercera lista en el informe de importación |
| `SUP-04` Listado de proyectos | Distintivo de no reproducible en la tarjeta, sin insignia ni color propios |
| `SUP-10` Descubrimiento e incorporación | Adoptar pasa a ser acción de este listado |
| `SUP-11` Catálogo de plantillas | Usar el catálogo pasa a acción del listado; el editor hereda el campo de origen |

**Qué se conservó.** `SUP-01`, `SUP-02`, `SUP-03`, `SUP-07`, `SUP-08`, `SUP-12`, `SUP-13`, `SUP-14`, `SUP-16` y `SUP-19`, todas con su marca de tiempo anterior intacta.

**Las dos representaciones** se materializan dentro de las superficies que las invocan: el nodo de servicio pasó de cinco zonas a seis, con la zona de distintivos intercalada entre el subtítulo y las métricas y sin alterar el ancho de la caja, y los distintivos componen sobre el par de estado sin cambiarle la variante.

La maqueta pasó de **282 a 302 estados demostrables** sobre las mismas diecinueve superficies.

---

## 2. Qué mirar, y en qué orden

1. **`Alta-De-Servicio`**, que es la que se rehizo entera y la que más se aleja de lo que viste la última vez. El punto a validar es si el campo de origen de tres valores dice lo mismo que decían las siete vías, y si la pérdida del formulario se declara con la claridad suficiente.
2. **`Panel-Lateral-Del-Servicio`**, por el reparto de las acciones de ejecución entre grupo primario y secundario. Es donde vive el punto A, que es el único que te bloquea de entrada.
3. **`Lienzo-Del-Proyecto`** y **`Tablero-De-Estado`**, donde aparecen los distintivos y la cuarentena. El punto a validar es si un servicio en cuarentena se distingue sin leer, y si el distintivo compone sobre el estado sin competir con él.
4. **`Imagenes`**, por el indicador de uso y por la asimetría deliberada entre el grupo administrado y el ajeno.

Cada superficie trae su barra de validación, rotulada como instrumento de la maqueta y no como parte del producto, que permite alternar los estados sin recargar. El flujo feliz no alcanza para validar: el material está en los estados vacío, cargando y error.

---

## 3. Las cuatro ausencias deliberadas

Lo que vas a ver faltar no es un olvido de la maqueta: son brechas abiertas de la especificación que la maqueta materializa como ausencias declaradas, en lugar de completarlas por su cuenta. Están enumeradas en `SDD/Docs/03-UX-UI-DX/README.md` §9.4.

| Brecha | Qué vas a ver | Qué falta decidir |
|---|---|---|
| `B-UX-31` · política de cuarentena | Un servicio en cuarentena se ve en el nodo del lienzo, en el panel lateral y en el bloque del tablero. **No hay ninguna acción para retirarlo de ahí** | Qué levanta la cuarentena: si alcanza con un despliegue exitoso, si el administrador la retira a mano, o si son dos actos distintos |
| `B-UX-33` · imagen que sostiene un servicio no reproducible | El indicador de uso cuenta al servicio declarado como referencia. **La imagen no se marca como protegida** y la limpieza no la trata distinto | Si el usuario puede marcar una imagen como protegida y con qué alcance |
| `B-UX-32` · variante de origen `repositorio` | El panel lateral la **exhibe en lectura** y declara que la interfaz ya no la produce; el alta **no la ofrece**. Las dos mitades están a propósito | Nada por ahora: es consecuencia de la acotación de `F-10` y está declarada |
| `B-UX-28` · umbral de la sugerencia de limpieza | Los dos campos de configuración se maquetan **sin valor por defecto**, y la línea de sugerencia del tablero se demuestra con dato de ejemplo | Qué valores toman los dos umbrales |

Si al mirarlo alguna de estas ausencias no se banca —típicamente la primera, porque un servicio que queda en cuarentena y no tiene forma de salir es un callejón—, eso es exactamente el material del paso 5: se decide, se consolida en el intake y se propaga a las categorías alcanzadas.

---

## 4. Punto A · Contradicción sobre el grupo de acciones de ejecución

**Qué pasó.** El plan de maqueta que el orquestador te presentó declaraba que «desplegar sin arrancar, arrancar y detener» van al **grupo primario** de acciones de ejecución del panel lateral, y que reiniciar, redesplegar y parar se reubican al secundario. El dato salió del §9.3 del índice de `03-UX-UI-DX`, que es el resumen que esa categoría publicó de lo que la ronda agrega.

**La contradicción.** El wireframe `Wireframes-Panel-Lateral-Del-Servicio.md`, en su §3.8 consecuencia 1 y en su §4, dice lo contrario: el grupo primario exhibe **una sola acción de arranque a la vez**, y **las cuatro restantes viven en el grupo secundario contiguo**.

**Qué hizo la maqueta.** Siguió el wireframe, no el índice, y lo hizo bien: el wireframe es el artefacto titular de la superficie y el índice es un resumen derivado de él. Lo que estás viendo en la maqueta es la versión del wireframe.

**Por qué importa.** No es una diferencia cosmética. El grupo primario es lo que el administrador ve sin desplegar nada, y de eso depende cuántos clics hay entre él y la acción que más va a usar. Aguas abajo, `06-Backlog-Tecnico` deriva historias de usuario de esta forma y `08-Calidad-Y-Pruebas` convierte los criterios de aceptación en tests: si el índice y el wireframe dicen cosas distintas, una de las dos versiones llega a la suite como requisito verificado.

**Opciones.**

- **(a)** Rige el wireframe. Una sola acción de arranque en el primario, las otras cuatro en el secundario. La maqueta ya está así y no hay que tocarla; se corrige el §9.3 del índice, que quedó mal resumido.
- **(b)** Rige el índice. Tres acciones en el primario. Hay que rehacer la cabecera del panel lateral en la maqueta y corregir el wireframe, que es un artefacto aprobado y por lo tanto sube versión y archiva.

**Decisión:**

---

## 5. Punto B · No hay servicio de ejemplo que instancie la variante nueva ni la cuarentena

Es el hallazgo más valioso de la reconstrucción, y el que más conviene resolver bien.

**Qué pasó.** El intake declara la variante de origen `imagen-local` y declara la política ante un despliegue que no arranca, con su cuarentena. Pero **no emite un solo servicio de ejemplo que lleve ninguna de las dos cosas**, y cinco superficies tienen que demostrarlas: el lienzo, el panel lateral, el tablero, el listado y las imágenes.

**Qué hizo la maqueta.** No inventó datos y no se detuvo: **compuso** los dos casos con elementos que otras fuentes sí declaran —el parque de referencia de los anexos E-19 y E-20, el despliegue 5472 del anexo E-3, la referencia `registro-privado/video:0.3` del inventario de imágenes— y los rotuló visiblemente como composición de la maqueta, en lugar de presentarlos como dato del corpus. Es la conducta correcta: `Maqueta-Rules.md` §4.2 prohíbe inventar datos y obliga a que salgan de la documentación.

**Lo que queda sin fuente y necesita tu decisión.** La **causa concreta del fallo de arranque**. El único despliegue fallido que el intake declara falló porque la imagen no existía en el registro, y eso es un fallo de la **primera** fase —obtener la imagen—, no de la tercera. La política de cuarentena se dispara con un despliegue que **se crea bien y no arranca**, que es un caso del que no hay ningún ejemplo declarado.

**Por qué importa.** Sin un ejemplo del caso quedan tres cosas sin verificar: qué mensaje ve el administrador cuando un servicio entra en cuarentena, si ese mensaje alcanza para que entienda qué pasó, y si la banda de resultado lleva el código correcto. Además, `08-Calidad-Y-Pruebas` va a necesitar ese caso para escribir el test y `10-Examples` para el sample.

**Qué haría falta.** Un servicio de ejemplo, con nombre, imagen, causa del fallo de arranque y el texto que el administrador lee. Puede ser inventado por vos —sos la fuente— pero no por el agente.

**Decisión:**

---

## 6. Punto C · Tres rótulos de interfaz sin cadena literal declarada

**Qué pasó.** La ronda del 2026-08-01 actualizó el cuerpo de tres wireframes pero **no actualizó sus bloques de arte ASCII** de §2, que es de donde la maqueta toma los literales de la interfaz. Quedaron sin cadena declarada:

- El rótulo del bloque de servicios en cuarentena del tablero.
- El distintivo de no reproducible en la tarjeta del listado de proyectos.
- El encabezado de la tercera lista del informe de importación.

**Qué hizo la maqueta.** Eligió «Servicios en cuarentena» y «No van a poder levantar en este destino», y las declaró en la propia superficie **como decisión de maqueta y no como transcripción**, que es la distinción que evita que un literal inventado se lea después como especificación.

**Por qué se sabe que es una omisión y no una convención.** Porque la versión 2.1 del mismo tablero **sí** había actualizado su bloque ASCII cuando incorporó la sugerencia de limpieza. La práctica del proyecto es actualizarlo; esta ronda se lo salteó.

**Qué hay que hacer.** Confirmar los tres literales, o proponer otros, y que `03-UX-UI-DX` los incorpore a los bloques ASCII de los tres wireframes.

**Decisión:**

---

## 7. Punto D · Tres desfases de conteo dentro de los wireframes

Son inconsistencias internas de la documentación, detectadas al maquetar. Ninguna afecta lo que se ve, pero las tres alimentan conteos que después se citan como verificados.

| Wireframe | Qué declara | Qué tiene |
|---|---|---|
| `SUP-05` Lienzo | §8 declara 21 estados | §5 enumera 20. El que falta es «Nodo borrador», que §3.3 especifica, §4 cita y la representación del nodo tiene como variante. **La maqueta lo demuestra** |
| `SUP-09` Tablero | §8 declara 17, de los cuales 16 demostrables | La tabla tiene 18 filas con una sola «no aplica», o sea 17 demostrables. El desfase se arrastra desde la versión 1.0 |
| `SUP-18` Imágenes | §8 «Tests previstos» dice veinte estados | §5 y la fila de maqueta dicen veintiuno |

**Qué hay que hacer.** Que `03-UX-UI-DX` rehaga los tres conteos sobre sus propias tablas. No requiere decisión tuya, salvo que quieras que se resuelva de otro modo.

**Decisión:**

---

## 8. Punto E · Dos categorías nombran distinto la misma variante

**Qué pasó.** La variante de origen que el modelo llama `dockerfile` se nombra de dos maneras en la documentación:

- `03-UX-UI-DX`, en las versiones 2.2 del panel lateral y del alta, la renombró a **«Definición local»**.
- `02-Especificacion-Funcional`, en su `Glosario-Funcional.md` §2, la sigue nombrando **«Archivo de construcción en línea»**.

**Qué hizo la maqueta.** Conservó los dos rótulos con su dueño declarado —uno como etiqueta de interfaz y otro como etiqueta del modelo— en lugar de elegir. Elegir habría sido decidir por una de las dos categorías, que no es trabajo de la maqueta.

**Por qué importa.** El gobierno del glosario es criterio de audit: un término con dos nombres entre categorías es una contradicción que el auditor levanta como hallazgo. Además el usuario final ve uno de los dos y el equipo lee el otro.

**Opciones.**

- **(a)** Manda el rótulo de interfaz, «Definición local», y `02` lo adopta en su glosario declarando el cambio de nombre.
- **(b)** Manda el término del modelo, «Archivo de construcción en línea», y `03` revierte el renombre en las dos superficies.
- **(c)** Son dos cosas distintas y conviven: el término del modelo y su rótulo en la interfaz. En ese caso el glosario tiene que declarar la correspondencia explícitamente, que es lo que hoy falta.

**Decisión:**

---

## 9. Punto F · La celda «Vacío» de SUP-11 se contradice con su propia sección

**Qué pasó.** En el catálogo de plantillas, la celda del estado «Vacío» de §5 conserva «acceso directo a las otras **seis** vías de alta», que es la formulación anterior a la unificación del alta. Su propia §3.8 ya lo corrigió a «otras **dos** formas de tener un servicio».

**Qué hizo la maqueta.** Siguió §3.8, que es la sección que la ronda actualizó, y declaró la discrepancia en la superficie.

**Qué hay que hacer.** Que `03-UX-UI-DX` corrija la celda de §5. Es residuo de la unificación del alta, no una decisión pendiente.

**Decisión:**

---

## 10. Punto G · El modelo de estados no distingue el contenedor en reposo

**Qué pasó.** La ronda declaró que el estado intermedio —contenedor creado y sin arrancar— es **reposo legítimo y no paso transitorio**. Pero la tabla de correspondencia del anexo E-17, que traduce el estado del motor de contenedores al estado del producto, **tiene una sola fila** para los dos casos: no distingue «contenedor creado con el despliegue en curso» de «contenedor creado y terminado, en reposo».

**Qué hizo la maqueta.** Demuestra las dos representaciones con un conmutador de escenario propio, y **no inventa el campo** que las separaría.

**Por qué importa, y por qué es el punto más de fondo de la lista.** El campo que falta no es de interfaz: es del modelo. Alguien tiene que poder saber, mirando el estado del sistema, si un contenedor creado está esperando que termine su despliegue o si ya terminó y quedó a propósito sin arrancar. Si el dato no existe, la interfaz no puede mostrarlo por más que el wireframe lo declare, y el sensado de deriva no puede verificar que lo construido corresponda a lo aprobado.

**Qué haría falta.** Que se declare cómo se distinguen los dos casos: por un campo propio del despliegue, por el estado del despliegue asociado, o por otra vía. Es material de `05-Arquitectura-Tecnica`, pero la decisión de que los dos casos son distintos ya la tomaste; lo que falta es cómo se sostiene.

**Decisión:**

---

## 11. Punto H · Vocabulario obsoleto, ya corregido

Tres ocurrencias de «solución» y de `SOLUTION-INTAKE` designando el nivel superior, residuo del vocabulario anterior a la migración normativa 6.0: dos en los assets de la maqueta y una en un microcopy del catálogo de plantillas. Corregidas a «producto» y `PRODUCT-INTAKE`. No requiere decisión; se registra para que conste que se buscó y se encontró.

---

## 11 bis. Punto I · La propuesta de distinción visual de las aristas

Este punto es de otra clase que los anteriores: no es un hallazgo de la reconstrucción, es una **propuesta de diseño que la maqueta emite** y que está esperando que la apruebes desde antes de esta ronda. Se incorpora acá porque aparece rotulada en el lienzo y es una de las decisiones que el paso 5 puede cerrar.

**Qué es una arista.** En el lienzo, la línea entre dos servicios. Tiene dos ejes independientes: puede declarar **espera al destino** —el servicio no arranca hasta que el otro esté listo— y puede referenciar el host o variables del otro. Y puede quedar **inválida**, cuando referencia el host del destino y no hay canal alcanzable, lo que bloquea el arranque del proyecto.

**Por qué hay una propuesta y no un dibujo a secas.** Es la brecha `B-UX-01`, la más vieja de la categoría: viene heredada como `B-07` desde `02-Especificacion-Funcional`. El catálogo de diseño **no tiene ninguna regla sobre representación de aristas de un lienzo** —cubre componentes, estados, formularios y tablas, pero no grafos— y el anexo E-18 del intake, que declara el lenguaje visual de estados, **no tiene fila para aristas**. No había de dónde derivarla.

Eso deja a la maqueta ante tres salidas, y sólo una es aceptable: dibujar callada, que convertiría la elección en especificación de hecho sin que nadie la decidiera; no dibujar aristas, que dejaría sin validar la superficie principal del producto; o **dibujar una propuesta concreta y declararla como tal en la propia pantalla**, que es lo que hace.

**Qué propone, concretamente.**

| Clase de arista | Cómo se dibuja |
|---|---|
| Declara espera al destino | Trazo sólido neutro, punta de flecha **rellena**, marcador de doble barra en el punto medio, rótulo «espera» junto al marcador |
| No declara espera | Trazo sólido neutro, punta de flecha **hueca**, sin marcador medio |
| Inválida · bloquea el arranque | Trazo **punteado** en color de error, marcador de cruz en el punto medio, rótulo «inválida» |

**Las tres restricciones que `03` sí había declarado**, y que la propuesta cumple. Son lo único que la especificación aportaba, y cualquier alternativa tiene que cumplirlas igual:

1. **No usa el violeta**, que está reservado en exclusiva al estado «pendiente de aplicar».
2. **No distingue sólo por color**: suma la forma de la punta y el marcador del punto medio, que es lo que hace que la distinción funcione para alguien que no distingue colores. Es requisito de accesibilidad, no preferencia estética.
3. **Es distinguible de la arista inválida**, que cambia color y patrón de trazo a la vez.

**Un detalle del juego de datos.** En el parque del anexo E-1 **todos** los pares de servicios declaran espera, de modo que la clase «no declara espera» no aparece dibujada en el lienzo: se exhibe sólo en la leyenda, con las mismas marcas que usaría la arista real. Aparecería en cuanto se desmarque la espera de una arista. La maqueta lo declara en su nota en lugar de fabricar un dato que el corpus no tiene.

**Opciones.**

- **(a)** Aprobar la propuesta tal cual. Deja de ser propuesta: se incorpora al wireframe del lienzo y a la representación del lenguaje visual de estados, y `B-UX-01` se cierra.
- **(b)** Aprobarla con cambios. Indicá qué canal cambiar —punta, marcador, rótulo, grosor— respetando las tres restricciones.
- **(c)** Reemplazarla por otra representación. Misma condición: las tres restricciones no son negociables, porque dos de ellas vienen de reglas del catálogo y la tercera de accesibilidad.

**Decisión:**

---

## 11 ter. Punto J · Los cuatro puntos abiertos de la política de cuarentena

Es el complemento del punto I y funciona al revés: acá la maqueta **no dibuja**, en lugar de proponer. Aparece rotulado en tres superficies —lienzo, panel lateral y tablero— como `B-UX-31`.

**Lo que sí está decidido.** El cuerpo de la política, tomado el 2026-07-31 y consolidado en el `PRODUCT-INTAKE` §4, nota «Política ante un despliegue que no arranca»: un despliegue que **se crea bien y no arranca** hace que el sistema informe el error, recupere el despliegue anterior, sirva la versión previa si ésa sí arranca, y si tampoco arranca deje **el proyecto corriendo** con ese servicio en cuarentena. El fundamento también está declarado: sin cuarentena, un servicio roto reintenta en cada reinicio y falla siempre. La política es **por servicio y no por proyecto**, de modo que no contradice el arranque parcial.

**Lo que no está decidido.** El intake declara **tres** puntos abiertos en lugar de suponerlos, y `02-Especificacion-Funcional` levantó un **cuarto** al especificar el caso de uso:

| # | Punto abierto | Dónde se declara |
|---|---|---|
| J-1 | Si la recuperación del despliegue anterior es **automática u ofrecida** | `PRODUCT-INTAKE` §4 |
| J-2 | Si la cuarentena apaga el **autoarranque** o la **participación en el arranque del proyecto** | `PRODUCT-INTAKE` §4 |
| J-3 | **Dónde se avisa** | `PRODUCT-INTAKE` §4 |
| J-4 | **Qué levanta la cuarentena**: si alcanza con un despliegue exitoso, si el administrador la retira a mano, o si son dos actos distintos | `B-31` de `02`, y `B-UX-31` de `03` |

**Por qué la maqueta igual muestra la cuarentena.** La visibilidad persistente **se deriva, no se decide**. Si el intake exige que el administrador se entere de que un servicio quedó en cuarentena *antes de necesitarlo*, entonces la cuarentena tiene que verse cuando mira, sin depender de que haya estado presente en el momento en que ocurrió. Eso está implicado por el cuerpo decidido, así que se dibuja: distintivo en el nodo del lienzo, en el panel lateral del servicio y bloque propio en el tablero de estado.

**Qué no dibuja, y de qué punto depende cada ausencia.**

| Lo que no dibuja | Punto |
|---|---|
| Ninguna acción de **retirar la cuarentena**, ni habilitada ni deshabilitada | J-4 |
| Ningún **aviso en el momento** en que ocurre: nada de notificación superpuesta, contador en la navegación ni centro de avisos. Ver y avisar son cosas distintas | J-3 |
| Ninguna acción de **recuperar el despliegue anterior**: si resultara ofrecida en vez de automática haría falta un control que hoy no existe | J-1 |
| El estado se rotula «autoarranque apagado» **declarando que la elección no está tomada**, siguiendo el cuerpo decidido del intake | J-2 |

**Por qué esa disciplina importa, y no es prolijidad.** Dibujar un botón de «retirar cuarentena» decidiría J-4 por la puerta de atrás, y ese botón llegaría a `06-Backlog-Tecnico` como historia de usuario y a `08-Calidad-Y-Pruebas` como test: una decisión que nadie tomó terminaría verificada por la suite. Es exactamente el error por el que el audit del 2026-08-01 rechazó la ronda anterior, cuando `CU-38` cerró dos de estos puntos por arrastre en su flujo alternativo y en dos criterios de aceptación.

**Un eje que sí está cerrado.** La cuarentena **no tiene umbral**: un solo servicio ya justifica exhibir el bloque del tablero. No hay valor por defecto que declarar y por lo tanto no se abre brecha nueva por ahí.

**Opciones, punto por punto.**

- **J-1.** *Automática*: el sistema vuelve al anterior sin preguntar; el argumento a favor es que pedir confirmación deja el servicio abajo esperando a alguien. *Ofrecida*: el sistema informa y espera; el argumento a favor es que volver atrás es una decisión con consecuencias. Si elegís ofrecida, hay que dibujar el control en el panel lateral.
- **J-2.** *Autoarranque*: el servicio no arranca solo al reiniciar el sistema, pero sigue siendo parte del proyecto y arranca si alguien lo arranca. *Participación*: queda fuera del arranque del proyecto hasta que se lo reincorpore. La diferencia se ve cuando el administrador arranca el proyecto entero.
- **J-3.** *Sólo visibilidad persistente*, que es lo que la maqueta ya demuestra y no requiere nada más. *Visibilidad más aviso en el momento*: hay que decidir dónde vive el aviso, y eso agrega superficie.
- **J-4.** *Un despliegue exitoso la levanta solo*; *el administrador la retira a mano*; o *son dos actos distintos* y hay que declarar cuál corresponde a cada caso. Es el que más consecuencia tiene sobre la interfaz: de él depende si existe una acción y dónde vive.

**Decisión J-1:**

**Decisión J-2:**

**Decisión J-3:**

**Decisión J-4:**

---

## 11 quater. Punto K · El lenguaje del alta y el eje que no se ve

Sale de la observación del agente humano del proyecto del 2026-08-04 mirando `SUP-17` en el estado «formulario abierto, sin nombre». El análisis completo, con la lectura de framework, vive en `InformUX-UI.md`, documento hermano de éste; acá van sólo las decisiones.

**Lo que se observó.** Los tres rótulos del campo de origen están escritos en vocabulario del modelo —«registro», «almacén local», «definición local»—, no nombran los ejemplos que el usuario reconoce —Docker Hub no aparece en ninguna parte— y «Definición local» no dice ni la acción ni el resultado. **No son invención de la maqueta**: están transcriptos del wireframe, de modo que son especificación aprobada y auditada.

**Lo que además se observó, y es más de fondo.** El escenario «desplegar desde GitHub» se buscó en el campo de origen y no está ahí: vive en el eje del **disparador**, que el intake declara independiente y que esta superficie no muestra. La decisión del 2026-07-31 dice textualmente que confundir origen con disparador era lo que hacía que el menú anterior se leyera mal: el modelo separó los ejes y la interfaz no heredó ninguna defensa contra la confusión.

**Decisiones.**

- **K-1 · Rótulos.** Propuesta:

  | Hoy | Propuesta |
  |---|---|
  | Imagen de un registro · «La imagen ya está publicada en un registro» | **Descargar una imagen ya publicada** · «Está en Docker Hub, en GitHub Container Registry o en un registro privado tuyo. El panel la descarga.» |
  | Imagen del almacén local · «La imagen ya está en este servidor» | **Usar una imagen que ya está en este servidor** · «Alguien la descargó o la construyó antes. No se vuelve a descargar.» |
  | Definición local · «Quiero tomar una imagen publicada y ajustarla con un archivo de construcción declarado acá» | **Ajustar una imagen con un archivo de construcción** · «Partís de una imagen que ya existe en Docker Hub o en otro registro y le agregás lo tuyo: paquetes, variables de entorno, el comando de arranque. El archivo lo escribís acá y el panel construye la imagen nueva. **No podés copiar archivos de tu máquina ni compilar código**: para eso publicá la imagen desde tu pipeline y usá la primera opción.» |

  **Los dos primeros quedaron validados por el agente humano del proyecto el 2026-08-04**, con sus palabras: del primero, «se entiende a primeras, me doy una idea que necesito que esté la imagen publicada en Docker Hub»; del segundo, «sé que las imágenes están localmente porque las he descargado o construido antes».

  **El tercero se reescribió después de esa lectura**, que lo calificó de regular y produjo cuatro preguntas que la propuesta anterior no contestaba: publicada dónde, si la base puede ser local, si se puede crear una imagen a partir de otra, y si hace falta un archivo que cite esa imagen. La versión de arriba contesta tres —la cuarta es el punto K-5— y **suma el límite que ninguna versión del rótulo declaraba**: sin contexto de construcción no se pueden copiar archivos locales ni compilar código, que es lo que `F-10` acotada declara y lo que separa esta capacidad del ejecutor de integración continua.

  ¿Se adopta el tercero así, o se ajusta?

  **Decisión K-1:**

- **K-2 · El fragmento para el automatismo.** Es la pendiente `Q-10` del intake, hoy abierta y con vos como destinatario: si el panel genera el fragmento de configuración del automatismo de integración continua, con el identificador del servicio ya puesto, listo para pegar. Tu observación la cierra en positivo si la confirmás. Si sí, `03-UX-UI-DX` especifica dónde vive y qué contiene.

  **Decisión K-2:**

- **K-3 · El eje del disparador en el alta.** Que la superficie declare que de dónde sale la imagen **no determina quién dispara el despliegue**, con el puntero a dónde se configura el disparo externo. La alternativa es resolverlo en otra superficie y dejar el alta como está. No se trata de meter el disparador dentro del origen: eso sería volver al error que la decisión del 2026-07-31 corrigió.

  **Decisión K-3:**

- **K-4 · El puntero a las otras dos formas de tener un servicio.** El 2026-07-31 decidiste que adoptar un contenedor y usar el catálogo **salen del alta**, por ser listados y no formularios. La decisión se aplicó, pero el alta **no declara a dónde se fueron**, y el catálogo sí declara el camino inverso en su estado vacío. Dos salidas: se mantiene la decisión y el alta suma el puntero, o se revierte y el catálogo vuelve a ser un valor del campo de origen.

  **Decisión K-4:**

- **K-5 · ¿El archivo de construcción puede partir de una imagen del almacén local?** Punto abierto que **ninguna fuente declara**, detectado por la pregunta del agente humano del proyecto del 2026-08-04. **Queda subsumido por el punto L**, que muestra que la pregunta no tiene respuesta porque el modelo no tiene dónde alojarla: es la celda que falta de un cuadro de dos por dos que el campo de origen, unidimensional, no puede expresar. Si se adopta L-1 este punto se responde solo; si no se adopta, hay que responderlo igual. El intake dice que la variante «sirve para ajustar una imagen ya publicada» y no se pronuncia sobre una base local. Técnicamente el motor lo resuelve solo: la instrucción `FROM` busca primero en las imágenes del servidor y sólo descarga si no la encuentra, de modo que **si nadie lo impide, va a funcionar**. Las salidas: se admite y la pantalla lo dice; se impide y la validación del archivo lo rechaza con su motivo; o se declara explícitamente como no soportado sin impedirlo. La tercera es la peor, porque deja al usuario descubriendo por accidente algo que la documentación no reconoce.

  **Decisión K-5:**

- **K-6 · ¿La pantalla ofrece un ejemplo editable del archivo de construcción?** El intake ya trae uno canónico, el del servicio `proxy-interno` del anexo E-2: parte de `nginx:1.25-alpine`, instala `curl`, fija una variable, declara el puerto y el comando. Es el mismo pedido que `Q-10` hace para el automatismo de integración continua: que el panel entregue algo que se pueda pegar en lugar de una caja vacía. La evidencia de que hace falta es directa: sin ejemplo, la opción no se entendió.

  **Decisión K-6:**

---

## 11 quinquies. Punto L · El origen es una composición de dos ejes, no un campo de tres valores

Sale del planteo del agente humano del proyecto del 2026-08-04, que describió el despliegue como una secuencia de pasos en lugar de un menú de alternativas. Es el punto de mayor alcance del documento: **no es de interfaz, es de modelo**, y de él depende que la interfaz pueda ser simple sin mentir.

### L.0 El planteo, transcripto

El agente humano describió primero la cadena completa, incluyendo lo que ocurre fuera del producto:

1. Fuentes.
2. Build de las fuentes.
3. Construcción del archivo de construcción o de la composición.
4. Construcción de la imagen.
5. Construcción del contenedor y puesta en marcha.

Y observó dónde está la frontera: el ejecutor de GitHub —o una máquina cualquiera— hace los pasos 1 a 4 y publica la imagen; el panel recibe el disparo del flujo de trabajo, descarga la imagen del registro, instancia el contenedor y lo deja pendiente de correr. Si el registro es privado, se acredita y sigue igual.

Después propuso el modelo de cuatro pasos que generaliza todas las opciones del alta:

| # | Paso | Qué resuelve |
|---|---|---|
| 1 | **Resolver la imagen** | Viene de un registro o del almacén local. Si viene de un registro puede haberla pedido un automatismo o el usuario; si viene del almacén, hay que elegirla de una lista |
| 2 | **Ajustar la imagen** | *Opcional.* Si hay un archivo de construcción que la toma como base, se le agregan paquetes y demás y **se produce una imagen nueva**. Si no, queda como está |
| 3 | **Instanciar el contenedor** | Se crea el contenedor |
| 4 | **Correr** | Se pone en marcha |

### L.1 Reconciliación con lo ya decidido: el planteo refina, no contradice

El intake declara **tres fases** del despliegue desde el 2026-07-31, y son casi las mismas:

| Fase declarada | Qué hace | Paso del planteo |
|---|---|---|
| 1 · Obtener la imagen | Descargarla del registro, construirla desde el archivo declarado, tomarla del almacén local, o encontrarla ya presente | **Pasos 1 y 2** |
| 2 · Crear el contenedor | Resolver variables, reservar la dirección y crear el contenedor | Paso 3 |
| 3 · Poner en marcha | Arrancar el proceso y esperar su verificación de salud | Paso 4 |

**El aporte del planteo es partir la fase 1 en dos.** Hoy esa fase enumera cuatro maneras alternativas de terminar con una imagen; el planteo dice que en realidad son **dos preguntas encadenadas**: de dónde sale la imagen base, y si se la ajusta o no. Todo lo demás —que las fases 2 y 3 son idénticas en todos los casos, que eso funda el alta unificada, que el orden de arranque aplica sólo a la fase 3— **queda intacto**.

### L.2 Lo que el refinamiento destapa: el modelo no puede expresar una de las cuatro combinaciones

Si las dos preguntas son independientes, el espacio real es un cuadro de dos por dos:

| | **Sin ajuste** | **Con archivo de construcción** |
|---|---|---|
| **Base de un registro** | `imagen-publica` / `imagen-privada` | `dockerfile` |
| **Base del almacén local** | `imagen-local` | **no existe** |

La celda vacía es exactamente la pregunta del punto K-5. **No tiene respuesta porque el modelo no tiene dónde alojarla**: el campo de origen es unidimensional y trata `dockerfile` como hermano de `imagen-local`, cuando en realidad son respuestas a preguntas distintas.

**Y hay una consecuencia peor, que no es de interfaz.** `RN-08` declara qué datos exige cada variante: `imagen-privada` exige registro, imagen, etiqueta **y la credencial de registro**; `dockerfile` exige **únicamente el contenido del archivo**. Es decir que la imagen base de una construcción vive **dentro del texto** de la primera línea del archivo, opaca para el modelo. De ahí se siguen tres cosas verificables:

1. **Un archivo de construcción cuya base está en un registro privado no tiene dónde declarar su credencial.** El modelo no admite ese campo en esa variante —«un campo que pertenece a otra variante es dato inválido»—, así que la construcción va a fallar al intentar bajar la base, y el fallo va a aparecer recién en tiempo de ejecución.
2. **La verificación del origen no puede verificar la base.** Para las variantes de imagen, la verificación comprueba que la imagen y la etiqueta existan y devuelve el digesto; para `dockerfile` sólo comprueba que el contenido sea interpretable y no lleve instrucciones de copia local. La base no se verifica porque el modelo no sabe cuál es.
3. **No se puede responder «de qué imagen partió esto» sin interpretar texto libre**, lo que alcanza a la trazabilidad de los despliegues y a la higiene de imágenes: una base que sostiene una construcción no figura como usada por nadie.

Ninguna de las tres es un defecto de la maqueta ni de la especificación de experiencia: son del modelo, y se hacen visibles recién cuando alguien describe el proceso como cadena en vez de como menú.

### L.3 Qué cambiaría en la interfaz, y qué no

**Lo que no cambia.** El alta sigue siendo **una sola entrada**, que es lo decidido el 2026-07-31 y cuyo fundamento —«con una tarjeta te alcanza; el concepto es simple, es decir de dónde va a obtener la imagen»— este planteo refuerza en vez de contradecir. `F-10` sigue acotada: construir desde archivo declarado, sin contexto y sin compilar código. Adoptar y el catálogo siguen fuera del alta.

**Lo que cambia.** El apartado de origen deja de ser un campo de tres valores y pasa a ser **dos campos encadenados**:

| Campo | Valores | Datos que exige |
|---|---|---|
| **De dónde sale la imagen** | De un registro · De este servidor | Registro, imagen, etiqueta y credencial si el registro la pide · o la imagen elegida de la lista del almacén |
| **¿La ajustás?** *(opcional, apagado por defecto)* | No · Sí, con un archivo de construcción | Nada · el contenido del archivo y sus argumentos |

Con eso, las tres opciones de hoy dejan de competir y **la cuarta combinación pasa a existir sin agregar ninguna opción nueva a la pantalla**: es la casilla de ajuste marcada sobre una base local.

**Efecto lateral que conviene mirar.** La variante persistida podría seguir llamándose como hoy y derivarse de la combinación, igual que la variante pública o privada ya se deriva de si hay credencial. Es una decisión de modelo, no de interfaz.

### L.4 Lo que ya está previsto y no hace falta abrir

Tres cosas del planteo ya tienen tratamiento y conviene no reabrirlas:

- **Elegir la imagen de una lista del almacén local.** Ya está especificado así: la variante local declara «la referencia de la imagen del almacén, elegida de lo que el almacén tiene».
- **Administrar las imágenes para hacer limpieza.** Existe como superficie propia, `SUP-18`, con la higiene de imágenes como caso de uso y con la limpieza **sugerida** ya decidida el 2026-07-30. Quedan abiertas tres pendientes de ese eje: la marca de pertenencia, el ámbito de credencial de la limpieza y la protección de la imagen conservada.
- **Que el panel llame al motor de contenedores para descargar e instanciar.** Es el sustrato declarado del producto y su punto de extensión: todo pasa por una única abstracción del motor. No hay nada que decidir acá.

### L.5 Decisiones

- **L-1 · ¿Se adopta el modelo de dos ejes?** El apartado de origen pasa de un campo de tres valores a dos campos encadenados: de dónde sale la imagen base, y si se ajusta con un archivo de construcción. Si sí, propaga a `02-Especificacion-Funcional` —`RN-08`, el modelo conceptual y los casos de uso de despliegue—, al intake —anexos de origen y de despliegue— y a `03-UX-UI-DX` —`SUP-17` y el panel lateral, que exhibe el origen en lectura—. Es una intervención grande y conviene decidirla antes de seguir corrigiendo rótulos.

  **Decisión L-1:**

- **L-2 · ¿Cómo se declara la credencial cuando la base del archivo de construcción está en un registro privado?** Hoy no se puede. Con el modelo de dos ejes se resuelve solo, porque la credencial pertenece al primer campo y el ajuste al segundo. Sin el modelo de dos ejes hay que decidirlo aparte: o se admite el campo en la variante de construcción, o se declara explícitamente que la base tiene que ser pública.

  **Decisión L-2:**

- **L-3 · ¿La verificación del origen verifica la imagen base de una construcción?** Hoy no la verifica porque el modelo no sabe cuál es. Con la base como campo propio, se puede verificar antes de construir, igual que en las otras variantes.

  **Decisión L-3:**

- **L-4 · ¿La pantalla declara la frontera del producto?** Que se vea qué queda afuera —las fuentes, su compilación y la publicación de la imagen, que hacen el ejecutor de integración continua o una máquina cualquiera— y qué hace el panel: resolver la imagen, ajustarla si corresponde, crear el contenedor y ponerlo en marcha. Es lo que le faltaba al agente humano para ubicar su escenario, y se relaciona con `K-3`.

  **Decisión L-4:**

---

## 12. Tabla de decisiones

Resumen para completar de un vistazo. La columna «bloquea» indica si el paso 5 puede cerrarse sin resolverlo.

| # | Punto | Quién decide | ¿Bloquea el cierre del paso 5? | Decisión |
|---|---|---|---|---|
| A | Grupo de acciones de ejecución del panel lateral | Agente humano del proyecto | **Sí.** La maqueta muestra una de las dos versiones | |
| B | Servicio de ejemplo con la variante nueva y con cuarentena, y causa del fallo de arranque | Agente humano del proyecto | **Sí**, si querés validar el mensaje que ve el administrador | |
| C | Tres rótulos de interfaz sin declarar | Agente humano del proyecto, y `03` los incorpora | No, pero quedan como decisión de maqueta hasta que se declaren | |
| D | Tres desfases de conteo | `03-UX-UI-DX` | No | |
| E | Dos nombres para la misma variante | Agente humano del proyecto, y las dos categorías lo aplican | No, pero el audit lo va a levantar | |
| F | Celda «Vacío» de `SUP-11` | `03-UX-UI-DX` | No | |
| G | Cómo se distingue el contenedor en reposo del que está desplegándose | Agente humano del proyecto, y `05` lo materializa | No para la maqueta; **sí** para la Fase C | |
| H | Vocabulario obsoleto | Cerrado | No | Corregido el 2026-08-02 |
| I | Distinción visual de las aristas que declaran espera (`B-UX-01`) | Agente humano del proyecto | No, pero es de las que más conviene cerrar: está abierta desde la Fase A y la maqueta ya dibuja una propuesta concreta | |
| J-1 | Recuperación del despliegue anterior: automática u ofrecida | Agente humano del proyecto | No. Si es ofrecida, hay que dibujar un control que hoy no existe | |
| J-2 | La cuarentena apaga el autoarranque o la participación en el arranque | Agente humano del proyecto | No. Hoy se rotula «autoarranque apagado» declarando que la elección no está tomada | |
| J-3 | Dónde se avisa que un servicio quedó en cuarentena | Agente humano del proyecto | No. Hoy sólo hay visibilidad persistente, que se deriva | |
| J-4 | Qué levanta la cuarentena | Agente humano del proyecto | No para la maqueta, **sí para que exista la acción**: hoy no se dibuja ni deshabilitada | |
| K-1 | Rótulos del campo de origen, en lenguaje del usuario | Agente humano del proyecto | No, pero es lo que disparó la ronda | |
| K-2 | `Q-10`: el panel genera el fragmento para el automatismo | Agente humano del proyecto | No. Si es que sí, agrega superficie a especificar | |
| K-3 | El alta declara el eje del disparador | Agente humano del proyecto | No | |
| K-4 | Puntero a adoptar y al catálogo desde el alta, o revertir la salida | Agente humano del proyecto | No | |
| K-5 | Si el archivo de construcción puede partir de una imagen del almacén local | Agente humano del proyecto | No, pero **si nadie lo impide el motor lo permite**: hoy el producto no sabe qué hace | |
| K-6 | Si la pantalla ofrece un ejemplo editable del archivo de construcción | Agente humano del proyecto | No | |
| **L-1** | **El origen pasa a ser dos campos encadenados: base y ajuste opcional** | Agente humano del proyecto | No bloquea el paso 5, pero **conviene decidirlo antes que K-1 a K-6**: si se adopta, cambia la pantalla sobre la que se están corrigiendo los rótulos | |
| L-2 | Credencial de la base privada en la variante de construcción | Agente humano del proyecto | No. Hoy **es imposible declararla** y el fallo aparece en ejecución | |
| L-3 | Si la verificación del origen verifica la imagen base de una construcción | Agente humano del proyecto | No | |
| L-4 | Si la pantalla declara la frontera del producto | Agente humano del proyecto | No. Se solapa con `K-3` | |

Y las cuatro brechas abiertas de §3, que no son hallazgos de esta ronda pero condicionan lo que la maqueta puede demostrar:

| Brecha | ¿Se decide ahora? |
|---|---|
| `B-UX-31` · los cuatro puntos de la política de cuarentena | Desarrollada como **punto J**, con una decisión por punto. Es la ausencia más visible de la maqueta |
| `B-UX-33` · protección de la imagen conservada | Puede esperar |
| `B-UX-32` · variante `repositorio` en lectura | No requiere decisión: está declarada |
| `B-UX-28` · valores del umbral de limpieza | Puede esperar; hoy se maquetan sin valor por defecto |

---

## 13. Qué pasa después de que decidas

1. **Ciclo de corrección** (paso 5, iterativo). Cada decisión que cambie lo que se ve vuelve a la maqueta. Podés pedir los cambios o editarla a mano; si la editás, el orquestador relee lo editado, declara qué entendió y espera confirmación antes de propagarlo. La fase no cierra sin tu aprobación explícita.
2. **Retroalimentación** (paso 6). Las decisiones se consolidan en el `PRODUCT-INTAKE` y se propagan a las categorías que la matriz de propagación alcance. Si alcanzan a `00-Contexto` o a `01-Necesidades-Negocio`, que son de nivel producto, el orquestador se detiene y te pide confirmación antes de tocarlas.
3. **Captura del modelo UX-UI** (paso 7, opcional). Se te ofrece registrar el diseño de esta maqueta como modelo reutilizable en el repositorio del framework, con su ejemplo ofuscado. El catálogo hoy está vacío: este sería el primero. Requiere aceptación explícita y la verificación de ofuscación es bloqueante, porque ese repositorio es público.
4. **Línea de base del sensado de deriva** (paso 8). Se emiten `Linea-Base-Visual.md`, `Contrato-Datos-Maqueta.md` y `Bitacora-Validacion-Maqueta.md` en `03-UX-UI-DX`, y `Matriz-Sensado-Deriva.md` en `08-Calidad-Y-Pruebas`, cuya carpeta todavía no existe. Es el instrumento con el que después se verifica, sprint a sprint, que lo construido sigue siendo lo aprobado.
5. **Audit independiente de la Fase B2** y recién entonces la **Fase C**, que es la arquitectura técnica.

---

## Control de cambios

| Versión | Fecha | Cambios | Autor |
|---|---|---|---|
| 1.5 | 2026-08-04 | **Incorporación del punto L**, el origen como composición de dos ejes, a partir del planteo del agente humano del proyecto que describió el despliegue como cadena de pasos en lugar de menú de alternativas. Sube **minor**: agrega un punto y no altera ninguno de los once anteriores ni ninguna decisión tomada, aunque **el punto propone una intervención de modelo y no de interfaz**, que es la de mayor alcance del documento. Se transcribe el planteo, se lo reconcilia con las **tres fases del despliegue** ya decididas el 2026-07-31 —el planteo no las contradice: parte la fase 1 en dos, «resolver la base» y «ajustar», y deja intactas las fases 2 y 3 y el fundamento del alta unificada—, y se muestra lo que el refinamiento destapa: el espacio real es un cuadro de dos por dos del que el campo de origen, unidimensional, **no puede expresar una de las cuatro combinaciones**, que es exactamente la pregunta del punto K-5. Se declaran las tres consecuencias verificables de que la imagen base de una construcción viva dentro del texto del archivo y no como campo: una base privada **no tiene dónde declarar su credencial** y el fallo aparece en ejecución, la verificación del origen no puede verificar la base, y la trazabilidad y la higiene de imágenes no ven esa base como usada. §L.3 declara qué cambiaría en la interfaz y qué no —el alta sigue siendo una sola entrada—, §L.4 enumera lo que ya está previsto y no hay que reabrir, y §L.5 abre cuatro decisiones. §12 suma cuatro filas y declara que **L-1 conviene decidirlo antes que K-1 a K-6**, porque cambia la pantalla sobre la que se están corrigiendo los rótulos. El punto K-5 queda subsumido y remite acá. | Orquestador SDD |
| 1.4 | 2026-08-04 | **Lectura del punto K por el agente humano del proyecto, y sus consecuencias.** Sube **minor**: precisa un punto y agrega dos, sin alterar los diez anteriores ni ninguna decisión tomada. **Los dos primeros rótulos quedan validados**, con las palabras del agente humano citadas como evidencia de que se entienden sin conocer el sistema. **El tercero se reescribe**: la lectura lo calificó de regular y produjo cuatro preguntas que la propuesta no contestaba —publicada dónde, si la base puede ser local, si se puede crear una imagen a partir de otra, si hace falta un archivo que la cite—. La versión nueva nombra el registro, declara que se parte de una imagen que ya existe y **suma el límite que ninguna versión del rótulo declaraba**: sin contexto de construcción no se pueden copiar archivos locales ni compilar código, que es lo que declara `F-10` acotada y lo que separa esta capacidad del ejecutor de integración continua. **Punto K-5 nuevo**: si el archivo de construcción puede partir de una imagen del almacén local. Ninguna fuente lo declara, y la particularidad es que **el motor lo resuelve solo** —`FROM` busca primero en el servidor—, de modo que si nadie lo impide va a funcionar sin que el producto lo haya decidido. **Punto K-6 nuevo**: si la pantalla ofrece un ejemplo editable del archivo de construcción, con el caso canónico que el anexo E-2 del intake ya trae. §12 suma dos filas. | Orquestador SDD |
| 1.3 | 2026-08-04 | **Incorporación del punto K**, el lenguaje del alta de servicio y el eje del disparador que la superficie no muestra, por la observación del agente humano del proyecto sobre `SUP-17`. Sube **minor**: agrega un punto y no altera los diez anteriores. Declara que los tres rótulos observados **no los inventó la maqueta** sino que están transcriptos del wireframe, de modo que son especificación aprobada y auditada, y que el escenario «desplegar desde GitHub» vive en el eje del disparador y no en el del origen, con la cita de la decisión del 2026-07-31 que había identificado esa misma confusión como causa del defecto anterior. Cuatro decisiones: los rótulos propuestos, `Q-10` sobre el fragmento para el automatismo, si el alta declara el eje del disparador, y si el alta suma el puntero a las otras dos formas de tener un servicio o se revierte su salida del alta. §12 suma cuatro filas. El análisis completo, con la lectura de framework y los cinco cambios normativos que propone, se emite aparte en `InformUX-UI.md`. | Orquestador SDD |
| 1.2 | 2026-08-04 | **Incorporación del punto J**, los cuatro puntos abiertos de la política de cuarentena (`B-UX-31`), a pedido del agente humano del proyecto tras encontrarlo rotulado en el lienzo. Sube **minor**: agrega un punto y no altera ninguno de los nueve anteriores. Es el complemento del punto I y funciona al revés —acá la maqueta **no dibuja** en lugar de proponer—. Se separa lo decidido de lo abierto: el cuerpo de la política está tomado desde el 2026-07-31, y quedan **cuatro** puntos sin decidir, los tres que el intake declara más el que `02` levantó al especificar el caso de uso. Se documenta por qué la visibilidad persistente **se deriva** y por lo tanto se dibuja, qué cuatro cosas no se dibujan y de qué punto depende cada ausencia, y por qué esa disciplina no es prolijidad: dibujar la acción de retirar la cuarentena decidiría por la puerta de atrás un punto abierto, que es el error por el que el audit del 2026-08-01 rechazó la ronda anterior. Cada punto lleva sus opciones con la consecuencia de cada una y su propia línea de decisión. §12 suma cuatro filas, una por punto, y la fila de `B-UX-31` de la tabla de brechas remite acá. | Orquestador SDD |
| 1.1 | 2026-08-04 | **Incorporación del punto I**, la propuesta de distinción visual de las aristas del lienzo (`B-UX-01`), a pedido del agente humano del proyecto tras encontrarla rotulada en la superficie. Sube **minor**: agrega un punto y no altera ninguno de los ocho anteriores. Es de otra clase que el resto —no es un hallazgo de la reconstrucción sino una propuesta que la maqueta emite y que está esperando aprobación desde la Fase A—, y se declara así en el propio punto. Se documenta qué es una arista y sus dos ejes, por qué la brecha no se pudo resolver por derivación —ni el catálogo de diseño ni el anexo E-18 cubren representación de aristas—, las tres salidas que la maqueta tenía y por qué eligió declarar la propuesta en pantalla, la representación concreta de las tres clases, las tres restricciones que cualquier alternativa debe cumplir, y el detalle de que la clase «no declara espera» no aparece dibujada porque el parque del anexo E-1 no la instancia. §12 suma su fila. | Orquestador SDD |
| 1.0 | 2026-08-03 | Volcado inicial del análisis de la reconstrucción de la maqueta del 2026-08-02. Recoge el estado de los pasos de la fase, qué se rehizo y qué se conservó, las cuatro ausencias deliberadas que las brechas abiertas imponen, y los ocho puntos que la construcción dejó abiertos, cada uno con su evidencia, su consecuencia aguas abajo y sus opciones. Los puntos A y B se declaran bloqueantes del cierre del paso 5; el G no bloquea la maqueta pero sí la Fase C. | Orquestador SDD |
