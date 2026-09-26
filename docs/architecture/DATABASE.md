# Nuvora — Persistencia local del MVP

## 1. Propósito y límites

Este documento concreta la persistencia financiera local del MVP conforme a `VISION.md`, `PRD.md`, `SRS.md`, `ROADMAP.md`, `USER_FLOWS.md`, `DESIGN_SYSTEM.md` y, como autoridad arquitectónica, `ARCHITECTURE.md`. El MVP comienza sin cuenta, en Android, para Colombia y únicamente en COP.

**SQLite en el dispositivo es la única fuente de verdad financiera.** Crear, consultar, editar y eliminar movimientos, mostrar el historial y calcular el dashboard deben funcionar sin conexión. El backend atiende capacidades puntuales de voz e IA; no recibe una réplica automática del historial, no participa en el CRUD local y no calcula el dashboard como autoridad. SQLite no es una caché del backend.

La versión inicial tiene una sola tabla financiera, `movements`. No se crean tablas de categorías, usuarios, cuentas financieras, propuestas de IA, telemetría, resúmenes, backup o sincronización. La base conserva únicamente movimientos finales, válidos y confirmados por la persona.

## 2. Esquema inicial

El siguiente SQL define el esquema **conceptual de la versión 1**. La migración real deberá expresar estas mismas restricciones mediante Expo SQLite.

```sql
CREATE TABLE movements (
    id TEXT PRIMARY KEY NOT NULL
        CHECK (length(trim(id)) > 0),
    type TEXT NOT NULL
        CHECK (type IN ('expense', 'income', 'own_transfer')),
    amount_minor INTEGER NOT NULL
        CHECK (
            typeof(amount_minor) = 'integer'
            AND amount_minor > 0
            AND amount_minor <= 9007199254740991
        ),
    currency TEXT NOT NULL
        CHECK (currency = 'COP'),
    effective_date TEXT NOT NULL
        CHECK (
            length(effective_date) = 10
            AND effective_date GLOB
                '[0-9][0-9][0-9][0-9]-[0-9][0-9]-[0-9][0-9]'
        ),
    concept TEXT,
    category_id TEXT
        CHECK (category_id IS NULL OR length(trim(category_id)) > 0),
    subcategory_id TEXT
        CHECK (subcategory_id IS NULL OR length(trim(subcategory_id)) > 0),
    created_at TEXT NOT NULL,
    updated_at TEXT NOT NULL,
    CHECK (
        (type = 'expense' AND category_id IS NOT NULL)
        OR (type = 'income' AND category_id IS NOT NULL
            AND subcategory_id IS NULL)
        OR (type = 'own_transfer' AND category_id IS NULL
            AND subcategory_id IS NULL)
    )
);

CREATE INDEX idx_movements_effective_date
    ON movements (effective_date);
```

| Campo | Significado y regla de escritura |
| --- | --- |
| `id` | Identidad opaca, única y estable, generada por la aplicación antes del guardado. No se usa `rowid` ni autoincremento como identidad de dominio; no se prescribe una versión de UUID. Un reintento de la misma intención conserva el mismo ID. |
| `type` | Valor persistido estable: `expense`, `income` u `own_transfer`. Los nombres traducidos pertenecen a Presentation. |
| `amount_minor` | Monto positivo y exacto en unidades menores de COP, limitado al rango entero seguro de JavaScript para el intercambio con Expo SQLite. El signo contable se deriva de `type`; nunca se almacena un gasto negativo. |
| `currency` | Siempre `COP`. Se conserva como parte de `Money`, sin habilitar multimoneda ni conversiones. |
| `effective_date` | Día calendario financiero en formato canónico `YYYY-MM-DD`. Determina el período de historial y dashboard. |
| `concept` | Descripción, concepto o comercio opcional; `NULL` cuando se omite. No contiene JSON genérico. |
| `category_id` | Identificador estable del catálogo fijo de Domain. Obligatorio para gastos e ingresos; `NULL` para transferencias propias. |
| `subcategory_id` | Identificador estable de subcategoría de gasto, cuando se elige una; `NULL` en ingresos y transferencias propias. |
| `created_at` | Instante técnico de creación, fijado una vez por la aplicación. |
| `updated_at` | Instante técnico de la última escritura del movimiento; cambia al editarlo. |

La aplicación genera `created_at` y `updated_at` en UTC con una única representación ISO 8601 de precisión de milisegundos, `YYYY-MM-DDTHH:mm:ss.sssZ`. En la creación ambos reciben el mismo valor. Una edición conserva `created_at` y actualiza `updated_at`; `effective_date` solo cambia si la persona modifica expresamente la fecha financiera. Los timestamps técnicos no deciden el período ni sustituyen la fecha efectiva.

Los `CHECK` de SQLite impiden estados estructuralmente inválidos. El patrón de `effective_date` comprueba la forma, pero no que exista el día calendario: Domain valida, por ejemplo, años bisiestos y días de cada mes antes de guardar. Domain también comprueba que la categoría pertenece al catálogo de gasto o ingreso correcto y que una subcategoría pertenece a la categoría de gasto elegida. No se replica el catálogo entero mediante un `CHECK` extenso.

## 3. Dinero exacto

`amount_minor` utiliza la unidad menor de COP como representación exacta de persistencia: **1 COP = 100 unidades menores**. La conversión entre la representación recibida por Application/Domain y `amount_minor` debe producir un entero exacto sin utilizar aritmética binaria de punto flotante. `DATABASE.md` no determina si Presentation permite o muestra fracciones de COP; esa decisión pertenece a los requisitos de producto y UX. Cualquier valor que llegue a persistencia debe poder representarse exactamente en unidades menores y no se redondeará silenciosamente para hacerlo válido.

No se usa `REAL`, `FLOAT`, `DOUBLE` ni aritmética financiera con `number` fraccionario. Domain y Financial Engine trabajan con enteros exactos, por ejemplo `bigint`; sumas y restas se hacen sobre unidades menores. Antes de enlazar `amount_minor` como parámetro de Expo SQLite, Infrastructure exige un entero positivo `<= Number.MAX_SAFE_INTEGER` y solo entonces lo convierte a un `number` entero seguro. Al leerlo, verifica que el valor recuperado sea entero seguro antes de reconstruir `Money`. Los totales no se calculan acumulando valores en `number`, pues una suma podría exceder el rango seguro aunque cada fila sea válida.

La representación visible de COP es responsabilidad de Presentation y no altera estas unidades. No hay tasas de cambio ni saldos de cuentas en este esquema.

## 4. Fechas, consultas e índices

`effective_date` es una fecha calendario, no un instante UTC. Una conversión de zona horaria no puede mover un registro de `2026-09-26` a otro día financiero. El formato de anchura fija `YYYY-MM-DD` permite ordenar y filtrar cronológicamente mediante comparación lexicográfica. Para un mes se calculan en Domain dos fechas calendario válidas y se consulta el intervalo semiabierto:

```sql
SELECT id, type, amount_minor, currency, effective_date, concept,
       category_id, subcategory_id, created_at, updated_at
FROM movements
WHERE effective_date >= ? AND effective_date < ?
ORDER BY effective_date DESC, created_at DESC, id DESC;
```

Los parámetros son el primer día del mes y el primer día del mes siguiente, ambos `YYYY-MM-DD`. El historial general usa el mismo orden sin filtro de mes. `created_at` y luego `id` desempatan de forma estable; `updated_at` no reordena el historial por una edición. Una paginación futura deberá conservar los tres criterios de orden.

La persistencia debe permitir crear, obtener por ID, listar, actualizar y eliminar por ID, además de recuperar movimientos por período. Historial mensual y dashboard consumen los movimientos del intervalo de fecha efectiva; la distribución de gastos puede partir de los gastos del mismo intervalo con `category_id` disponible. SQLite filtra y entrega datos, mientras el Financial Engine decide qué movimientos son contabilizables, excluye `own_transfer`, calcula ingresos y gastos, agrupa gastos por categoría y obtiene **resultado neto = ingresos contabilizables − gastos contabilizables**. No se crea `monthly_summary`, `dashboard_cache` ni otra tabla precalculada. Una edición o eliminación obliga a recalcular los períodos afectados a partir de los movimientos vigentes.

El índice inicial único adicional es `idx_movements_effective_date`, dirigido a las consultas mensuales. La clave primaria ya proporciona acceso por `id`. No se añade por anticipación un índice compuesto: un índice nuevo requerirá una consulta real o una medición que justifique su costo de escritura y espacio.

## 5. Escrituras, validación y transacciones

La secuencia de responsabilidades es: Presentation/Form valida entradas y muestra la versión que se confirmará; Application coordina el caso de uso; Domain exige invariantes financieras; el contrato `MovementRepository` reside junto al consumidor en Application/feature; Infrastructure traduce `Movement` a SQLite y ejecuta SQL; SQLite protege la integridad estructural esencial. Presentation y la IA nunca escriben directamente en la base.

Solo una confirmación de datos visibles y válidos inicia la escritura. El éxito se informa después de completar el guardado local. Un intento repetido de la misma confirmación no debe producir otro movimiento: Application controla la intención en curso y conserva el ID generado para su reintento; la clave primaria evita insertar dos veces ese mismo ID. Un conflicto de ID se informa como fallo de persistencia, sin crear uno nuevo de forma silenciosa.

La actualización identifica la fila por `id`, conserva su identidad y `created_at`, y reemplaza solo los datos confirmados y `updated_at`. La eliminación confirmada ejecuta un `DELETE` local real por `id`; no existen `deleted_at`, soft delete ni tombstones. Si una actualización o eliminación no afecta ninguna fila, Infrastructure comunica ese resultado al caso de uso en lugar de anunciar éxito. Cancelar no escribe.

Cada operación con varias escrituras relacionadas se realiza en una transacción SQLite. Si falla, se revierte completa y la UI conserva el último estado financiero válido. Las lecturas que deban representar una misma instantánea se agrupan coherentemente cuando el caso de uso lo requiera. No hay transacciones distribuidas: toda escritura financiera del MVP ocurre en SQLite local.

Toda entrada variable se enlaza mediante parámetros o prepared statements: concepto, IDs, fechas, montos y filtros. No se concatena texto del usuario dentro del SQL. Los nombres de tabla y columna son constantes de la implementación, no entradas dinámicas.

## 6. Inicialización y migraciones

Se usa Expo SQLite de forma directa y asíncrona para el trabajo normal que pueda afectar el hilo de JavaScript; el MVP no requiere ORM ni framework de migraciones. En la apertura se configura la conexión local y se establece `PRAGMA journal_mode = WAL`. Se habilita `PRAGMA foreign_keys = ON` cuando existan relaciones que utilicen claves foráneas. La versión 1 no tiene claves foráneas ni tablas relacionadas.

El esquema comienza en **versión 1** con `PRAGMA user_version`. Antes de permitir el uso normal de la base, el inicializador lee esa versión y aplica, en orden, cada migración explícita pendiente. Cada migración agrupa cambios de esquema, transformación de datos si corresponde y actualización de `user_version` en una transacción; solo se considera aplicada tras el commit. Ante un fallo se revierte la migración, se detiene la apertura financiera normal y se informa un error recuperable, conservando los datos existentes. No se borra ni recrea automáticamente la base en producción. Una versión de base mayor que la conocida por la app tampoco se abre para escritura normal.

Las migraciones del dispositivo pueden ser **forward-only** durante el MVP: una nueva versión de la app sabe avanzar desde versiones anteriores, sin prometer downgrade del esquema. Cualquier evolución posterior requiere una migración que preserve movimientos confirmados; no se usan resets destructivos como estrategia ordinaria.

## 7. Seguridad y límites de persistencia

La base financiera no almacena secretos de proveedores y no sustituye a SecureStore. Las operaciones SQL y sus errores no deben registrar accidentalmente conceptos, importes ni otros contenidos financieros. La política de cifrado de la base queda para `SECURITY.md`; este documento no la presupone.

Audio, transcripción, prompt, respuesta cruda del modelo, propuesta sin confirmar y confianza de proveedor no se guardan automáticamente en `movements`. La IA interpreta, clasifica, propone y explica; no calcula totales, no persiste movimientos y no es fuente de verdad. La telemetría de uso, si corresponde, pertenece a la estrategia de analytics, no a esta tabla.

El MVP no ofrece backup cloud, sincronización, multidispositivo, repositorio remoto ni resolución de conflictos. Los datos quedan ligados al dispositivo; pérdida o reinstalación pueden afectar su continuidad. Una capacidad futura deberá introducir sus propios mecanismos mediante nuevas decisiones y migraciones, sin campos especulativos en la versión 1. La política general de conservación y eliminación sigue abierta en `TBD-005`; backup y sincronización futuros siguen abiertos en `TBD-010`.

## 8. Propiedades que deberá verificar la implementación

- CRUD confirmado y eliminación real; cancelar o fallar no altera la base.
- Movimientos confirmados disponibles tras cerrar, reabrir y reiniciar el dispositivo.
- Consultas y orden mensual determinados por `effective_date`, incluidos límites de mes y fechas históricas.
- Gasto e ingreso con categoría; solo gasto con subcategoría; transferencia propia sin clasificación y excluida de totales.
- Rechazo de tipos, monedas, montos y combinaciones estructurales inválidos; validación de calendario y catálogo en Domain.
- Cálculos exactos en unidades menores, incluidos valores cercanos al límite seguro y sumas mayores que ese límite.
- Migraciones que conservan datos y revierten por completo una migración fallida; escrituras relacionadas atómicas.
- Reintentos de una misma intención sin duplicados y uso de parámetros para todas las entradas variables.

Estas propiedades guían las pruebas posteriores, sin definir aquí la estrategia de `TESTING.md` ni implementar código de repositorio.
