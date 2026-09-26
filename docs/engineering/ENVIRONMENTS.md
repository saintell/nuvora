# Nuvora — Entornos y configuración del MVP

## 1. Propósito y límites

Este documento define los entornos y los límites de configuración del MVP. Se rige por `VISION.md`, `PRD.md`, `SRS.md`, `ROADMAP.md`, `USER_FLOWS.md`, `DESIGN_SYSTEM.md` y por las decisiones aprobadas en `ARCHITECTURE.md`, `DATABASE.md` y `API.md`. Nuvora se lanza inicialmente en Android, en español para Colombia y con COP como única moneda.

El documento establece qué configuración necesita cada capacidad y dónde puede residir. No selecciona hosting, proveedores, mecanismos de despliegue ni políticas completas de seguridad, pruebas o release.

## 2. Separación entre núcleo local y capacidades remotas

SQLite en cada instalación es la única fuente de verdad financiera del dispositivo; no existe una base SQLite central compartida entre entornos o dispositivos. El backend es un monolito modular pequeño con FastAPI, Pydantic y adaptadores de proveedores, destinado a voz, IA y otras integraciones que requieran secretos. No recibe una réplica automática del historial, no ejecuta el CRUD financiero ni calcula el dashboard como autoridad. La API versionada bajo `/v1` entrega propuestas para revisión; únicamente el mobile valida, obtiene la confirmación de la persona y guarda un movimiento en SQLite.

El *walking skeleton* y el desarrollo del registro manual, SQLite, historial, edición, eliminación, Financial Engine y dashboard no requieren backend ni conexión. Antes de habilitar voz o IA, mobile debe arrancar y ofrecer todo el núcleo financiero aunque falten una URL de API, proveedores STT o IA y secretos de backend. Una capacidad remota solo exige su configuración cuando entra en alcance; su ausencia o fallo no convierte los datos financieros locales en inválidos ni impide el registro manual.

Las reglas financieras, los catálogos aprobados y el comportamiento funcional central son idénticos en Development y Production. `NODE_ENV`, `APP_ENV` y `EXPO_PUBLIC_APP_ENV` no modifican reglas de dominio, cálculo, validación ni contabilización de transferencias propias. Las diferencias entre entornos son operativas.

## 3. Entornos del MVP

**Development** sirve para desarrollo local, Expo Development Builds, integración mobile, pruebas manuales durante implementación y, cuando corresponda, integración con el backend. Puede usar un backend local o uno remoto de desarrollo y credenciales de proveedores destinadas a desarrollo una vez seleccionados. No usa configuración ni secretos de producción. Expo Development Builds y Continuous Native Generation (CNG) son la estrategia aprobada; Expo Go no determina los límites de la aplicación. Este documento no elige un servicio de distribución o compilación de builds.

**Production** sirve a la aplicación distribuida a usuarios reales y al backend de las capacidades remotas disponibles. Usa configuración y proveedores destinados a producción, endpoint HTTPS y secretos inyectados desde un mecanismo seguro de la plataforma que se seleccione posteriormente.

Son los únicos dos entornos desplegables previstos para el MVP. No se crea de forma anticipada un entorno permanente `staging`, `qa`, `preview` o `sandbox`; una necesidad operativa futura podrá justificarlo sin cambiar estos principios.

| Aspecto | Development | Production |
| --- | --- | --- |
| Mobile | Expo Development Build | Build distribuido a usuarios |
| SQLite | Local a cada instalación | Local a cada instalación |
| Backend para núcleo manual y dashboard | No requerido | No requerido |
| Backend para voz/IA | Se ejecuta cuando se integra o prueba la capacidad | Atiende la capacidad cuando está disponible |
| API base URL | Configurable para backend local o remoto de desarrollo; puede faltar antes de voz | URL del backend de producción mediante HTTPS cuando hay capacidad remota |
| Credenciales de proveedores | Exclusivas del backend de desarrollo, si aplican | Exclusivas del backend de producción, si aplican |
| Datos financieros | Ficticios o controlados para desarrollo | Datos reales de la persona en SQLite local |
| Secretos en mobile | Nunca | Nunca |
| Logs | Diagnóstico técnico controlado | Verbosidad mínima necesaria |

## 4. Configuración pública de mobile

Todo valor que el código mobile deba conocer es **configuración pública**. Un valor `EXPO_PUBLIC_*` queda disponible en el bundle de la aplicación y debe considerarse visible para quien tenga acceso a ella. El prefijo no ofrece almacenamiento seguro.

| Variable conceptual | Valores o función | Cuándo se requiere |
| --- | --- | --- |
| `EXPO_PUBLIC_APP_ENV` | Identifica la configuración operativa del build: `development` o `production`. | Desde la configuración inicial del mobile. |
| `EXPO_PUBLIC_API_BASE_URL` | URL base configurable del backend Nuvora. | Solo cuando el build ofrece una capacidad remota que la consume. |

`EXPO_PUBLIC_APP_ENV` no determina reglas financieras. El contexto automatizado `test` puede manejarse mediante configuración propia de las pruebas, pero no representa un valor de un build distribuido.

En Development, `EXPO_PUBLIC_API_BASE_URL` puede variar entre emulador Android, dispositivo físico, backend local y backend remoto de desarrollo. Ejemplos conceptuales son `http://<host-local>:<port>` y `https://<backend-development>`; no se fija IP LAN, puerto ni dominio. HTTP local se admite cuando sea necesario dentro del desarrollo controlado. En Production, la URL apunta al backend de producción y usa HTTPS. Ninguna feature debe tener una URL de backend hardcodeada o editada manualmente antes de compilar: cada build recibe su configuración correspondiente.

Antes de voz/IA, la URL puede estar ausente sin impedir abrir la aplicación o usar SQLite, movimientos, historial y dashboard. Cuando se habilite una función remota, mobile validará que el valor público consumido sea adecuado para ese entorno. Una URL ausente o inválida hará que la función remota se trate como no disponible y permita continuar con el flujo manual; no alterará los datos locales ni presentará una propuesta como movimiento guardado.

Nunca se colocan en `EXPO_PUBLIC_*` API keys, credenciales STT o IA, passwords, tokens privados, secretos de proveedores o infraestructura, secretos de firma ni información financiera del usuario. Expo SecureStore no se utiliza para ocultar secretos de infraestructura que nunca deberían distribuirse al dispositivo.

## 5. Configuración del backend y clasificación de valores

El backend recibe configuración por variables de entorno o un mecanismo equivalente de inyección. Solo se ejecuta cuando alguna capacidad remota lo requiere. Sus valores iniciales no secretos son:

| Variable conceptual | Valores o función | Regla |
| --- | --- | --- |
| `APP_ENV` | `development` o `production`. | Selecciona configuración operativa externa y diagnóstico o tooling apropiado; no cambia reglas financieras. |
| `LOG_LEVEL` | Verbosidad técnica. | Puede tener un valor razonable de desarrollo si la implementación lo define; nunca autoriza registrar secretos o contenido financiero sensible. |

Cuando se aprueben los proveedores, el backend incorporará la selección/configuración del proveedor STT, su credencial secreta, la selección/configuración del proveedor IA, su credencial secreta y solo los parámetros operativos realmente necesarios. Las credenciales y cualquier futuro secreto operativo justificado existen exclusivamente en backend; no llegan al mobile. Los nombres concretos de variables de proveedores se definirán después de seleccionarlos.

La clasificación de cualquier futura configuración de analytics u observabilidad dependerá de sus privilegios: un identificador diseñado para cliente podrá ser público; una credencial que conceda acceso privado permanecerá en backend. Este documento no selecciona proveedor ni introduce variables por anticipación.

## 6. Aislamiento, valores por defecto y validación

Development y Production deben obtener configuraciones separadas por entorno y build, sin editar código fuente para alternar URLs o credenciales. No se reutilizan secretos de producción en desarrollo. Los valores reales de archivos locales de variables de entorno, si se emplean, no se versionan y deben quedar cubiertos por `.gitignore`. Un futuro `.env.example` podrá listar nombres y placeholders inocuos, sin secretos reales; tampoco se copian secretos a la documentación.

En Production, los secretos se inyectan desde el mecanismo seguro de la plataforma de despliegue que se elija. No se guardan en Git, no se empaquetan en APK/AAB, no forman parte de `EXPO_PUBLIC_*` y no se imprimen en logs. Cada componente y capacidad recibe solo el acceso necesario. La comunicación mobile–backend de producción usa HTTPS; la configuración de certificados, terminación TLS y red pertenece a decisiones posteriores.

Mobile valida los valores públicos que consume. Backend valida al iniciar la configuración necesaria para las capacidades habilitadas. Si falta una credencial requerida, la inicialización falla explícitamente o esa capacidad queda declarada no disponible según la composición implementada; no opera silenciosamente con configuración parcial. No hay fallback a una URL de producción, una credencial de producción, una API key dummy ni un secreto hardcodeado. Si el entorno requerido es ambiguo, se falla explícitamente en vez de escoger Production por defecto. La definición de health checks queda pendiente.

## 7. Configuración exigida por el roadmap

| Hito | Configuración exigida en esa etapa |
| --- | --- |
| 0 — Foundation | Este contrato de entornos y límites de valores; sin despliegue de backend obligatorio. |
| 1 — Walking skeleton | Configuración operativa básica del mobile para Development Build; `EXPO_PUBLIC_APP_ENV` identifica el build. No requiere API URL ni backend. |
| 2–4 — Núcleo manual, dashboard y robustez offline | Persistencia SQLite y reglas locales; sin proveedor STT, IA, API URL ni secreto de backend como requisito para usar el núcleo. |
| 5 — Captura por voz | Al habilitar y probar la transcripción remota, URL de backend y configuración backend/STT de Development; para ofrecerla a usuarios, sus equivalentes de Production. Se resuelven previamente las decisiones aplicables de privacidad y conectividad del roadmap. |
| 6 — Interpretación inteligente | Añadir configuración y credencial de IA en el backend correspondiente para completar la propuesta remota, manteniendo validación, revisión y persistencia local en mobile. |
| 7 — Categorización y medición | La configuración de analytics se concreta solo cuando se defina su implementación; no se presupone proveedor ni variable. |
| 8–9 — Preparación y validación con usuarios | Verificar la configuración de Production de las capacidades entregadas y su separación de Development. |

Esta secuencia no adelanta el backend al *walking skeleton*. El hito 5 requiere únicamente la configuración necesaria para validar la captura y obtener una transcripción revisable conforme al `ROADMAP.md`; el hito 6 incorpora la interpretación estructurada mediante IA. `POST /v1/voice/movement-proposals` representa el contrato funcional final del MVP para el flujo remoto completo definido en `API.md`. Este documento describe qué configuración se necesita en cada etapa y no define contratos HTTP transitorios utilizados durante la construcción.

## 8. Datos locales y contexto de test

Cada instalación conserva su propia base SQLite. No se copian bases de producción a Development como flujo normal ni se crean seeds automáticos con datos financieros reales. Para desarrollo y pruebas se utilizan datos ficticios o controlados; el detalle de fixtures corresponde a `TESTING.md`.

`test` es un modo aislado de ejecución de pruebas automatizadas, **no un tercer entorno desplegado**. No requiere infraestructura dedicada en este documento. Su configuración no debe apuntar accidentalmente a Production, sus pruebas no utilizan datos financieros reales y las pruebas del núcleo local no requieren backend. Las integraciones remotas podrán usar dobles o un entorno Development controlado conforme a la estrategia que se defina en `TESTING.md`.

## 9. Decisiones pendientes y fuera de alcance

Permanecen abiertos los proveedores STT e IA y sus nombres de variables, la plataforma de hosting y su mecanismo de inyección de secretos, el proveedor y configuración de analytics y observabilidad, y los parámetros operativos que dependen de esas selecciones. También siguen pendientes las decisiones aplicables del roadmap sobre tratamiento de voz y datos, conectividad, protección técnica y medición; este documento no las resuelve.

No se diseña aquí infraestructura cloud, base financiera remota, PostgreSQL, Redis, sincronización, backup cloud, cuenta de usuario, API Gateway, plataforma de feature flags, CI/CD, distribución de builds, identificadores de aplicación, firma, canales de tienda, política completa de logging, seguridad, testing ni release. La presencia progresiva de funciones sigue el roadmap y el código de cada build; no requiere habilitación remota de features. Un pipeline futuro deberá inyectar la configuración del entorno correspondiente sin incorporar secretos al repositorio.
