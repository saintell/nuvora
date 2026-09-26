# Nuvora — API remota del MVP

## 1. Propósito y límite

Este documento define el contrato HTTP entre el mobile y el backend para la captura de voz asistida del MVP. Respeta `VISION.md`, `PRD.md`, `SRS.md`, `ROADMAP.md`, `USER_FLOWS.md`, `DESIGN_SYSTEM.md` y las decisiones aprobadas en `ARCHITECTURE.md` y `DATABASE.md`. El lanzamiento inicial es Android, en español para Colombia y únicamente en COP.

**La API no es un repositorio financiero remoto.** SQLite en el dispositivo es la única fuente de verdad de los movimientos. El registro manual, la gestión del historial y el dashboard funcionan localmente, incluso sin conexión. El backend, un monolito modular pequeño con FastAPI, Pydantic y adaptadores de proveedores, atiende capacidades que requieren secretos, Speech-to-Text, IA o integraciones externas. No persiste movimientos, no recibe automáticamente el historial, no confirma registros ni calcula ingresos, gastos o resultado neto como autoridad.

> La IA interpreta y explica; el motor financiero calcula y ejecuta.

## 2. Contrato y flujo

La API utiliza HTTP con contratos versionados bajo `/v1`. Las respuestas funcionales y los errores utilizan JSON; el endpoint de voz recibe `multipart/form-data` porque transporta audio junto con campos estructurados. El único endpoint funcional del MVP es `POST /v1/voice/movement-proposals`. Su resultado es una **propuesta**, nunca un `Movement` persistido ni un DTO de la tabla SQLite.

1. La persona inicia explícitamente la captura en mobile, con la información y autorización aplicables. Mobile envía el audio de esa interacción y una referencia temporal.
2. El backend valida el request y el audio, solicita la transcripción al proveedor, exige una transcripción utilizable, solicita la interpretación a IA y valida estructuralmente su salida. Los adaptadores ocultan los contratos particulares de los proveedores.
3. El backend devuelve la transcripción y un resultado estructurado con, como máximo, una propuesta parcial. No escribe en el dispositivo ni mantiene un repositorio de movimientos.
4. Mobile valida la respuesta con Zod, presenta transcripción, propuesta, ausencias e incertidumbres, y aplica las reglas deterministas de Domain. La persona puede corregir, confirmar una versión válida o cancelar. Solo después de la confirmación, Application ejecuta el guardado local en SQLite; el éxito se muestra tras persistir.

Una respuesta HTTP `200` significa que la operación remota produjo un resultado de interpretación válido; **no** significa que exista un movimiento válido, confirmado o guardado. Tampoco habrá una llamada posterior al backend para crear el movimiento.

## 3. `POST /v1/voice/movement-proposals`

### Request

`Content-Type: multipart/form-data`, con estas partes obligatorias:

| Parte | Contenido y validación |
| --- | --- |
| `audio` | Archivo de la captura iniciada por la persona. El backend verifica presencia, contenido no vacío, formato aceptable y límites configurados. Audio ausente o vacío es un request inválido; formato no admitido y tamaño excesivo tienen errores propios. |
| `reference_datetime` | Texto ISO 8601 con offset, por ejemplo `2026-09-26T12:00:00-05:00`, generado por el dispositivo. El backend valida su forma. Sirve únicamente para interpretar expresiones relativas como «hoy», «ayer», «esta mañana» o «anoche». No es la fecha efectiva definitiva. |

Los codecs, MIME types, duración y tamaño máximos se configurarán explícitamente antes de entregar voz, de acuerdo con el proveedor elegido. Sus valores concretos no cambian este contrato conceptual. No se agregan parámetros configurables de idioma, país o moneda: el MVP está fijado en español, Colombia y COP. El request no incluye ubicación precisa ni contexto personal adicional por defecto.

### Response `200`

```json
{
  "request_id": "req_example_123",
  "transcription": "Gasté 42 mil en gasolina ayer",
  "interpretation": {
    "status": "proposal",
    "proposal": {
      "type": "expense",
      "amount": "42000",
      "currency": "COP",
      "effective_date": "2026-09-25",
      "concept": "gasolina"
    },
    "missing_fields": ["category_id"],
    "uncertain_fields": []
  }
}
```

Ejemplo conceptual de solicitud correspondiente: `POST /v1/voice/movement-proposals` con una parte `audio` que contiene «Gasté 42 mil en gasolina ayer» y una parte `reference_datetime` con valor `2026-09-26T12:00:00-05:00`. El ejemplo omite `category_id` porque el catálogo aprobado aún no formaliza sus identificadores técnicos; la clasificación debe completarse o proponerse con un ID aprobado antes del guardado. La fecha es una inferencia revisable a partir de la referencia, no una decisión financiera definitiva.

`request_id` es un identificador opaco generado por el backend para correlación técnica. `transcription` es texto no vacío, visible durante la revisión. `interpretation` tiene un `status` discriminante, `missing_fields` y `uncertain_fields`, ambos arreglos presentes incluso cuando están vacíos.

La propiedad `proposal` puede aparecer con `status = proposal` y también con `status = insufficient_information` cuando existan algunos campos confiablemente identificables de un único movimiento. En ambos casos puede ser parcial. `proposal` no aparece con `status = multiple_movements`, porque una instrucción múltiple no debe dividirse ni convertirse en una lista de propuestas. Una explicación breve para la UI puede añadirse como `explanation` dentro de `interpretation`; es texto no confiable, no sustituye los campos estructurados, no determina reglas financieras y no expone razonamiento interno del modelo.

| `status` | Significado y contenido |
| --- | --- |
| `proposal` | Se identificó una sola intención financiera y hay una propuesta parcial o completa. Los campos ausentes se solicitan al usuario; los inciertos requieren revisión. |
| `insufficient_information` | La transcripción es válida, pero no alcanza para una propuesta útil. Puede incluir una `proposal` parcial con campos identificables de un único movimiento, o carecer de `proposal` si no existe ningún dato útil. Siempre incluye `missing_fields` y `uncertain_fields`. Es un resultado HTTP `200`, no un error técnico; mobile permite completar manualmente o iniciar otro intento. |
| `multiple_movements` | La instrucción expresa más de un movimiento. No lleva `proposal`; no se divide ni se elige uno arbitrariamente. Mobile pide corrección, otro intento o registros manuales individuales. |

Ejemplo breve del último caso:

```json
{
  "request_id": "req_example_124",
  "transcription": "Compré almuerzo por 30 mil y después tanqueé 80 mil",
  "interpretation": {
    "status": "multiple_movements",
    "missing_fields": [],
    "uncertain_fields": []
  }
}
```

`proposal` nunca es una lista. `multiple_movements`, una propuesta incompleta e `insufficient_information` son resultados válidos de la operación remota, no errores HTTP. Ninguno crea un movimiento.

### Campos de `proposal`

Todos los campos de la propuesta son opcionales y solo se incluyen cuando pueden identificarse para ese único movimiento. Un campo ausente se omite; no se inventa un valor obligatorio ni se lo reemplaza por uno arbitrario. `missing_fields` nombra campos necesarios para completar el registro según el tipo identificado, como `type`, `amount`, `effective_date` o `category_id`. `uncertain_fields` nombra campos **presentes** cuyo valor propuesto requiere revisión; un mismo campo no debe figurar en ambos arreglos. No se introduce una puntuación ni umbral numérico de confianza: el criterio de incertidumbre significativa sigue en `TBD-003`.

| Campo | Contrato de propuesta |
| --- | --- |
| `type` | `expense`, `income` u `own_transfer`. |
| `amount` | Texto decimal normalizado, por ejemplo `"42000"` o `"42000.50"`; nunca un número JSON de punto flotante. Domain interpreta y valida el importe exacto. La API no lo convierte directamente a `amount_minor`, que pertenece a la persistencia local. |
| `currency` | Únicamente `"COP"`; el campo no habilita multimoneda. |
| `effective_date` | Día calendario `YYYY-MM-DD` cuando sea inferible. Si no lo es, se omite. La referencia temporal ayuda a interpretarlo, pero mobile y Domain validan la fecha definitiva. |
| `concept` | Concepto o comercio identificable en la expresión, si existe. |
| `category_id` | ID estable del catálogo fijo y aprobado de gasto o ingreso, si se puede proponer uno válido. No se crean categorías ni se inventan IDs. |
| `subcategory_id` | ID estable de una subcategoría compatible de gasto, si aplica. Un ingreso no lleva subcategoría de gasto; `own_transfer` no lleva categoría ni subcategoría. |

El backend restringe y valida la clasificación contra una copia o versión interna del catálogo aprobado cuando sea necesaria para interpretar. Esto no convierte el catálogo de Domain en un recurso CRUD remoto ni define sincronización de catálogos. Mobile vuelve a comprobar tipo, categoría y pertenencia de subcategoría antes de guardar. La propuesta remota puede carecer de campos que un `Movement` local exige; por ello no se reutiliza el modelo SQLite como respuesta.

## 4. Errores HTTP

Todo error controlado devuelve un envelope pequeño y neutral respecto del proveedor:

```json
{
  "error": {
    "code": "PROVIDER_UNAVAILABLE",
    "message": "No fue posible procesar la solicitud.",
    "retryable": true
  },
  "request_id": "req_example_125"
}
```

`message` es comprensible y seguro para el cliente; `retryable` indica que **puede** resultar útil un nuevo intento explícito, no que mobile deba repetir automáticamente el POST. La implementación no devuelve errores crudos del proveedor, prompts, claves, stack traces ni nombres internos innecesarios.

| Código estable | HTTP | Uso |
| --- | --- | --- |
| `INVALID_REQUEST` | `400` | La solicitud HTTP/multipart no puede interpretarse: multipart mal formado, parte obligatoria ausente que impide interpretar el request o estructura inválida a nivel de protocolo. |
| `INVALID_REQUEST` | `422` | El request se interpreta estructuralmente, pero un valor incumple el contrato: `reference_datetime` presente pero inválido, audio presente pero vacío u otro campo presente semánticamente inválido. |
| `UNSUPPORTED_AUDIO` | `415` | Tipo o formato de audio no admitido. |
| `PAYLOAD_TOO_LARGE` | `413` | Audio o solicitud por encima del límite configurado. |
| `TRANSCRIPTION_EMPTY` | `502` | El proveedor no produjo una transcripción utilizable. |
| `PROVIDER_UNAVAILABLE` | `503` | Proveedor o dependencia de transcripción o IA indisponible. |
| `UPSTREAM_TIMEOUT` | `504` | Tiempo de espera agotado al consultar un proveedor. |
| `INVALID_PROVIDER_RESPONSE` | `502` | Respuesta externa imposible de validar o convertir al contrato Nuvora. |
| `RATE_LIMITED` | `429` | Solicitud limitada por protección operativa o capacidad disponible. |
| `INTERNAL_ERROR` | `500` | Fallo interno no clasificado. |

Un fallo de red o timeout observado solo por mobile puede no producir un envelope recibido: mobile conserva el estado local y comunica que el resultado remoto es desconocido. Un resultado de IA estructuralmente inválido es error técnico; una propuesta estructuralmente válida pero incompleta es resultado de negocio.

## 5. Reintentos, correlación y operación

El procesamiento de voz puede generar costo aunque el cliente no reciba la respuesta. El MVP no usa infraestructura persistente de claves de idempotencia, Redis ni una base remota para deduplicar llamadas a proveedores. Mobile no efectúa reintentos silenciosos de un POST con resultado ambiguo. Tras fallo o timeout, la persona puede decidir iniciar otro intento; ese intento es una nueva operación remota y puede generar otro costo. Esto es distinto de la idempotencia financiera local: Application y SQLite evitan duplicar una misma confirmación de guardado.

Mobile fija un timeout para la operación remota; su valor se definirá y medirá durante la implementación de acuerdo con los objetivos pendientes de rendimiento (`TBD-002`, `TBD-006`). Cancelar o agotar el tiempo no crea un movimiento, no cambia SQLite y no permite presentar el intento como guardado. El backend no adquiere autoridad para persistir en el dispositivo un resultado tardío. El registro manual y las consultas financieras locales siguen disponibles ante fallos remotos.

El backend incluye `request_id` en respuestas exitosas, errores controlados y observabilidad técnica para relacionar latencia, fallos y llamadas a proveedores. No representa un ID de movimiento, usuario, dispositivo ni sincronización; no agrupa ni almacena datos financieros bajo ese identificador como parte del contrato. La observabilidad evita por defecto audio, transcripciones, conceptos y secretos en logs. Las métricas de producto exigidas por PRD/SRS se resolverán en su estrategia de analytics: este contrato no crea un endpoint genérico ni adjunta historial financiero como telemetría.

## 6. Privacidad y protección

El request envía solo audio de la interacción y referencia temporal. No adjunta automáticamente historial, dashboard, totales, movimientos previos, conceptos históricos, categorías usadas, IDs locales, contenido de SQLite, nombre, email, teléfono ni ubicación precisa. Un contexto adicional futuro requiere justificación y cambio explícito del contrato. Audio y transcripción se usan para procesar la solicitud; este contrato no concede permiso para guardar movimientos financieros. La conservación y eliminación concretas, incluido el tratamiento del proveedor, siguen pendientes de `TBD-005`, `TBD-014` y `SECURITY.md`. No se presume una política de retención ni el comportamiento de un proveedor todavía no elegido.

El MVP permite usar la aplicación sin cuenta: no se define login, registro, JWT de usuario ni refresh tokens, y la solicitud no se asocia a una cuenta financiera. La **protección técnica del backend** frente a abuso y consumo de proveedores es una responsabilidad distinta. Rate limiting, app attestation u otro mecanismo concreto se definirán posteriormente en `SECURITY.md`, sin imponer aquí autenticación de usuario. Las comunicaciones remotas reales utilizan HTTPS y los secretos de proveedores permanecen en el backend, según `ARCHITECTURE.md`.

## 7. Límites y decisiones pendientes

No forman parte de la API funcional del MVP: CRUD remoto de movimientos o categorías; historial y dashboard remotos; sincronización, backup, cuentas, dispositivos, autenticación de usuario, PostgreSQL o repositorio financiero remoto; presupuestos, metas, deudas avanzadas, multimoneda, notificaciones automáticas o asistente financiero completo. Tampoco se definen GraphQL, gRPC, microservicios, event bus, colas, jobs asíncronos, webhooks, streaming realtime, WebSockets ni SSE. Eventuales endpoints de salud serían exclusivamente operativos y se concretarían durante implementación o entornos, no como recursos de negocio de este contrato.

Antes de entregar voz e IA deben concretarse los proveedores STT e IA, formatos y límites de audio, timeout operativo y protección técnica del endpoint; los criterios de incertidumbre significativa (`TBD-003`); la conservación y eliminación (`TBD-005`); y las condiciones de conectividad, información, autorización y tratamiento externo (`TBD-014`). Los objetivos de tiempo y medición siguen en `TBD-002` y `TBD-006`. Ninguna de estas decisiones pendientes autoriza ampliar la API financiera ni sustituir la validación local.
