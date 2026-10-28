# 04 · Reglas de negocio

> Índice destilado del catálogo completo de reglas de negocio (**RN-01 a RN-40**) del producto **SelfHosted Service**. Sirve para ubicar la regla correcta —su enunciado, la entidad que restringe, cuándo se evalúa, qué pasa si se viola y qué casos de uso la invocan— sin abrir los cuarenta documentos.
>
> **Fuente primaria:** `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/` (40 archivos, uno por regla; las copias de `_legacy/` quedan fuera de este índice).
> **Complemento:** las restricciones estructurales del modelo persistido no viven acá sino en las reglas conceptuales `RC-01..RC-19`, indexadas en [`05_Modelo-De-Datos.md`](05_Modelo-De-Datos.md), que además declara la correspondencia RN ↔ RC y la trazabilidad entidad → RN.
>
> Todo lo que sigue proviene de los documentos leídos. Lo que las fuentes no declaran se marca explícitamente.

---

## 1. Anatomía de un documento RN

Los cuarenta archivos comparten una estructura fija de siete secciones obligatorias, más una cabecera de metadatos.

| Bloque | Contenido | Uso al consultar |
| --- | --- | --- |
| Cabecera | Producto, documento, versión, estado, fecha, autor | Todas las RN leídas están en versión **2.0**, estado **Propuesto**, fecha **2026-07-30** |
| Trazabilidad upstream | Apunta siempre al `PRODUCT-INTAKE-SelfHosted-Service`, **anexo E-16**, fila homónima, más la **autoría declarada en la fuente** | Distingue si el enunciado es `[E]` (de la fuente base), `[D]` (decisión) o `[D-i]` (especificación derivada, a veces sin revisar) |
| §1 Enunciado de la regla | La invariante en sí | Es la sección canónica: la RN es lo que dice acá |
| §2 Justificación | Por qué existe, y con qué decisión del intake se sostiene | Explica los límites de la regla, no los amplía |
| §3 Ámbito de aplicación | **Momento de validación declarado en el anexo E-16** + la lectura en flujo | Responde «¿en qué operación se dispara?» |
| §4 Consecuencia si se viola | Código de respuesta, invariante verificable, o aviso | Es de donde se lee el carácter bloqueante o no bloqueante |
| §5 CU afectados | Lista de casos de uso | Trazabilidad hacia `Casos-De-Uso/` |
| §6 Pruebas que la verifican | Casos `T-xx` con entrada y resultado esperado | El anexo E-22 los declara; se ejecutan en `08-Calidad-Y-Pruebas` |
| §7 Control de cambios | Historial de versiones | Casi todas: 1.0 inicial (2026-07-29) + 2.0 migración normativa léxica |

**Nota declarada en las fuentes:** la migración 1.0 → 2.0 fue **léxica y de forma de cabecera**; el enunciado normativo de las reglas **no cambió de contenido** en ninguna de ellas.

**Criterio de emisión declarado:** cada regla transcribe el enunciado, el momento de validación y la respuesta ante incumplimiento que declara el anexo E-16, sin reinterpretarlos, y el catálogo está pensado para que **cada regla se traduzca en una prueba automatizada**.

---

## 2. Clasificación bloqueante / no bloqueante

Las fuentes **no declaran un campo explícito de clasificación**. Lo que sí declaran, regla por regla, es la §4 «Consecuencia si se viola», y de ahí se lee el carácter. Las etiquetas literales «bloqueante» y «no bloqueante» aparecen sólo en tres documentos: **RN-36** y **RN-37** («aviso no bloqueante») y **RN-38** («marcado como bloqueante» en el informe de validación de la configuración).

| Familia de consecuencia | Qué significa | Carácter | RN que la usan |
| --- | --- | --- | --- |
| `422` | Rechazo de la operación señalando el campo, la expresión o la causa | Bloqueante | RN-01, RN-05, RN-06, RN-07, RN-08, RN-15 (sólo el valor por defecto sobre parámetro secreto), RN-18, RN-19, RN-21, RN-22, RN-28, RN-29, RN-32, RN-34, RN-38 |
| `409` | Conflicto con un estado existente, con informe de quién lo produce | Bloqueante | RN-02, RN-03, RN-11, RN-27, RN-34 (segunda arista de espera sin variable) |
| Invariante verificable por prueba | No hay respuesta de error: el sistema no puede llegar a violarla; su violación es un defecto | Estructural | RN-09, RN-12, RN-14, RN-16, RN-17, RN-24, RN-25, RN-26, RN-30, RN-31, RN-33, RN-35, RN-39 |
| Enlace marcado inválido | El objeto queda marcado y el arranque se detiene | Bloqueante en arranque | RN-04 |
| Estado declarado | El sistema entra en un estado explícito en lugar de fallar en silencio | No bloquea la operación | RN-20 |
| Aviso informativo / conversión con informe | La operación procede y se informa qué se hizo | No bloqueante | RN-15 (guardado como plantilla), RN-36, RN-37, RN-40 |
| Interacción obligatoria | La operación exige un paso de confirmación de la persona | Bloqueante por interacción | RN-10 |
| Enmascarado `***` | El dato se devuelve ofuscado en toda respuesta y exportación | Estructural | RN-15, RN-23 |

**Precisión declarada en RN-38, que vale como criterio general de la validación de configuración:** un hallazgo bloqueante **bloquea el paso del servicio a `pendiente-de-aplicar`** pero **no bloquea guardar** — el servicio se puede guardar como borrador y corregir después.

---

## 3. Catálogo RN-01 a RN-40

Cuarenta reglas, una por archivo, verificadas sobre disco. Agrupadas por área temática; la numeración no es contigua dentro de cada grupo.

### 3.1 Identidad, nombres y unicidad

| ID | Enunciado en una línea | Entidad / área afectada | Momento de validación | Consecuencia | CU que la invocan |
| --- | --- | --- | --- | --- | --- |
| RN-01 | El nombre de servicio es único en su proyecto, minúsculas, guiones, 1–32 caracteres; se exige porque es el alias DNS, no porque identifique | Servicio | Alta y edición | `422`; al instanciar no rechaza, sufija (RN-36) | CU-03, CU-07, CU-08, CU-11, CU-16 |
| RN-02 | Un servicio pertenece a un único proyecto SelfHosted | Servicio, Proyecto | Alta y adopción | `409` | CU-03, CU-07, CU-11, CU-16 |
| RN-11 | Un contenedor adoptado no puede adoptarse dos veces | Servicio adoptado, Contenedor | Descubrimiento y adopción | Aparece deshabilitado con el proyecto que lo tomó | CU-06, CU-07 |
| RN-28 | La clave de una variable compartida **no** exige unicidad en el proyecto (sólo formato); la de una variable de servicio **sí** dentro de su servicio, porque es el contrato con el proceso | Variable compartida, Variable de servicio | Alta y edición | `422` sólo por formato, o por clave duplicada dentro de un servicio | CU-03, CU-34, CU-35, CU-36 |
| RN-33 | Renombrar un elemento referenciado no invalida ninguna referencia ni produce cambios pendientes; se logra persistiendo la expresión en forma vinculada y renderizando nombres al mostrar | Servicio, Variable de servicio | Renombrado de un servicio o de una variable | Invariante verificable | CU-02, CU-03, CU-04, CU-22, CU-25, CU-34, CU-35 |
| RN-35 | Las relaciones entre objetos se establecen por identidad y nunca por nombre; todo lo referenciable, sobreviviente o con ciclo de vida propio es objeto con identidad | Todas (secreto y red del proyecto en particular) | Alta y edición de cualquier vínculo | Invariante del modelo | CU-01, CU-02, CU-03, CU-04, CU-34, CU-35, CU-36 |

### 3.2 Red, direcciones y puertos

| ID | Enunciado en una línea | Entidad / área afectada | Momento de validación | Consecuencia | CU que la invocan |
| --- | --- | --- | --- | --- | --- |
| RN-03 | Dos servicios activos de proyectos distintos no pueden compartir dirección IP | Reserva de dirección, Red del proyecto | Arranque de proyecto o servicio | `409` con informe y tres resoluciones: detener el otro proyecto, reasignar a la siguiente libre, o arrancar parcialmente | CU-18, CU-19, CU-20, CU-21, CU-24 |
| RN-06 | Toda dirección IP fija debe pertenecer al rango gestionado y no estar excluida | Reserva de dirección | Alta y edición | `422` con la siguiente dirección libre sugerida | CU-03, CU-19, CU-20, CU-21 |
| RN-07 | Un servicio en macvlan no puede publicar puertos en el host | Servicio, Red del proyecto | Alta y edición | Campo deshabilitado en la interfaz; `422` desde la API | CU-03, CU-08, CU-11 |
| RN-18 | El escalado horizontal crea réplicas con nombres sufijados y sin dirección IP fija duplicada | Servicio, Reserva de dirección | Cambio de réplicas | `422` si hay una sola dirección fija y se piden más réplicas | CU-19 |
| RN-19 | El escalado vertical no puede exceder los recursos declarados del host | Servicio (recursos) | Cambio de límites | `422` con el máximo admisible | CU-03 |
| RN-38 | Un puerto del host no puede ser publicado por más de un servicio; no aplica a macvlan (RN-07) y **sí** aplica a servicios pendientes de aplicar | Servicio (puertos) | Alta, edición y validación de la configuración | `422` con el servicio y proyecto que ya lo publican y el próximo libre; bloquea el paso a pendiente, no el guardado. **CU-24 queda fuera a propósito**: la aplicación en lote no reevalúa | CU-03, CU-06, CU-08, CU-13, CU-15 |

### 3.3 Grafo de dependencias y enlaces

| ID | Enunciado en una línea | Entidad / área afectada | Momento de validación | Consecuencia | CU que la invocan |
| --- | --- | --- | --- | --- | --- |
| RN-04 | Toda arista que referencia el host de otro servicio exige canal alcanzable entre origen y destino según sus modos de red; ninguna otra arista lo exige, y la exigencia es independiente de la espera | Enlace, Variable provista por el sistema | Aplicación del changeset | Enlace marcado inválido; bloquea el arranque | CU-04, CU-18, CU-24 |
| RN-05 | El grafo de arranque —subgrafo de las aristas que declaran espera— no puede tener ciclos; un ciclo sin espera lo alcanza RN-22 si además es ciclo de valor | Enlace | Creación de enlace y cambio de la propiedad de espera | `422` señalando el ciclo | CU-04, CU-11, CU-18 |
| RN-14 | El arranque del proyecto respeta el orden topológico del grafo de arranque; una arista que no declara espera dibuja el vínculo y marca redespliegue, pero no ordena | Enlace, Despliegue | Arranque | Invariante | CU-04, CU-18 |
| RN-34 | Toda arista debe aportar: o referencia una variable del destino (con las dos claves o ninguna), o declara espera, o ambas; el sistema propone la espera y el usuario la cambia; máximo una arista de espera sin variable entre el mismo par | Enlace | Creación y edición de la arista | `422` si no referencia ni espera; `409` en la segunda arista de espera sin variable | CU-04, CU-08, CU-11, CU-16, CU-35 |

### 3.4 Variables y referencias

| ID | Enunciado en una línea | Entidad / área afectada | Momento de validación | Consecuencia | CU que la invocan |
| --- | --- | --- | --- | --- | --- |
| RN-21 | Toda referencia resuelve a una variable de ámbito válido, y los ámbitos son exactamente tres: propio servicio, compartida del proyecto, u otro servicio del mismo proyecto; la variable apuntada debe existir ya al validar | Variable de servicio | Alta y edición de la variable, aplicación del conjunto de cambios y creación del contenedor | `422` con la causa: clave inexistente, servicio inexistente o servicio de otro proyecto | CU-04, CU-11, CU-13, CU-15, CU-16, CU-24, CU-33, CU-34, CU-35 |
| RN-22 | Las referencias no pueden formar ciclo de valor; ningún ciclo puede atravesar el nivel proyecto porque una variable compartida nunca contiene una referencia | Variable de servicio, Variable compartida | Alta y edición de la variable, y resolución previa al despliegue | `422` señalando la cadena completa del ciclo | CU-16, CU-24, CU-34, CU-35 |
| RN-23 | El carácter de secreto se propaga por la referencia, incluso interpolada; ninguna variable provista por el sistema es secreta, así que referenciarla nunca dispara la propagación | Variable de servicio, Secreto | Resolución, toda respuesta de la API y toda exportación | Enmascarado `***`; el valor resuelto no se persiste en claro | CU-09, CU-10, CU-13, CU-34, CU-35 |
| RN-24 | La referencia se resuelve en el backend inmediatamente antes de crear el contenedor; el contenedor recibe el valor, nunca la expresión | Variable de servicio, Despliegue | Creación del contenedor | Invariante; una referencia no resoluble aborta ese servicio con causa (RN-21) sin afectar a los demás (RN-31) | CU-13, CU-15, CU-16, CU-18, CU-24, CU-33, CU-35, CU-38 |
| RN-27 | No se puede eliminar una variable compartida, ni una variable referenciada desde otro servicio, mientras exista una referencia vigente | Variable compartida, Variable de servicio | Eliminación de la variable | `409` con la lista de servicios y claves que la referencian | CU-03, CU-22, CU-25, CU-34, CU-35 |
| RN-29 | La incorporación de un contenedor no se completa sin el paso de clasificación de variables; la heurística premarca secretas pero sugiere, no decide | Servicio adoptado, Variable de servicio | Adopción | `422` al confirmar sin clasificación; el servicio no se crea | CU-06, CU-07, CU-08 |
| RN-32 | Cada servicio expone variables provistas por el sistema, de sólo lectura, exactamente dos —host interno y nombre del servicio—; no hay variable de puerto; llevan prefijo reservado y ninguna es secreta | Variable provista por el sistema | Alta y edición de variables | `422` al declarar o editar una clave con el prefijo reservado | CU-03, CU-04, CU-35 |

### 3.5 Origen y ciclo de vida del servicio

| ID | Enunciado en una línea | Entidad / área afectada | Momento de validación | Consecuencia | CU que la invocan |
| --- | --- | --- | --- | --- | --- |
| RN-08 | Cada variante de origen exige sus propios datos obligatorios y **ninguno de otra variante**; un campo ajeno es dato inválido, no campo opcional vacío. **Seis variantes**: registro público, registro privado, repositorio remoto, archivo de construcción en línea, sin origen, e imagen del almacén local (exige digesto y prohíbe política flotante) | Servicio (origen), Imagen | Alta, edición del origen y verificación del origen | `422` distinguiendo **campo faltante** de **campo ajeno a la variante** | CU-03, CU-08, CU-13, CU-15, CU-39 |
| RN-09 | Al detener un servicio, sus volúmenes y montajes no se tocan | Volumen o directorio de montaje | Detención | Invariante verificable | CU-18, CU-24 |
| RN-10 | Al eliminar un servicio se pide confirmación escribiendo su nombre, y se ofrece conservar los volúmenes | Servicio, Volumen o directorio | Eliminación | Interacción obligatoria | CU-02, CU-03 |

**Nota de RN-08 declarada en la fuente:** la variante `repositorio` sigue siendo exigible aunque **ninguna superficie del producto la produzca hoy** (`F-10` se acotó el 2026-08-01). La regla declara qué es válido, no qué produce la interfaz.

### 3.6 Cambios pendientes, arranque y despliegue

| ID | Enunciado en una línea | Entidad / área afectada | Momento de validación | Consecuencia | CU que la invocan |
| --- | --- | --- | --- | --- | --- |
| RN-12 | Los cambios visuales no entran al conjunto de cambios pendientes ni disparan redespliegue | Changeset, Proyecto (lienzo) | Edición del lienzo | Invariante | CU-03, CU-04, CU-05, CU-22, CU-23, CU-25 |
| RN-13 | Aplicar el conjunto de cambios pendientes redespliega sólo los servicios afectados | Changeset, Despliegue | Aplicación | El informe de impacto lo declara antes de ejecutar, con sus dos listas | CU-13, CU-21, CU-22, CU-23, CU-24, CU-25, CU-33, CU-38 |
| RN-20 | Un proyecto con al menos un conflicto puede arrancar parcialmente y queda `parcialmente-activo` | Proyecto, Despliegue | Arranque | Estado explícito, no error silencioso | CU-18, CU-20, CU-21, CU-24, CU-27, CU-28, CU-38 |
| RN-31 | El resultado de un despliegue se determina **por contenedor**, no por operación; la caída del circuito de la interfaz no lo altera y al reabrir el proyecto se verifica contra el motor; un despliegue parcial es estado legítimo | Despliegue | Despliegue y apertura del proyecto | Invariante; la operación en lote responde con el resultado de cada contenedor | CU-13, CU-15, CU-18, CU-24, CU-27, CU-28, CU-33, CU-38 |

**Nota de RN-20 declarada en la fuente:** la política de fallo al arrancar manda a **cuarentena** al servicio cuyo despliegue nuevo y cuyo despliegue anterior recuperado no arrancan, apagándole el autoarranque, y deja el proyecto corriendo. **Qué marca concreta se apaga —autoarranque o participación en el arranque— es el punto abierto `B-31` (b)** y no se resuelve en la regla; el enunciado no depende de cuál sea.

### 3.7 Seguridad, secretos y auditoría

| ID | Enunciado en una línea | Entidad / área afectada | Momento de validación | Consecuencia | CU que la invocan |
| --- | --- | --- | --- | --- | --- |
| RN-15 | Un secreto nunca se devuelve en texto plano por la API ni se escribe en una exportación; **alcanza también a la plantilla del catálogo**: guardar como plantilla convierte cada variable secreta en parámetro con generación automática, descarta el valor e informa; un parámetro secreto no admite valor por defecto | Secreto, Variable, Ítem del catálogo | Toda respuesta y exportación, más el guardado como plantilla y la publicación de un ítem | Enmascarado `***`. El guardado como plantilla **no es rechazo: es conversión con informe**. El valor por defecto sobre parámetro secreto sí se rechaza con `422` | CU-06, CU-07, CU-08, CU-09, CU-10, CU-12, CU-14, CU-16, CU-17, CU-32, CU-34, CU-35 |
| RN-16 | El token de API se muestra una única vez y sólo se persiste su resumen | Token de API | Creación de token | Invariante | CU-32, CU-33 |
| RN-17 | Toda operación de escritura queda registrada en auditoría con su actor | Evento de auditoría | Cada operación | Invariante | CU-01 a CU-05, CU-07, CU-11 a CU-13, CU-15 a CU-19, CU-21 a CU-24, CU-29 a CU-35, CU-37, CU-38 |

### 3.8 Plantillas del catálogo e instancias

| ID | Enunciado en una línea | Entidad / área afectada | Momento de validación | Consecuencia | CU que la invocan |
| --- | --- | --- | --- | --- | --- |
| RN-30 | Instanciar un ítem del catálogo crea **N servicios y N contenedores**, uno por nodo del subgrafo, más los enlaces; ningún servicio instanciado comparte contenedor con otro | Ítem del catálogo, Servicio, Despliegue | Instanciación | Invariante verificable | CU-16, CU-17 |
| RN-36 | Al instanciar, si un nombre ya existe en el proyecto destino el sistema **sufija automáticamente e informa**: no rechaza y no pregunta; una clave de variable ya existente no es conflicto (RN-37) | Ítem del catálogo, Servicio | Instanciación | Servicio creado con nombre sufijado y aviso **no bloqueante** | CU-16 |
| RN-39 | Cambiar o borrar un ítem del catálogo no afecta a los servicios ya instanciados: el vínculo es **débil y sólo en calidad de origen** — se guarda una **copia** del identificador, nombre y versión de contenido, no una clave foránea; borrar no advierte «en uso», el servicio sin plantilla no es huérfano, y una versión más nueva no se notifica | Ítem del catálogo, Servicio (procedencia) | Edición y borrado de un ítem; instanciación | Invariante; su violación se manifiesta como uno de cuatro defectos que la prueba busca | CU-16, CU-17 |

### 3.9 Interoperabilidad: exportación e importación

| ID | Enunciado en una línea | Entidad / área afectada | Momento de validación | Consecuencia | CU que la invocan |
| --- | --- | --- | --- | --- | --- |
| RN-25 | Una exportación al formato de composición nunca emite una expresión de referencia: emite el valor resuelto, o un marcador con archivo de variables vacío si es secreto; todo signo peso literal se emite escapado; la expresión sin resolver se preserva en el manifiesto propio | Servicio, Variable, Secreto | Exportación | Invariante verificable: ningún archivo exportado contiene la expresión sin escapar y el archivo levanta | CU-09, CU-10, CU-12, CU-35 |
| RN-26 | Una importación nunca deriva referencias de la interpolación del formato de composición: se traduce con las reglas del anexo E-21, el peso duplicado es escape, la expresión propia literal se importa como texto; la única referencia creada reexpresa una dependencia explícita cuyo literal ya coincide con host y puerto del destino | Servicio, Variable, Enlace | Importación | Invariante | CU-08, CU-11 |

### 3.10 Higiene del modelo e imágenes

| ID | Enunciado en una línea | Entidad / área afectada | Momento de validación | Consecuencia | CU que la invocan |
| --- | --- | --- | --- | --- | --- |
| RN-37 | El sistema detecta y advierte, **sin bloquear**, cinco condiciones de higiene: compartida sin referencias; dos elementos con el mismo nombre visible en el mismo ámbito; al instanciar, clave existente con el mismo valor (se ofrece reusar); al instanciar, clave existente con distinto valor (se crean separadas y se avisa); y referencia que quedó sin uso tras un cambio | Variable compartida, Ítem del catálogo, Servicio | Instanciación, alta y edición, y revisión periódica del proyecto | Aviso informativo. **Ninguna condición bloquea ninguna operación** | CU-16, CU-34, CU-35, CU-36 |
| RN-40 | Una imagen marcada como **conservada** no se limpia, aunque ningún despliegue activo la referencie; y una imagen **ajena** —sin marca de pertenencia del producto— no se toca | Imagen | Toda operación de limpieza de imágenes | La imagen se **excluye de la operación** y el informe declara el motivo: conservada o ajena; la limpieza procede sobre el resto | CU-15, CU-18, CU-37, CU-38 |

**Nota de RN-40 declarada en la fuente:** una limpieza que elimina una imagen protegida es «un defecto de la clase más grave que este producto puede tener», por ser **pérdida de datos ajenos y no recuperable**. `CU-18` entró el 2026-08-01 porque la recuperación del despliegue anterior por digesto depende de que la imagen siga existiendo.

---

## 4. Reglas que se citan entre sí

Relaciones explícitas declaradas en los enunciados, útiles para no aplicar una regla sin su vecina.

| Par | Relación declarada |
| --- | --- |
| RN-01 ↔ RN-36 | RN-01 rechaza el nombre duplicado en alta y edición; al instanciar del catálogo **no** rechaza y delega en el sufijado de RN-36 |
| RN-01 ↔ RN-35 | La unicidad del nombre es por resolución DNS; la identificación del objeto es tarea de la identidad (RN-35) |
| RN-05 ↔ RN-22 | Ciclo de **arranque** (aristas con espera) vs. ciclo de **valor** (cadena de referencias). Son reglas distintas |
| RN-04 ↔ RN-34 | RN-34 exige que la arista aporte algo; RN-04 sólo alcanza a las que referencian el host |
| RN-07 ↔ RN-38 | RN-38 no aplica a macvlan, porque RN-07 ya prohíbe publicar puertos ahí |
| RN-06 ↔ RN-38 | Mismo patrón de respuesta útil: no alcanza con decir que está tomado, hay que decir por quién y qué usar |
| RN-15 ↔ RN-23 | Mismo enmascarado `***`; RN-23 lo extiende a lo que hereda el carácter de secreto por referencia |
| RN-21 ↔ RN-24 | RN-24 resuelve antes de crear el contenedor; si no resuelve, aborta ese servicio con la causa de RN-21 |
| RN-24 ↔ RN-31 | El aborto de un servicio no afecta a los demás contenedores de la operación |
| RN-28 ↔ RN-37 | Dos compartidas con la misma clave no son error; el coincidir además en el valor es advertencia de higiene |
| RN-31 ↔ RN-40 | Mismo patrón de informe: la operación procede sobre el resto y declara qué dejó afuera y por qué |
| RN-13 ↔ RN-20 | El arranque parcial es el estado declarado cuando el redespliegue acotado no alcanza a todo |

---

## 5. Autoría y madurez del enunciado

Cada RN declara en su cabecera la **autoría que la fuente le asigna**. Es el dato que dice cuánto pesa el enunciado: `[E]` viene de la fuente base del intake, `[D]` es decisión cerrada del agente humano del proyecto, `[D-i]` es especificación derivada por el integrador, y las que siguen `sin revisar` **se consumen declarándose revisables**.

| Categoría | RN | Lectura |
| --- | --- | --- |
| `[E]` de la fuente base, completa | RN-02, RN-03, RN-06, RN-07, RN-09, RN-10, RN-11, RN-12, RN-13, RN-16, RN-17, RN-18, RN-19, RN-20 | Enunciado firme. RN-02 y RN-11 tienen su fundamento completado por la decisión D-3 (2026-07-28) |
| `[E]` con ampliación `[D-i]` **sin revisar** | RN-04, RN-05, RN-14 (la ampliación), RN-15 (alcance a la plantilla, `DI-21`) | El enunciado original es firme; **la ampliación es revisable** |
| `[E]` con ampliación `[D-i]` ya **confirmada** | RN-08 (reparto por variante, `DI-17`, confirmada el 2026-07-30 → `[D]`) | Deja de consumirse como revisable |
| `[D]` completa, decisión identificada | RN-24 y RN-32 (D-6), RN-28 y RN-35 (D-12), RN-30 (D-7), RN-31 (D-1), RN-36 y RN-37 (D-13), RN-39 (D-14, 2026-07-29) | Decisión cerrada. RN-01 tiene fundamento `[D]` D-12 sobre enunciado original `[E]` |
| `[D]` en el enunciado, `[D-i]` **sin revisar** en la exigibilidad | RN-21 (momento y códigos), RN-29 (código de rechazo), RN-32 (nombres y exigibilidad), RN-33 (forma) | El **qué** está cerrado; el **cómo se exige** es revisable |
| `[D-i]` completa, **sin revisar** | RN-22, RN-23, RN-25, RN-26, RN-27, RN-34 | Las seis reglas de menor respaldo del catálogo |
| `DI-17` confirmada el 2026-07-30 | RN-38, RN-40 | Pasan de `[D-i]` revisable a `[D]` cerrada; dejan de consumirse como propuesta del integrador |

---

## 6. Índice inverso CU → RN

Qué reglas hay que respetar al implementar cada caso de uso. Derivado de la §5 de los cuarenta documentos.

| CU | RN que lo alcanzan |
| --- | --- |
| CU-01 | RN-17, RN-35 |
| CU-02 | RN-10, RN-17, RN-33, RN-35 |
| CU-03 | RN-01, RN-02, RN-06, RN-07, RN-08, RN-10, RN-12, RN-17, RN-19, RN-27, RN-28, RN-32, RN-33, RN-35, RN-38 |
| CU-04 | RN-04, RN-05, RN-12, RN-14, RN-17, RN-21, RN-32, RN-33, RN-34, RN-35 |
| CU-05 | RN-12, RN-17 |
| CU-06 | RN-11, RN-15, RN-29, RN-38 |
| CU-07 | RN-01, RN-02, RN-11, RN-15, RN-17, RN-29 |
| CU-08 | RN-01, RN-07, RN-08, RN-15, RN-26, RN-29, RN-34, RN-38 |
| CU-09 | RN-15, RN-23, RN-25 |
| CU-10 | RN-15, RN-23, RN-25 |
| CU-11 | RN-01, RN-02, RN-05, RN-07, RN-17, RN-21, RN-26, RN-34 |
| CU-12 | RN-15, RN-17, RN-25 |
| CU-13 | RN-08, RN-13, RN-17, RN-21, RN-23, RN-24, RN-31, RN-38 |
| CU-14 | RN-15 |
| CU-15 | RN-08, RN-17, RN-21, RN-24, RN-31, RN-38, RN-40 |
| CU-16 | RN-01, RN-02, RN-15, RN-17, RN-21, RN-22, RN-24, RN-30, RN-34, RN-36, RN-37, RN-39 |
| CU-17 | RN-15, RN-17, RN-30, RN-39 |
| CU-18 | RN-03, RN-04, RN-05, RN-09, RN-14, RN-17, RN-20, RN-24, RN-31, RN-40 |
| CU-19 | RN-03, RN-06, RN-17, RN-18 |
| CU-20 | RN-03, RN-06, RN-20 |
| CU-21 | RN-03, RN-06, RN-13, RN-17, RN-20 |
| CU-22 | RN-12, RN-13, RN-17, RN-27, RN-33 |
| CU-23 | RN-12, RN-13, RN-17 |
| CU-24 | RN-03, RN-04, RN-09, RN-13, RN-17, RN-20, RN-21, RN-22, RN-24, RN-31. **RN-38 queda explícitamente fuera** |
| CU-25 | RN-12, RN-13, RN-27, RN-33 |
| CU-26 | **Ninguna RN lo cita.** No declarado en las fuentes leídas |
| CU-27 | RN-20, RN-31 |
| CU-28 | RN-20, RN-31 |
| CU-29 · CU-30 · CU-31 | RN-17 |
| CU-32 | RN-15, RN-16, RN-17 |
| CU-33 | RN-13, RN-16, RN-17, RN-21, RN-24, RN-31 |
| CU-34 | RN-15, RN-17, RN-21, RN-22, RN-23, RN-27, RN-28, RN-33, RN-35, RN-37 |
| CU-35 | RN-15, RN-17, RN-21, RN-22, RN-23, RN-24, RN-25, RN-27, RN-28, RN-32, RN-33, RN-34, RN-35, RN-37 |
| CU-36 | RN-28, RN-35, RN-37 |
| CU-37 | RN-17, RN-40 |
| CU-38 | RN-13, RN-17, RN-20, RN-24, RN-31, RN-40 |
| CU-39 | RN-08 |

**Los tres CU con más reglas encima** son CU-03 (alta y configuración de servicio, 15), CU-35 (14) y CU-16 (instanciación desde el catálogo, 12). **La RN de mayor alcance** es RN-17, presente en 27 casos de uso.

---

## 7. Índice inverso momento de validación → RN

Qué reglas hay que evaluar en cada punto del flujo. Los momentos son los que el anexo E-16 declara y que cada §3 transcribe.

| Momento de validación | RN a evaluar |
| --- | --- |
| Alta y edición de servicio | RN-01, RN-02 (alta y adopción), RN-06, RN-07, RN-08 (alta, edición y verificación del origen), RN-19 (cambio de límites), RN-38 (alta, edición y validación de la configuración) |
| Alta y edición de variables | RN-21, RN-22, RN-28, RN-32 |
| Creación y edición de aristas | RN-05 (creación de enlace y cambio de la espera), RN-34 |
| Aplicación del conjunto de cambios | RN-04, RN-13, RN-21 |
| Arranque de proyecto o servicio | RN-03, RN-14, RN-20 |
| Creación del contenedor / despliegue | RN-21, RN-24, RN-31 (despliegue y apertura del proyecto) |
| Detención y eliminación | RN-09 (detención), RN-10 (eliminación de servicio), RN-27 (eliminación de variable) |
| Adopción y descubrimiento | RN-02, RN-11, RN-29 |
| Instanciación desde el catálogo | RN-30, RN-36, RN-37, RN-39 (también edición y borrado del ítem) |
| Exportación e importación | RN-25 (exportación), RN-26 (importación), RN-15 y RN-23 (toda exportación) |
| Toda respuesta de la API | RN-15, RN-23 |
| Renombrado | RN-33 |
| Edición del lienzo | RN-12 |
| Creación de token | RN-16 |
| Cada operación de escritura | RN-17 |
| Alta y edición de cualquier vínculo | RN-35 |
| Limpieza de imágenes | RN-40 |
| Revisión periódica del proyecto | RN-37 |

---

## 8. Fuentes

Rutas relativas al repositorio indexado. Todos los archivos están en estado `Propuesto`, versión `2.0`, salvo indicación en su propia cabecera.

| Archivo | Qué contiene |
| --- | --- |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-01-Unicidad-Y-Formato-Del-Nombre-De-Servicio.md` | Unicidad y formato del nombre de servicio como alias DNS |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-02-Pertenencia-Del-Servicio-A-Un-Unico-Proyecto.md` | Un servicio pertenece a un solo proyecto SelfHosted |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-03-Exclusividad-De-Direccion-Entre-Servicios-Activos.md` | Exclusividad de IP entre proyectos activos y sus tres resoluciones |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-04-Canal-Alcanzable-En-La-Arista-Que-Referencia-El-Host.md` | Exigencia de canal alcanzable sólo en aristas que referencian el host |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-05-Aciclicidad-Del-Grafo-De-Arranque.md` | Prohibición de ciclos en el subgrafo de espera |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-06-Pertenencia-De-La-Direccion-Al-Rango-Gestionado.md` | Rango gestionado y exclusiones de la IP fija |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-07-Prohibicion-De-Publicar-Puertos-En-Macvlan.md` | Macvlan no publica puertos en el host |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-08-Datos-Obligatorios-Del-Origen-Repositorio.md` | Tabla de datos obligatorios por cada una de las seis variantes de origen |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-09-Conservacion-De-Volumenes-Al-Detener.md` | Los volúmenes no se tocan al detener |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-10-Confirmacion-Escrita-Al-Eliminar-Un-Servicio.md` | Confirmación escribiendo el nombre y opción de conservar volúmenes |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-11-Adopcion-Unica-De-Un-Contenedor.md` | Un contenedor se adopta una sola vez |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-12-Exclusion-De-Los-Cambios-Visuales-Del-Changeset.md` | El lienzo no entra al changeset |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-13-Redespliegue-Acotado-A-Lo-Afectado.md` | Redespliegue acotado y las dos listas del informe de impacto |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-14-Orden-Topologico-Del-Grafo-De-Arranque.md` | Orden topológico del arranque; la arista sin espera no ordena |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-15-Prohibicion-De-Devolver-Secretos-En-Claro.md` | Secretos enmascarados y conversión a parámetro al guardar como plantilla |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-16-Exhibicion-Unica-Y-Persistencia-Del-Resumen-Del-Token.md` | El token se muestra una vez; se persiste el resumen |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-17-Registro-De-Auditoria-De-Toda-Escritura.md` | Auditoría de toda escritura con su actor (la RN de mayor alcance de CU) |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-18-Escalado-Horizontal-Con-Direccion-Por-Replica.md` | Réplicas sufijadas y una dirección por réplica |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-19-Limite-Del-Escalado-Vertical-A-Los-Recursos-Del-Host.md` | Techo del escalado vertical en los recursos del host |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-20-Arranque-Parcial-Como-Estado-Declarado.md` | Arranque parcial, cuarentena y el punto abierto `B-31` (b) |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-21-Validez-Del-Ambito-De-Una-Referencia.md` | Los tres ámbitos válidos de una referencia |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-22-Prohibicion-Del-Ciclo-De-Valor.md` | Ciclo de valor y por qué no puede atravesar el nivel proyecto |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-23-Propagacion-Del-Caracter-De-Secreto.md` | Propagación del secreto por referencia, incluso interpolada |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-24-Resolucion-De-La-Referencia-Antes-De-Crear-El-Contenedor.md` | Momento exacto de resolución de referencias |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-25-Ausencia-De-Expresiones-En-La-Exportacion.md` | Exportación sin expresiones y escapado del signo peso |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-26-Ausencia-De-Referencias-Derivadas-De-La-Importacion.md` | Importación que no inventa referencias; reglas del anexo E-21 |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-27-Proteccion-De-La-Variable-Referenciada-Ante-La-Eliminacion.md` | Bloqueo de borrado de variable con referencias vigentes |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-28-Unicidad-De-La-Clave-Segun-El-Ambito-De-La-Variable.md` | Asimetría de unicidad entre clave compartida y clave de servicio |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-29-Clasificacion-Obligatoria-De-Variables-En-La-Adopcion.md` | Paso obligatorio de clasificación de variables al adoptar |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-30-Instanciacion-Como-N-Servicios-Y-N-Contenedores.md` | Instanciación como N servicios y N contenedores |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-31-Resultado-Del-Despliegue-Por-Contenedor.md` | Resultado por contenedor y despliegue parcial legítimo |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-32-Variables-Provistas-Por-El-Sistema-Y-Prefijo-Reservado.md` | Las dos variables provistas y el prefijo reservado |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-33-Invariancia-De-Las-Referencias-Ante-El-Renombrado.md` | Renombrar no rompe referencias; forma vinculada persistida |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-34-Aporte-Obligatorio-De-La-Arista.md` | Qué debe aportar toda arista y la propuesta de espera |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-35-Vinculo-Por-Identidad-Y-Nunca-Por-Nombre.md` | Vínculo por identidad; criterio de objeto con identidad |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-36-Sufijo-Automatico-Al-Instanciar-Un-Nombre-Existente.md` | Sufijado automático e informe al instanciar |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-37-Deteccion-No-Bloqueante-De-Higiene-Del-Modelo.md` | Las cinco condiciones de higiene, todas no bloqueantes |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-38-Unicidad-Del-Puerto-Publicado-En-El-Host.md` | Puerto único por host, alcance a pendientes y exclusión de CU-24 |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-39-Desvinculacion-De-La-Instancia-Respecto-De-La-Plantilla.md` | Vínculo débil por copia entre ítem del catálogo e instancia |
| `SDD/Docs/02-Especificacion-Funcional/Reglas-De-Negocio/RN-40-Proteccion-De-La-Imagen-Conservada-Y-De-La-Imagen-Ajena.md` | Protección de la imagen conservada y de la imagen ajena en la limpieza |
