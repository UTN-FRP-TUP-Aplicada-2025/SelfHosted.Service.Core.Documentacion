# Tool-Prompt — Ejecutar Fix sobre el SDD del producto · definiciones de servicio

> **Invocación**:
> - `Lee y ejecuta /DEV/SelfHosted.Service.Core.Documentos/PROMPTs/05-Fix-SDD-Redefinir-Servicios/Ejecutar-Fix-SDD-Definiciones-Servicios.md`
>
> Overview: incorporar a la especificación del producto las definiciones de alta
> y configuración de servicios y de ítems del catálogo, ya analizadas y
> decididas, dejándola en condiciones de sostener la reconstrucción de la maqueta.

---

# Contexto

  **Los dos repositorios.** `/IA/IA.SDD` es el `Framework SDD`, repositorio
  fuente y **de sólo lectura** en esta ejecución. `/DEV/SelfHosted.Service.Core`
  es el `repositorio destino`, donde vive el diseño de la solución.

  **Estado del destino.** El intake y el manifiesto declaran su procedencia bajo
  el conjunto normativo **4.1**, de modo que la versión con la que se especificó
  ya no es una incógnita. Las Fases A y B están emitidas con auditoría
  independiente —`SDD/Docs/00-Contexto`, `01-Necesidades-Negocio`,
  `02-Especificacion-Funcional` y `03-UX-UI-DX`—, y la Fase B2 dejó una maqueta
  navegable en `SDD/Maquetas/SelfHosted-Service/`.

  **La entrada de esta ejecución.**
  `/DEV/SelfHosted.Service.Core/SDD/Estado/Redefinicion-Servicio.md`, versión
  2.0. Recoge el análisis del alta de servicio que la maqueta destapó, sus
  decisiones cerradas y las que quedan abiertas.

  **Cómo está estructurada esa entrada, y por qué importa antes de tocar nada.**
  El documento tiene **dos partes con estatuto distinto**, declaradas en su §0:

  - **Normativa, §16 a §23.** Es lo que se aplica. Autocontenida.
  - **Derivación, §1 a §15.** Antecedente y evidencia. **Contiene pasajes
    superados**, que §0.1 enumera uno por uno.

  Donde las dos difieran, **manda la normativa**.

  **Lo que no forma parte de esta ejecución.** `/SDD/Estado/Fix-Ejecución-Glosario-Framework.md`
  es una orden de trabajo sobre el `Framework SDD` y se ejecuta aparte, en una  sesión propia. Acá el framework no se toca.

---

# Objetivos

  1. Incorporar a `/DEV/SelfHosted.Service.Core/SDD/Docs/` lo que la parte
     normativa de `Redefinicion-Servicio.md` establece sobre el **alta y la
     configuración de servicios y de ítems del catálogo**: las siete vías de
     alta sobre cinco variantes de origen, el tronco común de diez pasos, la
     configuración como reentrada del tronco, y las dos operaciones del
     catálogo.

  2. Dejar la especificación en condiciones de **sostener la reconstrucción de la maqueta**. La maqueta **no se rehace en esta ejecución**: se rehace desde la especificación corregida, por §21.4 del documento de entrada.

  3. **No decidir lo que está abierto.** Lo que §23 declara abierto se
     incorpora como brecha declarada con destinatario, nunca como dato cerrado.

  4. Dejar trazable qué se aplicó: cada fila de §22 del documento de entrada debe poder responder si se aplicó, dónde, y con qué versión del artefacto.

---

# Solicitudes

  1. **Leer §0 del documento de entrada antes de cualquier otra cosa.** Declara la separación de las dos partes y enumera los seis pasajes superados. Sin ese paso, la ejecución puede aplicar material retirado.

  2. **Analizar el estado actual del repositorio destino** y contrastarlo con §22 de la entrada, para confirmar fila por fila qué ya está cubierto y qué falta. §22 es un punto de partida verificable, no un inventario cerrado: lo que se encuentre de más se declara.

  3. **Corregir el intake** según §22.1, que es la primera dependencia:
     `E-2` —las cinco variantes de origen, comando de arranque, Dockerfile en
     línea, credenciales de registro, digesto, procedencia de plantilla—,
     `E-6` —tipos cerrados, `porDefecto` prohibido sobre `secreto`, conversión
     con `generar`—, `E-7` —puertos publicados—, la imagen como objeto con
     identidad, y §4 con la vía de alta como eje propio del origen.

  4. **Corregir los casos de uso** según §22.2: `CU-03` y su `FA-01`, `CU-16`,
     `CU-17`, `CU-06`, `CU-08`, `CU-13`, `CU-15`, más los dos casos de uso
     nuevos: higiene de imágenes, y volver a un despliegue anterior.

  5. **Emitir las reglas de negocio nuevas y corregir las alcanzadas**, según
     §22.3: colisión de puerto publicado en el host, desvinculación de la
     plantilla, protección de la imagen conservada; y `RN-08`, `RN-15`.

  6. **Actualizar el modelo conceptual y las restricciones** según §22.4:
     entradas de glosario para vía de alta, plantilla, las dos versiones,
     imagen y digesto; la imagen como entidad con identidad por `D-12`; y la
     restricción de puerto único publicado por host.

  7. **Actualizar la capa de experiencia** según §22.5, sin tocar la maqueta.

  8. **Registrar cada decisión con el marcador que le corresponde:**
     - `Q-4a`, `Q-4b`, `Q-9` y `Q-23` son **datos cerrados del agente humano
       del proyecto, fecha 2026-07-29**: se marcan `[D]`, nunca `[S]`.
     - Las ocho de §23.2 tienen propuesta escrita y **no están confirmadas**:
       se marcan como propuesta del integrador y se listan como pendientes.
     - Las dieciséis de §23.3 se incorporan como **brechas declaradas** con
       destinatario, sin valor supuesto.

  9. **Actualizar `SDD/Estado/Informe-Avance.md`** con lo aplicado, lo declarado
     como brecha, y lo que quedó fuera con su motivo.

  10. **Auditar de forma independiente** lo emitido, contra el documento de
      entrada y contra los artefactos vigentes.

---

# Reglas

  - No inventar información.
  - Toda afirmación deberá estar respaldada por evidencias verificables.
  - **No modificar el `Framework SDD`.** `/IA/IA.SDD` es de sólo lectura.
  - **No escribir en carpetas `PROMPTs/`** salvo pedido explícito del agente
    humano del proyecto.
  - **Precedencia dentro del documento de entrada:** §1 a §15 es antecedente, no
    especificación. Ningún pasaje de esa parte puede usarse para contradecir
    §16 a §23, ni siquiera cuando no lleve marca de superado.
  - **Archivar antes de editar** todo artefacto vigente, por `Master-Prompt.md`
    §5.1 y la nomenclatura D4/D5. Un desvío se declara explícitamente en el
    registro de cambios del propio artefacto.
  - **No rehacer la maqueta en esta ejecución** (§21.4 de la entrada).
  - **No cerrar por cuenta propia** ninguna decisión que §23 declare abierta.
  - Un hallazgo de auditoría es un **piso, no una medida**: en este trabajo la
    auditoría localizó bien los defectos y subestimó su extensión tres veces de
    tres. Verificar el alcance real antes de dar por cerrado un hallazgo.
