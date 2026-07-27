
# ​Plan del proyecto - Servicio Administración Self Hosting

## Contexto

Leer `/Repos-Hosts/Host.Infra.Documentos/ia-db/README.md` describe un pequeño servidor en el que me permite montar servicios internos para mantenimiento de mi pequña infraestructura y desarrollo de software. De ahi centrate en las caracteristicas del servidor y sus capacidades y en los contenedores actuales montados como ejemplo de uso de los contenedores.

## Objetivos 

Crear un administrador de contenedores basado en proyectos mediante una interfaz canvas intuitiva adaptado a las necesidades de un servidor pequeño para desarrollo.


## ​Definición y Alcance del Servicio: 

modulo de descubrimiento y adopción de contenedores donde el sistema consulta el demonio de docker para que los pueda seleccionar y asignar al proyecto que estoy dando de alta sin necesidad de reinstanciarlos. Debería verificar que otro

## 1. Características funcionales.

### 1.1 Primer alcance 
- Crear un proyecto de arquitectura de infraestructura con todos los servicios que debe tener ese proyecto. Es decir el proyecto contiene la arquitectura de todos servicios que se quieren incluir. 
- La arquitectura consiste en un conjunto de servicios.
- Cada servicio esta definido por un contenedor docker. 
- El proyecto, definido sus servicios, debe realizar el despliegue automático o debe contempla el despliegue automático.
- El despliegue debe ser automático. Podría basarse desde repositorio github, especificando dicho repositorio en el momento de crear o dar de alta el servicio dentro de lo que va a ser el diagramando de la arquitectura del proyecto que se esté creando o editando. Una forma es que yo especifique en el alta del servicio un dockerfile, o bien la imagen en dockerhub.
- El servicio Paas a diseñar debe contemplan dentro de este primer incremento solo un usuario administrador.
- La arquitectura del proyecto debe ser modelado en un Espacio visual, donde el canvas representa todo el espacio, y cada bloque el servicio conteneirizado, y con bordes o conectores o lineas que vinculan estos servicios. mediante variables de entornos que definen la ruta privadas de un servicio a otro, por ejemplo defino un backend, y un servicio de bases de datos. el backend se conecta al servicio. dirección interna y el puerto necesario para la comunicación privada. 
- Debería poder iniciar y para un proyecto completo, debería poder especificar si queda auto iniciable.
- Debería poder iniciar y parar un servicio en particular de un proyecto particular, debería poder especificar si queda autoiniciable.
- Administración de escalado horizontal: consiste en agregar más instancias del mismo servidor para distribuir el trafico entre ellas. Esto es manual. 
- Administración de escalado vertical: consiste en aumentar los recursos más instancias para distribuir el tráfico entre ellas. esto permite manejar una carga mayor- aumentando la capacidad - del sistema a medida que aumenta las solicitudes. Esto es manual.
- Sobre la administracion de la Red por parte del servicio saas, al ser un servidor pequeño, deberían los servicios correr con su propia ip de la lan, y estas deben ser asignadas manualmente durante el alta o edición. Pero el administrado Saas debería detectar los conflictos de ip de un proyecto  y no debería dejar comenzar la ejecución del proyecto completo si hay servicios que ocupan ip que están en uso en otro proyecto. Si el proyecto con el proyecto que están en conflicto esta parado, lo deja continuar porque las ips estarían libres. En conclusión el Saas debería tener asignado un rango de direcciones ip exclusivos para la gestión y control de conflictos. Entonces, puede haber múltiples servicios configurados con una misma IP, pero permitirá la ejecución de aquel proyecto que tenga servicios que no estén en conflicto con otros servicios activos.

### 1.2 Segundo alcance

- Genere a dashboard. a nivel servicio quisiera un estado del servidor, memoria y disco rígido, y memoria ram. Estructura en capas para
una vista general de cada proyecto - y después por proyecto una vista de cada contenedor en general.

### 1.3 Tercer alcance

- Exportar e importar la arquitectura completa del proyecto en un archivo Docker Compose
- Debe contar con un catálogo de servicios. Los servicios son despliegue genéricos reutilizables en otros proyectos. Usando el caso de uso de alta de servicio para la arquitectura de un proyecto. Este catalogo debe ser editable, y exportable e importable. Por si se reinstala el servicio se importa el catalogo. 

### 1.4 Cuarto alcance

- La funcionalidad de poder hacer despliegues automatizados desde un workflow de github action. Previamente se debería haber creado el proyecto , configurado los servicios, y los token y todo lo que se requiera para que desde github actions dispare el despliegue.

### 1.5 Características técnicas

- La tecnología debe ser desarrollada en .NET 10 usando páginas interactive server y librerias mudblazor y Entity Framework.
- Debe utilizar como base de datos SQLite.
- La arquitectura de un proyecto debe ser modelada utilizando un lienzo o tablero canvas interactivo para la edición de la arquitectura de cada proyecto y su servicio. Utilizar `https://github.com/Blazor-Diagrams/Blazor.Diagrams` 
- Las Rest APIs deben ser con autentificación ROPC, jwt bearer token. e implementación mediante Controladores.
- Patrón de diseño de software. Clean Arquitecture, estándar .NET . Organizar El proyecto una estructura de carpeta por modulos.
- El proyecto tendrá una arquitectura monolítica donde todos los componentes y servicios se ejecutan juntos en un solo servicio o contenedor.

### 1.6 Queda fuera el alcance

- Administración de proxies o proxies reverso
- Balanceo de carga.
