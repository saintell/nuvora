# Nuvora — Estrategia de pruebas del MVP

## 1. Propósito y límites

Este documento define cómo obtener evidencia de que el MVP cumple los requisitos funcionales, las reglas de negocio y los requisitos no funcionales de `VISION.md`, `PRD.md`, `SRS.md`, `ROADMAP.md`, `USER_FLOWS.md`, `DESIGN_SYSTEM.md`, `ARCHITECTURE.md`, `DATABASE.md`, `API.md`, `ENVIRONMENTS.md` y `SECURITY.md`. La aceptación inicial corresponde a Android, español para Colombia y COP. La estrategia se incorpora por hitos; no afirma que las pruebas o las capacidades ya existan.

SQLite privada en el dispositivo es la única fuente de verdad financiera. El núcleo manual, el historial y el dashboard deben funcionar sin red. El backend entrega resultados de voz e IA para revisión, pero no guarda movimientos ni calcula totales como autoridad. Solo datos válidos, visibles y confirmados por la persona llegan a persistencia local.

Esta estrategia no prescribe frameworks, porcentajes de cobertura, configuración de CI/CD, proveedores, infraestructura permanente de QA ni un proceso completo de release. Las herramientas concretas se eligen al implementar cada nivel, respetando las que ya estén aprobadas o configuradas.

## 2. Principios y niveles

1. Probar primero las reglas deterministas de Domain y Financial Engine, con casos explícitos para cada invariante financiera.
2. Mantener la mayoría de pruebas rápidas, aisladas, reproducibles y sin React Native, SQLite o red cuando esas fronteras no sean el objeto de la prueba.
3. Integrar componentes reales cuando el resultado dependa de Expo SQLite, Android o HTTP. Un doble no demuestra persistencia, permisos ni operación offline reales.
4. No depender de STT, IA ni otros servicios externos para verificar el núcleo financiero. La IA nunca es oráculo de cálculos financieros.
5. Para las mismas entradas y un estado inicial controlado, un test automatizado ordinario debe dar el mismo resultado. Controlar reloj, datos, base y respuestas externas; no depender del orden de ejecución.
6. Usar solo datos ficticios o sintéticos. Aislar la configuración de test y hacer imposible que una prueba apunte accidentalmente a Production.
7. Probar sin conexión las capacidades esenciales offline. Los requisitos `MUST` aplicables deben contar con evidencia verificable antes de cerrar el hito 8.
8. La cobertura puede señalar código sin ejercitar, pero ningún porcentaje sustituye una prueba directa de una regla crítica. Automatizar al nivel menos costoso que demuestre correctamente el comportamiento.

| Nivel | Responsabilidad principal | Frontera real necesaria |
| --- | --- | --- |
| 1. Unitarias | Domain, Financial Engine, validadores y transformaciones puras: dinero, fechas, tipos, categorías y cálculos. Constituyen el mayor volumen. | Ninguna: sin React Native, SQLite, red ni proveedores. |
| 2. Application | Coordinación de crear, consultar, editar, eliminar, confirmar, cancelar y manejar fallos; repositorios en memoria, clientes remotos dobles y tiempo controlado. | Contratos de casos de uso, sin exigir infraestructura real. |
| 3. Integración | Repositorio y migraciones con Expo SQLite aislada; esquema y transacciones; serialización HTTP, FastAPI/Pydantic, configuración y adapters con doubles. | SQLite y HTTP reales donde su comportamiento importa; proveedores externos simulados. |
| 4. Mobile integrado | Pantallas, componentes y navegación mediante comportamiento perceptible, incluidas revisión, errores, permisos y alternativa manual. | Aplicación integrada cuando los niveles inferiores no bastan. |
| 5. E2E Android | Un conjunto pequeño de recorridos críticos en Android compatible: guardado, corrección, eliminación, offline y voz cuando exista. | Dispositivo o emulador Android, con almacenamiento y conectividad pertinentes. |

Las combinaciones exhaustivas de reglas pertenecen a los niveles 1–3, no a E2E. La suite automatizada ordinaria no llama a STT, IA, analytics ni crash reporting reales. Las verificaciones live que se justifiquen se ejecutan por separado, de forma controlada, con datos sintéticos y credenciales Development; no reemplazan los contratos deterministas.

## 3. Domain y Financial Engine

### Movimientos, clasificación y cálculos

- Probar `Expense`, `Income` y `OwnTransfer`, COP como única moneda y monto positivo. Un monto vacío, inválido o que requiera redondeo silencioso se rechaza antes de persistir.
- Un gasto exige categoría del catálogo de gastos y admite subcategoría opcional solo si pertenece a esa categoría. Un ingreso exige categoría de ingresos y no admite subcategoría de gasto. Una transferencia propia no tiene categoría ni subcategoría. Rechazar IDs inexistentes o del catálogo equivocado; los catálogos aprobados no son editables por la persona en el MVP.
- Probar directamente que `own_transfer` no incrementa ingresos ni gastos, no modifica el resultado neto ni integra la distribución de gastos.
- Verificar **resultado neto = ingresos contabilizables − gastos contabilizables**. No presentarlo ni probarlo como saldo bancario.
- Calcular ingresos, gastos y distribución de gasto por categoría con movimientos válidos del período. Probar estado vacío, recálculo tras creación, edición y eliminación, y traslado entre meses al editar la fecha efectiva. La misma entrada debe producir el mismo resultado.

### Dinero exacto

- `amount_minor` representa unidades menores enteras de COP, con **1 COP = 100 unidades menores**. Probar cantidades pequeñas, cantidades grandes válidas, cero, negativas, valores malformados y conversiones exactas desde texto decimal, incluido el texto remoto `amount`.
- Probar el máximo entero seguro `Number.MAX_SAFE_INTEGER` permitido para una fila al intercambiar datos con Expo SQLite y el rechazo del valor superior, tanto antes de enlazar como al reconstruir `Money` desde una lectura inválida.
- Probar una suma de filas individualmente válidas cuyo total supera `Number.MAX_SAFE_INTEGER`: el total sigue siendo exacto. Domain y Financial Engine no acumulan importes con `number` fraccionario; SQLite no almacena dinero como `REAL`.
- Rechazar entradas que exigirían redondeo silencioso. Estas pruebas no fijan una regla nueva sobre visualización o introducción de decimales en la UI.

### Fechas y períodos

- `effective_date` es el día calendario financiero `YYYY-MM-DD`, no un timestamp. Validar días inexistentes, límites de mes, cambio de año, febrero y años bisiestos; verificar que conversiones UTC o de zona horaria no cambien el día financiero.
- Probar que `created_at` y `updated_at` no asignan períodos. Editar `effective_date` mueve el movimiento y recalcula ambos períodos; editar otros datos sin cambiarla no mueve el registro. La edición conserva identidad y `created_at` y actualiza `updated_at`.
- Verificar el orden descendente y los desempates del historial: `effective_date`, después `created_at` y finalmente `id`; `updated_at` no altera ese orden.
- Las comparaciones y tendencias dependientes de «historial suficiente» requieren cerrar `TBD-004` antes de definir y aceptar sus casos de umbral. Mientras tanto, comprobar que un historial vacío o insuficiente no produce tendencias inventadas.

## 4. Application y confirmación

Con repositorios y clientes dobles, verificar que una creación válida llama al repositorio una vez y solo tras confirmación; entrada inválida o cancelación no escribe; fallo de guardado no comunica éxito. En edición, repetir validaciones, conservar ID y `created_at`, actualizar `updated_at` y no mostrar estado parcial si falla. En eliminación, exigir la intención y confirmación correspondientes, ejecutar la eliminación real, comunicar la ausencia del registro y comprobar que cancelar no elimina.

Repetir **la misma intención local de confirmación** debe conservar el ID y producir un solo movimiento, incluso al reintentar tras un resultado de guardado incierto; una nueva intención puede producir otro. Un conflicto de ID no genera silenciosamente uno nuevo. Esta regla es distinta de los reintentos HTTP de voz: un POST remoto con resultado ambiguo no se repite automáticamente.

Una propuesta automática, completa o parcial, no escribe ni modifica totales antes de validación determinista, revisión y confirmación explícita. Una respuesta tardía tras cancelar, una validación fallida o un error remoto dejan intactos historial y dashboard. Comprobar que los casos de uso locales no esperan ni requieren configuración o servicios remotos.

## 5. SQLite, migraciones y persistencia

Usar una SQLite de test aislada y un estado inicial controlado por prueba o suite; no sustituir SQLite por un mock al verificar su comportamiento. Probar creación del esquema versión 1, `PRAGMA user_version`, `PRAGMA journal_mode = WAL`, tabla e índice `idx_movements_effective_date`. Comprobar `CHECK type`, `CHECK currency`, `amount_minor`, combinaciones estructurales de tipo y categorías, clave primaria y fechas con forma canónica. La validez del calendario y la pertenencia al catálogo corresponden además a Domain: el `CHECK` de fecha solo comprueba la forma.

Cubrir inserción, obtención por ID, listado general y mensual mediante intervalo semiabierto de fechas efectivas, orden estable, actualización y `DELETE` físico por ID. Una actualización o eliminación sin fila afectada debe comunicar ese resultado. Verificar que concepto, IDs, fechas, montos y filtros se enlazan mediante parámetros o prepared statements; no se concatena entrada de usuario en SQL. Las escrituras relacionadas deben ser atómicas y hacer rollback completo ante fallo; las lecturas que requieran una misma instantánea deben mantenerse coherentes.

En una instalación sin base, el inicializador crea la versión actual y la deja utilizable. Cuando existan migraciones posteriores, probar upgrades desde cada versión anterior soportada relevante, preservación de movimientos válidos y actualización de `user_version` solo tras commit. Una migración fallida revierte datos, esquema y versión, conserva la base, detiene la apertura financiera normal y comunica el fallo. Una versión más nueva que la conocida no se abre para escritura normal. Las migraciones son forward-only: no se exige downgrade ni se usa reinstalación como sustituto de upgrade correcto.

La evidencia de integración debe mostrar que creación y edición confirmadas sobreviven al cierre y reapertura y al reinicio del dispositivo; que una eliminación confirmada sigue eliminada funcionalmente; y que un fallo no deja datos ni resúmenes parciales. Un mock de repositorio por sí solo no satisface estas condiciones.

## 6. Mobile, offline y accesibilidad

Las pruebas de UI se centran en lo que la persona percibe y puede hacer: campos requeridos, errores asociados, estado vacío, progreso durante operaciones, navegación, corrección, confirmación, cancelación y éxito solo después de persistencia. Evitar depender de estructura privada de componentes, snapshots masivos o detalles visuales sin requisito. Probar que la ruta manual sigue disponible cuando una capacidad remota falla o carece de configuración.

En una verificación integrada **sin red real disponible**, comprobar apertura, consulta del historial existente, creación manual de gastos, ingresos y transferencias propias, edición, eliminación, dashboard, cambio de período y conservación tras cierre/reinicio. Un mock que devuelva error de red no basta como única evidencia offline. Voz e IA, cuando requieran conectividad, deben comunicar indisponibilidad sin fabricar resultados ni bloquear el núcleo local.

Verificar los criterios aprobados de WCAG 2.2 AA en las combinaciones reales de la app: contraste de 4.5:1 para texto normal y 3:1 para texto grande; controles e indicadores conforme a esa referencia; escalado de texto hasta 200 % sin truncar información financiera esencial; áreas táctiles operables de al menos 48 × 48 dp; y alternativas a gestos para acciones críticas. Comprobar nombres, roles, valores y estados accesibles, orden de lectura, foco, asociación entre campo y error, resultado de acciones y que ningún tipo, categoría, importe, error o éxito dependa solo del color. Inicio, fin, procesamiento y error de voz deben tener señales accesibles. Complementar comprobaciones automatizables con revisión en Android para comportamiento dependiente del dispositivo. `TBD-008` está resuelto en `DESIGN_SYSTEM.md`; falta verificar el cumplimiento real.

## 7. Voz, API e IA

### Captura y permisos

Comprobar que el núcleo manual no pide permiso de micrófono; la captura requiere inicio explícito, información previa y permiso Android cuando aplique. Rechazar el permiso deja la ruta manual disponible. Verificar indicador activo, detención, cancelación, transcripción visible y comunicación de estados. Audio temporal, si se crea, permanece privado, fuera de backups y se elimina cuando deja de ser necesario en éxito, cancelación o fallo, según las condiciones que cierren `TBD-005` y `TBD-014`. Fallo de STT, transcripción vacía, pérdida de conexión y respuesta tardía tras cancelar no crean movimientos.

### Contrato HTTP y fallos

Probar con HTTP/FastAPI/Pydantic y adapters simulados `POST /v1/voice/movement-proposals`: `multipart/form-data` con `audio` y `reference_datetime` ISO 8601 con offset obligatorios. Cubrir multipart malformado, parte ausente, audio vacío, formato no admitido, tamaño excesivo y referencia inválida. Los límites concretos de formato, tamaño y duración se prueban una vez configurados para la capacidad; no se inventan aquí.

La respuesta `200` debe cumplir los estados `proposal`, `insufficient_information` (con o sin propuesta parcial) y `multiple_movements` (sin propuesta). Probar `missing_fields` y `uncertain_fields` presentes y coherentes, `proposal` como objeto único y nunca lista, `amount` como texto decimal, moneda COP y `request_id` como correlación técnica. `200` no equivale a movimiento confirmado o guardado. Mobile valida la respuesta con Zod y Domain vuelve a validar la propuesta antes de cualquier escritura.

Verificar el envelope seguro y los pares de error aprobados: `INVALID_REQUEST` 400/422, `PAYLOAD_TOO_LARGE` 413, `UNSUPPORTED_AUDIO` 415, `RATE_LIMITED` 429, `TRANSCRIPTION_EMPTY` 502, `INVALID_PROVIDER_RESPONSE` 502, `PROVIDER_UNAVAILABLE` 503, `UPSTREAM_TIMEOUT` 504 e `INTERNAL_ERROR` 500. Un timeout o fallo de red observado solo por mobile puede no traer envelope: el resultado remoto se trata como desconocido, sin guardado ni reintento silencioso. Ningún error expone secretos, prompts, stack traces ni respuestas crudas del proveedor.

### Salidas no confiables y evaluación

Con doubles, cubrir éxito, timeout, indisponibilidad, transcripción vacía, respuesta inválida, propuesta incompleta y múltiples movimientos. Inyectar además campos fuera del esquema, tipo o moneda inválidos, categoría inexistente, subcategoría incompatible, monto malformado, fecha inválida, explicación inesperada y texto que ordene omitir la confirmación. El resultado debe validarse o rechazarse sin ejecutar instrucciones, exponer secretos ni persistir automáticamente. La IA no calcula ingresos, gastos, neto ni agrupaciones.

Estos casos verifican fronteras y control del software, no la calidad semántica de un modelo concreto. La evaluación de STT, interpretación, categorización y lenguaje colombiano deberá existir antes de aceptar la capacidad correspondiente, con criterios aprobados en su estrategia específica; un resultado live de modelo no es una aserción determinista de la suite ordinaria. No se fijan aquí proveedor, dataset ni umbral de calidad.

## 8. Seguridad, privacidad y analytics

Probar los controles de `SECURITY.md` a medida que se implementen: datos financieros y audio temporal en almacenamiento privado; exclusión Android de backups y transferencias automáticas para artefactos sensibles; permisos mínimos; SQL parametrizado; validación de entrada y de salida externa; rate limiting, límites de audio, timeouts y mecanismo de integridad de app cuando entren en alcance. Comprobar que Production usa HTTPS, que Development y Production están separados y que la falta de configuración remota no impide el núcleo local. Un contexto de test no es un tercer entorno desplegado ni debe apuntar a Production.

Inspeccionar bundle/configuración pública, respuestas, logs, crashes y telemetría para evitar secretos y, por defecto, montos, conceptos, audio, transcripciones, historial, prompts o salida cruda de IA. El request remoto solo debe llevar audio y `reference_datetime`, sin adjuntar automáticamente historial u otros datos personales. Verificar la información y autorización aplicables antes de capturar y enviar audio y la política de conservación que se apruebe; no presumir retención cero de proveedores.

Los eventos de producto deben distinguir registro manual iniciado, completado y abandonado; intento de voz iniciado; interpretación aceptada sin cambios o corregida; y categoría sugerida aceptada o corregida. Probar que un intento no recibe resultados incompatibles, que «completado» ocurre solo después de persistencia y que no se registra aceptación automática sin confirmación. Analytics no modifica reglas ni movimientos y, por defecto, no incluye contenido financiero o sensible. Las metas y ventanas cuantitativas siguen `TBD-009`; no se elige proveedor.

## 9. Rendimiento, compatibilidad y datos de prueba

Medir, una vez cerrado `TBD-006`, apertura, registro y persistencia local, consulta del historial, cálculo del dashboard, operación de voz e interpretación remota en las condiciones aprobadas. Antes de fijar objetivos se puede reunir una línea base y detectar regresiones evidentes, sin convertir un número supuesto en gate. Las operaciones financieras locales no deben esperar servicios remotos; las operaciones pendientes muestran progreso y permiten salida segura.

Con la compatibilidad Android ya definida en `RNF-COMP-001` como Android 10 (API 29) o superior, verificar la versión mínima Android y al menos una versión representativa más reciente, especialmente SQLite, permisos de micrófono, almacenamiento privado, exclusiones de backup, reinicio y offline. No se exige una gran granja de dispositivos. iOS no es criterio de aceptación del MVP.

Usar fixtures o builders pequeños con movimientos y audios ficticios, sin bases Production, logs financieros reales ni transcripciones identificables. Los casos que dependan de fecha actual sugerida, `reference_datetime`, `created_at`, `updated_at` o expresiones relativas usan tiempo fijo/controlable, por ejemplo `2026-09-26T12:00:00-05:00`; no dependen del día real de ejecución. Aislar SQLite por prueba o suite y controlar las respuestas externas.

Una prueba que pasa y falla sin cambio relevante requiere investigación de su causa; no ocultar inestabilidad con reintentos automáticos indiscriminados. Evitar red real en la suite determinista y dependencias del orden de pruebas. Para cada defecto relevante corregido, agregar cuando sea razonable un caso que falle antes y pase después, con prioridad para cálculos, pérdida de datos, duplicados, migraciones, fechas, offline, seguridad y persistencia automática indebida.

## 10. Smoke y E2E críticos

Un build candidato requiere un smoke pequeño: apertura y navegación base; crear gasto, ingreso y transferencia propia; consultar historial y dashboard; cerrar/reabrir conservando datos; y operación manual básica sin red. Cuando voz esté incluida, comprobar permiso y captura, resultado revisable o fallo seguro, y ausencia de persistencia automática. No se fija herramienta.

| Escenario E2E | Recorrido y evidencia |
| --- | --- |
| E2E-01 — Gasto manual | Abrir, registrar y confirmar gasto; comprobar historial y dashboard. |
| E2E-02 — Corrección | Crear, editar datos o fecha efectiva; comprobar período y totales afectados. |
| E2E-03 — Eliminación | Crear y eliminar deliberadamente; comprobar historial y dashboard tras la operación. |
| E2E-04 — Offline | Sin red, crear, consultar y editar; cerrar/reabrir y comprobar persistencia. |
| E2E-05 — Voz | Cuando esté habilitada, capturar, revisar o corregir una propuesta válida, confirmar y comprobar guardado local. |
| E2E-06 — Cancelación de voz | Capturar o procesar, cancelar y comprobar que no apareció otro movimiento. |

Cada escenario se incorpora cuando su flujo entra en alcance. No se automatizan todos en el hito 1 ni se trasladan a E2E todas las combinaciones de campos.

## 11. Evidencia por hito

| Hito | Evidencia mínima para aceptar la capacidad incorporada |
| --- | --- |
| 1 — Walking skeleton | La app inicia en Android compatible, se recorre la navegación mínima, la configuración Development funciona y Expo SQLite está disponible; no se declaran satisfechas reglas financieras aún. |
| 2 — Movimientos manuales | Invariantes de Movement, categorías, dinero exacto y CRUD; entradas inválidas, confirmación, persistencia, cierre/reinicio y errores sin estado parcial. |
| 3 — Dashboard | Historial y períodos por fecha efectiva; totales, distribución, `own_transfer`, recálculo por crear/editar/eliminar y estado vacío. Comparaciones requieren `TBD-004`. |
| 4 — Offline | Flujo integrado sin red, reinicio, fallos, atomicidad y repetición de acciones sin duplicados. |
| 5 — Voz | Información y permiso, captura/detención/cancelación, STT y transcripción, fallos remotos y privacidad aplicable, siempre con ruta manual. |
| 6 — IA | Contrato y estados de propuesta, ausencias e incertidumbres, revisión y confirmación, múltiples movimientos, salidas adversariales y ausencia de escritura automática. |
| 7 — Categorización y medición | Sugerencia válida o ausencia explícita, aceptación/corrección y eventos de analytics distinguibles sin contenido sensible por defecto. |
| 8 — Hardening | Evidencia integral de todos los `MUST` aplicables: funcionalidad, finanzas, migraciones, offline, errores, accesibilidad, seguridad, privacidad, permisos, rendimiento, Android, voz e IA. Resolver antes los TBD necesarios para aceptar cada capacidad. |
| 9 — Validación con usuarios | Se inicia únicamente con el MVP que superó el hito 8; la observación de usuarios no sustituye pruebas de software. |

## 12. Matriz de trazabilidad

Los grupos siguientes orientan la evidencia sin copiar cada caso del SRS. «Principal» no excluye los demás niveles cuando una frontera real lo exige.

| Grupo | RF y RN relevantes | RNF relevantes | Nivel principal |
| --- | --- | --- | --- |
| Inicio y movimientos manuales | `RF-ONB-001–004`, `RF-MOV-001–009`, `RN-001–003`, `RN-013` | `RNF-UX-001–004`, `RNF-REL-004`, `RNF-MANT-002` | Unitarias, Application y mobile |
| Categorías y transferencias | `RF-CAT-001–004`, `RF-MOV-004`, `RN-002–006`, `RN-011`, `RN-013` | `RNF-REL-003`, `RNF-MANT-002` | Unitarias y Application |
| Gestión y persistencia | `RF-GES-001–005`, `RF-PER-001–003`, `RN-012` | `RNF-REL-001–005`, `RNF-SEC-004` | Application, SQLite y Android |
| Dashboard y períodos | `RF-DASH-001–007`, `RN-005–007`, `RN-010`, `RN-012` | `RNF-REL-003`, `RNF-MANT-002` | Unitarias, SQLite y mobile |
| Offline y compatibilidad | `RF-OFF-001–004`, `RF-PER-001–002` | `RNF-PERF-003`, `RNF-COMP-001–003` | Android integrado y E2E |
| Voz | `RF-VOZ-001–007`, `RN-009` | `RNF-PRIV-001–005`, `RNF-PERF-002`, `RNF-ACC-004` | Mobile, API y E2E |
| IA y confirmación | `RF-IA-001–005`, `RF-CONF-001–005`, `RN-008–009` | `RNF-REL-005`, `RNF-SEC-003–004` | Application, API y mobile |
| Categorización automática y analytics | `RF-AUT-001–004`, `RF-ANA-001–003`, `RN-004`, `RN-011` | `RNF-SEC-002`, `RNF-PRIV-003–005` | Application y contratos de eventos |
| Seguridad y privacidad | `RF-PER-001–003`, `RF-VOZ-001–006`, `RF-CONF-004` | `RNF-SEC-001–004`, `RNF-PRIV-001–005` | Integración, inspección y Android |
| Accesibilidad y rendimiento | Flujos `RF-ONB`, `RF-MOV`, `RF-GES`, `RF-DASH`, `RF-VOZ`, `RF-CONF` aplicables | `RNF-ACC-001–005`, `RNF-PERF-001–003`, `RNF-UX-001–004` | Mobile y revisión Android |

## 13. Done, gate del MVP y defectos críticos

Una capacidad está terminada cuando cumple sus requisitos y reglas aplicables, tiene pruebas automatizadas en los niveles adecuados, evidencia real donde los dobles no demuestran el resultado, escenarios pertinentes de error y cancelación, y no rompe el núcleo offline. Debe cumplir seguridad, privacidad y accesibilidad aplicables; sus TBD necesarios deben estar resueltos y no debe tener defectos críticos conocidos.

Para salir del hito 8 y comenzar el 9, todos los `MUST` aplicables deben estar implementados y verificados. Deben pasar las reglas financieras, CRUD e historial íntegros, persistencia y migraciones que preservan datos, offline, permisos, seguridad, privacidad, accesibilidad, compatibilidad Android conforme a `RNF-COMP-001`, rendimiento conforme a `TBD-006` y voz/IA con fallo seguro. Ninguna propuesta automática puede modificar datos sin confirmación. Un `MUST` cuya decisión previa necesaria siga abierta no se marca como satisfecho; cualquier excepción de alcance requiere decisión explícita conforme al roadmap. No se exige cero defectos de toda severidad, un SLA ni un porcentaje de cobertura.

Son ejemplos de defectos críticos: pérdida o corrupción de movimientos confirmados; duplicación financiera involuntaria; totales o neto incorrectos; `own_transfer` contabilizada; guardado sin confirmación o propuesta de IA persistida automáticamente; migración destructiva; mensaje de éxito sin persistencia; exposición de secretos o envío inesperado del historial; imposibilidad general de usar el núcleo offline; y bypass de controles que comprometa datos de Production. No se introduce una escala corporativa de severidades.

## 14. Decisiones pendientes y exclusiones

| Decisión | Efecto sobre pruebas futuras |
| --- | --- |
| `TBD-001`, `TBD-002` | Fijar método y metas del tiempo de registro manual y por voz antes de evaluar esos resultados cuantitativos. |
| `TBD-003` | Definir incertidumbre significativa antes de aceptar revisión y sugerencias dependientes de ella. |
| `TBD-004` | Definir historial suficiente antes de aceptar comparaciones y tendencias. |
| `TBD-005` | Concretar conservación, eliminación y comunicación de políticas para datos y artefactos de voz. |
| `TBD-006` | Fijar metas y condiciones de medición de rendimiento antes de aplicar umbrales de aceptación. |
| `TBD-009` | Fijar objetivos, ventanas y línea base de métricas sin confundirlos con la verificación de eventos. |
| `TBD-014` | Concretar conectividad, información, autorización y tratamiento externo antes de aceptar voz/IA. |

`TBD-008` ya está resuelto a nivel de diseño con WCAG 2.2 AA y los valores de `DESIGN_SYSTEM.md`; su conformidad debe probarse. No se fijan aquí proveedores ni políticas aún abiertas. Quedan fuera de esta estrategia un entorno QA o staging permanente, device farm empresarial, chaos engineering, mutation testing obligatorio, plataforma de contratos o gestión de tests, laboratorio de rendimiento, regresión visual cloud, mocks indiscriminados y pruebas directas contra Production.
