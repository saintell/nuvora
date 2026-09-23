# Nuvora — Product Requirements Document

## 1. Resumen del producto

Nuvora es una aplicación móvil de finanzas personales que ayuda a las personas a registrar y comprender sus movimientos con el menor esfuerzo posible. Su oportunidad está en cerrar la brecha entre las herramientas que exigen disciplina contable constante y la necesidad cotidiana de saber qué está ocurriendo con el dinero.

El MVP se orientará inicialmente a Colombia, se ofrecerá en español, utilizará COP como moneda principal y se lanzará para Android. El lenguaje cotidiano, los ejemplos y la experiencia inicial responderán al contexto colombiano. La expansión a iOS, otros mercados, idiomas y monedas se evaluará posteriormente.

El producto comenzará demostrando que la captura financiera puede ser rápida e inteligente: el usuario podrá registrar movimientos manualmente o mediante voz, revisar la interpretación propuesta, corregirla cuando sea necesario y obtener una visión mensual clara. La evolución posterior reducirá aún más la intervención manual y añadirá planificación y orientación conversacional.

Este documento define el producto, su alcance inicial y su evolución prevista. No prescribe arquitectura ni decisiones de implementación.

## 2. Problema

Registrar las finanzas personales suele competir con actividades más urgentes. Incluso una interacción breve, repetida después de cada compra o ingreso, puede sentirse como una carga. Cuando el registro depende por completo de la memoria y la disciplina, se posterga, se acumula o se abandona.

Esto produce varios problemas relacionados:

- **Fricción del registro manual:** introducir importes, conceptos y categorías repetidamente requiere tiempo y atención.
- **Abandono de las herramientas:** si el esfuerzo aparece antes que el valor, el hábito no se consolida.
- **Información incompleta:** los movimientos omitidos o registrados tarde reducen la confiabilidad de cualquier resumen.
- **Datos difíciles de interpretar:** disponer de una lista de transacciones no equivale a entender hábitos, tendencias o margen financiero.
- **Barreras de conocimiento:** muchas herramientas utilizan conceptos o flujos que presuponen experiencia financiera.
- **Falta de confianza en la automatización:** una clasificación opaca o difícil de corregir puede ser peor que el registro manual.

Nuvora debe producir utilidad desde las primeras interacciones, reducir progresivamente el trabajo requerido y presentar información financiera comprensible sin exigir conocimientos especializados.

## 3. Objetivos del producto

- Permitir que una persona capture gastos e ingresos en segundos y con interrupción mínima.
- Ofrecer una alternativa de registro por voz que convierta lenguaje cotidiano en un movimiento revisable.
- Organizar los movimientos mediante categorías útiles, con automatización visible y fácil de corregir.
- Dar una lectura mensual sencilla que permita saber cuánto se ha gastado, en qué se está yendo el dinero, cuál es el resultado neto del período y, cuando exista historial suficiente, qué categorías de gasto están creciendo.
- Construir confianza mediante resultados verificables, correcciones accesibles y comunicación transparente de la incertidumbre.
- Validar que la captura inteligente mejora la recurrencia y la integridad del registro frente a una experiencia exclusivamente manual.
- Establecer una base de comportamiento y confianza para incorporar posteriormente planificación y asistencia financiera.

## 4. Usuarios objetivo

### 4.1 Segmento prioritario del MVP

Personas en Colombia con ingresos relativamente estables, principalmente fijos o recurrentes, que tienen sus gastos desorganizados o encuentran difícil mantener un registro actualizado. Necesitan entender su situación cotidiana sin mantener hojas de cálculo ni aprender un sistema contable.

Nuvora debe ayudarlas especialmente a responder:

- ¿En qué se me está yendo el dinero?
- ¿Cuánto he gastado este mes?
- Cuando exista historial suficiente, ¿qué categorías de gasto están creciendo?
- ¿Cuánto de mis ingresos del mes no he gastado?

### 4.2 Perfil secundario: persona que quiere ahorrar con objetivos

Desea reservar dinero para una meta concreta y entender si sus hábitos actuales la acercan o alejan de ella. El seguimiento de objetivos no forma parte del MVP, pero este perfil orienta la evolución hacia planificación financiera sencilla.

### 4.3 Perfil secundario: persona con ingresos y gastos frecuentes que quiere reducir el esfuerzo de registro

Ya intenta llevar control financiero, pero encuentra repetitivo introducir y categorizar cada movimiento. Valora especialmente la voz, la categorización automática y la posibilidad de corregir rápidamente.

### 4.4 Necesidad potencial de multimoneda

Viajeros frecuentes, trabajadores remotos, migrantes y personas que reciben ingresos en distintas monedas pueden constituir un segmento futuro. Antes de priorizar esta capacidad se debe validar su tamaño, frecuencia de uso y complejidad percibida. El MVP para Colombia utilizará exclusivamente COP.

## 5. Jobs To Be Done

- Cuando gasto dinero, quiero registrarlo en segundos para no interrumpir lo que estoy haciendo ni depender de recordarlo después.
- Cuando recibo dinero, quiero incorporarlo con la misma facilidad para que mi resumen represente mi situación real.
- Cuando no puedo o no quiero escribir, quiero describir un movimiento con mi voz y confirmar que fue entendido correctamente.
- Cuando Nuvora propone una categoría, quiero comprobarla y corregirla fácilmente para conservar el control sobre mis datos.
- Cuando reviso mis movimientos, quiero encontrar y corregir errores sin reconstruir mi historial.
- Cuando termina la semana o el mes, quiero entender adónde fue mi dinero sin analizar hojas de cálculo.
- Cuando observo mis hábitos, quiero distinguir rápidamente mis principales categorías de gasto.
- Cuando dispongo de períodos anteriores comparables, quiero identificar qué categorías de gasto están creciendo para poder actuar a tiempo.
- Cuando reviso el mes, quiero conocer el resultado neto del período y cuánto de mis ingresos registrados no he gastado, sin hacer cálculos por mi cuenta.
- Cuando quiero comprar algo, quiero llegar a entender si puedo permitírmelo sin afectar mis compromisos; esta orientación pertenece a una etapa posterior al MVP.
- Cuando defino una meta financiera, quiero visualizar mi avance y saber qué acciones pueden acercarme a ella; esta capacidad pertenece a V1.

## 6. Propuesta de experiencia

Usar Nuvora debe sentirse:

- **Rápido:** las acciones frecuentes deben completarse en segundos y con el menor número razonable de pasos.
- **Visual:** los resúmenes deben destacar la información importante sin sobrecargar la pantalla.
- **Sencillo:** el lenguaje y los flujos deben ser comprensibles sin conocimientos financieros.
- **Progresivamente automatizado:** el producto debe comenzar asistiendo y avanzar hacia una menor intervención manual a medida que demuestre confiabilidad.
- **Conversacional cuando aporte valor:** la voz y, posteriormente, el asistente deben simplificar tareas o explicaciones, no añadir novedad sin utilidad.
- **Corregible:** toda interpretación o clasificación automática relevante debe poder revisarse y modificarse.
- **Confiable:** Nuvora debe diferenciar hechos, cálculos e interpretaciones, e indicar cuándo existe incertidumbre.
- **Tranquilizador:** el producto debe ayudar a comprender y actuar sin juzgar ni generar ansiedad innecesaria.

La IA interpreta lenguaje y explica información. El motor financiero conserva la responsabilidad exclusiva de calcular resultados y ejecutar reglas financieras.

## 7. Capacidades del producto

Esta sección describe el mapa del producto, no el alcance de una sola versión.

### Transacciones

Captura, consulta, edición y eliminación de movimientos; incorporación progresiva de distintas fuentes y controles para evitar registros duplicados. El producto distinguirá conceptualmente, como mínimo, gastos, ingresos y transferencias.

Todo movimiento del MVP tendrá una fecha efectiva que indique cuándo ocurrió y determine el período financiero al que pertenece, independientemente de cuándo haya sido creado o modificado en Nuvora. Durante el registro podrá utilizarse inicialmente la fecha actual y el usuario podrá modificarla antes de confirmar.

Una transferencia entre fondos o cuentas propias no representa consumo ni generación de dinero. Por lo tanto, no debe contabilizarse como gasto, ingreso ni alterar el resultado neto del período.

### Reembolsos — V1 / futuro

Los reembolsos no forman parte del MVP. En una versión posterior, un reembolso asociado a un gasto previo no debería considerarse un ingreso ordinario y debería reducir total o parcialmente el efecto del gasto relacionado cuando exista información suficiente para establecer esa relación.

La decisión de incorporar esta capacidad y la definición detallada de su comportamiento se realizarán posteriormente.

### Categorías

En el MVP, Nuvora proporcionará catálogos separados para gastos e ingresos. Cada gasto tendrá una categoría principal obligatoria y podrá tener una subcategoría opcional. Cada ingreso tendrá una categoría obligatoria perteneciente al catálogo de ingresos. Las transferencias propias no utilizarán los catálogos de gastos o ingresos. Los usuarios podrán seleccionar o corregir estas clasificaciones, pero no crear, editar ni eliminar categorías personalizadas.

#### Catálogo de gastos del MVP

1. **Alimentación:** Supermercado; Restaurantes; Domicilios; Café / Snacks; Otros alimentos.
2. **Vivienda:** Arriendo; Hipoteca; Administración; Mantenimiento; Reparaciones; Muebles / Hogar; Otros vivienda.
3. **Servicios:** Energía; Agua; Gas; Internet; Telefonía móvil; Televisión; Otros servicios.
4. **Transporte:** Combustible; Transporte público; Taxi / Apps; Parqueadero; Peajes; Mantenimiento vehículo; Repuestos; Otros transporte.
5. **Deudas y obligaciones:** Tarjeta de crédito; Crédito de consumo; Crédito vehicular; Crédito hipotecario; Préstamos personales; Otras obligaciones.
6. **Salud:** Medicamentos; Consultas; Exámenes; Odontología; Seguro / Medicina prepagada; Otros salud.
7. **Educación:** Matrícula; Cursos; Libros; Materiales; Otros educación.
8. **Compras personales:** Ropa; Calzado; Tecnología; Cuidado personal; Accesorios; Otras compras.
9. **Entretenimiento:** Cine; Salidas; Juegos; Eventos; Hobbies; Otros entretenimiento.
10. **Suscripciones:** Streaming; Música; Software; Apps; Membresías; Otras suscripciones.
11. **Familia y personal:** Hijos; Mascotas; Regalos; Ayuda familiar; Otros personales.
12. **Otros**, sin subcategorías iniciales.

#### Catálogo de ingresos del MVP

- Salario.
- Honorarios / Freelance.
- Negocio / Ventas.
- Comisiones.
- Bonificaciones.
- Rendimientos.
- Otros ingresos.

El aprendizaje progresivo a partir de correcciones se evaluará en versiones posteriores y no modificará estos catálogos durante el MVP.

### Dashboard y análisis

Resúmenes mensuales de ingresos, gastos y resultado neto del período; distribución por categorías; consulta del mes actual y de meses anteriores que contengan movimientos, usando la fecha efectiva para determinar el período; comparaciones temporales, cuando exista historial suficiente, para identificar categorías cuyo gasto está aumentando; y explicaciones comprensibles de patrones relevantes.

### Presupuestos

Definición de límites, seguimiento del consumo y alertas o explicaciones que ayuden a mantenerlos.

### Objetivos de ahorro

Creación de metas, seguimiento de progreso y orientación para alcanzarlas.

### Deudas

Registro y seguimiento personal de saldos, pagos y evolución, sin convertirse en una herramienta crediticia o contable.

### Voz e interpretación

Conversión de voz a texto e interpretación estructurada de movimientos expresados en lenguaje cotidiano, siempre con mecanismos de revisión.

### Categorización automática

Propuesta de categorías con niveles crecientes de personalización y aprendizaje, sin ocultar incertidumbre.

### Asistente financiero

Consultas sobre la información del usuario, explicaciones y orientación contextual respaldadas por datos y cálculos verificables.

### Multimoneda

Representación de movimientos y análisis en más de una moneda cuando la necesidad del segmento haya sido validada.

### Persistencia y sincronización

Conservación segura de la información y continuidad de la experiencia entre sesiones y, en etapas posteriores, entre dispositivos y fuentes.

### Automatización

Reducción progresiva del registro manual mediante señales permitidas por cada plataforma, integraciones y aprendizaje de preferencias.

## 8. Definición estricta del MVP

### 8.1 Hipótesis del MVP

El MVP debe validar que combinar una captura manual excelente con captura por voz, interpretación estructurada y categorización corregible reduce la fricción y produce una visión mensual suficientemente útil para fomentar el uso recurrente.

El lanzamiento inicial se realizará en Android para el mercado colombiano, en español y con COP como única moneda. La compatibilidad futura con iOS deberá preservarse como objetivo de evolución, pero no forma parte del lanzamiento del MVP.

### 8.2 Capacidades incluidas

| Capacidad | Resultado esperado | Razón para incluirla |
| --- | --- | --- |
| Onboarding mínimo | La persona comprende el valor principal y llega rápidamente al primer registro. | Reduce el tiempo hasta obtener utilidad. |
| Inicio sin cuenta obligatoria | La persona puede comenzar a registrar y consultar sus finanzas sin crear una cuenta previamente. | Reduce la fricción de activación y permite demostrar valor antes de solicitar registro. |
| Experiencia inicial para Colombia | La aplicación se ofrece en español, utiliza lenguaje y ejemplos del contexto colombiano y se lanza en Android. | Concentra la validación del MVP en un mercado y una plataforma definidos. |
| Moneda principal | Todos los movimientos y resúmenes del MVP se expresan en COP. | Permite cálculos coherentes sin introducir multimoneda. |
| Registro manual de gastos | Se puede crear un gasto rápidamente con la información esencial. | Es el método universal y el respaldo de cualquier automatización. |
| Registro manual de ingresos | Se pueden incorporar ingresos con la misma simplicidad. | Hace posible calcular un resultado neto del período mensual significativo. |
| Tratamiento básico de transferencias | El usuario puede identificar un movimiento como transferencia propia para excluirlo de los totales de ingresos, gastos y resultado neto. La gestión completa de cuentas de origen y destino no forma parte necesariamente del MVP. | Evita que movimientos internos del dinero distorsionen los análisis sin introducir todavía un sistema avanzado de cuentas. |
| Categorías iniciales | Los gastos usan una categoría principal obligatoria y una subcategoría opcional; los ingresos requieren una categoría de su catálogo separado; las transferencias propias no utilizan ninguno de estos catálogos. Ambos catálogos son definidos por Nuvora. | Permite organizar y analizar movimientos desde el primer uso con una taxonomía consistente. |
| Fecha efectiva del movimiento | Todo movimiento tiene una fecha efectiva válida que determina su período financiero. Puede utilizar inicialmente la fecha actual y el usuario puede modificarla antes de confirmar o al editar el movimiento. | Permite atribuir cada movimiento al período en que ocurrió y mantener resúmenes mensuales correctos. |
| Gestión de movimientos | Se pueden listar, consultar, editar y eliminar movimientos. Modificar la fecha efectiva actualiza los períodos afectados. | Garantiza control y capacidad de corregir el historial. |
| Persistencia de datos | Los movimientos y configuraciones esenciales permanecen disponibles después de cerrar y volver a abrir la aplicación. | La continuidad de los datos es indispensable para que el producto tenga utilidad financiera real. |
| Dashboard mensual básico | Muestra ingresos, gastos, resultado neto del período y distribución por categoría para el mes actual, y permite consultar meses anteriores que contengan movimientos. Cuando exista historial suficiente, permite comparar períodos e identificar categorías cuyo gasto está aumentando. | Responde las preguntas principales del segmento prioritario sin presentar comparaciones cuando todavía no existe información suficiente. |
| Registro mediante voz | El usuario puede describir un gasto o ingreso sin escribirlo. | Demuestra una reducción diferenciada de la fricción. |
| Speech-to-Text | La voz se convierte en texto visible para su revisión. | Hace posible la interpretación y aporta transparencia. |
| Interpretación estructurada mediante IA | Nuvora propone los datos esenciales del movimiento a partir del lenguaje cotidiano. | Reduce el trabajo de completar el registro. |
| Confirmación y corrección | El usuario puede confirmar o modificar la propuesta antes de guardar cuando haya ambigüedad. | Protege la exactitud y el control humano. |
| Categorización automática básica | Nuvora propone una categoría que puede ser aceptada o corregida. | Valida el primer nivel de automatización útil. |

La interpretación mediante IA nunca sustituirá los cálculos del dashboard. Los totales, el resultado neto del período y las agrupaciones procederán del motor financiero determinista. Las transferencias propias quedarán excluidas de los totales de ingresos y gastos.

### 8.2.1 Alcance de la captura por voz en el MVP

En el MVP, cada interacción de voz estará orientada a interpretar un único movimiento financiero.

Ejemplo:

"Gasté 45 mil en Uber."

La interpretación de múltiples movimientos dentro de una misma instrucción de voz se considera una capacidad posterior y no será necesaria para validar el MVP.

### 8.2.2 Funcionamiento sin conexión

Las funciones financieras esenciales del MVP no deberán depender permanentemente de una conexión a Internet.

El usuario deberá poder, como mínimo:

- consultar movimientos previamente disponibles;
- registrar gastos e ingresos manualmente;
- corregir movimientos;
- consultar los resúmenes que puedan calcularse con los datos disponibles.

Las capacidades de voz e interpretación mediante IA podrán requerir conexión durante el MVP si la solución seleccionada técnicamente lo necesita.

La estrategia técnica de persistencia y sincronización se definirá posteriormente.

### 8.3 Capacidades consideradas y excluidas del MVP

- **Presupuesto básico:** se pospone a V1 porque amplía el MVP desde captura y comprensión hacia planificación y seguimiento de reglas.
- **Objetivo de ahorro básico:** se pospone a V1; requiere validar primero que los movimientos proporcionan una base confiable.
- **Deuda básica:** se pospone a V1 para evitar introducir un dominio adicional antes de validar el núcleo.
- **Reembolsos asociados a gastos previos:** su asociación y tratamiento se posponen a V1 o una versión posterior para evitar ampliar el núcleo transaccional del MVP.
- **Asistente V0:** se pospone a V1; sus respuestas serán más valiosas después de comprobar la calidad y continuidad de los datos.
- **Multimoneda:** se excluye mientras no se valide la importancia del segmento y el producto opere con una moneda principal.
- **Aprendizaje de correcciones:** el MVP mide las correcciones, pero no promete personalización automática a partir de ellas.
- **Detección automática de movimientos:** se excluye para no depender de capacidades variables por plataforma o integraciones externas durante la validación inicial.
- **Cuenta y autenticación:** se evaluarán posteriormente para habilitar backup, recuperación, sincronización y uso en múltiples dispositivos, pero no serán necesarias para comenzar a usar el MVP.
- **Compatibilidad con iOS:** se conserva como objetivo futuro, pero no forma parte del lanzamiento inicial en Android.

## 9. Funcionalidades posteriores

### 9.1 V1

- Presupuesto mensual básico y seguimiento de consumo.
- Objetivo de ahorro básico con progreso visible.
- Registro y seguimiento básico de deudas personales.
- Asociación y tratamiento de reembolsos vinculados a gastos previos, sujeto a definición posterior.
- Asistente inicial limitado a consultas concretas respaldadas por datos, como gastos del mes o distribución por categoría.
- Primer nivel de aprendizaje a partir de correcciones de categorías, sujeto a controles y evaluación.
- Experimentos de captura contextual permitidos por la plataforma, incluida la evaluación de notificaciones en Android.
- Widgets o shortcuts para reducir pasos en acciones frecuentes.
- Multimoneda limitada únicamente si la investigación confirma un segmento prioritario y un caso de uso frecuente.
- Mejoras en detección de posibles duplicados y gestión de movimientos recurrentes.
- Evaluación de una cuenta opcional para backup, recuperación, sincronización y uso en múltiples dispositivos.
- Compatibilidad con iOS, conservando una experiencia consistente con los principios del producto.

### 9.2 V2 / futuro

- Sincronización bancaria u Open Banking donde exista cobertura, viabilidad y confianza suficientes.
- Automatización avanzada de captura, conciliación y categorización.
- Aprendizaje personalizado avanzado con controles explícitos para el usuario.
- Detección avanzada de duplicados.
- Cálculo de safe-to-spend basado en compromisos y datos confiables.
- Forecasting de flujo de dinero y escenarios futuros.
- Identificación y gestión de suscripciones o gastos recurrentes.
- Asistente proactivo que detecte información relevante sin asumir autoridad sobre las decisiones.
- Multimoneda avanzada con conversión, reportes y reglas claramente definidas.
- Nuevas superficies de acceso rápido cuando demuestren reducir fricción de manera medible.

Estas capacidades son oportunidades, no compromisos. Su prioridad dependerá de evidencia de usuario, calidad de los datos, viabilidad y alineación con los principios del producto.

## 10. Fuera de alcance

### 10.1 Fuera del MVP

- Presupuestos, objetivos de ahorro y gestión de deudas.
- Asociación y tratamiento de reembolsos vinculados a gastos previos.
- Asistente conversacional o recomendaciones personalizadas.
- Soporte para múltiples monedas.
- Lectura de notificaciones y otras fuentes automáticas de movimientos.
- Widgets, shortcuts e integraciones con asistentes del sistema.
- Sincronización bancaria y Open Banking.
- Detección avanzada de duplicados o suscripciones.
- Aprendizaje automático avanzado a partir del comportamiento individual.
- Safe-to-spend, forecasting y automatización proactiva.
- Funciones sociales, familiares o de finanzas compartidas.
- Interpretación de múltiples transacciones dentro de una única instrucción de voz.
- Creación, edición o eliminación de categorías personalizadas.
- Cuenta obligatoria, backup, recuperación, sincronización entre dispositivos y uso multidispositivo.
- Lanzamiento para iOS u otros mercados durante el MVP.

### 10.2 Fuera del producto

- Contabilidad empresarial, ERP, facturación, inventario o nómina.
- Preparación o presentación de impuestos y cumplimiento tributario.
- Auditoría financiera o contable.
- Ejecución autónoma de pagos, inversiones, créditos o transferencias.
- Asesoría financiera, contable, tributaria o legal profesional.
- Promesas de rentabilidad o decisiones financieras tomadas en nombre del usuario.

## 11. Principales user journeys

### 11.1 Primer uso

La persona comprende la propuesta de Nuvora y llega directamente al primer registro sin crear una cuenta. La experiencia inicial está en español y utiliza COP.

### 11.2 Registrar un gasto manual

La persona inicia un registro, introduce la información esencial, revisa o modifica la fecha efectiva inicialmente propuesta, selecciona o acepta una categoría y confirma. El movimiento aparece en su historial y en el resumen mensual correspondiente a su fecha efectiva.

### 11.3 Registrar un movimiento mediante voz

La persona describe el gasto o ingreso, revisa la transcripción y la interpretación estructurada, corrige cualquier dato necesario y confirma el registro. Si la interpretación falla, puede completar el flujo manualmente sin perder el contexto útil.

### 11.4 Corregir un movimiento o categoría

La persona localiza el movimiento, modifica la información incorrecta —incluida su fecha efectiva— y observa el resultado actualizado en los períodos y resúmenes correspondientes.

### 11.5 Registrar una transferencia propia

La persona identifica un movimiento entre fondos o cuentas propias como transferencia. Nuvora lo conserva como movimiento, pero no lo suma a los ingresos o gastos ni modifica el resultado neto del período.

### 11.6 Consultar el resumen del mes

La persona abre el dashboard en el mes actual y comprende cuánto ha gastado, en qué se está yendo el dinero y cuál es el resultado neto del período. También puede consultar meses anteriores que contengan movimientos. Cuando existe historial suficiente, puede comparar períodos e identificar categorías cuyo gasto está aumentando.

### 11.7 Crear un objetivo de ahorro — V1

La persona define una meta, establece el importe deseado y consulta su progreso. El flujo detallado se definirá fuera de este PRD y no pertenece al MVP.

## 12. Métricas del producto

Una métrica describe qué se observará. Un objetivo numérico fija el resultado esperado para esa métrica. Este PRD define las métricas, pero los objetivos se establecerán después de obtener una línea base o evidencia suficiente.

| Métrica | Qué permite evaluar | Objetivo numérico |
| --- | --- | --- |
| Activación | Proporción de nuevos usuarios que registran su primer movimiento y alcanzan una primera vista útil del resumen. | Por definir. |
| Finalización de registro | Proporción de intentos de registro que terminan en un movimiento guardado. | Por definir. |
| Tiempo de registro manual | Tiempo desde el inicio del flujo hasta la confirmación del movimiento. | Por definir. |
| Tiempo de registro por voz | Tiempo desde el inicio de la captura hasta la confirmación o corrección. | Por definir. |
| Adopción de voz | Proporción de usuarios activos que prueban y vuelven a utilizar el registro por voz. | Por definir. |
| Interpretación correcta | Proporción de propuestas de voz aceptadas sin cambios en sus datos esenciales. | Por definir. |
| Corrección de categoría | Proporción de categorías automáticas que el usuario modifica antes o después de guardar. | Por definir. |
| Integridad percibida | Percepción del usuario sobre cuán completo y representativo es su historial. | Por definir. |
| Recurrencia | Frecuencia con la que los usuarios activos registran o revisan información. | Por definir. |
| Retención | Proporción de usuarios que continúa utilizando Nuvora después de periodos definidos. | Por definir. |
| Utilidad percibida | Percepción de que Nuvora ayuda a entender mejor las finanzas y decidir con mayor seguridad. | Por definir. |
| Confianza | Percepción de exactitud, transparencia, privacidad y control. | Por definir. |

El MVP deberá instrumentar los eventos necesarios para distinguir, como mínimo:

- interpretaciones aceptadas sin cambios;
- interpretaciones corregidas;
- categorías sugeridas aceptadas;
- categorías sugeridas modificadas;
- registros manuales;
- registros iniciados mediante voz;
- registros abandonados antes de guardar.

La definición técnica de esta instrumentación se realizará posteriormente.

Las métricas de velocidad no deben optimizarse a costa de exactitud o confianza. La adopción de voz debe analizarse junto con finalización, correcciones y recurrencia.

## 13. Riesgos y supuestos

### 13.1 Riesgos

- **Precisión de Speech-to-Text:** acentos, ruido, velocidad del habla y vocabulario local pueden degradar la transcripción.
- **Calidad de interpretación:** el lenguaje cotidiano puede omitir información o admitir varias interpretaciones.
- **Costo de IA:** el procesamiento frecuente de voz y lenguaje puede afectar la sostenibilidad del producto.
- **Confianza del usuario:** errores financieros, automatizaciones opacas o correcciones difíciles pueden provocar abandono.
- **Privacidad:** voz y datos financieros son sensibles y requieren expectativas, consentimiento y controles claros.
- **Evolución hacia iOS:** las diferencias de permisos y capacidades pueden dificultar ofrecer en el futuro una experiencia equivalente a la validada inicialmente en Android.
- **Dependencia de intervención manual:** si la captura inteligente no ofrece una mejora suficiente, el producto conservará el problema que busca resolver.
- **Datos incompletos:** los resúmenes pierden utilidad cuando el usuario omite movimientos de forma habitual.
- **Complejidad futura:** añadir fuentes automáticas, bancos y monedas puede comprometer simplicidad y consistencia.
- **Alcance prematuro:** incorporar planificación o asistencia antes de validar la captura puede diluir el aprendizaje del MVP.

### 13.2 Supuestos a validar

- Los usuarios aceptarán revisar una propuesta si hacerlo requiere menos esfuerzo que completar el registro manualmente.
- La voz será útil en contextos cotidianos y no solamente una función de demostración.
- Un resumen mensual básico aportará valor suficiente para incentivar registros recurrentes.
- Empezar sin cuenta reducirá la fricción de activación sin comprometer la utilidad inicial del producto.
- Los catálogos definidos por Nuvora serán comprensibles y suficientes para la mayoría del segmento inicial en Colombia.
- La corrección explícita generará datos útiles para mejorar la automatización futura.
- El segmento inicial puede operar satisfactoriamente con COP como única moneda.

## 14. Dependencias de producto

Estas dependencias expresan capacidades necesarias, no proveedores ni soluciones técnicas.

- **Persistencia:** conservar de forma confiable movimientos, categorías, preferencias y correcciones.
- **Procesamiento de voz:** obtener una transcripción utilizable y mostrarla para revisión.
- **Interpretación de lenguaje:** convertir expresiones cotidianas en propuestas estructuradas, comunicando ambigüedad.
- **Motor financiero determinista:** calcular totales, resultado neto del período y agrupaciones sin delegarlos a la IA.
- **Catálogo de categorías:** ofrecer una base comprensible y consistente para registro y análisis.
- **Analítica de producto:** medir activación, fricción, calidad de interpretación, correcciones y recurrencia.
- **Privacidad y consentimiento:** informar y controlar el tratamiento de voz y datos financieros.
- **Continuidad local:** mantener la información disponible entre sesiones sin exigir una cuenta.
- **Cuenta y sincronización futuras:** evaluar identidad, backup, recuperación y continuidad entre dispositivos después del MVP.
- **Investigación con usuarios:** validar segmentos, lenguaje, confianza, valor de la voz y prioridades posteriores.

## 15. Preguntas abiertas

1. ¿Qué estrategia de sincronización y resolución de conflictos deberá utilizarse cuando se introduzcan backup, cuenta o sincronización?
2. ¿Qué políticas de retención, consentimiento y uso de datos se aplicarán a voz, transcripciones, movimientos y correcciones?
3. ¿Cuál será el modelo de negocio y qué capacidades, si alguna, formarán parte de una oferta de pago?
4. ¿Qué objetivos numéricos definirán éxito para activación, velocidad, calidad de interpretación, recurrencia y retención una vez exista una línea base?
5. ¿La necesidad de multimoneda es suficientemente frecuente y urgente para incluir una versión limitada en V1?
6. ¿Qué grado de confianza permitirá simplificar la confirmación de registros por voz sin sacrificar control ni exactitud?
7. ¿Qué evidencia mínima debe alcanzarse en captura y calidad de datos antes de incorporar presupuesto, objetivos, deudas o asistente?
