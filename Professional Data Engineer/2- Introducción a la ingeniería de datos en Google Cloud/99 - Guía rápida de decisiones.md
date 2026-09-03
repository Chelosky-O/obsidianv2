# Guía rápida de decisiones

> [!abstract] Cómo usarla
> Lee la necesidad, elige un producto y explica por qué las alternativas no cumplen el requisito principal.

[[00 - Índice del curso|← Índice del curso]] · [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/06 - Técnicas de automatización/00 - Índice|← Técnicas de automatización]]

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
| Preparación visual del material del examen | **Dataprep by Trifacta** | *Data wrangling*, recetas y vista previa |
| Preparación asistida nativa en BigQuery | **BigQuery data preparation** | Sugerencias y transformaciones dentro de BigQuery |
| Integración empresarial visual y con conectores | **Cloud Data Fusion** | Pipeline gráfico y extensible |
| Spark/Hadoop con control del entorno | **Managed Service for Apache Spark — clúster** | Compatibilidad OSS y configuración detallada |
| Spark sin administrar un clúster | **Managed Service for Apache Spark — serverless** | Infraestructura y escalado administrados |
| Eventos desacoplados entre productores y consumidores | **Pub/Sub** | Mensajería asíncrona mediante temas y suscripciones |
| Procesamiento programable batch y streaming | **Dataflow** | Apache Beam con ejecución administrada |

> [!example]- Comparación mostrada en el curso
> ![[Pasted image 20260903121158.png|900]]
>
> La lámina usa los nombres de la grabación. Actualmente, Dataproc y Serverless for Apache Spark forman **Managed Service for Apache Spark**. “Serverless” tampoco debe confundirse con “servicio administrado”: Data Fusion es administrado, pero sus pipelines batch suelen ejecutar en un clúster Spark efímero.

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

### Procesamiento de datos

| Necesidad | Opción |
|---|---|
| SQL dentro de BigQuery | **Dataform** |
| Integración visual con fuentes y destinos empresariales | **Cloud Data Fusion** |
| Pipeline batch/streaming con Apache Beam | **Dataflow** |
| Carga de trabajo Spark/Hadoop | **Managed Service for Apache Spark / Dataproc** |
| Entrega Pub/Sub → BigQuery sin transformación compleja | **Suscripción de BigQuery** |

La suscripción directa admite transformaciones ligeras mediante SMT, pero entrega al menos una vez. Usa Dataflow si necesitas ventanas, agregaciones, lógica compleja o deduplicación exactamente una vez.

## Confusiones frecuentes

> [!warning]
> - **BigQuery ≠ Bigtable:** SQL analítico frente a NoSQL wide-column.
> - **Storage Transfer Service ≠ BigQuery Data Transfer Service:** objetos y archivos frente a cargas hacia BigQuery.
> - **Migración ≠ replicación:** trasladar un conjunto finito no equivale a capturar cada cambio.
> - **Tabla externa ≠ BigLake:** ambas consultan datos externos; BigLake añade delegación y seguridad detallada.
> - **UDF ≠ procedimiento almacenado:** valor calculado frente a secuencia de operaciones.
> - **Pub/Sub ≠ Dataflow:** Pub/Sub transporta eventos; Dataflow los transforma.
> - **Dataform ≠ Dataflow:** SQL dentro de BigQuery frente a procesamiento batch/streaming con Beam.
> - **ETL/ELT ≠ batch/streaming:** son decisiones independientes.
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

> [!question]- Debes unir dos fuentes SAP con una interfaz visual y escribir ramas en Cloud Storage y BigQuery. ¿Qué eliges?
> **Cloud Data Fusion**, por sus conectores, Pipeline Studio, transformaciones y vista previa por etapa.

> [!question]- Llegan eventos sin fin y necesitas ventanas de cinco minutos antes de escribir en BigQuery. ¿Qué eliges?
> **Pub/Sub + Dataflow**: Pub/Sub recibe los eventos y Dataflow aplica ventanas y transformaciones con Apache Beam.

> [!question]- Debes ejecutar PySpark de forma intermitente sin mantener un clúster. ¿Qué eliges?
> **Managed Service for Apache Spark en modo serverless**, llamado Dataproc Serverless en el curso.

## Profundizar

- [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/01 - Tareas y componentes de ingeniería de datos/00 - Índice|01 — Tareas y componentes]]
- [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/02 - Replicación y migración de datos/00 - Índice|02 — Replicación y migración]]
- [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/03 - Patrón de extracción y carga (EL)/00 - Índice|03 — Patrón EL]]
- [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/04 - Patrón de extracción, carga y transformación (ELT)/00 - Índice|04 — Patrón ELT]]
- [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/05 - Patrón de extracción, transformación y carga (ETL)/00 - Índice|05 — Patrón ETL]]
- [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/06 - Técnicas de automatización/00 - Índice|06 — Técnicas de automatización]]
