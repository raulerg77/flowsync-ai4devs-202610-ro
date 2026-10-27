PARTE A
1.- Terreno que ya existe:
Hoy el codebase es solo un sistema de autenticación. Tiene dos tablas, users y auth_access_tokens, y todavía ninguna entidad de negocio. El frontend sigue siendo la plantilla estándar de Vite con React.

2.- Preguntas
  1. ¿Para quién es la visibilidad y por qué la quieren? ¿Es sobre todo para que los compañeros se coordinen entre sí, o para que un lead o un manager supervise al equipo? La respuesta decide si esto es un espacio de trabajo compartido o una herramienta de supervisión, y eso cambia las funcionalidades y el tono.
  2. ¿Cómo es un equipo típico? ¿Cuántas personas tiene cada equipo, cuánto se reparten entre husos horarios y si una misma persona está en varios equipos o proyectos? Un equipo de cinco personas en un solo huso horario necesita algo muy distinto que veinticinco personas repartidas en tres continentes.
  3. ¿De dónde sale "en qué está cada miembro"? ¿La gente lo actualiza a mano, por ejemplo eligiendo una tarea o escribiendo un estado breve? ¿O debería salir de herramientas que ya usan, como los commits de Git, los PRs o su calendario? ¿Y con qué detalle: la tarea en la que están ahora mismo, el plan del día o el avance hacia un objetivo?
  4. ¿Qué te da hoy una reunión de sincronización que FlowSync tiene que sustituir? Por ejemplo: saber quién está bloqueado y por quién, evitar trabajo duplicado, detectar sobrecarga o simplemente tener sensación de avance. ¿Y con "tiempo real" te refieres a que los cambios aparezcan en segundos, o a una vista que esté siempre al día cuando la abres?
  5. ¿Cuál es la versión más pequeña que contaría como éxito y qué queda fuera? ¿Qué tiene que hacer la primera versión y qué debería dejar fuera a propósito? Algunos ejemplos son sprints, estimaciones, comentarios, notificaciones, integraciones y una app móvil.

3.- Alcance de FlowSync (MVP)

## 1. Problema

En un equipo remoto pequeño nadie sabe en qué está cada uno sin interrumpir a alguien. Eso
tiene dos costes concretos:

- **Trabajo duplicado.** Dos personas tocaron el mismo módulo la misma semana porque una empezó
  sin que la otra lo supiera. Se perdieron dos días.
- **Interrupciones y tiempo de reunión.** El "¿en qué estás?" se repite por Slack o chat y se
  come la mitad de los 15 minutos de la daily.

La causa es que el estado del equipo no está visible en ningún sitio sin preguntar. El gestor
de tareas que se usa hoy es demasiado pesado como para mantenerlo al día.

## 2. Usuarios

- **Equipos remotos pequeños, de 3 a 10 personas**, repartidos en varios husos horarios.
- **Los roles son planos.** Todos ven y editan lo mismo y no hay jerarquía de permisos.
- **El valor es para los pares, no para un lead.** No hay reporte hacia arriba. Gana quien
  descubriría tarde que iba a lo mismo que otro y quien hoy interrumpe para preguntar.
- **Caso de estudio** (no es un cliente real): un equipo de 6 personas de producto SaaS, en 3
  husos horarios, con un gestor de tareas pesado y una daily de 15 minutos por videollamada.

## 3. Propuesta de valor

**Una lista de tareas compartida en tiempo real, que es a la vez la cola de trabajo de cada
persona.**

- **Para el equipo:** se ve de un vistazo quién está en qué, qué está libre y qué se ha pasado
  de plazo. Eso permite no empezar algo que otra persona ya está tocando y elegir lo siguiente
  sabiendo qué queda libre.
- **Para quien actualiza:** gana en el momento. Es su propia lista de trabajo, actualizarla
  cuesta dos clics y deja de recibir interrupciones.
- **Frente a Jira:** crear una tarea y cambiarle el estado lleva segundos, sin flujos que
  configurar ni campos obligatorios salvo el título.
- **Lo que no promete:** la daily no desaparece entera. Desaparece la ronda de "¿en qué estás?"
  y la parte de bloqueos sigue.

**Criterio de éxito (una semana de uso real):** el equipo cancela la ronda de "¿en qué estás?" y
nadie pide que vuelva. Si la siguen haciendo igual, no funcionó.

## 4. Alcance

Es una vertical fina y usable de punta a punta: una capability terminada mejor que tres a
medias.

### Capacidades

- **Un único espacio compartido** para todos los usuarios registrados, aprovechando el registro
  y el login que ya existen.
- **Crear una tarea en segundos.** Solo el título es obligatorio y la fecha de vencimiento es
  opcional. Toda tarea nueva nace en "Por hacer" y sin responsable.
- **Editar el título y la fecha** de una tarea, solo mientras está "Por hacer".
- **Borrar una tarea**, solo mientras está "Por hacer" y con una confirmación antes.
- **Coger, soltar y terminar tareas en uno o dos clics**, desde la propia lista. Solo puedes
  coger una tarea para ti.
- **Tiempo real:** cada cambio aparece en la lista de los demás sin refrescar. Eso incluye las
  tareas creadas, editadas y borradas, y los cambios de estado.
- **Tareas vencidas visibles de un vistazo**, cuando tienen fecha y no están "Hechas".

### Ciclo de vida de la tarea

| Estado | Responsable | Acciones posibles |
|---|---|---|
| **Por hacer** | Ninguno (libre) | **Cogerla:** te asigna y la pasa a "En curso". **Editar** el título o la fecha. **Borrarla**, con confirmación. Cualquiera puede hacer las tres cosas |
| **En curso** | Quien la cogió (no se puede reasignar) | **Soltarla:** vuelve a "Por hacer" sin responsable. **Terminarla:** pasa a "Hecha". No se puede editar ni borrar |
| **Hecha** | Quien la cerró | Ninguna. Es un estado final y no se puede editar, borrar ni reabrir |

Los estados son fijos, no se configuran, y no hay estado "Bloqueada".

### Supuestos y restricciones

- Hay un solo espacio, sin entidad "equipo".
- El estado lo teclea quien hace la tarea.
- No hay plazo.
- La tecnología es el stack actual: API AdonisJS 7 y SPA React 19 + Vite, en TypeScript.
- Solo web.

### Riesgos y limitaciones conocidas

- **Riesgo #1, que la información se quede vieja.** La mitigación es que actualizar cueste dos
  clics, nunca obligar a nadie.
- **La lista crece sin límite.** Las tareas "Hechas" no se pueden borrar ni filtrar. Es un riesgo
  de usabilidad a vigilar y "filtrar por estado" sería lo primero que volvería al MVP.
- **Corregir una tarea "En curso"** obliga a soltarla, editarla y volver a cogerla, y mientras
  tanto otra persona puede cogerla.

## 5. NO-alcance

- **Gestión de proyecto "pesada":** sprints, estimaciones, épicas, backlog priorizado e informes.
- **Varios equipos** o personas que estén en más de uno, y permisos o roles.
- **Integraciones y estado derivado:** Git/PRs, CI, calendario u OAuth de terceros.
- **Importar o sincronizar tareas** de otro gestor. FlowSync lo sustituye, no convive con él.
- **Presencia y vigilancia:** "quién está conectado", indicadores de actividad o última conexión.
- **Comunicación:** chat, comentarios en tareas, videollamada y edición simultánea.
- **Notificaciones push** o avisos que interrumpen.
- **Gestión de bloqueos** y un estado "Bloqueada".
- **Estados configurables.**
- **Asignar una tarea a otra persona** o reasignar una tarea "En curso".
- **Editar o borrar tareas** "En curso" o "Hechas".
- **App móvil**, nativa o PWA.

## Después del MVP

- Resumen de lo que se ha movido desde tu última visita.
- Reabrir una tarea "Hecha" y devolverla a "Por hacer".
- Filtrar por estado.


PARTE B
1. 8 propuestas / 6 se quedaron / 1 adicional surgió durante discusión

2. "Filtro por estado" / "Marcar lo que se ha movido" / "Tarea Hecha puede reabrirse a Por Hacer" quedaron fuera. Su no-implementación no rompe la premisa del producto de proveer a equipos remotos información sobre en qué está trabajando cada uno sin tener que hacer reuniones de sincronización.

3. En general, me pareció bastante acertado el scope que propuso la IA. Lo que quité del alcance fue porque, aunque son nice-to-have features, no contribuyen significativamente a la misión de la aplicación como MVP.

