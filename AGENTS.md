# Guía operativa para agentes de Nuvora

No inventes producto, arquitectura, alcance ni valores para decisiones pendientes. Implementa la tarea solicitada conforme a los documentos aprobados; si una decisión necesaria sigue abierta, identifícala en vez de resolverla silenciosamente. Prefiere cambios pequeños, localizados, simples, explícitos y verificables. Esta guía orienta el trabajo: no sustituye los requisitos ni los contratos detallados.

## Fuentes de autoridad

Consulta los documentos que gobiernan el tipo de decisión antes de modificarla:

| Decisión | Documentos aprobados |
| --- | --- |
| Visión, alcance, reglas funcionales y prioridades | [VISION](docs/product/VISION.md), [PRD](docs/product/PRD.md), [SRS](docs/product/SRS.md) y [ROADMAP](docs/product/ROADMAP.md). SRS rige los requisitos verificables; ROADMAP, la secuencia y la Definition of Done de cada hito. |
| Recorridos, estados visibles, accesibilidad y tokens | [USER_FLOWS](docs/ux/USER_FLOWS.md) y [DESIGN_SYSTEM](docs/ux/DESIGN_SYSTEM.md). |
| Responsabilidades y dependencias entre capas | [ARCHITECTURE](docs/architecture/ARCHITECTURE.md). |
| Esquema SQLite, dinero, fechas, consultas y migraciones | [DATABASE](docs/architecture/DATABASE.md). |
| Contrato HTTP, estados, errores y reintentos | [API](docs/architecture/API.md). |
| Configuración y separación de entornos | [ENVIRONMENTS](docs/engineering/ENVIRONMENTS.md). |
| Seguridad, privacidad y secretos | [SECURITY](docs/engineering/SECURITY.md). |
| Niveles de prueba y evidencia de aceptación | [TESTING](docs/engineering/TESTING.md). |

Los documentos que no figuran en la tabla anterior no son fuentes autoritativas para introducir nuevas decisiones mientras no hayan sido aprobados explícitamente para ese propósito. `docs/ai/AI_ARCHITECTURE.md`, `docs/ai/AI_EVALUATION.md` y `docs/engineering/RELEASE.md` pueden existir en el repositorio, pero no deben utilizarse para ampliar o modificar las decisiones vigentes hasta que su estado sea aprobado y esta guía se actualice cuando corresponda. `README.md` es un punto de entrada y navegación del repositorio; aunque posteriormente sea aprobado, no prevalece sobre los documentos autoritativos especializados de la tabla anterior. No cambies documentación aprobada por rutina ni para justificar código existente. Si la tarea aprueba un cambio de esquema, contrato API, arquitectura, seguridad o alcance, identifica primero el documento que debe reflejarlo.

Si dos fuentes aprobadas parecen contradecirse, el código contradice una decisión aprobada o la solicitud exige romper una frontera, detén solo la parte afectada. Señala la contradicción y sus archivos o secciones; continúa lo independiente cuando sea seguro y solicita una decisión humana si es indispensable. No reinterpretes silenciosamente un requisito ambiguo.

Los TBD del SRS son decisiones reales pendientes. Comprueba su estado en los documentos aprobados al trabajar; no inventes umbrales, versiones, proveedores ni políticas, ni conviertas una suposición en requisito. Si un TBD bloquea una parte, implementa solo lo independiente y reporta el ID y la parte bloqueada. `TBD-008` quedó resuelto a nivel de diseño en `DESIGN_SYSTEM.md` con WCAG 2.2 AA; la conformidad de la app aún requiere pruebas.

## Producto y arquitectura del MVP

- Lanzamiento inicial: Android, español para Colombia y solo COP; se puede empezar sin cuenta. Expo, React Native y TypeScript son la base mobile, con Expo Router, Expo SQLite y Expo Development Builds + CNG. El backend especializado usa Python, FastAPI y Pydantic cuando una capacidad remota lo requiera.
- SQLite privada en cada dispositivo es la única fuente de verdad financiera. Registro y gestión manual, historial y dashboard funcionan sin conexión y no esperan al backend. El backend no es un repositorio financiero central ni recibe automáticamente el historial.
- La IA interpreta, clasifica, propone y explica; **Financial Engine calcula y aplica las reglas financieras**. Una propuesta pasa por validación del backend, contrato API, validación estructural mobile, Domain, revisión y confirmación humana, Application y, finalmente, SQLite. IA no escribe movimientos, confirma por la persona ni calcula totales como autoridad.
- Una interacción de voz produce como máximo una propuesta de un movimiento. Una propuesta incompleta, incierta, inválida o cancelada no se guarda automáticamente; una instrucción múltiple no se divide en registros. El registro manual permanece disponible cuando voz o IA fallan.

Invariantes que no se deben romper: tipos `expense`, `income` y `own_transfer`; monto mayor que cero y exacto, sin aritmética financiera de punto flotante binario; COP únicamente; `effective_date` determina el período, no `created_at` ni `updated_at`. Gasto e ingreso requieren categoría de su catálogo correspondiente; solo el gasto admite subcategoría compatible opcional; la transferencia propia no usa ninguna. `own_transfer` no cuenta como ingreso ni gasto ni altera el resultado neto, que es ingresos contabilizables menos gastos contabilizables. Consulta SRS y DATABASE para detalles; no copies ni amplíes aquí el catálogo.

## Repositorio y fronteras mobile

Respeta la estructura real `apps/mobile`, `apps/api`, `docs`, `infra` y `scripts`. En mobile, `app/` aloja rutas de Expo Router; `src/features/`, features existentes o necesarias; `domain/`, reglas transversales cuando existan; `infrastructure/`, implementaciones y composición; `shared/`, solo elementos efectivamente compartidos. La estructura crece con capacidades reales: no crees carpetas vacías, módulos futuros, adaptadores sin consumidor ni repositorios remotos preventivos.

- **Presentation → Application → Domain.** Presentation presenta UI, navegación, formularios y estados; no escribe SQLite ni implementa reglas financieras.
- **Application** coordina casos de uso y define los contratos que necesita junto al feature consumidor; no contiene SQL ni depende de componentes UI.
- **Domain** contiene entidades, invariantes y Financial Engine puros. No depende de Application ni importa React, React Native, Expo, SQLite, FastAPI, proveedores de IA, Infrastructure o Presentation.
- **Infrastructure** implementa los contratos mediante SQLite, HTTP o almacenamiento técnico, traduce fallos y puede usar tipos puros de Domain. No depende de Presentation. Conecta implementaciones en la composición y evita imports circulares.

No añadas capas, interfaces ni abstracciones sin una responsabilidad y un consumidor reales.

## Datos, API y seguridad

Antes de tocar SQLite, lee ARCHITECTURE, DATABASE, SECURITY y TESTING. Movimientos confirmados viven solo en SQLite local, nunca en AsyncStorage o SecureStore como repositorio financiero. No crees tablas ni campos especulativos. Usa SQL parametrizado, operaciones atómicas cuando correspondan y migraciones versionadas que preserven datos; un reset destructivo no es una migración normal. Todo cambio de esquema requiere migración, pruebas pertinentes y compatibilidad con DATABASE. Si la contradice, detén ese cambio hasta que exista una decisión documentada.

El único endpoint funcional remoto aprobado es `POST /v1/voice/movement-proposals`. No agregues CRUD remoto de movimientos o categorías, historial, dashboard, sync o auth sin decisión explícita en arquitectura y API. El backend valida request y salida del proveedor, protege secretos y normaliza errores; no persiste movimientos. No reutilices el modelo SQLite como DTO remoto ni reintentes silenciosamente un POST de voz con resultado ambiguo. Tampoco persistas automáticamente audio, transcripciones, prompts, respuestas crudas o propuestas sin confirmar.

Distingue errores de dominio, validación corregible, persistencia local, red/proveedor e imprevistos. Infrastructure traduce fallos técnicos; Presentation comunica una salida segura y comprensible. No muestres stack traces, secretos ni errores crudos del proveedor, y no comuniques éxito antes de completar la operación persistente.

Antes de tocar secretos, permisos, audio, logs, analytics, red, almacenamiento o protección del backend, lee SECURITY y ENVIRONMENTS. Los secretos de proveedores quedan solo en backend: `EXPO_PUBLIC_*` es público. Production usa HTTPS; Development y pruebas no usan secretos ni datos financieros reales de Production. Mantén datos financieros y audio temporal en almacenamiento privado; evita su backup o transferencia automática en Android según SECURITY. El MVP no exige SQLCipher. Envía al backend solo los datos del contrato y no incluyas por defecto contenido financiero, audio, transcripciones, prompts ni secretos en logs o analytics. El permiso de micrófono no reemplaza la información y autorización aplicables; `TBD-005` y `TBD-014` siguen gobernando su definición. No inventes autenticación obligatoria.

## UI y accesibilidad

Antes de cambiar UI, lee USER_FLOWS, DESIGN_SYSTEM y los requisitos SRS de la capacidad. Usa los tokens aprobados en vez de fijar valores visuales locales; Inter y Lucide Icons son las familias aprobadas. Verifica WCAG 2.2 AA en la interfaz real, incluido texto escalado, contraste, nombres y estados accesibles y área táctil efectiva mínima de 48 × 48 dp. No dependas solo del color ni de iconos ambiguos sin texto o nombre accesible; identifica categorías principalmente por nombre. Comunica incertidumbre y resultados reales: éxito financiero solo después de persistir.

## Flujo de trabajo, dependencias y pruebas

1. Lee la solicitud exacta; inspecciona estado, árbol, código vecino, manifests y configuración del componente antes de editar. No supongas scripts, gestor de paquetes, puertos, frameworks de prueba o dependencias instaladas.
2. Lee las fuentes aprobadas pertinentes, identifica el hito, las fronteras y los TBD que afectan la tarea. Sigue las convenciones existentes cuando no contradigan esas fuentes.
3. Haz el cambio mínimo coherente. No aproveches para reformatear, mover, renombrar, actualizar dependencias o refactorizar áreas ajenas. Reporta problemas externos al alcance; si uno compromete seguridad o integridad de datos, señálalo inmediatamente.
4. Añade o actualiza las pruebas que demuestren el límite modificado y ejecuta las verificaciones pertinentes disponibles. Descubre comandos en los manifests y configuración reales; si falta uno, informa su ausencia sin afirmar que se ejecutó.
5. Revisa el diff completo y reporta cambios, verificaciones reales y pendientes. No declares éxito, pruebas pasadas ni revisión manual que no hayan ocurrido.

Sigue TESTING: Domain y Financial Engine requieren pruebas unitarias; Application, casos de uso con dobles; SQLite y migraciones, integración con SQLite real; API, contrato e integración HTTP; UI, comportamiento perceptible cuando aporte valor; capacidades Android, comprobación en Android cuando dependan del dispositivo. Un mock no demuestra persistencia, operación offline ni permisos reales. Un defecto financiero o de integridad corregido debe tener prueba de regresión cuando sea razonable. No fijes un porcentaje de cobertura.

La suite determinista normal no llama proveedores reales de STT, IA, analytics o crash reporting; usa adaptadores o dobles. Las integraciones live justificadas se ejecutan aparte en Development con datos sintéticos y sin secretos Production. Usa fixtures ficticios y reloj controlado para casos sensibles al tiempo. No conviertas una respuesta probabilística del modelo en un assert determinista ordinario.

Agrega una dependencia solo si la capacidad está aprobada, la plataforma y el proyecto carecen de una solución suficiente, respeta la arquitectura y su costo se justifica. Limítala al componente consumidor, explica el motivo y actualiza manifests, lockfile y pruebas pertinentes. No actualices paquetes ajenos a la tarea.

## Anticipación, documentación y Git

No incorpores por anticipación sync, repositorio financiero cloud, PostgreSQL financiero, Redis, microservicios, Kafka, event bus, CQRS, event sourcing, cuentas o auth obligatoria, presupuestos, metas, deudas avanzadas, multimoneda, iOS, ingestión de notificaciones, asistente financiero completo, feature flags remotos ni infraestructura enterprise. Tampoco añadas un ORM, Redux global, GraphQL, gRPC o paquetes «por si acaso». Una capacidad futura aprobada requiere actualizar la autoridad correspondiente antes o junto con su adopción.

No borres cambios ajenos ni ejecutes automáticamente reset destructivo, borrado masivo, force push o eliminación de datos. No hagas commits salvo solicitud explícita. El código debe ser legible, pequeño y explícito; evita errores tragados silenciosamente, `any` para eludir validación, números mágicos frente a tokens aprobados y frameworks internos para una sola tarea.

## Cierre de cada tarea

Declara una tarea terminada cuando el comportamiento solicitado esté implementado sin ampliar alcance, respete las fuentes aprobadas, tenga pruebas pertinentes, hayan pasado las verificaciones disponibles necesarias, no introduzca errores conocidos y el diff esté revisado. Si no pudiste verificar algo, indica exactamente qué faltó, por qué y qué queda por comprobar; un TBD bloqueante o una decisión humana pendiente impiden afirmar que esa parte está terminada.

Responde brevemente con **Cambios** (archivos y comportamiento), **Verificación** (checks realmente ejecutados y resultado) y **Pendientes** solo si existen bloqueos, riesgos o verificaciones sin ejecutar. No expongas razonamiento interno ni datos sensibles.

Cuando dudes entre una solución mayor y una suficiente que respete los documentos aprobados, elige la suficiente. Nuvora crece por capacidades reales y decisiones explícitas, no por anticipación técnica.
