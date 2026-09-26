# Nuvora — Design System

## 1. Propósito

Este documento define la fuente de verdad conceptual para las decisiones visuales reutilizables del MVP de Nuvora. Traduce los principios de [VISION](../product/VISION.md), el alcance del [PRD](../product/PRD.md), los requisitos del [SRS](../product/SRS.md), el orden del [ROADMAP](../product/ROADMAP.md) y los recorridos de [USER_FLOWS](./USER_FLOWS.md) en reglas para una interfaz coherente. No diseña pantallas ni prescribe tecnología, navegación o archivos de implementación.

El MVP se dirige a teléfonos Android en Colombia, en español y con COP como única moneda. Este documento incorpora la dirección visual, la paleta light, la tipografía, los tamaños, los radios, el movimiento, la familia de iconos y los criterios de accesibilidad aprobados para el diseño. La verificación de conformidad de la interfaz se realizará durante la implementación y las pruebas.

## 2. Principios visuales y de interacción

- **Simple antes que decorativo.** Cada elemento debe ayudar a entender un dato o completar una acción. Evitar saturación, adornos, gamificación y una apariencia de software contable, hoja de cálculo o banca corporativa.
- **Información financiera primero.** Dar jerarquía a importe, tipo, fecha efectiva y resumen del período. La densidad nunca debe exigir conocimientos contables.
- **Acciones frecuentes evidentes.** Registrar, revisar, corregir y cancelar deben ser fáciles de encontrar y distinguir, con el menor número razonable de acciones (UF-001–007, RNF-UX-001–004).
- **Confianza y tranquilidad.** Comunicar qué se propuso, qué se confirmó, qué quedó guardado y qué falló. No aparentar precisión cuando faltan datos ni presentar tendencias sin historial suficiente.
- **Automatización visible y corregible.** Distinguir sugerencias de datos confirmados; señalar incertidumbre y ofrecer corrección. Voz o IA no sustituyen el registro manual ni guardan directamente (UF-010–014).
- **Consistencia y accesibilidad.** Reutilizar roles, escalas y patrones; expresar categorías, estados, errores y resultados mediante texto u otra señal además del color (RNF-ACC-001–005).
- **Cercanía sobria.** La dirección visual aprobada es serena, moderna, cercana, tecnológica, limpia y confiable, con azul petróleo o teal y neutros claros. Evitar rigidez corporativa, apariencia de banco tradicional, exceso de verde o rojo como base, saturación y gamificación. Usar lenguaje cotidiano apropiado para Colombia.

## 3. Sistema de tokens

Un **token base** nombra un valor de una escala reutilizable (`spacing.2`, `radius.md`). Un **token semántico** nombra su función (`color.text.primary`, `color.feedback.error`, `size.touch.minimum`). Un componente futuro debe consumir preferentemente el rol semántico; la relación con un valor base se define una sola vez y puede cambiar sin alterar su significado. Ninguna familia de tokens implica crear archivos o elegir una herramienta ahora.

| Familia conceptual | Tokens base o escala | Roles semánticos y uso |
| --- | --- | --- |
| `color` | Paleta light aprobada. | Marca, fondo, superficie, texto, borde, acción, feedback y finanzas; véase sección 4. |
| `typography` | Inter y escala aprobada de tamaño, peso y altura de línea. | Jerarquías de título, cuerpo, etiqueta e importes; véase sección 5. |
| `spacing` | Escala recomendada de 4 unidades. | Separación de contenido, controles y secciones; véase sección 6. |
| `radius` | Valores aprobados de `none` a `full`. | Forma consistente según función; véase sección 7. |
| `size` | Valores aprobados para controles, área táctil e iconos. | Dimensiones recurrentes; véanse secciones 6 y 14. |
| `elevation` / `shadows` | `none`, `low`, `medium`, `high`. | Separación de superficies cuando espacio, borde y contraste no basten. |
| `opacity` | Intensidades aún no fijadas. | Estados secundarios sin perder legibilidad; nunca única señal de deshabilitado o incertidumbre. |
| `motion` | `fast = 120 ms`, `normal = 200 ms`, `slow = 300 ms`. | Cambios de estado sin retrasar acciones ni ocultar información; respetar la reducción de movimiento. |

Los tokens semánticos deben conservar su significado aunque más adelante se añada otro tema. Los roles light reciben valores de la paleta aprobada; una eventual paleta Dark podrá reutilizar los mismos nombres semánticos.

## 4. Color

Los nombres de la tabla se entienden bajo `color.` (por ejemplo, `color.action.danger`). Los valores siguientes constituyen la **paleta light aprobada**; no se derivan nuevos colores de ella en este documento.

| Grupo | Token | Valor aprobado |
| --- | --- | --- |
| Brand | `brand.primary` | `#0F766E` |
| Brand | `brand.primaryPressed` | `#115E59` |
| Brand | `brand.soft` | `#CCFBF1` |
| Brand | `brand.secondary` | `#0E7490` |
| Background | `background.primary` | `#F8FAFC` |
| Background | `background.secondary` | `#F1F5F9` |
| Background | `background.elevated` | `#FFFFFF` |
| Surface | `surface.default` | `#FFFFFF` |
| Surface | `surface.subtle` | `#F1F5F9` |
| Surface | `surface.interactive` | `#FFFFFF` |
| Text | `text.primary` | `#0F172A` |
| Text | `text.secondary` | `#475569` |
| Text | `text.muted` | `#64748B` |
| Text | `text.inverse` | `#FFFFFF` |
| Text | `text.disabled` | `#64748B` |
| Border | `border.default` | `#CBD5E1` |
| Border | `border.subtle` | `#E2E8F0` |
| Border | `border.focus` | `#0F766E` |
| Border | `border.error` | `#B91C1C` |
| Actions | `action.primary` | `#0F766E` |
| Actions | `action.primaryPressed` | `#115E59` |
| Actions | `action.secondary` | `#0E7490` |
| Actions | `action.secondaryPressed` | `#155E75` |
| Actions | `action.danger` | `#B91C1C` |
| Actions | `action.dangerPressed` | `#991B1B` |
| Actions | `action.disabled` | `#CBD5E1` |
| Feedback | `feedback.success` | `#15803D` |
| Feedback | `feedback.warning` | `#A16207` |
| Feedback | `feedback.error` | `#B91C1C` |
| Feedback | `feedback.info` | `#0369A1` |
| Finance | `finance.income` | `#15803D` |
| Finance | `finance.expense` | `#B45309` |
| Finance | `finance.transfer` | `#2563EB` |
| Finance | `finance.neutral` | `#64748B` |
| Finance | `finance.netPositive` | `#0F766E` |
| Finance | `finance.netNegative` | `#B45309` |
| Finance | `finance.netNeutral` | `#475569` |

Todos los roles light de esta sección tienen un valor aprobado. Compartir hexadecimal no fusiona significados; cada combinación real de texto, icono, borde y superficie debe verificarse según los criterios de accesibilidad de la sección 14.

`action.danger` expresa una acción destructiva; `feedback.error` expresa el resultado de un error. Comparten `#B91C1C`, pero su significado y consumo son distintos. `finance.netPositive`, `finance.netNegative` y `finance.netNeutral` representan el resultado neto; no se reutilizan conceptualmente `finance.income` ni `finance.expense` para ese fin. Ingreso, gasto, transferencia y resultado neto positivo, negativo o cero necesitan texto, signo o contexto además del color.

### Dirección visual aprobada

La identidad visual se basa principalmente en azul petróleo o teal y neutros claros. La paleta debe comunicar serenidad, cercanía, tecnología y confianza sin parecer un banco tradicional ni una aplicación corporativa rígida. Evitar exceso de verde o rojo como base, saturación y gamificación.

Dark Mode está **fuera del MVP**. Los roles semánticos permitirán considerar otro tema en el futuro sin rediseñar su significado.

## 5. Tipografía

La familia principal aprobada es **Inter**. Los únicos pesos del sistema base son **400 Regular, 500 Medium, 600 SemiBold y 700 Bold**; no se incorporan 300, 800 ni 900. Los valores de tamaño y altura de línea son unidades de diseño y no deben impedir el escalado de texto configurado por la persona.

| Rol | Tamaño | Altura de línea | Peso | Propósito y uso recomendado |
| --- | ---: | ---: | ---: | --- |
| `display` | 32 | 40 | 700 | Cifra o información realmente prioritaria; uso excepcional. |
| `headingLarge` | 28 | 36 | 700 | Encabezado principal de un contexto. |
| `headingMedium` | 24 | 32 | 700 | Sección importante o resumen. |
| `headingSmall` | 20 | 28 | 600 | Grupo de contenido o encabezado local. |
| `bodyLarge` | 18 | 28 | 400 | Explicación de lectura prioritaria. |
| `bodyMedium` | 16 | 24 | 400 | Datos y explicaciones habituales. |
| `bodySmall` | 14 | 20 | 400 | Información secundaria aún legible. |
| `labelLarge` | 16 | 20 | 600 | Acción o dato importante. |
| `labelMedium` | 14 | 20 | 600 | Etiqueta habitual de acción o campo. |
| `caption` | 12 | 16 | 400 | Aclaración secundaria; nunca única representación de información financiera o crítica. |

Los importes financieros usan Inter: los principales pueden usar peso 700 y los de listas peso 600. Usar cifras tabulares cuando implementación y plataforma lo permitan. Reservar `display` para lo prioritario y mantener legibles textos e importes al escalar hasta 200 % sin pérdida de contenido o función esencial.

## 6. Espaciado y layout

La escala base **recomendada** usa incrementos de 4 unidades: `spacing.0 = 0`, `.1 = 4`, `.2 = 8`, `.3 = 12`, `.4 = 16`, `.5 = 20`, `.6 = 24`, `.8 = 32`, `.10 = 40` y `.12 = 48`. Es una convención de diseño para separar contenido, acciones y secciones; no establece dimensiones rígidas de pantalla.

Los roles semánticos futuros, como espacio entre elementos relacionados, separación entre grupos y margen del contenido, deberán referirse a esta escala. Evitar valores locales arbitrarios; una excepción necesita una justificación documentada y repetible. Mantener juntos etiqueta, dato, ayuda y error correspondiente; separar suficientemente grupos diferentes sin llenar el espacio con decoraciones.

Los tamaños base aprobados, en dp, son `size.touch.minimum = 48`; `size.icon.sm = 16`, `size.icon.md = 20`, `size.icon.lg = 24`; `size.control.sm = 40`, `size.control.md = 48`, `size.control.lg = 56`. Un control visual de menos de 48 dp solo puede usarse si su área táctil efectiva mide al menos **48 × 48 dp**. No se define tamaño de avatar, ya que los flujos del MVP no requieren uno. El layout debe admitir textos largos, importes y escalado sin recortes ni solapamientos.

## 7. Radios y formas

La escala aprobada es `radius.none = 0`, `radius.sm = 8`, `radius.md = 12`, `radius.lg = 16`, `radius.xl = 24` y `radius.full = 9999`. `sm` sirve para elementos compactos; `md`, para inputs y botones; `lg`, para cards y superficies agrupadas; `xl`, para superficies destacadas puntuales; `full`, para chips, badges y elementos circulares.

No introducir radios arbitrarios por feature ni formas que reduzcan espacio útil, legibilidad o área táctil.

## 8. Elevación y profundidad

Usar una escala pequeña: `elevation.none` para contenido común, `low` para una superficie que necesite separación leve, `medium` para contenido temporal destacado y `high` solo cuando la jerarquía lo exija. Estos niveles son roles relativos; sombras, desplazamientos y opacidades concretos no están decididos.

Priorizar **espacio → contraste de superficie → borde → elevación**. Usar sombras solo cuando sean necesarias para comunicar jerarquía; no deben ser la única señal de foco, selección o estado. Evitar efectos decorativos que hagan que Nuvora parezca una interfaz cargada.

## 9. Iconografía

La familia base aprobada es **Lucide Icons**. Usar `size.icon.sm = 16`, `.md = 20` y `.lg = 24`, con grosor y estilo visual consistentes; no mezclar familias dentro del producto. Los iconos de acciones ambiguas o críticas deben ir acompañados de texto visible o nombre accesible. Eliminar no puede depender exclusivamente de un icono. Un icono de categoría es apoyo visual: el nombre de la categoría sigue siendo la referencia principal. No usar iconografía como única señal de error, ingreso, gasto, transferencia o incertidumbre.

La elección exacta del icono para cada categoría o feature se hará durante el diseño de interfaz. Todo icono accionable requiere nombre accesible, estado y área táctil efectiva de al menos 48 × 48 dp. Esta decisión no crea imports, dependencias ni código.

## 10. Estados interactivos

Los componentes interactivos comparten un vocabulario: `default`, `pressed`, `focused`, `disabled`, `loading`, `error`, `success` y, cuando aplique, `selected`. Cada estado debe distinguirse por una combinación apropiada de etiqueta, indicador, borde, contenido y feedback; el color o la opacidad por sí solos no bastan.

| Estado | Regla observable |
| --- | --- |
| `default` | La acción y su propósito son comprensibles. |
| `pressed` | La interacción recibe respuesta perceptible sin simular que ya terminó. |
| `focused` | El elemento activo y su nombre se reconocen de forma accesible. |
| `disabled` | Se informa su indisponibilidad sin ocultar una ruta alternativa necesaria. |
| `loading` | Se comunica una operación pendiente; no se presenta éxito anticipado. |
| `error` | Se identifica el problema y la forma de corregirlo o salir. |
| `success` | Solo se comunica cuando la acción realmente terminó, incluido el guardado aplicable. |
| `selected` | La elección vigente puede identificarse sin depender solo del color. |

Las acciones repetidas no deben sugerir que se crearon varios movimientos. Una operación de voz o IA pendiente debe permitir salida segura y no bloquear el registro manual (RNF-PERF-001–002, RNF-REL-004–005).

La escala de movimiento aprobada es `motion.fast = 120 ms` para `pressed`, selección y cambios pequeños de estado; `motion.normal = 200 ms` para feedback y aparición o desaparición de contenido; `motion.slow = 300 ms` para transiciones importantes y poco frecuentes. Ninguna animación retrasa guardado, confirmación o navegación, ni es la única señal de éxito, error o incertidumbre. Respetar la preferencia del sistema para reducción de movimiento y reducir o eliminar animaciones no esenciales cuando corresponda. No se fijan animaciones complejas ni curvas de easing.

## 11. Componentes base

Los nombres siguientes son **etiquetas conceptuales**, no nombres de archivos, componentes React ni APIs. Las variantes son las mínimas útiles para los flujos aprobados; toda implementación futura debe reutilizar tokens y accesibilidad transversal de las secciones 3, 10 y 14.

| Primitiva | Propósito y variantes mínimas | Estados aplicables | Tokens principales | Accesibilidad |
| --- | --- | --- | --- | --- |
| `Button` | Ejecutar acción `primary`, `secondary` o `danger`; contemplar `disabled` y `loading`. | `default`, `pressed`, `focused`, `disabled`, `loading`, `success`. | `color.action.primary`, `.primaryPressed`, `.secondary`, `.secondaryPressed`, `.danger`, `.dangerPressed`; `typography.labelLarge`, `spacing.*`, `radius.md`, `size.control.*`, `size.touch.minimum`. La variante `danger` consume `color.action.danger`, no `color.feedback.error`. | Nombre y resultado claros; área táctil efectiva mínima de 48 × 48 dp. |
| `IconButton` | Acción compacta con icono cuando hay contexto suficiente. | `default`, `pressed`, `focused`, `disabled`, `loading`. | `color.action.*`, `size.icon.*`, `size.touch.minimum`, `radius.*`. | Nombre accesible; texto visible si el significado puede ser ambiguo; área táctil efectiva mínima. |
| `TextInput` | Introducir concepto u otro texto editable del flujo. | `default`, `focused`, `disabled`, `error`. | `color.text.*`, `color.border.*`, `typography.bodyMedium`, `spacing.*`, `radius.*`. | Etiqueta persistente; error asociado al campo. |
| `MoneyInput` | Introducir un monto en COP y mostrar su validación. | `default`, `focused`, `disabled`, `error`. | `typography.bodyLarge`, `color.text.*`, `color.border.*`, `spacing.*`, `size.control.*`, `size.touch.minimum`. | Leer importe y moneda; comunicar vacío, cero o valor inválido con texto. |
| `DateInput` / `DateSelector` | Consultar o cambiar fecha efectiva; entrada directa o selección, sin fijar mecanismo. | `default`, `focused`, `disabled`, `error`, `selected`. | `typography.bodyMedium`, `color.border.*`, `spacing.*`, `size.control.*`, `size.touch.minimum`. | Identificar fecha efectiva, valor vigente y error. |
| `Card` | Agrupar información relacionada sin imponer una pantalla. | `default`; `selected` o `focused` solo si es accionable. | `color.surface.*`, `color.border.*`, `spacing.*`, `radius.*`, `elevation.*`. | Orden de lectura claro; no aparentar acción si no la tiene. |
| `TransactionListItem` | Identificar y abrir un movimiento del historial. | `default`, `pressed`, `focused`, `selected` cuando aplique. | `typography.*`, `color.finance.*`, `spacing.*`, `size.touch.minimum`. | Exponer tipo, monto, fecha y clasificación o concepto disponible. |
| `CategorySelector` | Elegir categoría válida de gasto o ingreso. | `default`, `focused`, `disabled`, `error`, `selected`. | `color.surface.*`, `color.border.*`, `typography.labelMedium`, `spacing.*`. | Nombrar catálogo y selección; señalar categoría obligatoria. |
| `CategoryChip` | Mostrar una categoría o sugerencia breve, y permitir selección solo si aplica. | `default`, `focused`, `selected`, `disabled`, `error` si es interactivo. | `color.surface.*`, `color.text.*`, `radius.full`, `spacing.*`. | Nombre textual; distinguir sugerida de confirmada. |
| `AmountDisplay` | Presentar ingreso, gasto, transferencia o resultado neto positivo, negativo o cero con jerarquía adecuada. | Tipo vigente, propuesta cuando aplique, resultado positivo, negativo o cero. | `typography.*`, `color.finance.income`, `.expense`, `.transfer`, `.netPositive`, `.netNegative`, `.netNeutral`, `color.text.*`, `spacing.*`. | Leer COP, tipo, signo y contexto; el color nunca es la única señal. |
| `EmptyState` | Explicar historial o período sin movimientos. | `empty`. | `typography.bodyMedium`, `color.text.*`, `spacing.*`. | Indicar ausencia válida, sin tratarla como error. |
| `ErrorState` | Explicar fallo y recuperación del flujo. | `error`; `loading` si se reintenta. | `color.feedback.error`, `typography.*`, `spacing.*`. | Describir qué falló, estado de datos y alternativa. |
| `LoadingState` | Comunicar espera de una operación. | `loading` o `processing`. | `color.feedback.info`, `typography.*`, `motion.*`. | Estado anunciable y salida segura para voz/IA. |
| `FeedbackMessage` | Confirmar resultado, advertencia o información contextual. | `success`, `error`, `warning`, `info`. | `color.feedback.*`, `typography.bodyMedium`, `spacing.*`. | Texto específico; no comunicar resultado solo con color o icono. |
| `Modal` / `ConfirmationSurface` | Dar contexto a una confirmación deliberada, como eliminar; formato sin decidir. | `default`, `focused`, `loading`, `error`, `success`. | `color.background.elevated`, `typography.*`, `spacing.*`, `radius.*`, `elevation.*`. | Propósito, opciones y foco comprensibles; cancelación disponible. |

## 12. Representación de información financiera

- **Moneda y formato:** expresar siempre COP. Como convención de presentación propuesta, usar `COP 30.000` cuando el importe aparezca aislado o pueda haber ambigüedad; `$ 30.000` puede usarse cuando el contexto COP sea inequívoco. Usar punto para agrupar miles y coma si se muestran decimales. No definir aquí precisión, redondeo ni reglas de cálculo; esos aspectos no deben deducirse de la apariencia.
- **Tipo de movimiento:** nombrar explícitamente **Ingreso**, **Gasto** o **Transferencia propia** en contextos donde pueda confundirse. El monto del registro sigue siendo mayor que cero; el signo de una vista no cambia su tipo. Las transferencias figuran en historial pero no en ingresos, gastos ni resultado neto.
- **Resultado neto:** etiquetar **Resultado neto del período** y explicar, cuando sea necesario, que equivale a **ingresos contabilizables menos gastos contabilizables** del mes seleccionado. Un resultado positivo puede mostrar `+`, uno negativo `−` y cero un valor neutro; acompañar el signo de una etiqueta o contexto. Nunca llamarlo saldo bancario disponible.
- **Jerarquía y alineación:** destacar el total pertinente al contexto y mantener alineación consistente de importes comparables; dar espacio al símbolo, signo, separadores y cifras grandes, sin truncar el dato esencial. Fechas y categorías deben ayudar a entender a qué movimiento o período pertenece cada valor.
- **Distribución y comparación:** el total de categorías de gasto debe concordar con los gastos contabilizables del período. Mostrar comparaciones solo con historial suficiente conforme a TBD-004; no inferir tendencia desde un estado vacío.
- **Señales redundantes:** `finance.income`, `finance.expense`, `finance.transfer`, `finance.neutral`, `finance.netPositive`, `finance.netNegative` y `finance.netNeutral` son ayudas visuales. Nombre, signo o etiqueta transmiten la misma distinción aun sin color. El neto tiene roles propios y no adopta semánticamente el rol de ingreso o gasto (RN-005–007, RF-DASH-001–007, RNF-ACC-005).

## 13. Estados de interfaz

| Estado | Patrón conceptual y salida |
| --- | --- |
| `empty` | Explicar que no hay movimientos o datos para el período; mostrar totales equivalentes a cero cuando corresponda y permitir iniciar registro. |
| `loading` | Indicar que una operación está en curso, sin presentar información provisional como definitiva. |
| `error` | Decir qué no se completó, conservar el último estado válido y ofrecer corrección, reintento o salida aplicable. |
| `offline` | Mantener apertura, historial disponible, registro manual de gastos, ingresos y transferencias propias, gestión y resúmenes locales; informar si voz o IA requieren conexión. |
| `disabled` | Comunicar por qué una acción no está disponible cuando el contexto lo requiera y conservar alternativas válidas. |
| `processing` | Separar procesamiento de voz o IA de captura activa y de guardado; permitir abandonar de forma segura. |
| `success` | Confirmar únicamente una acción terminada; en registro, edición o eliminación, después de persistir. |
| `uncertainty` / `needs review` | Identificar dato sugerido, ausente o incierto y pedir revisión explícita cuando corresponda antes de guardar. |

En voz e IA, la secuencia conceptual es **capturando → procesando → transcripción → interpretación → requiere revisión → confirmado**. Cada fase debe tener nombre o explicación perceptible. «Confirmado» solo corresponde a datos validados y guardados tras la acción del usuario; cancelar o fallar no produce un movimiento. Las duraciones generales de `motion` no prescriben una animación concreta para estas fases.

## 14. Accesibilidad

Estas reglas aplican desde los primeros flujos y se alinean con RNF-ACC-001–005. **TBD-008 queda resuelto a nivel de diseño** por la decisión humana de usar **WCAG 2.2 nivel AA** como referencia del MVP, con los criterios aprobados a continuación. Documentarlos no demuestra conformidad final: esta deberá verificarse durante la implementación y en `TESTING.md`.

- Verificar contraste mínimo de **4.5:1 para texto normal** y **3:1 para texto grande** sobre cada superficie real. Revisar también indicadores, bordes, foco, errores y feedback conforme a la referencia WCAG 2.2 AA; no usar solo color ni opacidad baja como explicación.
- Soportar escalado de texto de hasta **200 %** sin perder contenido ni funcionalidad esencial. Texto e importes financieros esenciales no deben truncarse ni solaparse; `caption` no puede ser su única representación.
- Garantizar área táctil efectiva mínima de **48 × 48 dp**. Un control visual de 40 dp o un icono de 16–24 dp necesita área operable ampliada. Ninguna acción crítica depende exclusivamente de un gesto.
- Exponer nombre, rol, valor y/o estado accesibles cuando corresponda, con orden de lectura comprensible. Asociar etiquetas y errores con el dato al que se refieren.
- Hacer perceptible el foco y permitir entender selección, confirmación, cancelación y eliminación. Tras una acción, comunicar su resultado real sin depender de una señal visual fugaz.
- Comunicar inicio, fin, procesamiento y error de voz con una señal accesible además de la visual; la alternativa manual debe seguir identificable.
- Usar texto y señales redundantes para tipos financieros, categorías, signos, errores, éxito, incertidumbre y estados vacíos. Lucide Icons no sustituye etiquetas accesibles.

## 15. Responsive y tamaños de pantalla

Diseñar para teléfonos Android del rango que se defina en TBD-007. Adaptar jerarquía, espaciado y agrupación al espacio disponible sin dimensiones rígidas innecesarias. Respetar áreas seguras; permitir desplazamiento del contenido cuando haga falta; mantener visible o recuperable la acción relevante al aparecer el teclado, sin tapar etiqueta, error o importe.

El escalado de texto y los importes largos deben poder reorganizar el contenido sin perder datos ni impedir confirmación o corrección. El documento no establece diseño para tablet ni incorpora iOS al alcance del MVP.

## 16. Reglas para implementación futura

- Centralizar los valores base y el mapeo de roles semánticos cuando se implemente el theme. No escribir hexadecimales ni otros colores directos dentro de componentes.
- Reutilizar `spacing`, `radius`, `size`, tipografía y elevación. Una excepción de feature solo es válida si responde a una necesidad real, se documenta y no redefine un rol global.
- Construir las primitivas compartidas de la sección 11 para consumir tokens semánticos y estados consistentes. No repetir estilos tipográficos ni redefinir `color.feedback.error`, `color.action.primary` o roles financieros por feature.
- Mantener separadas las señales de propuesta, validación, confirmación y guardado. Un estilo de éxito no debe anticipar persistencia; voz e IA no deben ocultar incertidumbre ni bloquear la ruta manual.
- Verificar combinaciones reales de superficie, texto, borde, foco y feedback en Android frente a WCAG 2.2 AA y las reglas aprobadas en la sección 14. Documentar la conformidad real en las pruebas; el Design System no la certifica.

Como **organización conceptual futura**, las familias corresponderían a `theme/colors`, `theme/typography`, `theme/spacing`, `theme/radius`, `theme/sizes`, `theme/elevation` y `theme/theme`. Esta lista comunica responsabilidades de tokens; no crea directorios, archivos ni una arquitectura aprobada. `opacity` y los tokens `motion` aprobados pertenecerán al mismo sistema.

## 17. Decisiones pendientes

| Decisión que requiere aprobación humana | Estado y efecto |
| --- | --- |
| Dark Mode | No es requisito del MVP; decidir si se ofrece y en qué etapa, sin romper los roles semánticos. |
| Valores concretos de sombra y elevación | Los cuatro roles están aprobados; definir efectos específicos solo tras validar su necesidad y comportamiento técnico. |
| Curvas de easing | Definirlas únicamente si una transición las requiere; las duraciones base ya están aprobadas. |
| Iconos específicos de categorías y features | Lucide Icons es la familia aprobada; la selección concreta corresponde al diseño de cada interacción. |
| Ajustes tras pruebas reales de accesibilidad | Corregir combinaciones o comportamientos que fallen en la implementación; esto no reabre TBD-008 como decisión de diseño. |

La dirección visual, todos los roles de la paleta light, Inter, los tamaños y radios base, `motion`, Lucide Icons y los criterios antes identificados como TBD-008 ya están aprobados y reflejados en el SRS. La conformidad real sigue sujeta a implementación y pruebas. Las decisiones visuales tampoco resuelven TBD-003 sobre incertidumbre, TBD-004 sobre historial suficiente, TBD-005 sobre conservación de datos ni TBD-014 sobre voz e IA.
