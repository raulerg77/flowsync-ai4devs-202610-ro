# Verificación — «Lo que cada tarea muestra de su responsable» (rerg)

## Matriz de trazabilidad

Requisito: `openspec/specs/tasks/spec.md:97-114`. La revisión la hizo el subagente `adversarial-reviewer` sobre el estado actual de la rama `trazabilidad-rerg`.

| Scenario (qué se espera y en qué situación) | Test que lo cubre | Cobertura | Por qué no se puede determinar |
|---|---|---|---|
| **Responsable identificable**: al obtener una tarea cuyo responsable es «Ada Lovelace», su `assignee` trae el nombre y las iniciales | Ninguno | No cubierto | — |
| **La tarea no filtra datos de cuenta**: al obtener cualquier tarea (suelta o en lista), su `assignee` no incluye el email ni otro dato de acceso | Ninguno | No cubierto | — |
| **Responsable sin nombre**: con un responsable registrado sin nombre, su nombre llega nulo y las iniciales siguen llegando | Ninguno | No cubierto | — |

### Después de añadir los tests (`backend/tests/functional/tasks/assignee.spec.ts`)

Cada scenario se comprueba en las cinco respuestas que devuelven una tarea: lista, detalle, alta, cambio de estado y cambio de fecha. Son 15 tests: 12 pasan y 3 fallan.

| Scenario | Tests que lo cubren (grupo `Tasks \| responsable`) | Cobertura | Resultado |
|---|---|---|---|
| Responsable identificable | `el responsable se identifica por nombre e iniciales {en la lista, en el detalle, al crearla, al cambiar su estado, al fijar su fecha}` | Cubierto | 5/5 en verde |
| La tarea no filtra datos de cuenta | `el responsable no expone su email ni otros datos de cuenta {…}` (mismas 5 variantes) | Cubierto | **3/5 en rojo**: `en la lista`, `al crearla` y `al cambiar su estado` devuelven `email`. Es un bug de la implementación, no del test. |
| Responsable sin nombre | `un responsable sin nombre llega con nombre nulo e iniciales {…}` (mismas 5 variantes) | Cubierto en parte | 5/5 en verde. Solo se comprueba que las iniciales llegan y no están vacías, no de dónde salen, porque la spec no lo fija. |

Corrección a la revisión anterior: el cambio de fecha (`task_due_dates_controller.ts:31`) usa `TaskDetailTransformer` y **no** filtra el email. Los que lo filtran son la lista, el alta y el cambio de estado.

Notas:

- **No hay ningún test de tareas.** Los únicos specs son `backend/tests/functional/auth/*.spec.ts`, y `assignee` no aparece ni una vez en `backend/tests`.
- **Cobertura incidental, que no cuenta.** `auth/initials.spec.ts:16-33` y `auth/signup.spec.ts:31` comprueban las iniciales, pero vía `/auth/login` y el modelo. No pasan por ningún endpoint de tareas.
- **Probable incumplimiento real del scenario 2**, comprobado en el código. `TaskTransformer` serializa `assignee` con `UserTransformer`, que incluye `email`, `createdAt` y `updatedAt`. Lo usan la lista (`tasks_controller.ts:47`), el alta (`:80`) y los controladores de estado y de fecha. Solo el detalle (`TaskDetailTransformer` → `TaskAssigneeTransformer`: `id`, `fullName`, `initials`) cumple el requisito.
- **Ambigüedades de la spec.** En el scenario 3 no queda claro si la falta de nombre es `null` o cadena vacía, ni de dónde salen las iniciales. Hoy se derivan del email, lo que choca con «sin recurrir a su email». En el scenario 1, «esos datos bastan para saber quién la lleva» no es comprobable: solo se puede verificar que `fullName` e `initials` son los esperados.



PARTE B:
1. solo habian test para AUTH.
2. 