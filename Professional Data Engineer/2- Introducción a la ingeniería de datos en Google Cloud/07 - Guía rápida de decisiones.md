# Guía rápida de decisiones

> [!abstract] Cómo usarla
> Lee la necesidad, elige un producto y explica por qué las alternativas no cumplen el requisito principal.

[[00 - Índice del módulo|← Índice del módulo]] · [[06 - ELT y Dataform|← ELT y Dataform]]

## Selector de productos

| Si necesitas… | Elige primero… | Razón |
|---|---|---|
| Objetos o datos no estructurados | **Cloud Storage** | Almacenamiento de objetos escalable |
| SQL transaccional regional | **Cloud SQL** | Base relacional administrada |
| PostgreSQL administrado de alto rendimiento | **AlloyDB** | Compatibilidad PostgreSQL y rendimiento |
| SQL transaccional global | **Spanner** | Coherencia sólida y escala horizontal |
| Documentos para una aplicación | **Firestore** | NoSQL documental y serverless |
| Data warehouse y BI | **BigQuery** | OLAP serverless |
| Clave-valor o series temporales masivas | **Bigtable** | Alto throughput y baja latencia |
| Descubrir y gobernar datos distribuidos | **Dataplex / Knowledge Catalog** | Metadatos, políticas y contexto |
| Compartir datos sin copiarlos | **BigQuery sharing / Analytics Hub** | Listings y datasets enlazados |
| Transferencia puntual pequeña con buena red | **`gcloud storage`** | Operación directa |
| Transferencias grandes o programadas | **Storage Transfer Service** | Movimiento administrado de objetos |
| Mucho volumen con conectividad deficiente | **Transfer Appliance** | Traslado físico y offline |
| Migrar una base relacional | **Database Migration Service** | Migración administrada del motor |
| Replicar cambios de una base | **Datastream** | Backfill y CDC casi en tiempo real |
| Automatizar cargas hacia BigQuery | **BigQuery Data Transfer Service** | Transferencias administradas al warehouse |
| Consultar objetos externos con gobierno detallado | **BigLake** | Acceso delegado sin mover los datos |
| Transformación SQL periódica y sencilla | **Consulta programada** | Menos piezas que operar |
| Transformaciones SQL con DAG, pruebas y documentación | **Dataform** | Dependencias y calidad integradas |

## Atajos de decisión

### Clase de Cloud Storage

| Acceso esperado | Clase clásica |
|---|---|
| Frecuente o vida corta | **Standard** |
| Una vez al mes o menos | **Nearline** |
| Una vez por trimestre o menos | **Coldline** |
| Una vez al año o menos | **Archive** |

### Datos en BigQuery o fuera de BigQuery

| Necesidad | Estrategia |
|---|---|
| Consultas frecuentes y máximo rendimiento | Tabla nativa de BigQuery |
| Consulta ocasional sin copiar | Tabla externa |
| Datos externos con acceso delegado y controles por fila o columna | Tabla BigLake |

### Transformación

| Necesidad | Opción |
|---|---|
| Cálculo reutilizable que devuelve un valor | UDF |
| Secuencia parametrizada de instrucciones | Procedimiento almacenado |
| Lógica externa desplegada en Cloud Run | Función remota |
| Flujo SQL complejo y gobernado | Dataform |

## Confusiones frecuentes

> [!warning]
> - **BigQuery ≠ Bigtable:** SQL analítico frente a NoSQL wide-column.
> - **Storage Transfer Service ≠ BigQuery Data Transfer Service:** objetos y archivos frente a cargas hacia BigQuery.
> - **Migración ≠ replicación:** trasladar un conjunto finito no equivale a capturar cada cambio.
> - **Tabla externa ≠ BigLake:** ambas consultan datos externos; BigLake añade delegación y seguridad detallada.
> - **UDF ≠ procedimiento almacenado:** valor calculado frente a secuencia de operaciones.
> - **Alta disponibilidad ≠ backup:** continuidad del servicio frente a recuperación de datos.

## Mini simulacro

> [!question]- Una aplicación SQL regional necesita transacciones y mínima administración. ¿Qué eliges?
> **Cloud SQL**. Spanner sería innecesario sin un requisito real de escala global.

> [!question]- Debes analizar petabytes con SQL y herramientas de BI. ¿Qué eliges?
> **BigQuery**, porque es un data warehouse serverless diseñado para OLAP.

> [!question]- Debes sincronizar INSERT, UPDATE y DELETE desde PostgreSQL hacia BigQuery. ¿Qué eliges?
> **Datastream**, porque realiza backfill y luego CDC con baja latencia.

> [!question]- Los datos deben permanecer en Cloud Storage, pero distintos usuarios necesitan controles por fila y columna. ¿Qué eliges?
> Una **tabla BigLake**, que delega el acceso al almacenamiento y permite seguridad detallada en la tabla.

> [!question]- Cinco tablas SQL dependen entre sí y requieren pruebas de calidad antes de publicarse. ¿Qué eliges?
> **Dataform**, porque administra el DAG, las aserciones, la documentación y la ejecución en BigQuery.

## Profundizar

- [[02 - Elegir almacenamiento en Google Cloud]]
- [[03 - Data lake, BigQuery y gobierno]]
- [[04 - Migración y replicación con Datastream]]
- [[05 - Carga de datos y BigLake]]
- [[06 - ELT y Dataform]]
