# Nuvora

Nuvora es una aplicación móvil de finanzas personales diseñada para reducir la fricción de registrar y comprender movimientos cotidianos. Su MVP está dirigido inicialmente a Android, en español para Colombia y con COP como única moneda. Se podrá comenzar sin crear una cuenta.

## Estado del proyecto

**Estado: Foundation / preparación para implementar.** La línea base de producto, UX, arquitectura, datos, API, entornos, seguridad y pruebas está documentada. El repositorio aún no contiene una aplicación ejecutable ni un backend implementado; las capacidades descritas abajo son el **alcance definido del MVP**, no funciones ya entregadas.

El siguiente paso es el [Hito 1 — Walking skeleton](docs/product/ROADMAP.md), que establece la base Android ejecutable. La definición del rango Android compatible (`TBD-007`) sigue siendo una dependencia para aceptar ese hito.

## Alcance definido del MVP

- **Núcleo financiero:** registro manual de gastos, ingresos y transferencias propias; categorías aprobadas, historial, consulta, edición, eliminación y dashboard mensual.
- **Local-first:** SQLite privada en el dispositivo como única fuente de verdad financiera. El registro y la gestión manual, el historial y el dashboard deben funcionar sin Internet y sin esperar al backend. No hay repositorio financiero central, sincronización ni backup cloud propio en el MVP; los datos quedan inicialmente ligados al dispositivo.
- **Voz e IA:** captura iniciada explícitamente, transcripción y propuesta de **un solo movimiento** por interacción. La persona revisa y puede corregir la propuesta antes de confirmar. Un fallo, una cancelación o una propuesta no confirmada no crea movimientos. Si falla una capacidad remota, el registro manual sigue disponible.

**La IA interpreta y explica; el motor financiero calcula y ejecuta.** El diseño exige validación por Domain y confirmación de la persona antes de guardar un movimiento en SQLite. La IA no tiene autoridad para escribir movimientos ni calcular totales.

Los tipos financieros son `expense`, `income` y `own_transfer`, siempre en COP. `effective_date` determina el período; las transferencias propias no cuentan como ingresos ni gastos ni alteran el resultado neto, que es ingresos contabilizables menos gastos contabilizables. Las reglas completas están en [SRS](docs/product/SRS.md) y [DATABASE](docs/architecture/DATABASE.md).

## Stack y arquitectura

| Componente | Tecnologías aprobadas | Responsabilidad |
| --- | --- | --- |
| Mobile | React Native, Expo, TypeScript, Expo Router, Expo SQLite, Expo Development Builds y Continuous Native Generation (CNG) | Interfaz, reglas y persistencia financiera local. |
| Backend especializado | Python, FastAPI y Pydantic | Voz, IA, secretos e integración con proveedores cuando una capacidad remota lo requiera. No almacena el historial financiero. |

La arquitectura mobile define **Presentation → Application → Domain**: la interfaz coordina con casos de uso, y Domain contiene entidades y reglas financieras puras. Infrastructure implementará las fronteras técnicas, incluida SQLite y, cuando corresponda, HTTP. Presentation no debe escribir directamente en SQLite; Domain no debe depender de la UI ni de proveedores.

El flujo de voz definido prevé que el backend entregue una propuesta estructurada; mobile la valide, Domain aplique las reglas y la persona la revise y confirme antes de cualquier escritura local. Véanse [ARCHITECTURE](docs/architecture/ARCHITECTURE.md), [API](docs/architecture/API.md) y [SECURITY](docs/engineering/SECURITY.md).

## Estructura del repositorio

```text
nuvora/
├── apps/
│   ├── api/                 # contenedor vacío; backend futuro
│   └── mobile/              # contenedor vacío; app futura
├── docs/
│   ├── ai/
│   ├── architecture/
│   ├── engineering/
│   ├── product/
│   └── ux/
├── infra/                   # contenedor vacío
├── scripts/                 # contenedor vacío
├── .gitignore
├── AGENTS.md
└── README.md
```

## Documentación

| Área | Documento | Consulta principal |
| --- | --- | --- |
| Producto | [VISION](docs/product/VISION.md) | Dirección y principios del producto. |
| Producto | [PRD](docs/product/PRD.md) | Objetivos y alcance. |
| Producto | [SRS](docs/product/SRS.md) | Requisitos verificables y decisiones TBD. |
| Producto | [ROADMAP](docs/product/ROADMAP.md) | Secuencia y criterios de los hitos. |
| UX | [USER_FLOWS](docs/ux/USER_FLOWS.md) | Recorridos y estados visibles. |
| UX | [DESIGN_SYSTEM](docs/ux/DESIGN_SYSTEM.md) | Diseño y accesibilidad. |
| Arquitectura | [ARCHITECTURE](docs/architecture/ARCHITECTURE.md) | Responsabilidades y fronteras técnicas. |
| Datos | [DATABASE](docs/architecture/DATABASE.md) | SQLite, dinero, fechas y migraciones. |
| API | [API](docs/architecture/API.md) | Contrato remoto y errores. |
| Ingeniería | [ENVIRONMENTS](docs/engineering/ENVIRONMENTS.md) | Configuración y separación de entornos. |
| Ingeniería | [SECURITY](docs/engineering/SECURITY.md) | Seguridad, privacidad y secretos. |
| Ingeniería | [TESTING](docs/engineering/TESTING.md) | Estrategia y evidencia de pruebas. |
| Agentes | [AGENTS](AGENTS.md) | Reglas de trabajo para agentes. |

Este README es una guía de entrada: ante una diferencia, prevalece el documento especializado aprobado. [AI_ARCHITECTURE](docs/ai/AI_ARCHITECTURE.md), [AI_EVALUATION](docs/ai/AI_EVALUATION.md) y [RELEASE](docs/engineering/RELEASE.md) existen, pero aún no son fuentes autoritativas para introducir decisiones nuevas según `AGENTS.md`.

## Cómo empezar a desarrollar

La implementación comienza en el Hito 1. Aún no hay manifests ni scripts de desarrollo en `apps/mobile` o `apps/api`, por lo que este README no prescribe comandos. Cuando exista una base ejecutable, consulta los manifests y scripts reales de cada componente antes de instalar, iniciar o probar.

Al trabajar en una capacidad, identifica su hito en [ROADMAP](docs/product/ROADMAP.md) y sus requisitos en [SRS](docs/product/SRS.md). Algunas decisiones siguen explícitamente abiertas allí; no deben resolverse por suposición. `TBD-008` ya está resuelto a nivel de diseño con WCAG 2.2 AA, aunque la conformidad de la app deberá verificarse.

## Pruebas y privacidad

La [estrategia de pruebas](docs/engineering/TESTING.md) contempla pruebas unitarias de Domain, casos de uso de Application, integración con SQLite y API, comportamiento mobile y recorridos críticos E2E en Android. Los proveedores reales no forman parte de la suite determinista cotidiana; no se impone un porcentaje de cobertura en lugar de verificar reglas críticas. Todavía no hay pruebas ejecutables en este repositorio.

La captura de audio requerirá una acción explícita. El backend recibirá solo los datos necesarios para la solicitud de voz; el historial financiero no se enviará automáticamente. Los secretos permanecerán en el backend y la persona revisará antes de guardar. Las condiciones definitivas de tratamiento externo y conservación de datos siguen sujetas a los TBD correspondientes en [SRS](docs/product/SRS.md) y a [SECURITY](docs/engineering/SECURITY.md).

## Camino del MVP

Foundation → Walking skeleton → Núcleo manual → Historial y dashboard → Offline y robustez → Voz → IA → Categorización y medición → Hardening → Validación con usuarios. El orden y las condiciones de avance están en [ROADMAP](docs/product/ROADMAP.md); no implica fechas de entrega.

Fuera del MVP quedan iOS, multimoneda, sincronización, backup cloud propio, uso multidispositivo, cuenta obligatoria, presupuestos, metas, deudas avanzadas, captura automática por notificaciones y un asistente financiero completo.

## Desarrollo asistido por agentes

Antes de modificar código, lee [AGENTS.md](AGENTS.md). Define las fuentes de autoridad, fronteras arquitectónicas, tratamiento de TBD, pruebas, seguridad y límites de cada cambio.
