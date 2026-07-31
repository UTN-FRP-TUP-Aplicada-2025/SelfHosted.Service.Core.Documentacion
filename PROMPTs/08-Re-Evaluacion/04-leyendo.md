# Sobre lo que planteaste del proyecto parado
Tenés razón y es el caso que ninguna pieza resuelve hoy. Tres cosas que encontré al verificarlo:

Railway no te sirve de guía acá. Su apagado es por despliegue, no por proyecto, y toda acción que produce un despliegue lo deja corriendo. 

**Un push a un servicio apagado lo vuelve a levantar.** No tienen el problema porque en su modelo el proyecto es un agrupador; acá el proyecto es la unidad de arquitectura, con grafo de arranque y red propia. Es diseño propio, no hay de dónde copiarlo.

«Desplegar» son dos operaciones que el modelo trata como una: materializar (descargar la imagen, crear el contenedor) y poner en marcha. Y el estado que necesitás ya existe: la correspondencia con el motor mapea created a «creado, aún sin arrancar». El problema es que comparte nombre con el estado inicial de la máquina y se dibuja como paso, no como reposo.

Falta la intención de ejecución. Ni la tabla de proyectos ni la de servicios tienen columna de estado — el estado real se deriva de los contenedores, y eso está bien. Pero «debe estar corriendo» es una declaración tuya y no se deriva de nada. Sin eso no hay contra qué contrastar el disparo externo.

La regla queda en una línea: un disparo externo materializa siempre y no cambia nunca la intención de ejecución.

Puntos pendientes para revisar
Son cuatro grupos. Los primeros dos bloquean a los otros.

# Decisiones tuyas, estructurales
| # | Qué decidir | Dónde |
|---|-------------|--------|
| R-01 | Confirmar el **tercer eje (disparador)**. Ya está persistido por `despliegue` en `solicitado_por` con valores `ui`, `api`, `autoranque` y `politica`, pero no gobierna comportamiento. | §2 |
| R-15 | Destino de **«Repositorio remoto»**: conservar, diferir o retirar. Recomiendo diferir. | §9 |
| R-07 | **Interfaz común** para imagen pública y privada, con variante derivada. Revierte `DI-18`. | §5.3 |
| R-17 | Adoptar la **intención de ejecución** en proyecto y servicio. | §8.7 |


### Decisiones tuyas, de seguridad del disparo externo

| # | Qué decidir | Por qué importa |
|---|-------------|-----------------|
| R-11b | **Ámbito con dimensión de recurso**: la credencial declara sobre qué servicios puede disparar. | Sin esto, delegar a un equipo externo les da todo el parque. |
| R-11c | **Identificador opaco del destino** de disparo. | En el alta no hay clave todavía, y la actual es enumerable. |
| R-20 | Las **opciones de orquestado** de §8.8, cuidando no confundirlas con el autoarranque. | Es lo que elegís vos por proyecto y por servicio. |

### Deuda que este análisis destapó
| # | Qué |
|---|-----|
| R-21 | La tabla de servicios **no tiene columna de estado**, pese a los tres valores que `DI-19` incorporó; y el comentario de la columna de origen dice tres variantes cuando son cinco. |
| R-14 | El **secreto** está declarado como objeto y sin modelar. Diferido a Fase C, y el despliegue automatizado lo vuelve urgente. |
| R-06 | **Credenciales de registro sin superficie de alta**. Ojo: `SUP-12` ya tiene seis secciones y su wireframe declara que seis está en el límite de Miller. |

---

Y lo que sigue abierto de antes, sin relación con esto: la Fase B2 no está aprobada, la maqueta tiene tres defectos de cableado corregidos y la verificación de navegación nunca se cerró. Como SUP-17 se rehace igual por estas redefiniciones, sigo pensando que conviene hacer las dos cosas en la misma pasada.


---

## El Caso 4 y su asimetría

Tu observación de que «el contenedor ya está, e incluso está corriendo» es la que más peso tiene, y la especificación la respalda: RA-02 dice que la adopción «crea el servicio sin recrear el contenedor». Contra la separación materializar / poner en marcha, eso significa que la adopción es el único alta que nace materializada, y puede nacer con el despliegue ya activo. Los otros tres nacen sin ninguno. Esa asimetría no está declarada en ningún lado.

Y separaste bien dos cosas que yo habría mezclado: adoptar un contenedor es uno a uno —lo impone la invariante I2— mientras que instanciar desde una imagen local es uno a muchos. Verifiqué: el sub-caso de la imagen no está modelado. No hay sexta variante de origen y el descubrimiento lista contenedores, no imágenes.

Antes de agregarla, algo que conviene mirar: un servicio cuyo origen es una imagen que sólo existe en el almacén local no se puede reproducir en otro servidor, y la reproducibilidad de la arquitectura es una necesidad de negocio declarada con documento propio. Dejé tres salidas; la que recomiendo es permitirlo marcando el servicio como no reproducible, con la marca visible en la exportación y no como nota al pie.

Dos correcciones mías
Sobre Railway tenías razón. Mi frase de que allá el proyecto «es un agrupador» y acá es la unidad de arquitectura era demasiado gruesa. Contrastado: comparten agrupar y aplicar cambios en lote. La única diferencia real es que nosotros tenemos encendido a nivel de proyecto y ellos no — y es justamente esa única diferencia la que crea todo el problema de coherencia.

Y la intención de ejecución en el servicio rozaba una invariante. I3 declara que el servicio no tiene estado de encendido, e I4 que el ciclo de vida vive en el despliegue. Un servicio con «siempre detenido» se lee como servicio apagado, que es lo que I3 quiere impedir. Corregido: la intención vive sólo en el proyecto, y en el servicio se declara su participación en el arranque del conjunto, que es atributo de configuración. Mismo comportamiento, invariante intacta. Queda registrada como decidida por vos.

Sobre el despliegue que necesita bajar el proyecto
Tu duda era la correcta. Hay dos relaciones y el modelo tiene una. El grafo de arranque dice en qué orden levanto; el alcance de indisponibilidad dice qué tiene que estar abajo mientras despliego. Y no se deriva uno del otro en ninguno de los dos sentidos: un servicio puede esperar a la base al arrancar y no necesitarla abajo para desplegarse; una migración puede exigir sus consumidores abajo aunque ninguno declare espera.

Tu preocupación por los orígenes distintos se disuelve sola: el alcance de indisponibilidad no tiene nada que ver con de dónde sale la imagen.

Descarté la forma que hoy funcionaría sin cambiar nada —el workflow llamando detener, desplegar, arrancar— por dos motivos: si el despliegue falla a la mitad, el proyecto queda abajo y nadie lo levanta, y obliga a darle al automatismo mucho más poder del que necesita. Y esto no contradice la regla del disparo externo: la parada por ventana es transitoria y no toca la intención declarada; si falla, el panel la restaura.

Lo que ya existe y no sabíamos que teníamos
Tu idea del despliegue de conjunto como unidad ya está en el modelo: el conjunto de cambios tiene tabla propia, estado, mensaje —que es un mensaje de confirmación— y momento de aplicación, y cada despliegue guarda de qué conjunto salió. Lo que falta no es la pieza sino la lectura: hoy se presenta como bandeja de pendientes y no como versión del proyecto. Y faltan dos cosas concretas: la operación en lote no se persiste (tiene identificador en la API pero no tabla), y no se puede volver a un conjunto anterior porque guarda el delta y no el estado resultante.

Un hallazgo que apareció de paso, y no es menor
POST /proyectos/{id}/detener comparte el ámbito despliegues:ejecutar con POST /servicios/{id}/desplegar. El token que le das al workflow para desplegar ya puede bajarte el proyecto entero, hoy, sin que nadie lo haya decidido. Es peor que lo de R-11b: ni siquiera separa acciones de riesgo muy distinto.

Treinta y dos redefiniciones, contadas en disco — el conteo se me fue dos veces en este turno y lo dejé calculado, no escrito a ojo.