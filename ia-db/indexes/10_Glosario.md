# 10 · Glosario consolidado

> Punto de entrada al vocabulario del corpus SDD de **SelfHosted Service**: qué significa cada término, de qué glosario sale y con qué se confunde. Fuente primaria: `SDD/Docs/02-Especificacion-Funcional/Glosario-Funcional.md` (vocabulario del dominio, 120 términos) y `SDD/Docs/03-UX-UI-DX/Glosario-UX.md` (vocabulario de superficie, 72 términos propios más 76 referenciados). **Este índice destila; no es canónico.** Cada definición de acá es una línea operativa para leer otros documentos: la definición canónica, sus artefactos de aparición, sus formas calificadas obligatorias y su evidencia de colisión viven en las dos fuentes. Ante cualquier discrepancia, mandan las fuentes. Los términos del **glosario raíz del dominio** (`00-Contexto/Vision-Producto.md` §9) no se redefinen en ninguna de las dos fuentes ni acá: se referencian, y se marcan en la columna de origen.

---

## Vocabulario de dominio

Los 120 términos de `Glosario-Funcional.md` §2, en el orden de sus diez subsecciones. La columna de origen indica la subsección exacta; `raíz` indica que el término lo declara `Vision-Producto.md` §9 y que el funcional sólo lo precisa (§4.1).

| Término | Definición en una línea | Glosario de origen | No confundir con |
| --- | --- | --- | --- |
| Registro | Término polisémico con **cinco** referentes; la forma desnuda «el registro» sólo vale para el registro del sistema y sólo si la sección ya lo fijó | Funcional §2.1 / §3.1 | Sus cinco referentes entre sí |
| Registro del sistema | El estado persistido del producto: proyectos, servicios, enlaces, despliegues, reservas, variables e ítems | Funcional §2.1 | Registro de auditoría; registro del producto |
| Administrador del producto | Actor humano primario: opera el panel, declara la arquitectura y confirma toda escritura | Funcional §2.1 | Registro del producto (actor de sistema) |
| Registro del producto | Actor de sistema que persiste y devuelve el registro del sistema | Funcional §2.1 | Registro del sistema (el dato, no el actor) |
| Identificador legible | Identificador de proyecto que el usuario declara y lee, **único en la instalación** | Funcional §2.1 | Nombre visible; identificador de objeto interno |
| Nombre visible | Denominación legible que la interfaz muestra; **no exige unicidad** | Funcional §2.1 | Identificador legible |
| Instalación | Ámbito de unicidad más amplio: un despliegue del sistema administrador sobre un host | Funcional §2.1 | Proyecto SelfHosted |
| Vía de alta | Cómo se llegó a crear un servicio; **no se persiste**, y desde el alta unificada es un eje trivial | Funcional §2.2 | Origen; disparador del despliegue |
| Origen | Qué se declara como fuente **de la imagen**; variante discriminada de seis valores, **se persiste** | Funcional §2.2 | Vía de alta; disparador |
| Variante de origen | Cada uno de los seis valores del origen, con su conjunto cerrado de campos obligatorios | Funcional §2.2 | Estar en el modelo ≠ ser producible por la interfaz |
| Disparador del despliegue | Tercer eje: quién inicia un despliegue (administrador, orquestador del proyecto, automatismo externo) | Funcional §2.2 | Vía de alta; origen |
| Imagen del almacén local | Sexta variante de origen: imagen que ya vive en el almacén local; exige digesto y marca no reproducible | Funcional §2.2 | Imagen de registro (remoto) |
| No reproducible | Atributo derivado del origen que declara que la arquitectura no puede reconstruirse en otra máquina | Funcional §2.2 | Marca editable: no lo es, se retira cambiando el origen |
| Imagen de registro público | Variante de origen sin credencial; exige registro, imagen y etiqueta | Funcional §2.2 | Imagen de registro privado |
| Imagen de registro privado | Igual que la pública más **credencial de registro** | Funcional §2.2 | Credencial de repositorio |
| Repositorio remoto | Variante de origen que construye desde un repositorio; **ninguna superficie la produce** desde la acotación de `F-10` | Funcional §2.2 | El repositorio donde vive el automatismo (CU-32/33) |
| Sin origen | Variante que no declara fuente de imagen: el tronco del alta detenido antes del origen | Funcional §2.2 | Una vía de alta con mecánica propia |
| Variante discriminada | Forma del modelo: un tipo declarado que exige un conjunto cerrado de campos y ninguno más | Funcional §2.2 | Objeto con campos opcionales |
| Verificación del origen | Consulta al sistema externo para comprobar que el origen declarado existe y es alcanzable | Funcional §2.2 | Informe de validación de la configuración |
| Registro de imágenes | Servidor externo del que se descargan imágenes, público o privado; el producto lo lee y no es su dueño | Funcional §2.2 | Almacén de imágenes (local); registro del sistema |
| Exploración de registro de imágenes | Operación de sólo lectura que recorre lo que un registro publica y devuelve la referencia completa | Funcional §2.2 | Una vía de alta; no reemplaza la verificación del origen |
| Credencial de registro | Credencial con la que el producto se autentica contra un registro privado | Funcional §2.2 | Credencial de repositorio |
| Archivo de construcción en línea | Variante de origen con el **contenido** del archivo de construcción; única construcción de imagen que conserva el producto | Funcional §2.2 | Repositorio remoto |
| Construir la imagen | Producir una imagen desde código o archivo de construcción; **puede vivir afuera del producto** | Funcional §2.2 / §3.10 | Crear el contenedor |
| Crear el contenedor | Instanciar una imagen con la configuración del servicio; **nunca sale del producto** | Funcional §2.2 / §3.10 | Construir la imagen |
| Estado de configuración | Estado del servicio como declaración: `borrador`, `pendiente-de-aplicar`, `aplicado` | Funcional §2.2 | Estado del despliegue (ortogonal) |
| Borrador | Servicio visible en el lienzo e incompleto de forma visible; **no entra al conjunto de cambios pendientes** | Funcional §2.2 | Pendiente de aplicar |
| Servicio declarado | Servicio que existe como configuración, tenga o no contenedor corriendo | Funcional §2.2 | Servicio activo |
| Efímero | Servicio pensado para reconstruirse en cada uso, sin estado persistente propio | Funcional §2.2 | El adjetivo corriente «efímero» de la prosa |
| Verificación de salud | Comprobación periódica que determina si el contenedor está sano | Funcional §2.2 | Verificación del origen |
| Montaje | Vínculo entre una ruta del contenedor y un volumen o directorio del host; sobrevive al servicio | Funcional §2.2 | Volumen o directorio de montaje (el objeto apuntado) |
| Volumen o directorio de montaje | El recurso de almacenamiento al que apunta un montaje; objeto con identidad que el modelo no diseña | Funcional §2.2 | Montaje (el vínculo) |
| Comando de arranque | Comando con el que arranca el contenedor; nulo hereda el de la imagen; cambiarlo **recrea** | Funcional §2.2 | Política de reinicio (cambiarla no recrea) |
| Política de reinicio | Regla de si el contenedor se reinicia solo, con cuatro valores del intake §12 | Funcional §2.2 | Política de fallo al arrancar |
| Réplica | Cada instancia paralela de un mismo servicio, con su contenedor y despliegue propios | Funcional §2.2 | Escalado vertical |
| Recursos declarados del host | Procesador y memoria que el host declara y que acotan el escalado vertical | Funcional §2.2 | Límites de recursos del servicio |
| Imagen | La imagen de contenedor como **objeto con identidad**, con ciclo de vida propio, pertenencia y marca de conservada | Funcional §2.2 | La cadena de imagen dentro del origen |
| Digesto | Identidad real de una imagen calculada sobre su contenido; responde qué corre exactamente | Funcional §2.2 | Etiqueta (reasignable) |
| Etiqueta | Nombre reasignable de una imagen, que no identifica nada de forma estable | Funcional §2.2 / §3.7 | Etiqueta de cabecera; etiqueta textual de UX |
| Línea de tiempo del despliegue | Secuencia de instantes que registra el despliegue (creación, arranque, retiro) | Funcional §2.2 | Registro de auditoría |
| Máquina de estados | Estados y transiciones en que se resuelve el despliegue; **describe un contenedor, no una operación** | Funcional §2.2 | Estado agregado |
| Resultado por contenedor | El resultado del despliegue se determina por contenedor y no por operación | Funcional §2.2 | Estado agregado (derivado) |
| Estado agregado | Estado derivado de los estados por contenedor de un proyecto o servicio | Funcional §2.2 | Resultado por contenedor |
| Ventana de indisponibilidad | Intervalo en que el servicio no responde porque su contenedor se reemplaza | Funcional §2.2 | Cuarentena |
| Bridge | Red virtual del motor con subred privada; resuelve por nombre y publica puertos | Funcional §2.3 | Macvlan |
| Macvlan | Modo en que el contenedor toma dirección propia de la red local; **no admite puertos publicados** | Funcional §2.3 | Bridge |
| Red del proyecto | La red en la que viven los servicios de un proyecto; objeto con identidad que el modelo no diseña | Funcional §2.3 | Modo de red del servicio |
| Modo de red | Polisémico: modo de la red del proyecto y modo de red del servicio; valores `bridge` o `macvlan` | Funcional §2.3 / §3.8 | Sus dos referentes entre sí; la forma «el modo» no se admite |
| Alias DNS | Nombre por el que un contenedor es resoluble dentro de la red del proyecto | Funcional §2.3 | Nombre visible; resolución de la referencia |
| Rango gestionado | Direcciones que el producto administra dentro de la red del proyecto, con sus exclusiones | Funcional §2.3 | Subred de la red |
| Reserva de dirección | Anotación que vincula una dirección del rango a una **réplica**; depende existencialmente del servicio | Funcional §2.3 | Dirección fija (la declaración) |
| Dirección fija | Dirección que el servicio declara en lugar de recibirla del motor; obliga a una por réplica | Funcional §2.3 | Reserva de dirección |
| Interfaz padre | Placa del host sobre la que se apoya una red macvlan | Funcional §2.3 | Pasarela |
| Dirección por réplica | Regla: un servicio con dirección fija exige una dirección reservada por réplica | Funcional §2.3 | Escalado vertical |
| Puerto publicado en el host | Puerto del host que un servicio bridge expone; único por instalación | Funcional §2.3 | Puerto de destino de una arista |
| Servicio activo | Servicio con despliegue vigente: contenedor creado y corriendo | Funcional §2.3 | Servicio declarado |
| Veredicto | Salida del algoritmo de validación de conflicto de direcciones: si el arranque procede | Funcional §2.3 | Informe de conflicto |
| Clase de conflicto | Tipo del conflicto de dirección, de un conjunto de tres; determina qué resoluciones se ofrecen | Funcional §2.3 | Resolución del conflicto |
| Informe de conflicto | Salida que acompaña al arranque bloqueado, con conflictos, clases y resoluciones admitidas | Funcional §2.3 | Informe de impacto; informe de validación |
| Grafo de arranque | Subgrafo de las aristas que declaran espera al destino; fija el orden topológico y no admite ciclos | Funcional §2.3 | Ciclo de valor (de referencias) |
| Espera al destino | Eje **declarado** de una arista: el origen espera a que el destino esté disponible | Funcional §2.3 | Referencia el host (eje deducido) |
| Referencia el host | Eje **deducido** de la clave referenciada; cuando aplica, exige canal alcanzable | Funcional §2.3 | Espera al destino |
| Canal alcanzable | Condición de que los modos de red de origen y destino permitan el tráfico | Funcional §2.3 | Alias DNS |
| Puerto de destino | Puerto que una arista anota sobre el servicio destino; **registro de dependencia, no mecanismo** | Funcional §2.3 | Puerto publicado en el host |
| Enlace de espera sin variable | Arista cuya única razón es esperar al destino; como máximo una entre dos servicios | Funcional §2.3 | Arista con par de claves de referencia |
| Fases del despliegue | Las tres: obtener la imagen, crear el contenedor y poner en marcha; el grafo ordena sólo la tercera | Funcional §2.3 | «El despliegue» a secas (son las dos primeras) |
| Materializado y sin arrancar | Imagen obtenida y contenedor creado, sin puesta en marcha: **reposo legítimo** | Funcional §2.3 | Despliegue a medias; fallo |
| Cuarentena | Estado del servicio cuyo despliegue nuevo y cuyo anterior recuperado no arrancan; apaga el autoarranque | Funcional §2.3 | Servicio detenido; ventana de indisponibilidad |
| Política de fallo al arrancar | Secuencia ante un despliegue que se crea bien y no arranca: informar, recuperar el anterior, cuarentena | Funcional §2.3 | Política de reinicio |
| Arranque parcial | Proyecto en el que algunos contenedores arrancaron y otros no; **estado legítimo, no error** | Funcional §2.3 | Fallo del despliegue |
| Servicio suelto | Servicio arrancado o detenido de a uno, fuera de la operación sobre el proyecto completo | Funcional §2.3 | Servicio huérfano |
| Variable de servicio | Par clave-valor que el proceso del contenedor recibe; clave única dentro del servicio | Funcional §2.4 | Variable compartida del proyecto; variable provista por el sistema |
| Variable provista por el sistema | Variable de sólo lectura que el sistema expone (host interno y nombre); lleva prefijo reservado | Funcional §2.4 | Variable de servicio declarada por el usuario |
| Prefijo reservado | Prefijo del sistema para sus variables; una clave de usuario con ese prefijo se rechaza | Funcional §2.4 | — |
| Resolución de la referencia | Sustituir una expresión de referencia por su valor, inmediatamente antes de crear el contenedor | Funcional §2.4 / §3.2 | Resolución del conflicto de direcciones |
| Momento de resolución | Instante del ciclo de vida en que la referencia se resuelve | Funcional §2.4 | Momento de validación (forma de las RN) |
| Último valor resuelto | Materialización de una referencia; la expresión sigue siendo la fuente de verdad | Funcional §2.4 | La expresión de referencia |
| Forma vinculada | La referencia se persiste vinculada al servicio y no a su nombre | Funcional §2.4 | Forma legible (la que el usuario escribe) |
| Referencia vigente | Referencia que todavía apunta a una variable existente; bloquea eliminar la apuntada | Funcional §2.4 | Referencia sin uso (higiene) |
| Ciclo de valor | Cadena de referencias que vuelve sobre sí misma; se rechaza con la cadena completa | Funcional §2.4 | Ciclo del grafo de arranque |
| Secreto | Material sensible que una variable **referencia** en lugar de contener; nunca se devuelve en claro | Funcional §2.4 | Marca de secreta (el atributo) |
| Carácter de secreto y su propagación | Marca de variable secreta que **se propaga por la referencia** | Funcional §2.4 | Secreto (el objeto con identidad) |
| Marcador de variable | Lo que la exportación emite en lugar del valor secreto, con entrada vacía | Funcional §2.4 | Expresión de referencia |
| Escape del signo peso | Duplicación obligatoria del signo peso literal en la exportación | Funcional §2.4 | — |
| Cambio visual | Cambio de disposición del lienzo, sin efecto sobre la configuración | Funcional §2.5 | Cambio que entra al conjunto pendiente |
| Nodo del lienzo | Cada bloque que el lienzo dibuja: **un nodo es siempre un servicio** | Funcional §2.5 | El proyecto (no es nodo) |
| Disposición del lienzo | Posición de cada nodo y trazado de cada arista; la preserva el manifiesto propio | Funcional §2.5 | Formato estándar de composición (la pierde) |
| Informe de impacto | Cálculo previo a la aplicación en lote: qué se redesplegaría y qué no, y por qué | Funcional §2.5 | Informe de conflicto |
| Servicio afectado | Servicio que el conjunto de cambios pendientes obliga a redesplegar | Funcional §2.5 | Servicio activo |
| Formato estándar de composición | Formato de intercambio de la industria; autosuficiente y **no preserva** disposición ni intención | Funcional §2.5 | Manifiesto propio |
| Archivo de composición | El archivo que el producto emite y admite en ese formato; autosuficiente por sí solo | Funcional §2.5 | Manifiesto propio (lo acompaña, no lo reemplaza) |
| Manifiesto propio | Formato de exportación del producto que preserva disposición, nivel de variable e intención | Funcional §2.5 | Formato estándar de composición |
| Plantilla | Un ítem del catálogo: el alta de un servicio **sin la cola de despliegue**, con parámetros | Funcional §2.6 | Servicio instanciado |
| Versión de contenido | Versión de lo publicado de un ítem del catálogo; la copia el servicio instanciado | Funcional §2.6 | Versión de formato |
| Versión de formato | Versión de la **forma del archivo** del catálogo | Funcional §2.6 | Versión de contenido |
| Procedencia | Polisémico con tres referentes; por defecto, la copia que el servicio instanciado conserva del ítem | Funcional §2.6 / §3.6 | Procedencia de la imagen; bloque de procedencia del framework |
| Conversión con informe | Se completa la operación transformando lo que no puede pasar tal cual, y se declara qué se transformó | Funcional §2.6 | Rechazo |
| Aviso no bloqueante | Salida que informa y **no impide** la operación | Funcional §2.6 | Rechazo; alerta |
| Descubrimiento | Operación de sólo lectura que lista contenedores ya corriendo como candidatos; **no escribe nada** | Funcional §2.7 | Incorporación (la escritura) |
| Candidato | Contenedor que el descubrimiento devuelve, con configuración observada y marca de incorporabilidad | Funcional §2.7 | Servicio incorporado |
| Motivo de no incorporabilidad | Razón escrita por la que un candidato no puede incorporarse; se muestra en lugar de omitirlo | Funcional §2.7 | Marca de incorporabilidad (definida en CU-06) |
| Configuración observada | Lo que el descubrimiento lee del contenedor antes de traducirlo a servicio | Funcional §2.7 | Configuración declarada del servicio |
| Clasificación de variables | Paso obligatorio de la incorporación: el administrador confirma cuáles variables son secretas | Funcional §2.7 | Heurística de sugerencia |
| Almacén de imágenes | Conjunto de imágenes que el motor tiene en el host; el producto lo lee sin ser su dueño | Funcional §2.8 | Registro de imágenes (remoto) |
| Marca de pertenencia | Marca que distingue lo que el producto administra de lo que sólo ve | Funcional §2.8 | Marca de conservada |
| Imagen ajena | Imagen del almacén sin marca de pertenencia; protegida de la limpieza sin excepción posible | Funcional §2.8 | Imagen conservada |
| Conservada | Marca retirable que protege una imagen de la limpieza aunque nadie la referencie | Funcional §2.8 | Imagen ajena |
| Limpieza | Borrado de imágenes descartables excluyendo conservadas y ajenas; disparo **sugerido** | Funcional §2.8 | Higiene del modelo |
| Volver a un despliegue anterior | Tomar un despliegue anterior, resolver **por digesto** su imagen y crear un despliegue nuevo | Funcional §2.8 | Redespliegue (reaplica la configuración actual) |
| Almacén local | El almacén de imágenes visto como **fuente de un origen**; es local a una máquina | Funcional §2.8 | Registro de imágenes |
| Registro de auditoría | Bitácora de toda operación de escritura con su resultado, incluido el intento rechazado | Funcional §2.9 | Evento de auditoría (la anotación) |
| Evento de auditoría | Registro de **una** operación: momento, actor, acción, entidad, detalle y resultado | Funcional §2.9 | Registro de auditoría (el conjunto) |
| Resumen del token | Lo único que se persiste del valor de un token de API; el valor se exhibe una sola vez | Funcional §2.9 | El valor del token |
| Informe de validación de la configuración | Salida única con el resultado de todas las validaciones del alta o edición de un servicio | Funcional §2.9 | Informe de verificación del origen |
| Momento de validación | Sección de forma de toda regla de negocio: en qué instante se evalúa | Funcional §2.10 | Momento de resolución |
| Entidades involucradas | Sección de forma de toda regla conceptual: sobre qué entidades recae la invariante | Funcional §2.10 | — |
| Tipo de restricción | Sección de forma de toda RC: a qué clase pertenece la invariante (cinco clases) | Funcional §2.10 | Clase de conflicto |
| Mecanismo de verificación conceptual | Sección de forma de toda RC: cómo se comprueba sin bajar a esquema ni tipos físicos | Funcional §2.10 | Momento de validación |
| Dependencia existencial | Restricción referencial: un objeto no puede existir sin el que lo contiene, con cascada | Funcional §2.10 | Referencia vigente |
| Proyecto SelfHosted | La entidad del dominio que agrupa servicios, su red y su lienzo | Raíz §9 (ref. Funcional §4.1) | Proyecto de código; proyecto como emprendimiento |
| Proyecto de código | Unidad de compilación; **no es entidad del modelo** | Raíz §9 (ref. Funcional §4.1) | Proyecto SelfHosted |
| Servicio | La unidad permanente que el usuario declara; el funcional le precisa estado de configuración y dimensiones | Raíz §9 (ref. Funcional §4.1) | Contenedor; despliegue |
| Despliegue | Lo volátil: la materialización de un servicio en contenedores | Raíz §9 (ref. Funcional §4.1) | Servicio |
| Arista o enlace | Vínculo entre dos servicios, con sus dos ejes (espera al destino, referencia el host) | Raíz §9 (ref. Funcional §4.1) | Referencia de variable |
| Changeset | El conjunto de cambios acumulados sin aplicar | Raíz §9 (ref. Funcional §4.2) | Cambio visual (no entra) |
| Adopción / incorporación | Traer al modelo un contenedor que ya existe fuera de él, sin recrearlo | Raíz §9 (ref. Funcional §4.1) | Crear (alta de algo nuevo) |
| Huérfano | Polisémico: servicio sin contenedor vinculado, y variable compartida sin referencias | Raíz §9 + Funcional §3.5 | Servicio suelto |
| Referencia de variable | Valor de una variable que apunta a otra variable; se persiste en forma vinculada | Raíz §9 (ref. Funcional §4.1) | Arista (una referencia al proyecto no dibuja arista) |
| Variable compartida del proyecto | Variable de nivel proyecto; su clave no exige unicidad y no puede contener referencias | Raíz §9 (ref. Funcional §4.1) | Variable de servicio |
| Objeto con identidad | Objeto que el modelo declara y no diseña; son cuatro: secreto, red del proyecto, imagen y volumen o directorio de montaje | Raíz §9 (ref. Funcional §4.1) | Atributo del servicio |
| Catálogo | Colección de plantillas; **nada de él corre** y arranca vacío en toda instalación | Raíz §9 (ref. Funcional §4.1) | Registro de imágenes |
| Subgrafo parametrizado | Plantilla que se instancia como N servicios y N contenedores, con su informe | Raíz §9 (ref. Funcional §4.1) | Plantilla de un solo servicio |
| Modo pendiente | Estado visual de lo que está en el changeset y todavía no se aplicó | Raíz §9 (ref. Funcional §4.1) | Borrador |
| Higiene del modelo | Condiciones del modelo que el sistema advierte sin bloquear | Raíz §9 + Funcional §3.4 | Higiene de imágenes |
| Token de API | Credencial de máquina, con sus ámbitos y su resumen | Raíz §9 (ref. Funcional §4.1) | Credencial de registro; credencial de repositorio |
| Ámbito | Polisémico con tres referentes: del token, de una variable o referencia, y título de sección de las RN | Raíz §9 + Funcional §3.3 | La forma desnuda «el ámbito» no se admite |

**Nada quedó fuera del vocabulario de dominio**: están los 120 términos de `Glosario-Funcional.md` §2 más los términos del glosario raíz que §4.1 y §4.2 referencian. Los **candidatos descartados** por vivir en un solo artefacto (informe de verificación del origen, traza de adopción, forma de creación, disparo externo, identificador del contenedor, marca de incorporabilidad, informe de importación, informe de instanciación, dimensión de configuración, huecos parametrizables, explorador de registro de imágenes) y el **vocabulario de industria** no acuñado (dispositivos, subred, pasarela, límites de recursos, contexto y argumentos de construcción, capacidades, solicitante, archivo de variables) **no entran a este índice**: están enumerados con su conteo en `Glosario-Funcional.md` §5.1.

---

## Vocabulario de interfaz

Los términos propios de `Glosario-UX.md`: §2 superficie y composición (26), §3 estado y feedback (28), §4 capacidades transversales (14). Los términos de dominio que 03 usa se **referencian y no se redefinen** (§5.1 y §5.2, 76 términos): no se repiten acá, ya están en la tabla anterior.

| Término | Definición en una línea | Glosario de origen | No confundir con |
| --- | --- | --- | --- |
| Superficie | Unidad de especificación de 03: pantalla, modal con flujo propio o panel con estados independientes; lleva `SUP-XX` | UX §2 | Representación |
| Nombre canónico de la superficie | Nombre estable con el que la superficie se identifica en toda la cadena | UX §2 | Título de la pantalla |
| Representación | Elemento visual o estructural reutilizado por varias superficies, especificado una sola vez | UX §2 | Superficie |
| Shell de acceso | Composición sin barra lateral ni navegación, para las superficies sin sesión | UX §2 | Shell de trabajo |
| Shell de trabajo | Composición completa con navegación, barra superior y área de contenido | UX §2 | Shell de acceso |
| Shell partido | La propiedad de que haya dos shells y de que la frontera entre ellos sea la sesión | UX §2 | Cambio de estado (es navegación completa) |
| Barra de identidad | Componente de la barra superior: identidad activa, cambio de contraseña y cierre de sesión | UX §2 | Banda de resultado |
| Banner de cambios pendientes | Banda fija arriba del lienzo con contador, detalle y acción de aplicar | UX §2 | Cajón de cambios pendientes |
| Cajón | Contenedor superpuesto anclado a un costado, con flujo propio | UX §2 | Panel contextual (no se superpone) |
| Panel contextual | Zona derecha del lienzo que cambia según haya o no nodo seleccionado | UX §2 | Cajón |
| Tarjeta de acceso | Patrón del catálogo base: ícono, título, descripción y toda la tarjeta clicleable | UX §2 | Grilla de tarjetas |
| Grilla de tarjetas | Rejilla de tarjetas de acceso; se adopta cuando las opciones **hay que comparar antes de elegir** | UX §2 | El alta de servicio, que dejó de usarla el 2026-08-01 |
| Campo de origen | Campo del alta con **tres valores** que declara de dónde sale la imagen | UX §2 | Variante de origen (seis, y se deriva) |
| Distintivo | Rótulo **textual** de una propiedad que no es estado de despliegue; compone sobre el par de estado | UX §2 | Insignia; par de estado |
| Orientación posterior | Grilla de tarjetas, una por paso recomendado, en el destino del aprovisionamiento | UX §2 | Asistente obligatorio |
| Vista de un solo uso | Superficie que muestra un valor que no vuelve a estar disponible, con copiado y aviso | UX §2 | Disclosure |
| Superficie con flujo propio | Superficie superpuesta con recorrido y desenlace propios; tres casos en el producto | UX §2 | Mensaje dentro de la superficie que la invoca |
| Acción primaria | La única acción destacada de una pantalla | UX §2 | Acción secundaria; acción diferenciada |
| Acción secundaria | Acción que acompaña a la primaria con jerarquía menor; su ausencia se declara | UX §2 | Acción diferenciada |
| Acción diferenciada | Acción cuyo tratamiento se aparta porque su consecuencia lo es; **nunca junto a la primaria** | UX §2 | Acción secundaria |
| Divulgación progresiva | Las opciones comunes quedan visibles y las avanzadas en un expansor colapsado | UX §2 | Disclosure (el control) |
| Disclosure | Control que expande contenido secundario declarando su estado y operable por teclado | UX §2 | Divulgación progresiva (el patrón) |
| Nodo de servicio | Unidad de lectura del lienzo; **el nodo es el servicio**, y el color refleja el despliegue | UX §2 | Nodo borrador; el proyecto (no es nodo) |
| Punto de quiebre | Ancho a partir del cual la composición reflúye; **no se acuña en 03** | UX §2 | Anchos de verificación |
| Reflujo | Reordenamiento de la composición al angostarse, legible sin desplazamiento horizontal a 320 px | UX §2 | Punto de quiebre |
| Anchos de verificación | Los anchos concretos en que la etapa `b` comprueba lo responsivo | UX §2 | La norma de diseño (declarada y aplicada) |
| Par de estado | Insignia vectorial + etiqueta textual + tratamiento de borde, siempre los tres juntos | UX §3 | Par de color |
| Insignia | Canal vectorial del par de estado; **no es glifo ni emoji** y es decorativa junto a su etiqueta | UX §3 | Distintivo |
| Etiqueta textual | Canal textual del par, **siempre presente**; un par sin etiqueta es defecto | UX §3 | Etiqueta de imagen (dominio) |
| Par de color | Tokens de fondo y texto del estado semántico; **nunca porta solo** | UX §3 | Par de estado |
| Presentación del par de estado | Las tres densidades: compacta, con antigüedad y con causa | UX §3 | Variante del par |
| Estado de deriva | El registro del sistema y el motor dejaron de coincidir; hoy su único caso es el huérfano | UX §3 | Modo pendiente |
| Pendiente de aplicar | Variante del par que corresponde al modo pendiente; **prevalece** sobre el despliegue subyacente | UX §3 | Nodo borrador |
| Precedencia de la marca de pendiente | La regla que hace prevalecer «pendiente de aplicar» sobre el estado subyacente | UX §3 | Estado de deriva |
| Nodo borrador | Nodo de un servicio en estado `borrador`; el contrato visual **no tiene señal** para él (`B-UX-24`) | UX §3 | Pendiente de aplicar |
| Región de estado | Forma de anuncio que informa **sin interrumpir**; obligatoria para todo aviso que no bloquea | UX §3 | Alerta |
| Esqueleto | Estructura de la pantalla exhibida mientras la carga está en curso | UX §3 | Los esqueletos ASCII del catálogo (§9.2) |
| Advertencia de corte | Aviso de una **consecuencia diferida** en el momento de decidir | UX §3 | Aviso de higiene |
| Banda de resultado | Componente de las superficies de identidad que comunica el desenlace de un acto | UX §3 | Región de estado |
| Variante de la banda de resultado | Sus tres formas: error, confirmación y genérica | UX §3 | Variante del par de estado |
| Catálogo de códigos de resultado | Lista de resultados con código estable y texto único que las superficies consultan | UX §3 | Catálogo de plantillas |
| Regla de continuidad del lazo | El lazo de un acto se cierra en la superficie de **destino** y no en la de origen | UX §3 | Rechazo indiferenciado |
| Rechazo indiferenciado | Forma obligatoria del rechazo de credenciales: no se dice qué parte falló | UX §3 | Requisito declarado |
| Requisito declarado | Texto de apoyo que enuncia la regla completa en positivo **antes** del intento | UX §3 | Mensaje de error |
| Aviso de higiene | Información sobre una condición del registro que **nunca bloquea** | UX §3 | Alerta; advertencia de corte |
| Estado vacío de primer uso | Vacío que corresponde a una instalación nueva y no a una anomalía | UX §3 | Estado vacío por filtro |
| Estado vacío por filtro | Vacío producido por una búsqueda sin resultados; su acción es limpiar el filtro | UX §3 | Estado vacío de primer uso |
| Interfaz optimista | Reflejar el resultado antes de la confirmación; sólo para cambios visuales del lienzo | UX §3 | Operaciones de despliegue (nunca) |
| Performance percibida | Umbrales de respuesta que deciden qué forma toma el feedback en cada superficie | UX §3 | Los umbrales en sí (vienen de `PT-01` y del intake) |
| Resultado indeterminado · consulta imposible | El externo **no respondió**: es un resultado y no un fallo; su acción es reintentar | UX §3 | «Respondió que no existe» |
| Enumeración no admitida | El externo no ofrece forma de listar; se declara como limitación y se ofrece búsqueda exacta | UX §3 | Lista vacía |
| Indicador de uso · uso no atribuible | Declara **quién referencia** una imagen, resolviendo por digesto y nunca por etiqueta | UX §3 | Criterio de descarte de la limpieza |
| Desplegado, sin arrancar | Etiqueta textual del reposo del contenedor creado, sobre par neutro y sin animación | UX §3 | «Creando o construyendo» (insignia animada) |
| Sugerencia de limpieza | Aviso de higiene que propone una operación sin ejecutarla; dos ubicaciones, una evaluación | UX §3 | Acción sugerida de un informe fallido |
| Predicado de aprovisionamiento | Único booleano que responde si el sistema está aprovisionado; ninguna superficie lo infiere | UX §4 | Artefacto mínimo |
| Artefacto mínimo | Lo indispensable para que el sistema sea operable: la identidad del administrador único | UX §4 | Predicado de aprovisionamiento |
| Destino al completar | Ruta a la que aterriza el administrador al terminar el aprovisionamiento | UX §4 | Orientación posterior |
| Corte en tres capas | Guard de primer arranque redundante: ruteo, superficie y acción contra el mismo predicado | UX §4 | Esqueleto de las tres capas |
| Descriptor de parámetro | Contrato único de un parámetro configurable y su fuente de verdad; **la pantalla lo lee, no lo inventa** | UX §4 | Campo del formulario |
| Frontera aplicación / entorno | Separación entre lo que se gobierna desde el sistema y lo que se fija al desplegar | UX §4 | Frontera de propuesta |
| Frontera de propuesta | Toda propuesta se previsualiza y se confirma; la interfaz nunca aplica directo | UX §4 | Frontera aplicación / entorno |
| Ranura del asistente | Hueco reservado y deshabilitado para un futuro asistente de configuración | UX §4 | Omisión declarada |
| Omisión declarada | Elemento de identidad que este perfil **no dibuja ni deshabilitado**; son cinco | UX §4 | Ranura del asistente |
| Sello de versión | Declaración de qué versión corre una instancia; **se deriva de la construcción, no se escribe** | UX §4 | Distintivo de artefacto preliminar |
| Ubicación obligatoria del sello | Las **dos** superficies que deben alojarlo: acceso al panel y configuración del sistema | UX §4 | Aprovisionamiento inicial (excluido) |
| Distintivo de artefacto preliminar | Marca contigua al sello, con texto explícito y no sólo color | UX §4 | Distintivo (§2, del servicio) |
| Detalle de diagnóstico | Disclosure con el contrato de identidad de versión completo y copiado en un gesto | UX §4 | Sello de versión |
| Marcador de origen indeterminado | Texto explícito que reemplaza a la versión cuando no pudo derivarse | UX §4 | Origen del servicio (dominio) |

**Vocabulario de microcopy** (`Glosario-UX.md` §6), cuatro elecciones fijadas y dos prohibiciones: **guardar** ≠ **desplegar/redesplegar/aplicar**; **incorporar** ≠ **crear**; **detener** ≠ **eliminar**; el verbo del acuse repite el verbo del botón. Prohibido decir «proyecto» a secas en interfaz y prohibido enunciar roles o permisos fuera de la sección de credenciales de máquina.

---

## Pares que se confunden

Contrastes que las fuentes declaran de forma explícita. La columna de criterio es el separador operativo, no un matiz.

| A | B | Criterio que los separa |
| --- | --- | --- |
| Servicio | Despliegue / contenedor | El servicio es permanente y posicionable; el despliegue es volátil. El servicio **no tiene estado de encendido**: lo que se pinta con color es el despliegue (UX §5.1) |
| Proyecto SelfHosted | Proyecto de código / proyecto como emprendimiento | Tres referentes de contextos **disjuntos** que el intake §12 decide no calificar; se escriben completos donde el otro está cerca (Funcional §3.9, UX §6 y §9.2) |
| Plantilla (ítem del catálogo) | Servicio instanciado | La plantilla declara el alta **sin la cola de despliegue**: no se instancia, no se despliega y no ocupa dirección. Del catálogo **nada corre** (Funcional §2.6, UX §5.1) |
| Variable | Referencia | La variable es el par clave-valor; la referencia es una expresión que apunta a otra variable y se sustituye antes de crear el contenedor (Funcional §2.4) |
| Construir la imagen | Crear el contenedor | Construir la imagen puede vivir afuera del producto; crear el contenedor **nunca sale del producto** y ocurre en los seis orígenes. Nunca se dice «construir» a secas (Funcional §3.10, UX §8.2) |
| Vía de alta | Origen | La vía es cómo se llegó y **no se persiste**; el origen es qué fuente de imagen quedó declarada y **sí se persiste** (Funcional §2.2) |
| Origen | Disparador del despliegue | El origen dice de dónde sale la imagen; el disparador dice quién inicia el despliegue. Son ejes independientes (Funcional §2.2) |
| Registro del sistema | Registro de auditoría / del contenedor / de imágenes / del producto | Cinco referentes; la forma desnuda «el registro» sólo vale para el registro del sistema y sólo si la sección ya lo fijó (Funcional §3.1) |
| Almacén de imágenes (local) | Registro de imágenes (remoto) | El almacén está en el host y es la fuente del inventario; el registro es externo y es de donde se descargan las imágenes (Funcional §2.8) |
| Digesto | Etiqueta | El digesto es identidad calculada sobre el contenido; la etiqueta es reasignable y no identifica nada de forma estable (Funcional §2.2, §3.7) |
| Servicio declarado | Servicio activo | El activo tiene despliegue vigente con contenedor corriendo; el declarado existe como configuración con o sin él (Funcional §2.2, §2.3) |
| Identificador legible | Nombre visible | El identificador es único en la instalación y deriva nombres de recursos; el nombre visible **no exige unicidad** (Funcional §2.1) |
| Versión de contenido | Versión de formato | La de contenido es de lo publicado del ítem; la de formato es de la forma del archivo del catálogo (Funcional §2.6) |
| Higiene del modelo | Higiene de imágenes | Objetos distintos: condiciones del modelo que se advierten sin bloquear vs. ciclo de vida de las imágenes del almacén (Funcional §3.4) |
| Servicio huérfano | Variable compartida huérfana | R1 se reconcilia; R2 se advierte sin bloquear. La forma desnuda «los huérfanos» no es resoluble (Funcional §3.5) |
| Resolución de la referencia | Resolución del conflicto de direcciones | Sustituir una expresión por su valor vs. la vía elegida para resolver un conflicto; conviven en CU-24 (Funcional §3.2, UX §8.1) |
| Modo de la red del proyecto | Modo de red del servicio | El del proyecto fija la red que todos comparten; el del servicio gatea sus puertos y su canal alcanzable (Funcional §3.8) |
| Procedencia del servicio | Procedencia de la imagen / bloque de procedencia | Copia del ítem del catálogo vs. de dónde salió una imagen vs. la versión normativa con que se emitió un documento (Funcional §3.6) |
| Borrador | Pendiente de aplicar | El borrador **no entra** al conjunto de cambios pendientes y no es aplicable; lo pendiente sí, y su variante prevalece sobre el estado subyacente (Funcional §2.2, UX §3) |
| Cambio visual | Cambio de configuración | El visual no entra al conjunto de cambios pendientes, no marca redespliegue y es lo único con interfaz optimista (Funcional §2.5, UX §5.2) |
| Guardar | Desplegar / redesplegar / aplicar | Guardar agrega al conjunto pendiente; los otros tres tocan contenedores. **Los dos conjuntos de verbos no se cruzan nunca** (UX §6) |
| Incorporar | Crear | Incorporar es traer al modelo un contenedor que ya existe, sin recrearlo; crear es dar de alta algo nuevo (UX §6) |
| Detener | Eliminar | Detener elimina el contenedor y conserva definición, variables y datos de montajes; eliminar borra el servicio (UX §6) |
| Volver a un despliegue anterior | Redespliegue | Volver resuelve la imagen **por digesto** del despliegue anterior; el redespliegue reaplica la configuración actual (Funcional §2.8) |
| Materializado y sin arrancar | Despliegue en curso / fallo | Es reposo legítimo: par neutro sin animación y etiqueta «Desplegado, sin arrancar», nunca «creando o construyendo» (Funcional §2.3, UX §3) |
| Región de estado | Alerta | La región informa sin interrumpir; anunciar como alerta un aviso que no bloquea contradice su definición (UX §3) |
| Formato estándar de composición | Manifiesto propio | El estándar es autosuficiente y pierde disposición, nivel de variable e intención; el manifiesto los preserva y lo acompaña (Funcional §2.5) |
| Registro de auditoría | Evento de auditoría | El registro es la bitácora; el evento es la anotación de **una** operación (Funcional §2.9) |
| Cajón | Panel contextual | El cajón se superpone y tiene flujo propio; el panel es parte de la composición (UX §2) |
| Estado vacío de primer uso | Estado vacío por filtro | Situación normal de instalación nueva vs. búsqueda sin resultados; sus acciones siguientes difieren (UX §3) |

---

## Convenciones de identificadores

Sólo los prefijos que las dos fuentes leídas confirman. Los que no se confirman están marcados al final.

| Prefijo | Qué numera | Confirmado en |
| --- | --- | --- |
| `CU-XX` | Caso de uso de 02; el corpus tiene 39 desde el 2026-07-30 | Funcional §2 (convención de la tercera columna) |
| `RN-XX` | Regla de negocio de 02; son 40 | Funcional §2, §2.10 |
| `RC-XX` | Regla conceptual de modelo de 02; son 19 | Funcional §2, §2.10 |
| `MC` | `Modelo-Datos/Modelo-Conceptual.md` | Funcional §2 (convención) |
| `IDX` | `Especificacion-Funcional.md`, el índice maestro de 02 | Funcional §2 (convención) |
| `RME` | El `README.md` de la sección 02 | Funcional §2 (convención) |
| `SUP-XX` | Una superficie de 03, con su nombre canónico; van de `SUP-01` a `SUP-19` | UX §7 |
| `B-UX-XX` | Brecha de 03: decisión que la categoría no resuelve y declara; hasta `B-UX-34` | UX §7 |
| `C-UX-XX` | Contradicción declarada entre el catálogo de diseño y el anexo E-18 del intake | UX §7 |
| `S-UX-XX` | Supuesto de derivación de 03: especificación derivada, con su derivación escrita | UX §7 |
| `B-XX` | Brecha declarada en el índice maestro de 02 (p. ej. `B-26`, `B-27`, `B-31`) | Funcional §2.3, §5.2 |
| `Q-XX` | Decisión abierta del intake, que cierra en positivo o en negativo (`Q-15`, `Q-17`, `Q-19`, `Q-21`, `Q-27`) | Funcional §2.8, §5.2, §6 |
| `E-XX` | Anexo del `PRODUCT-INTAKE` §20 (`E-8` conflictos, `E-16` momentos, `E-17` máquina de estados, `E-18` contrato visual, `E-23`) | Funcional §1.3, §4.1; UX §1.4 |
| `P0` · `P1` · `P2` · `P3` | Prioridad de un hallazgo de informe de audit (`P0-01`, `P1-r2-02`, `P2-04`, `P3-03`) | Funcional §1.1, §6; UX §1.1, §10 |
| `M4` · `M6` | Fase (y corte) de la migración normativa del conjunto 4.1 al 6.0 | Funcional §6; UX §1.1, §10 |
| `B2` | Fase B2 del proceso: la maqueta navegable que emite AG-03M | UX §1, §7, §8.2 |
| `D8` | Los ocho tipos de documento para los que la regla declara el glosario obligatorio | Funcional §1; UX §1 |
| `PT-XX` | Puerta técnica; `PT-01` aporta los umbrales de performance percibida | UX §3 |
| `AG-XX` | Agente autor del artefacto (`AG-02` analista funcional, `AG-03` UX/UI, `AG-03M` maqueta) | Cabeceras de ambas fuentes; UX §1 |
| `CMP-XX` · `EST-XX` · `NAV-XX` · `DM-XX` | Series de la línea base visual y del contrato de datos de maqueta; **cero ocurrencias**: las emite AG-03M en B2 | UX §7 |
| `F-XX` | Aparece como identificador de funcionalidad (`F-10`, «acotada el 2026-08-01»), pero **ninguna de las dos fuentes declara qué numera la serie**; vive en `00-Contexto` y `01-Necesidades-Negocio` | Funcional §2.2 (uso, no definición) |
| `NB-XX` | Aparece como origen de criterios medibles (`NB-01`), **sin definición en estas fuentes**; la serie es de `01-Necesidades-Negocio` | Funcional §4.2 (uso, no definición) |

**No confirmables en estas dos fuentes**, y por eso no se definen acá: `US-XX` (sin ocurrencias en el corpus fuera de `_legacy`), `AT-XX`, `DoD`, `DoR`, `RG-XX`, `RP-XX`, `CP-XX`. Existen en `SDD/Docs/00-Contexto/`, `SDD/Docs/01-Necesidades-Negocio/` y `SDD/Docs/Audit/`; su significado hay que tomarlo de esos documentos y no de este índice.

---

## Fuentes

| Archivo | Qué contiene |
| --- | --- |
| `SDD/Docs/02-Especificacion-Funcional/Glosario-Funcional.md` | Glosario del dominio, v1.2: 120 términos en §2 (diez subsecciones), nueve familias polisémicas con sus formas calificadas y su evidencia de colisión en §3, 18 términos referenciados al glosario raíz y 2 equivalencias de forma en §4, constancias del barrido y descartes en §5 |
| `SDD/Docs/03-UX-UI-DX/Glosario-UX.md` | Glosario de superficie, v2.3: 72 términos propios en §2 a §4 y §7, 76 términos del dominio referenciados sin redefinir en §5, cuatro elecciones de microcopy y dos prohibiciones en §6, identificadores de forma en §7, dos familias polisémicas en §8, descartes y criterio negativo en §9 |
| `SDD/Docs/00-Contexto/Vision-Producto.md` §9 | **Glosario raíz de la cadena**, referenciado por los dos anteriores. Ningún término suyo se redefine en 02 ni en 03; acá se marcan como «Raíz §9» |
| `SDD/Docs/02-Especificacion-Funcional/Modelo-Datos/Modelo-Conceptual.md` | Entidades y atributos de los que sale buena parte de las definiciones del funcional; su punto 6 fue el glosario de la categoría hasta la regla 3.0 y hoy remite al glosario funcional |
