
Planteo de casos a evaluar.

Tenemos ahora maquetado los siguientes card:

1. **Caso 1**

```
Imagen de registro público
«Sé qué imagen quiero y está publicada»
Origen resultante: Imagen de registro público
```

2. **caso 2**
```
Imagen de registro privado
«La imagen está en mi registro y necesita credencial»
Origen resultante: Imagen de registro privado
```

3. **Caso 3**
```
Repositorio remoto
«Tengo el código en un repositorio y quiero que el panel construya»
Origen resultante: Repositorio remoto
```

- En los casos: **Caso 1** y **Caso 2**, son para definir el despliegue desde un servicio a partir de una imagen en dockerhub. Ambos casos son similares, salvo que **Caso 2** que requiere las credenciales de dockerhub porque si la imagen es privada.

- En el **Case 3** es para establecer un servicio, dejándolo preparalo para crear un despliegue desde github action. En este establecia el workflow el cual le avisa a nuestro `selfhosted service` que despliegue. Si te fijas, ahí va atener que ir a dockerhub para tomar la imagen, lo que nos deja en el **Caso 1** y **Caso 2**. Es decir que el **Caso 3** se basa el **Caso 1/2**.


Entonces, para el **Caso 1** y **Caso 1** tendriamos que desplegar un servicio desde una imagen Docker. Así, para estos casos el flujo de despliegue sería en lo que es el alta del servicio:
1. Obtener datos de la imagen desde dokerhub (desconozco si hay otros medios despues de dockerhub).
2. (si corresponde) autenticarse. (opción de verificacion contra dockerhub+ check de conexión a dockerhub) 
3. Configuraciones de red - variables y secretos (puertos)
3. Descargar la imagen.
4. Crear/actualizar el contenedor.
5. Iniciar el servicio. (despliegue) o guardar para despliegue del proyecto completo

Aquí se requiere credenciales, se piden, por eso el usuario debería poder cargar las credenciales.

Ahora desde un repositorio remoto, aqui el workdflow va a necesitar el endopoint y las credenciales de selfhosted . Por lo tanto al crear un servicio caso 3:

a. Desde el lado de selfhosted service
  a.1. Mostrar endpoint y token (opción , regenerar para este servicio) que usara el workflow para el despliegue de este servicio.

  a.2. Se cargan las credenciales dockerhub para el caso de que sea un imagen privada.

b. De aquí en adelante queda desde el lado de github action

  b.1 Cuando el workflow de github action construya y envie la imagen a dockerhub dispara hacia selfhosted service el despliegue automatico.

a. Desde el lado de selfhosted service
  a.3 Cuando recibe el post al endpoint prefijado, sigue el procedimiento **Caso 1/2**.

Es necesario empezar a diferenciar lo que es flujo de proceso y de usuario.

En el flujo de usuario configura y prepara el servicio.
En el flujo de proceso - en este caso de despliegue - se realiza toda la cadena cuando hay confirmación.

Así, unificados **Caso 1** y **Caso 2** como **Caso 1/2** el despliegue se dispara desde el sistema , pero el origen puede venir desde el orquestado de despliegue del proyecto o bien desde el post desde el proyecto.

Surgen casos acá:
- ¿Cómo orquesto el despliegue de todo el proyecto?
- ¿Voy permitir el despliegue individual?
- En el despliegue, ¿tiene sus reglas opcionales y no opcionales?y estas son parte del orquestado


Resumen de puntos a ordenar y definir.
- Flujos de usuarios para el alta de un servicio. Solo es alta, no despliegue.
- Flujo de proceso de despliegue. Los origenes, y si se incluye como opción en el alta del servicio
- El proyecto, su despliegue, como un flujo de proceso de orquestado. Se permite el despliegue individual, el despliegue como proyecto implica dar un orden de despliegue de cada servicio individual.

Todo esto hay que revisarlo, se puede contrastar y resolver como guía tutora lo que presenta railway. `/DEV/SelfHosted.Service.Core.Documentos/Analisis/Analisis-SaaS-Service/Analisis-Rayway.md`

En base a esto y lo maquetado reconsiderarlo y reeditar el documento `/DEV/SelfHosted.Service.Core.Documentos/PROMPTs/08-Re-Evaluacion/OUTPUTs/Casos-Estudios-Re-Definidos.md` . este informe será utilizado para redefinir los workflow de usuario, modelos de datos, y ejemplos en la especificación y tambien para replantear la maqueta.


---

# Reglas

  - No inventar información.
  - Toda afirmación deberá estar respaldada por evidencias verificables.


---

# Framework

## Profile

Aplicar:

 - `/IA/IA.Prompts/PromptFramework/Profiles/Study-Guide-Documentation.md`

