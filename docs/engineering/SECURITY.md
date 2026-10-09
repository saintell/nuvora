# Nuvora — Seguridad y privacidad técnica del MVP

## 1. Propósito y límites

Este documento define los controles de seguridad y privacidad técnica del MVP de Nuvora, inicialmente para Android. Se rige por `VISION.md`, `PRD.md`, `SRS.md`, `ROADMAP.md`, `USER_FLOWS.md` y `DESIGN_SYSTEM.md`, y conserva las decisiones aprobadas de `ARCHITECTURE.md`, `DATABASE.md`, `API.md` y `ENVIRONMENTS.md`. Establece requisitos para la implementación; no certifica que ya estén implementados ni sustituye una política legal, un pentest, una guía de despliegue o una estrategia de pruebas.

**Límite central:** SQLite privada en cada dispositivo es la única fuente de verdad financiera. El registro manual, la gestión de movimientos, el historial y el dashboard funcionan sin conexión y sin cuenta. No hay backend financiero central, sincronización, backup cloud propio, multidispositivo, login, JWT ni refresh token de usuario. El backend especializado atiende voz e IA y recibe solo los datos necesarios para cada solicitud; no recibe automáticamente el historial ni tiene autoridad para escribir movimientos. **La IA interpreta y explica; el motor financiero calcula y ejecuta.** Solo un movimiento revisado, validado y confirmado se guarda localmente.

## 2. Activos y límites de confianza

| Clase | Contenido | Tratamiento base |
| --- | --- | --- |
| Datos financieros locales — sensibles | Movimientos, montos, conceptos, categorías, fechas efectivas, historial y resultados derivados del dashboard. | Fuente de verdad en SQLite privada; acceso a través de los casos de uso y reglas locales. |
| Datos transitorios de voz — sensibles | Audio, transcripción, propuesta estructurada y correcciones de la revisión que puedan revelar información financiera. | Solo durante el flujo necesario; no se incorporan automáticamente a la base financiera. |
| Secretos — altamente sensibles | Credenciales STT e IA, futuros secretos operativos y credenciales privadas de infraestructura. | Solo en backend y configuración segura del entorno; nunca en mobile, Git, respuestas o telemetría. |
| Configuración pública | `EXPO_PUBLIC_APP_ENV`, `EXPO_PUBLIC_API_BASE_URL` y cualquier otro valor expuesto al bundle. | Se presume visible; no sirve como secreto ni como credencial para proteger la API. |
| Telemetría | Estados, tiempos y errores técnicos o eventos de producto necesarios. | Minimizada y sin contenido financiero por defecto; no constituye una segunda base financiera. |

La persona controla la captura y confirma los cambios, pero los datos introducidos por ella siguen sujetos a validación. Android delimita el almacenamiento privado y los permisos de la aplicación; otras aplicaciones no deben acceder a los archivos privados por canales creados por Nuvora. El límite mobile–backend cruza una red no confiable: el backend no confía en el request, aunque proceda de la app. El límite backend–proveedores externos exige minimización, transporte cifrado y validación de sus respuestas. La transcripción y la salida de IA son entradas no confiables incluso cuando tengan JSON válido. Ni el backend ni los proveedores cruzan el límite hacia SQLite como autoridad financiera.

El modelo de amenazas del MVP contempla acceso indebido de otras apps o exposición accidental de archivos; secretos empaquetados o filtrados en Git y logs; interceptación de tráfico; requests manipulados, audio malformado o excesivo; salida inválida o maliciosa del modelo, incluida prompt injection en voz o transcripción; abuso del endpoint con costo de proveedor; envío excesivo de datos; filtración por logs, crashes o analytics; mezcla de entornos; y backups o transferencias automáticas del sistema operativo contrarias al alcance local. Los controles siguientes reducen esos riesgos sin prometer protección frente a un sistema operativo completamente comprometido.

## 3. Datos locales y operaciones financieras

SQLite y cualquier archivo financiero necesario permanecen en almacenamiento privado de la app, nunca en almacenamiento público o compartido. Nuvora no exporta automáticamente la base, no crea archivos financieros temporales innecesarios y no duplica movimientos en AsyncStorage. Expo SecureStore no es repositorio financiero. La protección base se apoya en el sandbox de Android, el almacenamiento privado y las protecciones del dispositivo ofrecidas por el sistema operativo.

El MVP **no añade una segunda capa propia de cifrado SQLite** ni exige SQLCipher. Así evita la complejidad y gestión de claves de una capa adicional sin cuenta o autenticación propia. El cifrado que pueda ofrecer Android al dispositivo no se presenta como cifrado de base gestionado por Nuvora. Una necesidad futura de cifrado adicional se evaluará con un modelo de amenaza actualizado.

La configuración Android debe impedir que la base financiera y los artefactos sensibles de Nuvora entren automáticamente en cloud backup, restauración automática o transferencia automática entre dispositivos cuando impliquen copiarlos fuera del almacenamiento local previsto. También debe excluir el audio temporal. La configuración nativa/CNG concreta y las diferencias entre versiones Android se verificarán durante implementación en el rango compatible definido por el SRS: Android 10 (API 29) o superior; aquí no se prescriben reglas XML ni de manifest. Esto mantiene coherencia con la ausencia de backup cloud y multidispositivo del MVP.

Crear, editar y eliminar requiere una acción deliberada de la persona sobre datos visibles y válidos. Presentation no escribe directamente en SQLite: Application coordina la intención, Domain valida las reglas financieras e Infrastructure ejecuta SQL parametrizado y las transacciones locales definidas en `DATABASE.md`. Una propuesta de IA, una cancelación o un fallo no escriben; el éxito solo se comunica tras persistencia real. La eliminación confirmada ejecuta `DELETE` físico de la fila, sin soft delete ni tombstones. El movimiento deja de formar parte del estado activo, pero Nuvora no promete borrado forense de bytes en un dispositivo comprometido.

## 4. Micrófono, audio y privacidad de voz

El núcleo manual no solicita permiso de micrófono. Cuando la persona inicia una función que necesita voz, Nuvora explica antes qué capturará y para qué, solicita el permiso técnico Android cuando corresponda y permite rechazarlo sin bloquear el registro manual. La captura comienza por acción explícita, tiene un indicador perceptible durante toda su actividad y termina al detener o cancelar. No hay escucha pasiva ni captura en background como comportamiento del MVP; tampoco se solicitan permisos ajenos a sus funciones.

El permiso Android, la información sobre captura y tratamiento, y las condiciones aplicables al envío externo son asuntos distintos. Conceder el permiso del sistema no resuelve por sí solo la información o autorización de privacidad. Antes de enviar audio, la experiencia debe explicar qué clases de datos se envían, para qué y cuándo, de acuerdo con `RNF-PRIV-001–005` y el cierre de `TBD-014`. El texto definitivo y las condiciones de conservación siguen sujetos a `TBD-005` y `TBD-014`; este documento no redacta consentimiento legal.

El audio usado para una solicitud es temporal. Si se crea un archivo local, reside en almacenamiento privado, fuera de galería, almacenamiento compartido y backups; se elimina cuando deja de ser necesario tras éxito, cancelación o fallo, en la medida técnicamente aplicable. No existe historial de voz del MVP. La transcripción, propuesta y correcciones pueden mantenerse durante revisión y confirmación, pero no se guardan automáticamente como entidades SQLite. No se crean tablas de transcripciones, prompts o respuestas de IA. Cancelar, fallar o recibir una propuesta tardía nunca convierte datos sin confirmar en un movimiento.

## 5. Tratamiento remoto y proveedores

El único endpoint funcional aprobado, `POST /v1/voice/movement-proposals`, recibe exclusivamente `audio` y `reference_datetime` en `multipart/form-data`, conforme a `API.md`. La referencia temporal sirve para interpretar expresiones relativas, no determina la fecha efectiva final. Mobile no adjunta automáticamente historial, dashboard, movimientos previos, IDs financieros locales, nombre, email, teléfono ni ubicación precisa. El backend usa el contenido solo para STT e interpretación de la solicitud puntual; no construye un perfil financiero remoto ni calcula resultados financieros como autoridad.

El backend no necesita una base de datos de audio o transcripciones ni conserva copias persistentes de requests de voz en almacenamiento de aplicación. Procesa los datos dentro del ciclo de la solicitud y libera artefactos temporales cuando ya no son necesarios. Esto **no equivale a afirmar retención cero del proveedor externo**. Antes de aprobar proveedores STT o IA para Production se evaluarán los datos que reciben, conservación y plazos, finalidad, entrenamiento o mejora, opciones para limitar uso y retención, regiones o transferencias relevantes, controles de seguridad y condiciones contractuales pertinentes. La evaluación y la información resultante deberán cerrar las partes aplicables de `TBD-005` y `TBD-014` antes de ofrecer voz o IA a usuarios reales. No se selecciona proveedor ni plazo aquí.

## 6. Backend, API e IA no confiable

El backend valida todas las entradas sin confiar en mobile: estructura multipart, presencia y contenido de las partes, forma de `reference_datetime`, formato, tamaño y duración de audio conforme a límites configurados, y cualquier otro campo del contrato. Aplica límites de payload y timeouts, restringe formatos, normaliza errores según `API.md` y no devuelve stack traces, prompts, respuesta cruda del proveedor ni secretos. No ejecuta contenido recibido como código ni concatena entradas del request en SQL. No posee una base financiera remota ni permiso para persistir movimientos.

Transcripción, `explanation`, `type`, `amount`, fecha, categoría y demás campos propuestos por IA se tratan como no confiables. El backend valida el esquema y convierte la respuesta del proveedor al contrato aprobado; mobile valida la respuesta con Zod; Domain exige las reglas financieras; la persona revisa y confirma. Un JSON válido no sustituye ninguna de esas barreras. Una instrucción incrustada en audio o transcripción no concede al modelo herramientas financieras, acceso a secretos o configuración, escritura SQLite, creación de múltiples movimientos, omisión de confirmación ni autoridad para decidir resultados financieros. La interpretación de más de un movimiento en una misma captura no provoca división o guardado automático. No se exponen chain-of-thought ni razonamientos internos en respuestas o logs.

El endpoint no requiere cuenta de usuario, pero sí protección operativa: rate limiting en backend, límites de formato, tamaño y duración de audio, timeouts, presupuestos o límites del proveedor cuando existan, y vigilancia de errores y consumo. Se usa `429` y el código `RATE_LIMITED` del contrato cuando corresponda. Los valores se fijarán durante implementación según proveedor y capacidad; no se introduce Redis solo para limitar solicitudes. Un POST con resultado remoto ambiguo no se reintenta silenciosamente desde mobile, conforme a `API.md`.

Antes de exponer voz en Production se debe evaluar y aplicar un mecanismo verificable por servidor para distinguir tráfico de una app Nuvora legítima cuando la plataforma de distribución elegida lo permita, por ejemplo una prueba de integridad de app o dispositivo. La selección concreta depende de la distribución y permanece pendiente. Este control complementa rate limiting, validación, límites y presupuestos; una API key embebida en APK/AAB se considera pública y no lo sustituye.

## 7. Secretos, entornos y transporte

Los secretos de STT, IA e infraestructura viven exclusivamente en backend, fuera de Git, `EXPO_PUBLIC_*`, APK/AAB, respuestas, logs y analytics. Production los inyecta mediante la configuración segura de la plataforma que se seleccione; cada componente recibe el acceso mínimo necesario. Development y Production usan endpoints, configuración y secretos separados. Production nunca acepta silenciosamente valores de Development. Un bypass exclusivamente de Development debe ser explícito, condicionado a `APP_ENV=development`, desactivado por defecto y sin fallback en Production; no existen claves maestras. Ante sospecha de exposición se revoca o rota la credencial afectada, se revisan configuración y logs relacionados y se impide seguir usándola.

En Production, mobile–backend usa HTTPS sin HTTP plaintext, y backend–proveedores usa el transporte cifrado ofrecido o requerido por ellos. Development local puede usar HTTP conforme a `ENVIRONMENTS.md`. No se exige certificate pinning, mTLS ni se elige terminación TLS, proxy o CDN en este documento.

## 8. Logs, observabilidad y analytics

Por defecto no se registran audio, transcripciones completas, montos, conceptos, historial, prompts, respuesta cruda de IA, claves, tokens, headers sensibles ni contenido SQLite. La observabilidad técnica puede usar `request_id`, código HTTP, latencia, tipo de error y adaptador lógico de proveedor; tamaños o duraciones pueden agruparse en rangos si son necesarios para operar. `request_id` es correlación técnica, nunca ID de usuario, dispositivo o movimiento ni clave para reunir un historial financiero.

Si se incorpora crash reporting, se sanitizan breadcrumbs y contexto y no se adjuntan por defecto dumps financieros completos, audio, transcripciones o secretos. No se selecciona proveedor de observabilidad. Analytics mide las métricas requeridas por PRD/SRS mediante eventos de flujo iniciado, completado o abandonado, propuesta aceptada o corregida, sugerencia de categoría aceptada o corregida y errores técnicos categorizados. Por defecto no envía montos, conceptos, audio, transcripciones, historial ni salida cruda de IA. Cualquier dato más sensible para una métrica futura exige justificación y revisión antes de implementarse; analytics no es una copia de SQLite.

## 9. Dependencias, datos de desarrollo e incidentes

Las dependencias se declaran y versionan mediante los mecanismos normales del proyecto. Se agregan solo cuando son necesarias, se revisan vulnerabilidades conocidas relevantes antes de releases y se actualizan dependencias críticas de seguridad de forma controlada. No se ejecutan scripts o binarios no confiables como parte normal del build; la automatización concreta de revisión queda para la implementación y CI.

Development y pruebas usan datos financieros ficticios o controlados y, cuando sea inevitable usar un proveedor real, credenciales Development. No se copian bases Production a Development, no se usan secretos Production en pruebas y la configuración de test no debe apuntar accidentalmente a Production. El núcleo local se puede verificar sin backend.

Ante un incidente confirmado o sospechado se contiene el componente o capacidad afectada, se revocan o rotan secretos comprometidos, se revisan los logs técnicos disponibles sin ampliar innecesariamente la recopilación, se evalúan los datos posiblemente afectados, se corrige la causa antes de reactivar y se documentan el incidente y las acciones. Si solo afecta voz o IA, esa capacidad debe poder deshabilitarse o retirarse mientras el núcleo financiero local continúa accesible.

## 10. Riesgos residuales y decisiones abiertas

- Una persona con acceso legítimo a un dispositivo desbloqueado puede abrir Nuvora: el MVP no exige bloqueo interno, PIN, biometría ni cuenta.
- Un dispositivo rooteado, un sistema operativo comprometido o análisis forense avanzado con control físico quedan fuera de las garantías razonables del MVP. Nuvora no añade cifrado propio de SQLite ni promete borrado forense.
- Sin backup o recuperación cloud de Nuvora, perder o reinstalar el dispositivo puede causar pérdida de información. Las decisiones futuras de backup y sincronización pertenecen a `TBD-010`, fuera del MVP.
- Proveedores externos introducen riesgos de privacidad y disponibilidad que deben evaluarse antes de Production. Un endpoint remoto sin cuenta conserva riesgo de abuso aun con límites e integridad de app.

Siguen abiertos `TBD-005` (conservación, eliminación y comunicación de políticas) y `TBD-014` (conectividad, información, autorización y tratamiento externo). Antes de habilitar cada capacidad remota se concretarán proveedores STT e IA, formatos y límites de audio, timeouts, valores de rate limiting, presupuestos, distribución y mecanismo de integridad de app, hosting e inyección de secretos. No se eligen aquí secret manager, observabilidad, analytics ni textos legales. La política de incertidumbre de propuestas sigue `TBD-003` y la medición que la requiera sigue los TBD aprobados del SRS.

Este documento no introduce autenticación obligatoria, OAuth, JWT de usuario, PIN, biometría, sync, backup cloud propio, PostgreSQL, Redis, infraestructura enterprise, detección anti-root obligatoria, remote wipe ni cifrado SQLite adicional como requisitos del MVP.

## 11. Trazabilidad al SRS

| Requisito | Controles de este documento |
| --- | --- |
| `RNF-SEC-001` | Sandbox y almacenamiento privado; HTTPS; validación en backend y mobile; reglas Domain y control de escritura SQLite. |
| `RNF-SEC-002` | Secretos solo en backend y fuera de bundle, respuestas, logs, crashes y analytics. |
| `RNF-SEC-003` | Mínimo privilegio, permiso de micrófono bajo demanda y envío remoto limitado a los datos del contrato. |
| `RNF-SEC-004` | Confirmación deliberada para crear, editar y eliminar; resultado verificable tras persistencia y `DELETE` real. |
| `RNF-PRIV-001` | Información y acción o autorización aplicable antes de capturar audio. |
| `RNF-PRIV-002` | Indicador perceptible de inicio y fin de la captura activa. |
| `RNF-PRIV-003` | Explicación del uso de audio, transcripción, movimiento y correcciones en el flujo. |
| `RNF-PRIV-004` | Información previa sobre clases de datos, finalidad y momento del envío externo; detalles pendientes de `TBD-014`. |
| `RNF-PRIV-005` | Distinción entre persistencia local confirmada y tratamiento temporal de voz; política comunicable y períodos pendientes de `TBD-005`. |
