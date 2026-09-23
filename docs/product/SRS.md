# Nuvora — Software Requirements Specification

**Estado:** Sprint 0 — Foundation  
**Alcance:** MVP  
**Mercado:** Colombia  
**Plataforma:** Android  
**Idioma:** español  
**Moneda:** COP

## 1. Propósito

Este documento transforma la visión y el PRD de Nuvora en requisitos identificables, verificables, trazables y libres de decisiones de implementación. Especifica qué comportamiento debe ofrecer el MVP y sirve como base para diseño, validación y pruebas posteriores.

## 2. Alcance

El MVP permitirá comenzar sin cuenta, registrar y gestionar movimientos manualmente, capturar un movimiento mediante voz, revisar propuestas de interpretación y categorización, conservar la información y consultar un resumen mensual. Las funciones financieras esenciales funcionarán sin conexión; voz e interpretación automática podrán requerirla.

El documento no define arquitectura, almacenamiento, APIs, proveedores, componentes de interfaz ni tecnologías. Nuvora no afirma conocer el saldo bancario disponible: calcula resultados exclusivamente con los movimientos válidos disponibles.

## 3. Referencias

- [VISION.md](./VISION.md), fuente de los principios, propuesta de valor y límites del producto.
- [PRD.md](./PRD.md), fuente del alcance, catálogos, journeys, reglas y métricas del MVP.

No se identificaron contradicciones directas entre ambas fuentes. El PRD sitúa explícitamente la asociación y el tratamiento de reembolsos fuera del MVP.

## 4. Definiciones y glosario

| Término | Definición |
| --- | --- |
| Movimiento | Registro financiero individual que Nuvora reconoce en el MVP como gasto, ingreso o transferencia propia. |
| Gasto | Salida de dinero contabilizable que representa consumo u obligación y reduce el resultado neto. |
| Ingreso | Entrada de dinero contabilizable que incrementa el resultado neto. No incluye transferencias propias. |
| Transferencia propia | Movimiento entre fondos o cuentas de la misma persona que no representa consumo ni generación de dinero. |
| Categoría | Clasificación principal de un gasto o ingreso dentro del catálogo definido por Nuvora. |
| Subcategoría | Clasificación secundaria opcional, disponible únicamente dentro de una categoría de gasto que la contenga. |
| Fecha efectiva | Fecha en la que ocurrió un movimiento y que determina el período financiero al que pertenece. |
| Resultado neto del período | Ingresos contabilizables menos gastos contabilizables del período. No equivale al saldo bancario disponible. |
| Movimiento manual | Movimiento cuyos datos introduce o selecciona directamente el usuario. |
| Movimiento por voz | Propuesta de un único movimiento obtenida a partir de una captura de voz, revisada antes de guardarse. |
| Interpretación automática | Propuesta estructurada derivada de texto o voz; no constituye por sí sola un movimiento válido. |
| Confianza | Grado de certeza comunicado por la interpretación automática sobre uno o varios datos propuestos. Su umbral está pendiente. |
| Período | Intervalo temporal usado para agrupar y calcular movimientos según su fecha efectiva; el dashboard del MVP utiliza períodos mensuales. |
| Historial suficiente | Conjunto mínimo de períodos comparables requerido para mostrar comparaciones. Su criterio exacto está pendiente. |
| Dato contabilizable | Movimiento válido que, conforme a las reglas de negocio, participa en un total financiero. |

## 5. Actores

### 5.1 Usuario de Nuvora

Persona que registra, revisa, corrige, elimina y consulta su información financiera. Conserva la autoridad para confirmar o cancelar propuestas automáticas. No se definen roles administrativos para el MVP.

### 5.2 Servicios externos de procesamiento

Actor secundario opcional que puede transformar voz o lenguaje cuando una capacidad lo requiera. No calcula resultados financieros, no confirma movimientos y no sustituye la decisión del usuario ni la validación determinista de Nuvora.

## 6. Requisitos funcionales

Las prioridades siguen MoSCoW: **MUST** es indispensable para el MVP, **SHOULD** es importante pero no bloqueante y **COULD** es opcional. Salvo indicación contraria, el actor es el usuario.

### 6.1 Primer uso y onboarding

#### RF-ONB-001 — Inicio sin cuenta
- **Descripción:** Nuvora debe permitir iniciar el uso, registrar movimientos y consultar información sin crear una cuenta.
- **Prioridad:** MUST
- **Precondiciones:** Aplicación disponible para primer uso.
- **Resultado esperado:** El usuario obtiene valor antes de cualquier solicitud futura de cuenta.
- **Criterios de aceptación:** Dado un primer inicio, cuando el usuario continúa, entonces puede acceder al primer registro sin autenticarse ni crear una cuenta.
- **Referencia al PRD:** 8.2, “Inicio sin cuenta obligatoria”; 11.1.

#### RF-ONB-002 — Comunicación del propósito
- **Descripción:** El primer uso debe comunicar brevemente que Nuvora permite registrar y comprender movimientos financieros con poco esfuerzo.
- **Prioridad:** MUST
- **Precondiciones:** Primer uso iniciado.
- **Resultado esperado:** El usuario comprende el valor principal antes o durante su acceso al primer registro.
- **Criterios de aceptación:** La comunicación describe registro y comprensión financiera en lenguaje cotidiano, sin exigir conocimientos especializados.
- **Referencia al PRD:** 1, 2, 6 y 8.2.

#### RF-ONB-003 — Acceso rápido al primer registro
- **Descripción:** Nuvora debe ofrecer una ruta directa desde el primer uso hasta el inicio de un registro.
- **Prioridad:** MUST
- **Precondiciones:** Propósito principal presentado.
- **Resultado esperado:** El usuario puede iniciar su primer movimiento con el menor número razonable de acciones.
- **Criterios de aceptación:** Existe una acción inequívoca para iniciar el registro; el objetivo temporal exacto se rige por TBD-001.
- **Referencia al PRD:** 6, 8.2 y 11.1.

#### RF-ONB-004 — Contexto del MVP
- **Descripción:** La experiencia inicial debe estar en español, utilizar COP y emplear lenguaje apropiado para Colombia.
- **Prioridad:** MUST
- **Precondiciones:** Aplicación iniciada.
- **Resultado esperado:** Textos, importes y contexto corresponden al mercado inicial.
- **Criterios de aceptación:** No se solicita idioma, mercado o moneda alternativos; todo importe del MVP se expresa en COP.
- **Referencia al PRD:** 1, 4.1 y 8.1.

### 6.2 Registro manual de movimientos

#### RF-MOV-001 — Selección del tipo de movimiento
- **Descripción:** El registro manual debe permitir identificar el movimiento como gasto, ingreso o transferencia propia.
- **Prioridad:** MUST
- **Precondiciones:** Flujo manual iniciado.
- **Resultado esperado:** El movimiento conserva un tipo válido y explícito.
- **Criterios de aceptación:** No se confirma un movimiento sin tipo; solo se ofrecen los tipos incluidos en el MVP.
- **Referencia al PRD:** 7 “Transacciones”; 8.2.

#### RF-MOV-002 — Registro manual de gasto
- **Descripción:** Nuvora debe permitir crear un gasto manual con la información esencial válida.
- **Prioridad:** MUST
- **Precondiciones:** Flujo manual disponible.
- **Resultado esperado:** El gasto confirmado aparece en historial y cálculos aplicables.
- **Criterios de aceptación:** Dado monto válido y categoría principal, cuando se confirma, entonces se guarda un gasto contabilizable y se actualizan los resúmenes.
- **Referencia al PRD:** 8.2 y 11.2.

#### RF-MOV-003 — Registro manual de ingreso
- **Descripción:** Nuvora debe permitir crear un ingreso manual con la información esencial válida.
- **Prioridad:** MUST
- **Precondiciones:** Flujo manual disponible.
- **Resultado esperado:** El ingreso confirmado aparece en historial y cálculos aplicables.
- **Criterios de aceptación:** Dado monto y categoría de ingreso válidos, cuando se confirma, entonces se guarda un ingreso contabilizable y se actualizan los resúmenes.
- **Referencia al PRD:** 8.2 y 5.

#### RF-MOV-004 — Registro de transferencia propia
- **Descripción:** Nuvora debe permitir identificar y conservar una transferencia propia como movimiento.
- **Prioridad:** MUST
- **Precondiciones:** Flujo manual iniciado.
- **Resultado esperado:** La transferencia queda en el historial sin afectar ingresos, gastos ni resultado neto.
- **Criterios de aceptación:** Al confirmar una transferencia válida, sus importes quedan excluidos de los tres cálculos indicados.
- **Referencia al PRD:** 7 “Transacciones”, 8.2 y 11.5.

#### RF-MOV-005 — Validación del monto
- **Descripción:** Todo movimiento debe tener un monto numérico válido mayor que cero.
- **Prioridad:** MUST
- **Precondiciones:** Registro o edición en curso.
- **Resultado esperado:** Solo se guardan montos válidos.
- **Criterios de aceptación:** Un monto vacío, igual a cero, negativo o inválido impide confirmar y produce una indicación corregible.
- **Referencia al PRD:** 8.2, registro con información esencial.

#### RF-MOV-006 — Clasificación obligatoria aplicable
- **Descripción:** Todo gasto debe tener una categoría principal de gastos, todo ingreso debe tener una categoría de ingresos, la subcategoría de gasto es opcional y una transferencia propia no utiliza estos catálogos.
- **Prioridad:** MUST
- **Precondiciones:** Tipo y monto disponibles.
- **Resultado esperado:** El movimiento respeta las reglas de clasificación de su tipo.
- **Criterios de aceptación:** Un gasto sin categoría principal no se guarda; un ingreso sin categoría de ingresos no se guarda; omitir la subcategoría de un gasto no impide guardarlo; una categoría del catálogo incorrecto no es válida; una transferencia propia se guarda sin categoría de gasto o ingreso.
- **Referencia al PRD:** 7 “Categorías” y 8.2.

#### RF-MOV-007 — Concepto del movimiento
- **Descripción:** El usuario debe poder registrar un concepto o descripción cuando disponga de él, sin que sea obligatorio para confirmar.
- **Prioridad:** SHOULD
- **Precondiciones:** Registro o edición en curso.
- **Resultado esperado:** El concepto aportado queda asociado al movimiento.
- **Criterios de aceptación:** Se puede guardar un movimiento válido con o sin concepto; si se proporciona, se muestra al consultarlo.
- **Referencia al PRD:** 2, “importes, conceptos y categorías”; 8.2.

#### RF-MOV-008 — Confirmación del registro manual
- **Descripción:** El registro manual solo debe convertirse en movimiento persistido mediante confirmación del usuario y validación satisfactoria.
- **Prioridad:** MUST
- **Precondiciones:** Datos del movimiento disponibles.
- **Resultado esperado:** Se guarda una sola vez la información confirmada y válida.
- **Criterios de aceptación:** Confirmar datos inválidos no crea un movimiento; confirmar datos válidos crea uno y refleja el cambio; cancelar no crea ninguno.
- **Referencia al PRD:** 6, 8.2 y 11.2.

#### RF-MOV-009 — Fecha efectiva del movimiento
- **Descripción:** Todo movimiento financiero persistido debe tener una fecha efectiva que indique cuándo ocurrió.
- **Prioridad:** MUST
- **Precondiciones:** Registro o edición de un movimiento en curso.
- **Resultado esperado:** El movimiento queda asociado a una fecha válida utilizada para determinar el período financiero al que pertenece.
- **Criterios de aceptación:** Ningún movimiento puede guardarse sin una fecha efectiva válida; durante el registro manual se puede utilizar inicialmente la fecha actual; el usuario puede modificar la fecha antes de confirmar; modificar la fecha de un movimiento actualiza los resúmenes de los períodos afectados.
- **Referencia al PRD:** 7 “Transacciones”; 8.2 “Dashboard mensual básico”.

### 6.3 Gestión de movimientos

#### RF-GES-001 — Listado de movimientos
- **Descripción:** Nuvora debe mostrar los movimientos disponibles en el historial.
- **Prioridad:** MUST
- **Precondiciones:** Aplicación iniciada.
- **Resultado esperado:** El usuario distingue los movimientos registrados o un estado vacío.
- **Criterios de aceptación:** Cada elemento permite identificar al menos tipo, monto en COP y clasificación o concepto disponible; sin datos se muestra un estado vacío no erróneo.
- **Referencia al PRD:** 7 “Transacciones”, 8.2 y 11.4.

#### RF-GES-002 — Consulta de movimiento
- **Descripción:** El usuario debe poder consultar la información disponible de un movimiento.
- **Prioridad:** MUST
- **Precondiciones:** Existe un movimiento.
- **Resultado esperado:** Se muestran sus datos vigentes y su tipo.
- **Criterios de aceptación:** Al seleccionar un movimiento del historial, se presenta la información guardada sin alterarla.
- **Referencia al PRD:** 7 “Transacciones” y 8.2.

#### RF-GES-003 — Edición de movimiento
- **Descripción:** El usuario debe poder modificar los datos editables de un movimiento y confirmar o cancelar el cambio.
- **Prioridad:** MUST
- **Precondiciones:** Existe un movimiento consultable.
- **Resultado esperado:** Solo una edición confirmada y válida reemplaza los datos anteriores.
- **Criterios de aceptación:** Guardar datos inválidos se impide; cancelar conserva el movimiento original; confirmar datos válidos actualiza historial y cálculos.
- **Referencia al PRD:** 8.2 y 11.4.

#### RF-GES-004 — Eliminación de movimiento
- **Descripción:** El usuario debe poder eliminar un movimiento mediante una acción deliberada.
- **Prioridad:** MUST
- **Precondiciones:** Existe un movimiento.
- **Resultado esperado:** El movimiento deja de aparecer y de participar en cálculos.
- **Criterios de aceptación:** La cancelación conserva el movimiento; la eliminación confirmada lo retira del historial y actualiza los resúmenes.
- **Referencia al PRD:** 7 “Transacciones” y 8.2.

#### RF-GES-005 — Recalculo ante cambios
- **Descripción:** La creación, modificación o eliminación confirmada debe reflejarse en todos los totales y agrupaciones afectados.
- **Prioridad:** MUST
- **Precondiciones:** Operación persistida satisfactoriamente.
- **Resultado esperado:** Historial y dashboard representan el mismo conjunto vigente de movimientos.
- **Criterios de aceptación:** Repetir un cálculo con los mismos movimientos produce el mismo resultado; una operación fallida no altera los resúmenes.
- **Referencia al PRD:** 6, 8.2 y 11.2–11.6.

### 6.4 Categorías

#### RF-CAT-001 — Catálogo de gastos
- **Descripción:** Nuvora debe ofrecer exactamente las categorías y subcategorías de gasto definidas en la tabla de esta sección.
- **Prioridad:** MUST
- **Precondiciones:** Se clasifica un gasto.
- **Resultado esperado:** El usuario selecciona una clasificación aprobada.
- **Criterios de aceptación:** No falta ni se añade ninguna categoría o subcategoría respecto del catálogo reproducido abajo.
- **Referencia al PRD:** 7 “Catálogo de gastos del MVP”.

#### RF-CAT-002 — Catálogo de ingresos
- **Descripción:** Nuvora debe ofrecer exactamente las categorías de ingreso definidas en la tabla de esta sección.
- **Prioridad:** MUST
- **Precondiciones:** Se clasifica un ingreso.
- **Resultado esperado:** El usuario selecciona una categoría de ingreso aprobada.
- **Criterios de aceptación:** No falta ni se añade ninguna categoría respecto del catálogo reproducido abajo.
- **Referencia al PRD:** 7 “Catálogo de ingresos del MVP”.

#### RF-CAT-003 — Separación de catálogos
- **Descripción:** Los catálogos de gastos e ingresos deben permanecer separados y aplicarse según el tipo de movimiento.
- **Prioridad:** MUST
- **Precondiciones:** Tipo de movimiento conocido.
- **Resultado esperado:** No se asigna una categoría de ingreso a un gasto ni una de gasto a un ingreso.
- **Criterios de aceptación:** Cambiar el tipo invalida una clasificación incompatible y exige una válida cuando corresponda.
- **Referencia al PRD:** 7 “Categorías”.

#### RF-CAT-004 — Catálogo no modificable
- **Descripción:** El MVP no debe permitir al usuario crear, editar ni eliminar categorías o subcategorías del catálogo.
- **Prioridad:** MUST
- **Precondiciones:** Usuario utilizando el MVP.
- **Resultado esperado:** La taxonomía aprobada permanece consistente.
- **Criterios de aceptación:** Ningún flujo del MVP ofrece operaciones para personalizar el catálogo.
- **Referencia al PRD:** 7 “Categorías”, 8.3 y 10.1.

#### Catálogo de gastos aprobado

| Categoría | Subcategorías |
| --- | --- |
| Alimentación | Supermercado; Restaurantes; Domicilios; Café / Snacks; Otros alimentos. |
| Vivienda | Arriendo; Hipoteca; Administración; Mantenimiento; Reparaciones; Muebles / Hogar; Otros vivienda. |
| Servicios | Energía; Agua; Gas; Internet; Telefonía móvil; Televisión; Otros servicios. |
| Transporte | Combustible; Transporte público; Taxi / Apps; Parqueadero; Peajes; Mantenimiento vehículo; Repuestos; Otros transporte. |
| Deudas y obligaciones | Tarjeta de crédito; Crédito de consumo; Crédito vehicular; Crédito hipotecario; Préstamos personales; Otras obligaciones. |
| Salud | Medicamentos; Consultas; Exámenes; Odontología; Seguro / Medicina prepagada; Otros salud. |
| Educación | Matrícula; Cursos; Libros; Materiales; Otros educación. |
| Compras personales | Ropa; Calzado; Tecnología; Cuidado personal; Accesorios; Otras compras. |
| Entretenimiento | Cine; Salidas; Juegos; Eventos; Hobbies; Otros entretenimiento. |
| Suscripciones | Streaming; Música; Software; Apps; Membresías; Otras suscripciones. |
| Familia y personal | Hijos; Mascotas; Regalos; Ayuda familiar; Otros personales. |
| Otros | Sin subcategorías iniciales. |

#### Catálogo de ingresos aprobado

- Salario.
- Honorarios / Freelance.
- Negocio / Ventas.
- Comisiones.
- Bonificaciones.
- Rendimientos.
- Otros ingresos.

### 6.5 Persistencia

#### RF-PER-001 — Conservación al cerrar
- **Descripción:** Cerrar y volver a abrir Nuvora no debe eliminar movimientos confirmados.
- **Prioridad:** MUST
- **Precondiciones:** Existe al menos un movimiento válidamente almacenado.
- **Resultado esperado:** El historial reaparece con la misma información vigente.
- **Criterios de aceptación:** Tras cerrar y abrir la aplicación, los movimientos confirmados continúan consultables.
- **Referencia al PRD:** 8.2 “Persistencia de datos”; 14 “Continuidad local”.

#### RF-PER-002 — Conservación tras reinicio
- **Descripción:** Reiniciar el dispositivo no debe eliminar datos válidamente almacenados.
- **Prioridad:** MUST
- **Precondiciones:** Existen datos confirmados antes del reinicio.
- **Resultado esperado:** Los datos permanecen disponibles al volver a abrir Nuvora.
- **Criterios de aceptación:** El historial posterior al reinicio coincide con el estado confirmado anterior.
- **Referencia al PRD:** 7 “Persistencia y sincronización” y 14.

#### RF-PER-003 — Persistencia atómica visible
- **Descripción:** Una operación debe presentarse como completada solo cuando sus datos puedan conservarse; si falla, no debe dejar un movimiento parcial.
- **Prioridad:** MUST
- **Precondiciones:** Creación, edición o eliminación solicitada.
- **Resultado esperado:** El estado visible coincide con el estado conservado.
- **Criterios de aceptación:** Un fallo informa que la operación no se completó y conserva el último estado válido; reabrir no revela datos parciales.
- **Referencia al PRD:** 6 “Confiable”, 8.2 y 14.

### 6.6 Funcionamiento sin conexión

#### RF-OFF-001 — Apertura y consulta sin conexión
- **Descripción:** Nuvora debe abrir y permitir consultar movimientos previamente disponibles sin Internet.
- **Prioridad:** MUST
- **Precondiciones:** Datos disponibles en el dispositivo y ausencia de conexión.
- **Resultado esperado:** El historial consultable no depende permanentemente de Internet.
- **Criterios de aceptación:** Sin conexión se puede abrir, listar y consultar los movimientos disponibles.
- **Referencia al PRD:** 8.2.2.

#### RF-OFF-002 — Registro manual sin conexión
- **Descripción:** El usuario debe poder registrar gastos e ingresos manualmente sin Internet.
- **Prioridad:** MUST
- **Precondiciones:** Aplicación disponible sin conexión.
- **Resultado esperado:** Los movimientos válidos quedan conservados localmente y participan en cálculos.
- **Criterios de aceptación:** La falta de conexión no impide confirmar un gasto o ingreso manual válido.
- **Referencia al PRD:** 8.2.2.

#### RF-OFF-003 — Gestión sin conexión
- **Descripción:** El usuario debe poder editar y eliminar movimientos disponibles sin Internet.
- **Prioridad:** MUST
- **Precondiciones:** Movimiento disponible y ausencia de conexión.
- **Resultado esperado:** Los cambios válidos se conservan y actualizan los cálculos.
- **Criterios de aceptación:** Editar o eliminar sin conexión produce el mismo resultado financiero observable que hacerlo con conexión.
- **Referencia al PRD:** 8.2.2, “corregir movimientos”; 8.2 “Gestión de movimientos”.

#### RF-OFF-004 — Resúmenes y capacidades remotas
- **Descripción:** Sin Internet, Nuvora debe calcular resúmenes con datos disponibles e identificar las capacidades remotas no disponibles.
- **Prioridad:** MUST
- **Precondiciones:** Ausencia de conexión.
- **Resultado esperado:** El dashboard sigue siendo útil y voz o IA no producen resultados engañosos.
- **Criterios de aceptación:** Los resúmenes calculables se muestran; si voz o IA requieren conexión, se informa y no se crea un movimiento automáticamente.
- **Referencia al PRD:** 8.2.2.

### 6.7 Dashboard mensual

#### RF-DASH-001 — Total de ingresos
- **Descripción:** El dashboard debe mostrar la suma de ingresos contabilizables del período mensual seleccionado.
- **Prioridad:** MUST
- **Precondiciones:** Dashboard disponible.
- **Resultado esperado:** Se presenta un total en COP basado en movimientos válidos.
- **Criterios de aceptación:** Excluye transferencias propias; sin ingresos muestra un total equivalente a cero.
- **Referencia al PRD:** 7 “Dashboard y análisis”; 8.2.

#### RF-DASH-002 — Total de gastos
- **Descripción:** El dashboard debe mostrar los gastos contabilizables del período.
- **Prioridad:** MUST
- **Precondiciones:** Dashboard disponible.
- **Resultado esperado:** Se presenta un total en COP coherente con el historial.
- **Criterios de aceptación:** Excluye ingresos y transferencias propias; solo incluye gastos cuya fecha efectiva pertenece al período consultado.
- **Referencia al PRD:** 7 “Dashboard y análisis”; 8.2.

#### RF-DASH-003 — Resultado neto
- **Descripción:** El dashboard debe mostrar ingresos contabilizables menos gastos contabilizables del período.
- **Prioridad:** MUST
- **Precondiciones:** Totales calculables.
- **Resultado esperado:** Se presenta un resultado determinista en COP.
- **Criterios de aceptación:** Para el mismo conjunto de movimientos válidos el resultado es idéntico; las transferencias no lo alteran.
- **Referencia al PRD:** 3, 7 y 8.2.

#### RF-DASH-004 — Distribución de gastos
- **Descripción:** El dashboard debe mostrar la distribución de gastos contabilizables por categoría principal.
- **Prioridad:** MUST
- **Precondiciones:** Existen gastos en el período.
- **Resultado esperado:** Cada gasto contabilizable participa en su categoría y el total distribuido coincide con el total de gastos.
- **Criterios de aceptación:** No incluye ingresos ni transferencias propias; solo incluye gastos cuya fecha efectiva pertenece al período consultado.
- **Referencia al PRD:** 3, 7 y 8.2.

#### RF-DASH-005 — Comparaciones históricas condicionadas
- **Descripción:** Las comparaciones con períodos anteriores y categorías en crecimiento solo deben mostrarse cuando exista historial suficiente.
- **Prioridad:** MUST
- **Precondiciones:** Dashboard consultado.
- **Resultado esperado:** No se presentan comparaciones sin base adecuada.
- **Criterios de aceptación:** Si no se satisface TBD-004, se omite la comparación o se informa su indisponibilidad sin inferir tendencias.
- **Referencia al PRD:** 3, 5 y 8.2.

#### RF-DASH-006 — Distinción frente al saldo bancario
- **Descripción:** Nuvora debe distinguir el resultado neto del saldo bancario disponible y no afirmar conocer este último sin información suficiente.
- **Prioridad:** MUST
- **Precondiciones:** Se presenta información financiera agregada.
- **Resultado esperado:** El usuario no recibe una representación engañosa de liquidez bancaria.
- **Criterios de aceptación:** El resultado se identifica como correspondiente a movimientos registrados y no se etiqueta como saldo bancario disponible.
- **Referencia al PRD:** 4.1, 5 y 8.2; principio de confianza de VISION.

#### RF-DASH-007 — Consulta por período mensual
- **Descripción:** El usuario debe poder consultar el resumen correspondiente al mes actual y a períodos mensuales anteriores para los que existan movimientos.
- **Prioridad:** MUST
- **Precondiciones:** Dashboard disponible.
- **Resultado esperado:** Los indicadores mostrados corresponden únicamente al período consultado.
- **Criterios de aceptación:** Al abrir el dashboard se presenta el período mensual actual; el usuario puede consultar un mes anterior que contenga información; cambiar de período recalcula ingresos, gastos, resultado neto y distribución por categoría utilizando únicamente movimientos cuya fecha efectiva pertenece a dicho período.
- **Referencia al PRD:** 7 “Dashboard y análisis”; 8.2 “Dashboard mensual básico”; 11.6.

### 6.8 Registro mediante voz

#### RF-VOZ-001 — Inicio de captura
- **Descripción:** El usuario debe poder iniciar una captura de voz para proponer un movimiento.
- **Prioridad:** MUST
- **Precondiciones:** Capacidad disponible y condiciones de consentimiento satisfechas.
- **Resultado esperado:** Nuvora comienza a capturar únicamente tras una acción del usuario.
- **Criterios de aceptación:** No hay captura antes de la acción explícita; si la capacidad no está disponible, se informa sin crear datos.
- **Referencia al PRD:** 8.2 y 11.3.

#### RF-VOZ-002 — Indicador de grabación activa
- **Descripción:** Nuvora debe informar visualmente mientras la captura de audio está activa.
- **Prioridad:** MUST
- **Precondiciones:** Captura iniciada.
- **Resultado esperado:** El usuario conoce cuándo está siendo grabado.
- **Criterios de aceptación:** El indicador aparece durante toda la captura y deja de indicar actividad al detener o cancelar.
- **Referencia al PRD:** 6 y 13.1 “Privacidad”.

#### RF-VOZ-003 — Detención y cancelación
- **Descripción:** El usuario debe poder detener o cancelar una captura activa.
- **Prioridad:** MUST
- **Precondiciones:** Captura activa.
- **Resultado esperado:** Detener continúa hacia transcripción; cancelar abandona el intento sin crear movimiento.
- **Criterios de aceptación:** Ambas acciones están disponibles; cancelar no persiste una propuesta como movimiento.
- **Referencia al PRD:** 6, 8.2 y 11.3.

#### RF-VOZ-004 — Conversión de audio a texto
- **Descripción:** El audio completado debe poder convertirse en una transcripción para revisión.
- **Prioridad:** MUST
- **Precondiciones:** Captura detenida con audio utilizable y capacidad disponible.
- **Resultado esperado:** Se obtiene texto o un fallo explícito.
- **Criterios de aceptación:** Nunca se presenta una transcripción inexistente como exitosa; una respuesta vacía se trata como fallo.
- **Referencia al PRD:** 7 “Voz e interpretación”, 8.2 y 14.

#### RF-VOZ-005 — Presentación de la transcripción
- **Descripción:** Nuvora debe mostrar al usuario la transcripción obtenida antes de confirmar el movimiento derivado.
- **Prioridad:** MUST
- **Precondiciones:** Transcripción no vacía disponible.
- **Resultado esperado:** El usuario puede comparar lo dicho con el texto interpretado.
- **Criterios de aceptación:** La transcripción permanece visible durante la revisión de la propuesta correspondiente.
- **Referencia al PRD:** 8.2 y 11.3.

#### RF-VOZ-006 — Manejo de fallos de voz
- **Descripción:** Ante pérdida de conexión, fallo de procesamiento o transcripción vacía, Nuvora debe informar el fallo y permitir reintentar, cancelar o continuar manualmente cuando haya contexto útil.
- **Prioridad:** MUST
- **Precondiciones:** Intento de voz no completado.
- **Resultado esperado:** No se guarda información inválida ni se bloquea el acceso al registro manual.
- **Criterios de aceptación:** El fallo no crea un movimiento; el usuario conserva una salida clara del flujo.
- **Referencia al PRD:** 11.3 y 13.1.

#### RF-VOZ-007 — Un movimiento por interacción
- **Descripción:** Cada interacción de voz del MVP debe producir como máximo una propuesta de movimiento.
- **Prioridad:** MUST
- **Precondiciones:** Transcripción disponible.
- **Resultado esperado:** Nuvora no divide una instrucción en múltiples movimientos.
- **Criterios de aceptación:** Si el contenido expresa varios movimientos, no se guardan automáticamente y se solicita corrección o registro individual.
- **Referencia al PRD:** 8.2.1 y 10.1.

### 6.9 Interpretación mediante IA

#### RF-IA-001 — Extracción estructurada
- **Descripción:** La interpretación debe intentar proponer tipo, monto, moneda, fecha efectiva cuando pueda determinarse a partir del lenguaje, comercio o concepto disponible, categoría y subcategoría cuando haya información suficiente.
- **Prioridad:** MUST
- **Precondiciones:** Texto no vacío disponible.
- **Resultado esperado:** Se obtiene una propuesta estructurada o se declara que no pudo obtenerse.
- **Criterios de aceptación:** Cada dato propuesto es revisable; los datos ausentes no se inventan ni se presentan como confirmados. Cuando el usuario expresa explícitamente una referencia temporal interpretable, Nuvora intenta reflejarla en la fecha efectiva propuesta.
- **Referencia al PRD:** 8.2 y 14 “Interpretación de lenguaje”.

#### RF-IA-002 — Propuesta explicable e incierta
- **Descripción:** Nuvora debe distinguir la propuesta automática de los datos confirmados, explicar lo relevante e indicar incertidumbre significativa.
- **Prioridad:** MUST
- **Precondiciones:** Resultado de interpretación disponible.
- **Resultado esperado:** El usuario comprende qué se propuso y qué requiere revisión.
- **Criterios de aceptación:** Una interpretación por debajo de los criterios de TBD-003 se señala sin presentarla como hecho.
- **Referencia al PRD:** 6, 8.2 y 13.1.

#### RF-IA-003 — Exclusión de cálculos financieros
- **Descripción:** La IA no debe producir como fuente de verdad totales, resultado neto ni agrupaciones financieras.
- **Prioridad:** MUST
- **Precondiciones:** Se requiere un cálculo financiero.
- **Resultado esperado:** Los cálculos se obtienen de reglas deterministas sobre movimientos válidos.
- **Criterios de aceptación:** Modificar la redacción de una interpretación no altera un total hasta que exista un movimiento confirmado válido.
- **Referencia al PRD:** 6, 8.2 y 14.

#### RF-IA-004 — Sin modificación directa
- **Descripción:** La interpretación automática no debe crear, editar ni eliminar directamente información persistida.
- **Prioridad:** MUST
- **Precondiciones:** Propuesta automática disponible.
- **Resultado esperado:** Toda modificación requiere validación y confirmación aplicables.
- **Criterios de aceptación:** Generar o regenerar una propuesta no cambia historial ni dashboard.
- **Referencia al PRD:** 6 y principios de VISION.

#### RF-IA-005 — Validación determinista posterior
- **Descripción:** Antes de convertir una interpretación en movimiento, Nuvora debe aplicar las mismas reglas deterministas de validez y negocio que al registro manual.
- **Prioridad:** MUST
- **Precondiciones:** Propuesta lista para confirmar.
- **Resultado esperado:** Solo se persiste información válida.
- **Criterios de aceptación:** Monto o clasificación inválidos impiden guardar aunque hayan sido propuestos por IA.
- **Referencia al PRD:** 6, 8.2 y 14.

### 6.10 Confirmación y corrección

#### RF-CONF-001 — Presentación de la interpretación
- **Descripción:** Nuvora debe mostrar la propuesta estructurada y su transcripción de origen antes de guardarla.
- **Prioridad:** MUST
- **Precondiciones:** Interpretación disponible.
- **Resultado esperado:** El usuario revisa la propuesta completa relevante.
- **Criterios de aceptación:** Tipo, monto, moneda, fecha efectiva, concepto disponible y clasificación propuesta son visibles o se señalan como ausentes.
- **Referencia al PRD:** 8.2 y 11.3.

#### RF-CONF-002 — Corrección previa
- **Descripción:** El usuario debe poder modificar los campos de la propuesta antes de confirmar.
- **Prioridad:** MUST
- **Precondiciones:** Propuesta en revisión.
- **Resultado esperado:** La versión corregida sustituye la propuesta solo para el intento actual.
- **Criterios de aceptación:** Cada campo editable puede corregirse y vuelve a validarse antes de guardar.
- **Referencia al PRD:** 6, 8.2 y 11.3.

#### RF-CONF-003 — Cancelación de propuesta
- **Descripción:** El usuario debe poder cancelar una interpretación sin guardar un movimiento.
- **Prioridad:** MUST
- **Precondiciones:** Propuesta en revisión.
- **Resultado esperado:** Historial y resúmenes permanecen sin cambios.
- **Criterios de aceptación:** Cancelar abandona el intento y no contabiliza la propuesta.
- **Referencia al PRD:** 6 y 8.2.

#### RF-CONF-004 — Guardado exclusivo de datos válidos
- **Descripción:** La confirmación debe guardar únicamente la versión visible, corregida y validada.
- **Prioridad:** MUST
- **Precondiciones:** Usuario solicita confirmar.
- **Resultado esperado:** El movimiento guardado coincide con lo confirmado.
- **Criterios de aceptación:** Si queda un error, no se guarda; si es válida, se crea un único movimiento y se actualizan los resúmenes.
- **Referencia al PRD:** 8.2 y 11.3.

#### RF-CONF-005 — Tratamiento de incertidumbre
- **Descripción:** La incertidumbre significativa debe comunicarse y requerir revisión explícita antes del guardado.
- **Prioridad:** MUST
- **Precondiciones:** Interpretación cumple la condición pendiente de TBD-003.
- **Resultado esperado:** Una suposición incierta no se confirma de forma inadvertida.
- **Criterios de aceptación:** El dato incierto se distingue y el movimiento no se guarda sin acción del usuario.
- **Referencia al PRD:** 6, 13.1 y 15.6.

### 6.11 Categorización automática

#### RF-AUT-001 — Sugerencia de categoría
- **Descripción:** Nuvora debe poder proponer una categoría del catálogo aplicable a partir de la información disponible.
- **Prioridad:** MUST
- **Precondiciones:** Movimiento en registro o revisión con datos interpretables.
- **Resultado esperado:** Se muestra una sugerencia válida o ninguna cuando no hay información suficiente.
- **Criterios de aceptación:** La sugerencia pertenece al catálogo del tipo de movimiento y se identifica como propuesta.
- **Referencia al PRD:** 7 “Categorización automática” y 8.2.

#### RF-AUT-002 — Aceptación de sugerencia
- **Descripción:** El usuario debe poder aceptar una categoría sugerida.
- **Prioridad:** MUST
- **Precondiciones:** Sugerencia disponible.
- **Resultado esperado:** La categoría aceptada forma parte del movimiento sujeto a confirmación.
- **Criterios de aceptación:** Aceptar no omite la validación ni guarda por sí solo el movimiento.
- **Referencia al PRD:** 8.2 y 12.

#### RF-AUT-003 — Corrección de sugerencia
- **Descripción:** El usuario debe poder sustituir la categoría sugerida por otra válida antes o después de guardar mediante edición.
- **Prioridad:** MUST
- **Precondiciones:** Sugerencia o movimiento categorizado disponible.
- **Resultado esperado:** La categoría corregida se utiliza en los cálculos una vez confirmada.
- **Criterios de aceptación:** La corrección actualiza la distribución correspondiente y no modifica el catálogo.
- **Referencia al PRD:** 5, 8.2, 11.4 y 12.

#### RF-AUT-004 — Registro analítico de aceptación o corrección
- **Descripción:** Nuvora debe distinguir analíticamente si una categoría sugerida fue aceptada o corregida, sin aprender ni alterar automáticamente el catálogo en el MVP.
- **Prioridad:** MUST
- **Precondiciones:** Sugerencia aceptada o corregida.
- **Resultado esperado:** El resultado de la interacción puede medirse sin cambiar el comportamiento futuro del catálogo.
- **Criterios de aceptación:** Se distingue aceptación de corrección; ninguna de las dos crea categorías ni personalización automática.
- **Referencia al PRD:** 8.3, 12 y 13.2.

### 6.12 Analítica de producto

#### RF-ANA-001 — Ciclo del registro manual
- **Descripción:** La analítica debe distinguir registro manual iniciado, completado y abandonado.
- **Prioridad:** MUST
- **Precondiciones:** Interacción manual correspondiente.
- **Resultado esperado:** Cada intento alcanza como máximo un resultado final distinguible.
- **Criterios de aceptación:** Inicio se registra al comenzar; completado solo tras guardado válido; abandonado cuando termina sin guardar.
- **Referencia al PRD:** 12.

#### RF-ANA-002 — Ciclo de voz e interpretación
- **Descripción:** La analítica debe distinguir registro por voz iniciado, interpretación aceptada sin cambios e interpretación corregida.
- **Prioridad:** MUST
- **Precondiciones:** Interacción de voz correspondiente.
- **Resultado esperado:** Se puede medir adopción y calidad de interpretación.
- **Criterios de aceptación:** Aceptación sin cambios y corrección son resultados mutuamente distinguibles para una propuesta confirmada.
- **Referencia al PRD:** 12.

#### RF-ANA-003 — Resultado de categoría automática
- **Descripción:** La analítica debe distinguir categoría automática aceptada y categoría automática corregida.
- **Prioridad:** MUST
- **Precondiciones:** Existió una categoría sugerida.
- **Resultado esperado:** Se puede calcular la proporción de sugerencias modificadas.
- **Criterios de aceptación:** No se registra aceptación automática si el usuario no confirmó el movimiento; los dos resultados son distinguibles.
- **Referencia al PRD:** 12.

## 7. Reglas de negocio

| ID | Regla |
| --- | --- |
| RN-001 | COP es la única moneda admitida y mostrada por el MVP. |
| RN-002 | Todo gasto válido debe tener una categoría principal del catálogo de gastos. |
| RN-003 | La subcategoría de gasto es opcional y, si existe, debe pertenecer a la categoría principal elegida. |
| RN-004 | Los catálogos de gastos e ingresos son separados y no modificables por el usuario durante el MVP. |
| RN-005 | Una transferencia propia no se contabiliza como ingreso ni como gasto. |
| RN-006 | Una transferencia propia no modifica el resultado neto del período. |
| RN-007 | Resultado neto del período = ingresos contabilizables − gastos contabilizables. |
| RN-008 | La IA interpreta, propone y explica; no es fuente de verdad para cálculos ni ejecuta reglas financieras por sí sola. |
| RN-009 | Una interacción de voz del MVP produce como máximo una propuesta de movimiento. |
| RN-010 | Las comparaciones entre períodos solo se presentan cuando existe historial suficiente. |
| RN-011 | El MVP no permite crear, editar ni eliminar categorías o subcategorías personalizadas. |
| RN-012 | Los movimientos participan en los períodos financieros según su fecha efectiva, no según la fecha en que fueron creados o modificados en el sistema. |
| RN-013 | Todo ingreso válido debe tener una categoría perteneciente al catálogo de ingresos del MVP. |

## 8. Requisitos no funcionales

### 8.1 Usabilidad

| ID | Requisito verificable | Prioridad | Referencia |
| --- | --- | --- | --- |
| RNF-UX-001 | Los flujos frecuentes deben presentar una ruta inequívoca y el menor número razonable de acciones; los objetivos cuantitativos se rigen por TBD-001 y TBD-002. | MUST | PRD 3 y 6 |
| RNF-UX-002 | El lenguaje debe ser comprensible en español colombiano y evitar exigir conocimientos contables. | MUST | PRD 4.1 y 6 |
| RNF-UX-003 | Cada acción de confirmar, cancelar, corregir o eliminar debe comunicar su resultado y no dejar ambiguo el estado del movimiento. | MUST | PRD 6 |
| RNF-UX-004 | El usuario debe poder completar el registro cotidiano en segundos; el valor objetivo exacto permanece en TBD-001. | MUST | PRD 3, 6 y 12 |

### 8.2 Rendimiento

| ID | Requisito verificable | Prioridad | Referencia |
| --- | --- | --- | --- |
| RNF-PERF-001 | Toda operación iniciada debe mostrar respuesta o indicación visible mientras continúa; los límites temporales están en TBD-006. | MUST | PRD 6 |
| RNF-PERF-002 | Una operación de voz o IA pendiente no debe impedir consultar ni abandonar de forma segura el flujo disponible. | MUST | PRD 6 y 13.1 |
| RNF-PERF-003 | Las operaciones financieras locales no deben requerir esperas por servicios remotos; sus metas numéricas se definirán en TBD-006. | MUST | PRD 8.2.2 |

### 8.3 Confiabilidad e integridad

| ID | Requisito verificable | Prioridad | Referencia |
| --- | --- | --- | --- |
| RNF-REL-001 | Un movimiento cuya confirmación fue presentada como exitosa debe permanecer disponible después de cerrar o reiniciar. | MUST | PRD 8.2 y 14 |
| RNF-REL-002 | Una operación fallida no debe crear movimientos parciales ni discrepancias entre historial y resúmenes. | MUST | PRD 6 |
| RNF-REL-003 | Los mismos movimientos y reglas deben producir los mismos totales, agrupaciones y resultado neto. | MUST | PRD 6 y 14 |
| RNF-REL-004 | Repetir una confirmación o acción no debe crear resultados duplicados o inesperados sin una nueva intención del usuario. | MUST | PRD 7 “Transacciones” |
| RNF-REL-005 | La información automática no confirmada no debe afectar datos persistidos ni cálculos. | MUST | PRD 6 y 8.2 |

### 8.4 Seguridad

| ID | Requisito verificable | Prioridad | Referencia |
| --- | --- | --- | --- |
| RNF-SEC-001 | Los datos financieros deben estar protegidos frente a consulta o modificación no autorizada durante su captura, conservación y procesamiento. | MUST | VISION “Confianza por diseño”; PRD 13.1 |
| RNF-SEC-002 | Nuvora no debe mostrar secretos, credenciales ni datos internos sensibles al usuario ni incluirlos en información analítica funcional. | MUST | VISION “Privacidad y seguridad” |
| RNF-SEC-003 | Cada capacidad debe acceder únicamente a la información y funciones necesarias para su propósito declarado. | MUST | VISION “Control humano” |
| RNF-SEC-004 | Las operaciones que modifican o eliminan información deben requerir una acción controlada y producir un resultado verificable. | MUST | PRD 6 y 8.2 |

### 8.5 Privacidad

| ID | Requisito verificable | Prioridad | Referencia |
| --- | --- | --- | --- |
| RNF-PRIV-001 | Antes de capturar audio, Nuvora debe informar qué se captura y solicitar la acción o autorización aplicable. | MUST | PRD 13.1 y 14 |
| RNF-PRIV-002 | El indicador de captura debe permitir conocer cuándo comienza y termina el uso del micrófono. | MUST | PRD 6 y 13.1 |
| RNF-PRIV-003 | El usuario debe poder comprender para qué se usan audio, transcripciones, movimientos y correcciones. | MUST | PRD 13.1 y 15.2 |
| RNF-PRIV-004 | Si se envía información a servicios externos, Nuvora debe comunicar qué clase de información se envía, para qué y cuándo; los detalles se rigen por TBD-014. | MUST | PRD 14 y 15.2 |
| RNF-PRIV-005 | Nuvora debe informar qué datos conserva y bajo qué política; los períodos y condiciones están en TBD-005. | MUST | PRD 15.2 |

### 8.6 Accesibilidad

| ID | Requisito verificable | Prioridad | Referencia |
| --- | --- | --- | --- |
| RNF-ACC-001 | El texto y los importes deben permanecer legibles en los tamaños de visualización admitidos; mínimos exactos en TBD-008. | MUST | VISION “experiencia accesible” |
| RNF-ACC-002 | Texto, controles e indicadores deben mantener contraste suficiente; el umbral aplicable está en TBD-008. | MUST | VISION “evolución sencilla y accesible” |
| RNF-ACC-003 | Los controles táctiles deben disponer de un área operable adecuada; el mínimo exacto está en TBD-008. | MUST | PRD 6 |
| RNF-ACC-004 | Las acciones y datos esenciales deben exponer nombre, estado y orden comprensibles a tecnologías de accesibilidad compatibles. | MUST | VISION “accesible” |
| RNF-ACC-005 | Ningún estado, error, categoría o resultado debe comunicarse exclusivamente mediante color. | MUST | VISION “claridad” |

### 8.7 Compatibilidad

| ID | Requisito verificable | Prioridad | Referencia |
| --- | --- | --- | --- |
| RNF-COMP-001 | El MVP debe poder utilizarse en Android dentro del rango compatible que se defina en TBD-007. | MUST | PRD 1 y 8.1 |
| RNF-COMP-002 | El lanzamiento del MVP no requiere funcionamiento en iOS ni en otros sistemas. | MUST | PRD 8.3 y 10.1 |
| RNF-COMP-003 | Las capacidades esenciales definidas como offline deben conservar su comportamiento en dispositivos Android compatibles sin conexión. | MUST | PRD 8.2.2 |

### 8.8 Mantenibilidad del producto

| ID | Requisito verificable | Prioridad | Referencia |
| --- | --- | --- | --- |
| RNF-MANT-001 | Las reglas compartidas deben producir comportamiento consistente en registro manual, voz, edición, historial y dashboard. | MUST | VISION “separación de responsabilidades”; PRD 6 |

## 9. Estados de error y casos límite

| Escenario | Comportamiento esperado | Requisitos relacionados |
| --- | --- | --- |
| Monto vacío, cero, negativo o inválido | Impedir confirmación, identificar el dato corregible y no modificar historial ni resúmenes. | RF-MOV-005, RF-MOV-008 |
| Categoría principal ausente en un gasto | Impedir el guardado hasta seleccionar una categoría válida. | RF-MOV-006, RN-002 |
| Categoría ausente en un ingreso | Impedir el guardado hasta seleccionar una categoría válida del catálogo de ingresos. | RF-MOV-006, RN-013 |
| Categoría incompatible con el tipo | Invalidar la selección y requerir una del catálogo correspondiente. | RF-CAT-003 |
| Fecha efectiva ausente o inválida | Impedir el guardado, solicitar una fecha válida y no modificar historial ni resúmenes. | RF-MOV-009, RN-012 |
| Pérdida de conexión durante una función esencial | Mantener disponibles consulta, registro manual, edición, eliminación y resúmenes calculables. | RF-OFF-001–004 |
| Pérdida de conexión durante voz o IA | Informar indisponibilidad o fallo, no guardar propuestas y permitir salida o alternativa manual. | RF-VOZ-006, RF-OFF-004 |
| Fallo de Speech-to-Text | No generar una interpretación como exitosa; permitir reintentar, cancelar o continuar manualmente. | RF-VOZ-004, RF-VOZ-006 |
| Transcripción vacía | Tratarla como fallo y no crear movimiento. | RF-VOZ-004 |
| Interpretación inválida o incompleta | Mostrar los datos ausentes o inciertos, permitir corregir y bloquear el guardado mientras sea inválida. | RF-IA-001, RF-CONF-002–005 |
| Información insuficiente para categorizar | No inventar categoría; solicitar selección o corrección cuando sea obligatoria. | RF-AUT-001, RF-MOV-006 |
| Instrucción de voz con múltiples movimientos | No crear varios movimientos; solicitar registros individuales o corrección. | RF-VOZ-007 |
| Cancelación por el usuario | Detener el flujo correspondiente sin crear ni modificar un movimiento. | RF-MOV-008, RF-VOZ-003, RF-CONF-003 |
| Ausencia de historial suficiente | No mostrar comparaciones ni afirmar tendencias. | RF-DASH-005 |
| Lista de movimientos vacía | Mostrar un estado vacío válido y totales equivalentes a cero, no un error. | RF-GES-001, RF-DASH-001–003 |
| Error al persistir | Informar que la operación no se completó, mantener el último estado válido y evitar datos parciales. | RF-PER-003, RNF-REL-002 |
| Repetición de una acción | No producir duplicados ni resultados inesperados sin nueva intención. | RNF-REL-004 |

## 10. Fuera de alcance del MVP

- Presupuestos.
- Objetivos de ahorro.
- Gestión completa de deudas.
- Asociación y tratamiento de reembolsos vinculados a gastos previos.
- Asistente financiero o recomendaciones personalizadas.
- Multimoneda.
- Lectura automática de notificaciones u otras fuentes automáticas.
- Widgets y shortcuts.
- Sincronización bancaria y Open Banking.
- Lanzamiento y compatibilidad en iOS.
- Categorías personalizadas.
- Aprendizaje personalizado avanzado.
- Safe-to-spend y forecasting.
- Interpretación de múltiples movimientos en una instrucción de voz.
- Cuenta obligatoria.
- Backup, recuperación, sincronización multidispositivo y uso multidispositivo.
- Detección avanzada de duplicados o suscripciones.
- Funciones sociales, familiares o de finanzas compartidas.

## 11. Matriz de trazabilidad PRD → SRS

| Fuente PRD | Requisitos SRS |
| --- | --- |
| 1. Resumen del producto | RF-ONB-001–004, RNF-COMP-001–002 |
| 2. Problema | RF-ONB-002–003, RF-MOV-007, RNF-UX-001–004 |
| 3. Objetivos del producto | RF-MOV-002–003, RF-DASH-001–007, RF-VOZ-001–007, RF-AUT-001–004 |
| 4. Usuarios objetivo | RF-ONB-004, RF-DASH-001–007 |
| 5. Jobs To Be Done | RF-MOV-002–003, RF-GES-001–005, RF-DASH-001–007, RF-VOZ-001–007 |
| 6. Propuesta de experiencia | RF-IA-002–005, RF-CONF-001–005, RNF-UX-001–004, RNF-REL-001–005 |
| 7. Transacciones | RF-MOV-001–009, RF-GES-001–005, RN-005–007, RN-012 |
| 7. Reembolsos — V1 / futuro | Sección 10 “Fuera de alcance del MVP” |
| 7. Categorías | RF-CAT-001–004, RF-AUT-001–004, RN-002–004, RN-011, RN-013 |
| 7. Dashboard y análisis | RF-DASH-001–007, RN-007, RN-010, RN-012 |
| 7. Voz, interpretación y automatización | RF-VOZ-001–007, RF-IA-001–005, RF-CONF-001–005 |
| 8.1 Hipótesis del MVP | RF-ONB-001–004, RN-001, RNF-COMP-001–002 |
| 8.2 Capacidades incluidas | RF-ONB-001–004, RF-MOV-001–009, RF-GES-001–005, RF-CAT-001–004, RF-PER-001–003, RF-DASH-001–007, RF-VOZ-001–007, RF-IA-001–005, RF-CONF-001–005, RF-AUT-001–004 |
| 8.2.1 Captura por voz | RF-VOZ-007, RN-009 |
| 8.2.2 Funcionamiento sin conexión | RF-OFF-001–004, RNF-PERF-003, RNF-COMP-003 |
| 8.3 Capacidades excluidas | RF-CAT-004, RF-AUT-004, sección 10 |
| 9.1 V1 | Sección 10 para reembolsos y demás capacidades futuras |
| 10. Fuera de alcance | Sección 10, RN-011 |
| 11. User journeys | RF-ONB-001–003, RF-MOV-002–004, RF-MOV-009, RF-GES-003, RF-DASH-001–007, RF-VOZ-001–007, RF-CONF-001–005 |
| 12. Métricas del producto | RF-ANA-001–003, RF-AUT-004, TBD-001–003, TBD-009 |
| 13. Riesgos y supuestos | RF-VOZ-006, RF-IA-001–002, RF-CONF-005, RNF-PRIV-001–005 |
| 14. Dependencias de producto | RF-PER-001–003, RF-VOZ-004–005, RF-IA-001–005, RNF-SEC-001–004, RNF-PRIV-001–005 |
| 15. Preguntas abiertas | TBD-003, TBD-005, TBD-009–011, TBD-014 |

## 12. Requisitos pendientes y decisiones TBD

| ID | Decisión pendiente | Requisitos impactados |
| --- | --- | --- |
| TBD-001 | Tiempo objetivo exacto y método de medición para completar un registro manual. | RF-ONB-003, RF-MOV-002–003, RNF-UX-001, RNF-UX-004 |
| TBD-002 | Tiempo objetivo exacto y método de medición para completar un registro por voz. | RF-VOZ-001–006, RNF-UX-001 |
| TBD-003 | Umbrales y criterios de confianza que determinan incertidumbre significativa. | RF-IA-002, RF-CONF-005, RF-AUT-001 |
| TBD-004 | Cantidad, comparabilidad y completitud requeridas para considerar que existe historial suficiente. | RF-DASH-005, RN-010 |
| TBD-005 | Políticas de conservación y eliminación de audio, transcripciones, movimientos y correcciones. | RNF-PRIV-003–005, RF-VOZ-003–005 |
| TBD-006 | Metas numéricas y condiciones de medición de respuesta, carga y cálculo. | RNF-PERF-001–003 |
| TBD-007 | Versión mínima y rango de versiones Android compatibles. | RNF-COMP-001, RNF-COMP-003 |
| TBD-008 | Estándar y umbrales cuantitativos de legibilidad, contraste y tamaño táctil. | RNF-ACC-001–005 |
| TBD-009 | Objetivos numéricos, ventanas y línea base para métricas de activación, velocidad, calidad, recurrencia y retención. | RF-ANA-001–003, RNF-UX-004 |
| TBD-010 | Estrategia futura de sincronización, resolución de conflictos, backup y recuperación. | Fuera del MVP; RF-PER-001–003 como restricción de continuidad actual |
| TBD-011 | Política futura de cuenta opcional y condiciones para solicitarla sin impedir valor previo. | RF-ONB-001; fuera del MVP |
| TBD-014 | Condiciones definitivas de conectividad, consentimiento, información enviada y tratamiento externo para voz e IA. | RF-OFF-004, RF-VOZ-001–006, RNF-PRIV-001–005 |

## 13. Resumen de cobertura

- **Requisitos funcionales:** 60.
- **Requisitos no funcionales:** 30.
- **Reglas de negocio:** 13.
- **Decisiones pendientes:** 12.
- **Inconsistencias entre VISION.md y PRD.md:** ninguna contradicción directa identificada.
- **Contradicciones restantes entre PRD.md y SRS.md:** ninguna identificada; la asociación y el tratamiento de reembolsos están explícitamente fuera del MVP en ambos documentos.
