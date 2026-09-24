# Nuvora — Roadmap

## 1. Propósito

Este roadmap ordena la construcción y validación del MVP de Nuvora a partir de `VISION.md`, `PRD.md` y `SRS.md`. Define hitos de producto, dependencias y condiciones para avanzar, sin fijar fechas ni sustituir los requisitos aprobados. El MVP se dirige inicialmente a personas en Colombia, en Android, en español y con COP como única moneda.

## 2. Principios de planificación

- Construir capacidades verticales que entreguen valor comprobable, en lugar de completar capas aisladas. Mantener el producto ejecutable y coherente después de cada hito.
- Validar primero el registro manual, la persistencia y los cálculos deterministas. Introducir voz e IA después de que el núcleo financiero sea confiable.
- Considerar el funcionamiento sin conexión parte esencial del MVP desde los primeros hitos y verificarlo de extremo a extremo antes de avanzar a la captura asistida.
- Considerar una capacidad terminada solo cuando funcionen, según corresponda, la interfaz, las reglas, la persistencia, la validación y las pruebas. La presencia de una pantalla no constituye por sí sola una entrega completa.
- Mantener el control del usuario sobre las propuestas automáticas: la IA interpreta y explica; el motor financiero calcula y aplica las reglas. Ninguna propuesta de IA modifica datos directamente.
- Priorizar resultados verificables y medir si la automatización reduce esfuerzo sin deteriorar exactitud o confianza. No adelantar capacidades de V1 o V2 por conveniencia técnica.

## 3. Etapas del producto

1. **Foundation — hito 0:** cerrar las decisiones de producto y desarrollo necesarias para iniciar la implementación.
2. **Construcción del MVP — hitos 1 a 7:** establecer una aplicación ejecutable, entregar el núcleo financiero manual y offline, y añadir captura asistida corregible y medible.
3. **Preparación para validación — hito 8:** comprobar integralmente los requisitos del MVP y corregir los defectos que impidan probarlo con usuarios reales.
4. **Validación con usuarios — hito 9:** observar las hipótesis y métricas del PRD para decidir mejoras y la posible evolución hacia V1.

Los hitos indican orden lógico y condiciones de avance, no duración. Una decisión pendiente del SRS debe resolverse cuando sea necesaria para aceptar el comportamiento correspondiente; los objetivos que requieran una línea base no se inventan durante Foundation.

## 4. Roadmap del MVP

### Hito 0 — Foundation

- **Objetivo:** cerrar las decisiones fundamentales del MVP antes del desarrollo funcional.
- **Capacidades y entregables:** `VISION.md`, `PRD.md`, `SRS.md` y `ROADMAP.md` — completados; `USER_FLOWS.md`, `DESIGN_SYSTEM.md`, `ARCHITECTURE.md`, `DATABASE.md`, `API.md`, `ENVIRONMENTS.md`, `SECURITY.md`, `TESTING.md`, `AGENTS.md` y `README.md` — pendientes dentro de Sprint 0. Esta relación no prescribe el contenido de los documentos pendientes.
- **Dependencias:** visión, alcance y requisitos aprobados; identificar las decisiones del SRS necesarias antes de implementar cada capacidad.
- **Requisitos SRS relacionados:** sección 12 (TBD-001–011 y TBD-014); `RNF-COMP-001`, `RNF-ACC-001–005`, `RNF-PRIV-003–005` como insumos para decisiones posteriores. Este hito no implementa requisitos funcionales.
- **Definition of Done:** las decisiones fundamentales permiten iniciar la implementación sin inventar arquitectura ni requisitos durante el desarrollo; las decisiones que dependen de evidencia posterior quedan identificadas con su hito afectado.

### Hito 1 — Walking skeleton

- **Objetivo:** demostrar que la base técnica mínima de la aplicación funciona de forma integrada y permite construir el primer flujo vertical del MVP.
- **Capacidades:** aplicación Android ejecutable, estructura base, navegación mínima, configuración inicial de ambientes, mecanismo de persistencia disponible y validación básica del proyecto; pipeline técnico mínimo solo si las decisiones posteriores de arquitectura o pruebas lo requieren. Aún no incluye todas las funciones financieras.
- **Dependencias:** cierre de Foundation y de la compatibilidad Android necesaria para iniciar una base verificable (`TBD-007`).
- **Requisitos SRS relacionados:** `RNF-COMP-001–002`, `RNF-PERF-001` y `RNF-MANT-001`; prepara `RF-PER-001–003`, sin darlos por cumplidos antes de probar movimientos reales.
- **Definition of Done:** la aplicación inicia en un dispositivo Android compatible, permite recorrer su navegación mínima y demuestra que las capacidades técnicas mínimas definidas posteriormente por la arquitectura están disponibles para construir y validar el primer flujo vertical. Este hito no obliga por sí mismo a introducir servicios remotos o backend.

### Hito 2 — Núcleo de movimientos manuales

- **Objetivo:** permitir el uso de Nuvora como registro financiero básico sin IA ni cuenta obligatoria.
- **Capacidades:** primer uso breve y ruta directa al registro; creación, consulta, edición y eliminación de gastos, ingresos y transferencias propias; monto, fecha efectiva, concepto opcional, categorías de gasto e ingreso y subcategorías de gasto conforme al catálogo aprobado; validación, confirmación y persistencia. Las transferencias propias no usan esos catálogos. Los movimientos se expresan en COP.
- **Dependencias:** hito 1; catálogo aprobado y reglas de validez disponibles; decisión sobre cómo medir el tiempo de registro manual (`TBD-001`) cuando se evalúe ese resultado.
- **Requisitos SRS relacionados:** `RF-ONB-001–004`, `RF-MOV-001–009`, `RF-GES-001–004`, `RF-CAT-001–004`, `RF-PER-001–003`, `RN-001–006`, `RN-011–013`, `RNF-REL-001–002` y `RNF-UX-001–004`.
- **Definition of Done:** una persona inicia sin cuenta, registra y gestiona cada tipo de movimiento; entradas inválidas no alteran los datos; los movimientos confirmados sobreviven al cierre y reinicio, y las acciones fallidas no dejan datos parciales.

### Hito 3 — Dashboard y análisis mensual

- **Objetivo:** convertir movimientos válidos en información mensual útil y verificable.
- **Capacidades:** evolución del historial básico hacia consulta por períodos mensuales; selección del mes actual y de meses anteriores con movimientos; cálculo de ingresos, gastos, resultado neto y distribución de gastos por categoría; estados vacíos y comparaciones únicamente cuando exista historial suficiente. La fecha efectiva determina el período y las transferencias propias se excluyen de los totales.
- **Dependencias:** hito 2 y reglas financieras deterministas; definir el criterio de historial suficiente (`TBD-004`) antes de aceptar comparaciones.
- **Requisitos SRS relacionados:** `RF-GES-001–005`, `RF-DASH-001–007`, `RN-005–007`, `RN-010`, `RN-012`, `RNF-REL-003` y `RNF-MANT-001`.
- **Definition of Done:** el usuario puede responder cuánto ingresó, cuánto gastó, cuál fue el resultado neto y en qué categorías gastó; crear, editar o eliminar movimientos actualiza los períodos afectados; un historial vacío y uno insuficiente no generan tendencias ficticias.

### Hito 4 — Experiencia offline y robustez

- **Objetivo:** comprobar que el núcleo financiero sigue siendo confiable sin conexión.
- **Capacidades:** abrir la aplicación, consultar historial, registrar y gestionar movimientos manualmente, consultar el dashboard con datos disponibles y conservar movimientos tras cerrar y reiniciar; manejo de errores, integridad y ausencia de duplicados inesperados.
- **Dependencias:** hitos 2 y 3 completos; las funciones esenciales no deben esperar un servicio remoto. Las condiciones de conectividad de voz e IA (`TBD-014`) no alteran esta obligación.
- **Requisitos SRS relacionados:** `RF-OFF-001–004`, `RF-PER-001–003`, `RNF-PERF-003`, `RNF-REL-001–005` y `RNF-COMP-003`.
- **Definition of Done:** los flujos financieros esenciales funcionan sin Internet y mantienen datos y resúmenes consistentes después de cierre, reinicio, errores y repetición de acciones.

### Hito 5 — Captura por voz

- **Objetivo:** reducir la fricción al convertir una expresión hablada en texto revisable.
- **Capacidades:** información y autorización aplicables antes de capturar, inicio explícito, indicador de grabación activa, detención y cancelación, conversión de voz a texto, transcripción visible y manejo de fallos; siempre existe una salida hacia el registro manual. Este hito no exige todavía la interpretación estructurada completa.
- **Dependencias:** hito 4; definir antes de aceptar el flujo las condiciones aplicables de conectividad y tratamiento externo (`TBD-014`) y conservación de audio y transcripciones (`TBD-005`). La medición de tiempo depende de `TBD-002`.
- **Requisitos SRS relacionados:** `RF-VOZ-001–006`, `RNF-PRIV-001–005`, `RNF-PERF-002` y `RF-OFF-004`.
- **Definition of Done:** el usuario puede obtener y revisar una transcripción; cancelar, recibir una transcripción vacía o sufrir un fallo no crea movimientos y permite reintentar, salir o registrar manualmente.

### Hito 6 — Interpretación inteligente

- **Objetivo:** convertir lenguaje cotidiano en una propuesta estructurada de un movimiento que el usuario pueda revisar.
- **Capacidades:** flujo voz → texto → interpretación → propuesta → validación determinista → revisión o corrección por el usuario → confirmación → persistencia. La propuesta puede incluir tipo, monto, COP, fecha efectiva inferible, concepto o comercio, categoría y subcategoría cuando haya evidencia; distingue ausencias e incertidumbre. Una interacción de voz produce como máximo una propuesta.
- **Dependencias:** hitos 2 y 5; reglas de registro manual reutilizables y criterios de incertidumbre significativa definidos (`TBD-003`); condiciones de voz e IA aplicables (`TBD-014`).
- **Requisitos SRS relacionados:** `RF-VOZ-007`, `RF-IA-001–005`, `RF-CONF-001–005`, `RF-MOV-005–009`, `RN-001–009`, `RN-012–013` y `RNF-REL-005`.
- **Definition of Done:** una expresión como «Gasté 45 mil ayer en Uber» puede producir una propuesta visible y corregible; solo la versión válida y confirmada se guarda. Una propuesta inválida, incierta sin revisión o cancelada no modifica historial ni dashboard. La IA no calcula totales ni persiste directamente.

### Hito 7 — Categorización y medición

- **Objetivo:** comprobar si la automatización reduce trabajo sin generar correcciones adicionales.
- **Capacidades:** sugerencia de una categoría válida o ausencia explícita de sugerencia, aceptación y corrección antes o después del guardado; medición de registros manuales iniciados, completados o abandonados, intentos de voz, interpretaciones aceptadas o corregidas y categorías sugeridas aceptadas o corregidas. Las correcciones no entrenan una personalización ni alteran el catálogo durante el MVP.
- **Dependencias:** hitos 2 y 6; criterios de incertidumbre (`TBD-003`) y definiciones de medición pertinentes (`TBD-001`, `TBD-002` y `TBD-009`). Los eventos se incorporan a sus flujos y se verifican conjuntamente en este hito.
- **Requisitos SRS relacionados:** `RF-AUT-001–004`, `RF-ANA-001–003`, `RF-CAT-001–004`, `RF-CONF-002–004` y `RN-011`.
- **Definition of Done:** el usuario distingue una sugerencia de un dato confirmado, puede aceptarla o corregirla y ve reflejada la corrección en los resúmenes; los resultados de interacción necesarios para las métricas del PRD son distinguibles sin guardar datos financieros no confirmados.

### Hito 8 — Hardening del MVP

- **Objetivo:** preparar un producto confiable para su validación con usuarios reales.
- **Capacidades:** revisión completa de los requisitos `MUST` aplicables al MVP y de las reglas financieras; pruebas funcionales, offline, de errores, accesibilidad, privacidad, seguridad, rendimiento, permisos y calidad de voz e interpretación; corrección de defectos críticos.
- **Dependencias:** hitos 1 a 7; decisiones de rendimiento (`TBD-006`), compatibilidad (`TBD-007`), accesibilidad (`TBD-008`) y tratamiento de datos (`TBD-005`, `TBD-014`) necesarias para aceptar las capacidades correspondientes.
- **Requisitos SRS relacionados:** todos los `MUST` aplicables de las secciones 6 a 8, en particular `RN-001–013` y `RNF-UX`, `RNF-PERF`, `RNF-REL`, `RNF-SEC`, `RNF-PRIV`, `RNF-ACC`, `RNF-COMP` y `RNF-MANT`.
- **Definition of Done:** cada `MUST` aplicable del MVP está implementado y probado; cualquier excepción tiene una decisión explícita documentada y no se considera satisfecha sin ajustar el alcance aprobado. No hay defectos críticos conocidos que comprometan datos financieros, control del usuario u operación esencial offline.

### Hito 9 — Validación del MVP

- **Objetivo:** evaluar con usuarios reales las hipótesis del PRD y reunir evidencia para decidir mejoras y una posible V1.
- **Capacidades:** observar activación, finalización y tiempos de registro manual y por voz, adopción de voz, interpretación correcta, correcciones de categoría, integridad percibida, recurrencia, retención, confianza y utilidad percibida; analizar velocidad junto con exactitud y confianza.
- **Dependencias:** hito 8 y eventos verificables del hito 7; las metas numéricas, ventanas y líneas base permanecen sujetas a `TBD-009`, sin inventar umbrales.
- **Requisitos SRS relacionados:** `RF-ANA-001–003`, `RF-AUT-004`, `RNF-UX-001`, `RNF-UX-004` y sección 12 (`TBD-001–003`, `TBD-009`).
- **Definition of Done:** existe evidencia de uso y percepción suficiente para identificar fricciones, calidad de automatización y utilidad del resumen, y para fundamentar qué mejorar y si priorizar alguna capacidad de V1; la validación no implica automáticamente avanzar a V1.

## 5. Criterios de salida del MVP

Al cerrar el hito 8, el MVP está listo para validación con usuarios cuando:

- Todos los requisitos `MUST` incluidos en su alcance están implementados y verificados. Una excepción documentada requiere resolver su alcance antes de considerar listo el MVP.
- No hay defectos críticos conocidos que comprometan la integridad de los datos financieros; movimientos confirmados persisten, las operaciones fallidas no dejan estados parciales y las reglas producen resultados deterministas.
- El registro y la gestión manual, el historial y los resúmenes disponibles funcionan sin conexión; voz e IA fallan de forma segura sin bloquear el camino manual.
- Ninguna propuesta automática afecta los datos antes de validación y confirmación del usuario; los cálculos y las reglas financieras siguen fuera de la IA.
- Están disponibles los eventos necesarios para medir las hipótesis del PRD, y privacidad, permisos y comunicación sobre el tratamiento de datos son coherentes con las decisiones aprobadas.

El hito 9 aporta la evidencia para decidir mejoras y la evolución posterior. No se fijan aquí porcentajes de cobertura, SLA ni objetivos numéricos de producto que continúan pendientes.

## 6. Evolución posterior

Estas capacidades del PRD son oportunidades sujetas a la evidencia obtenida del MVP, a la calidad de los datos, a su viabilidad y a los principios del producto; no constituyen compromisos de entrega.

- **V1:** presupuesto mensual y objetivo de ahorro básicos; seguimiento básico de deudas; tratamiento de reembolsos vinculados a gastos previos, sujeto a definición; asistente inicial para consultas concretas respaldadas por datos; aprendizaje inicial y controlado de correcciones de categoría; experimentos de captura contextual permitidos por la plataforma; widgets o shortcuts; posible multimoneda limitada si se valida su necesidad; mejoras en posibles duplicados y movimientos recurrentes; evaluación de cuenta opcional para backup, recuperación y sincronización; compatibilidad con iOS.
- **V2 / futuro:** sincronización bancaria u Open Banking cuando resulte viable; automatización avanzada de captura, conciliación y categorización; aprendizaje personalizado avanzado; detección avanzada de duplicados; safe-to-spend y forecasting basados en datos confiables; identificación y gestión de suscripciones o gastos recurrentes; asistente proactivo; multimoneda avanzada; nuevas superficies de acceso rápido que demuestren reducir fricción.

## 7. Dependencias y decisiones pendientes

La secuencia crítica es **Foundation → aplicación ejecutable → movimientos manuales persistentes → resúmenes correctos → robustez offline → voz revisable → interpretación confirmable → categorización medible → preparación integral → validación con usuarios**. La disponibilidad de una capacidad anterior es condición para aceptar la siguiente; la voz no sustituye al registro manual ni la medición sustituye a la corrección de errores.

| Decisión SRS | Hitos afectados y condición de avance |
| --- | --- |
| `TBD-001` | Hitos 2, 7 y 9: objetivo y método de medición del registro manual. |
| `TBD-002` | Hitos 5, 7 y 9: objetivo y método de medición del registro por voz. |
| `TBD-003` | Hitos 6, 7 y 9: criterios de incertidumbre para la propuesta y su revisión. |
| `TBD-004` | Hito 3: criterio de historial suficiente antes de presentar comparaciones. |
| `TBD-005` | Hitos 5, 6 y 8: conservación y eliminación de audio, transcripciones, movimientos y correcciones. |
| `TBD-006` | Hitos 1 y 8: metas y condiciones de medición del rendimiento. |
| `TBD-007` | Hitos 1, 4 y 8: rango de Android compatible. |
| `TBD-008` | Hito 8, con aplicación desde los flujos iniciales: estándar y umbrales de accesibilidad. |
| `TBD-009` | Hitos 7 y 9: objetivos, ventanas y línea base de las métricas; su definición puede requerir evidencia del MVP. |
| `TBD-014` | Hitos 4, 5, 6 y 8: conectividad, consentimiento e información y tratamiento externo de voz e IA. |
| `TBD-010` y `TBD-011` | Evolución posterior: sincronización, backup, recuperación y cuenta opcional; no habilitan ni bloquean el uso sin cuenta y la persistencia local del MVP. |

Ningún TBD queda resuelto por este roadmap. Las decisiones deben registrarse en los documentos correspondientes antes de aceptar el comportamiento que dependa de ellas.
