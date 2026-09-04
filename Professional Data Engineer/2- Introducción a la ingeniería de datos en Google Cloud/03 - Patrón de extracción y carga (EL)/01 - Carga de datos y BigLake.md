# Carga de datos y BigLake

[[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/03 - Patrón de extracción y carga (EL)/00 - Índice|← Índice de la sección]] · [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/02 - Replicación y migración de datos/01 - Migración y replicación con Datastream|← Sección anterior]] · [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/04 - Patrón de extracción, carga y transformación (ELT)/00 - Índice|Siguiente sección: patrón ELT →]]

> [!summary] Resumen
> En un patrón **EL**, los datos se extraen y cargan primero en BigQuery; las transformaciones posteriores con SQL convierten el flujo en **ELT**. Cargar crea una copia en el almacenamiento nativo de BigQuery. Una tabla externa consulta el origen sin copiarlo, mientras que BigLake añade delegación de acceso, gobierno detallado y caché de metadatos sobre datos que siguen en el lago.

## 1. De EL a ELT

```text
Extract → Load → Transform en BigQuery
```

Separar carga y transformación permite conservar datos crudos, empezar a ingerir con rapidez y aprovechar la capacidad de BigQuery para limpiar, combinar y modelar mediante SQL. La transformación se estudia en [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/04 - Patrón de extracción, carga y transformación (ELT)/01 - ELT y Dataform|ELT y Dataform]].

### Opciones de ingestión y acceso

| Opción | Mejor encaje | ¿Copia datos a BigQuery? |
|---|---|---:|
| Interfaz de BigQuery | Exploración o carga manual puntual | Sí |
| Sentencia `LOAD DATA` | Carga reproducible y controlada con SQL | Sí |
| Comando `bq load` | Scripts, automatización y CI/CD | Sí |
| BigQuery Data Transfer Service (DTS) | Cargas administradas, recurrentes o desde conectores | Sí |
| Tabla externa o BigLake | Consultar sin mover primero los objetos | No |

La interfaz permite escoger archivo, formato y esquema —incluida la detección automática cuando es compatible—. `LOAD DATA` y `bq load` hacen explícita esa configuración y se adaptan mejor a automatización.

## 2. Formatos

| Operación | Formatos habituales |
|---|---|
| Carga por lotes | Avro, Parquet, ORC, CSV, JSON delimitado por saltos de línea y exportaciones de Datastore/Firestore |
| Exportación de tablas o resultados | CSV, JSON delimitado por saltos de línea, Avro y Parquet |

Avro, Parquet y ORC incorporan información de esquema; CSV y JSON suelen necesitar más decisiones sobre tipos, encabezados y valores nulos. La compatibilidad exacta también depende del origen de la carga y del método utilizado.

## 3. Ejemplos de carga

### Con GoogleSQL

`LOAD DATA INTO` añade filas a una tabla; cuando el caso lo requiera, `LOAD DATA OVERWRITE` permite reemplazar datos conforme a la sintaxis admitida.

```sql
LOAD DATA INTO `mi-proyecto.ventas.pedidos`
FROM FILES (
  format = 'CSV',
  uris = ['gs://mi-bucket/landing/pedidos-*.csv'],
  skip_leading_rows = 1
);
```

Este ejemplo carga varios CSV de Cloud Storage gracias al comodín de la URI e ignora la fila de encabezado.

### Con la CLI `bq`

Crear un conjunto de datos:

```bash
bq mk --dataset mi-proyecto:ventas
```

Cargar varios archivos con un esquema explícito:

```bash
bq load \
  --source_format=CSV \
  --skip_leading_rows=1 \
  mi-proyecto:ventas.pedidos \
  "gs://mi-bucket/landing/pedidos-*.csv" \
  schema.json
```

Las piezas importantes son el formato de origen, las opciones de lectura, la tabla de destino, la URI —que puede usar comodines— y el esquema explícito o autodetectado.

### BigQuery Data Transfer Service

DTS configura movimientos administrados hacia BigQuery desde aplicaciones SaaS, almacenes de objetos, otros almacenes de datos y fuentes de Google. Puede ejecutar cargas programadas, recurrentes o bajo demanda según el conector, además de copias de conjuntos de datos y consultas programadas.

> [!important] Alcance de DTS
> Es un servicio para introducir o mover datos dentro de BigQuery; no es un servicio genérico para exportar datos a cualquier destino. La frecuencia, el modo incremental y las opciones dependen del conector elegido.

## 4. Almacenamiento nativo, tablas externas y BigLake

BigQuery puede consultar datos aunque no estén almacenados de forma nativa.

| Modelo | Ubicación de los datos | Ventaja principal | Coste o limitación |
|---|---|---|---|
| Tabla nativa de BigQuery | Almacenamiento de BigQuery | Mejor rendimiento y conjunto completo de funciones | Requiere cargar o copiar los datos |
| Tabla externa tradicional | Fuente externa | Consulta directa sin movimiento previo | Permisos separados y, normalmente, menor rendimiento |
| Tabla BigLake | Almacén de objetos externo | Delegación de acceso, seguridad detallada y caché de metadatos | Sigue siendo externa, de sólo lectura y no admite todas las funciones nativas |

Entre las fuentes externas se encuentran Cloud Storage, Google Drive/Sheets y Bigtable. Los datos en otros proveedores de nube se pueden consultar mediante opciones de BigLake y BigQuery Omni, según la región y el origen compatibles. Incluso una hoja de cálculo puede exponerse como tabla y consultarse con GoogleSQL.

### ¿Cuándo usar cada modelo?

- **Nativo:** consultas frecuentes, baja latencia y necesidad de todas las capacidades de BigQuery.
- **Externo tradicional:** acceso ocasional, datos que no se deben mover o una prueba rápida sobre la fuente.
- **BigLake:** analítica con GoogleSQL sobre un data lake manteniendo los objetos fuera de BigQuery, pero con gobierno centralizado y controles más precisos.

> [!warning] Límites de las tablas externas
> Suelen ser más lentas que las nativas, son de sólo lectura y no admiten funciones como clustering. Una prueba en seco puede informar `0` bytes o sólo un límite inferior porque el volumen externo no siempre se conoce antes de ejecutar la consulta. También existen restricciones de caché y de consulta según la fuente.

BigLake no mueve los datos: proporciona una interfaz de tabla para consultarlos con GoogleSQL, unirlos con tablas nativas y aplicar controles desde BigQuery. Los formatos comunes en almacenes de objetos incluyen Avro, CSV, JSON, ORC y Parquet; la lista exacta depende del tipo de tabla y origen.

## 5. Caché de metadatos

Antes de leer un lago, el motor necesita descubrir qué archivos y particiones existen. La caché de metadatos de BigLake puede conservar información como nombres y tamaños de archivos, particiones, recuentos de filas y, cuando el formato lo proporciona, estadísticas mínimas y máximas de columnas.

Esto permite:

- Evitar listar todos los objetos en cada consulta.
- Podar archivos o particiones que no satisfacen el filtro.
- Aprovechar estadísticas de formatos columnares como Parquet.
- Compartir metadatos entre BigQuery y herramientas compatibles del ecosistema de Spark.

| Modo | Comportamiento |
|---|---|
| Automático | BigQuery actualiza la caché en segundo plano |
| Manual | El equipo decide cuándo refrescarla |

El intervalo máximo de obsolescencia configurable está entre **30 minutos y 7 días**. Una ventana mayor reduce trabajo de actualización, pero aumenta el riesgo de consultar metadatos que aún no reflejan archivos añadidos, modificados o eliminados.

## 6. Seguridad: acceso directo frente a delegación

| Aspecto | Tabla externa tradicional | Tabla BigLake |
|---|---|---|
| Acceso al origen | El usuario necesita permisos sobre la tabla y la fuente | Una cuenta de servicio asociada a una conexión accede al almacenamiento |
| Administración | Identidades y permisos en ambos sistemas | Acceso al almacenamiento desacoplado del usuario final |
| Gobierno desde BigQuery | Más limitado | Puede aplicar seguridad por fila y columna y, cuando corresponde, enmascaramiento |

```text
Usuario → permisos sobre la tabla BigLake → conexión → cuenta de servicio → objetos
```

La conexión actúa como frontera de delegación: el usuario consulta la tabla sin recibir necesariamente acceso directo al bucket. BigQuery aplica los controles definidos en la tabla y la cuenta de servicio obtiene los objetos subyacentes.

## Puntos de examen

> [!tip] Qué distinguir
> - **Cargar** crea una copia nativa; **tabla externa/BigLake** consulta los datos donde están.
> - EL se convierte en ELT cuando la transformación ocurre después de cargar en BigQuery.
> - Interfaz: manual; `LOAD DATA`: SQL reproducible; `bq load`: automatización; DTS: transferencia administrada.
> - Las tablas nativas priorizan rendimiento y funciones; las externas, evitar movimiento; BigLake, gobierno del lago.
> - BigLake delega el acceso al objeto mediante una conexión y su cuenta de servicio.
> - La caché acelera el descubrimiento y la poda, pero introduce una decisión de frescura.

## Repaso activo

> [!question]- ¿Qué convierte un flujo EL en ELT?
> Que la transformación se ejecute después de cargar: BigQuery recibe primero los datos y luego los limpia o modela con SQL.

> [!question]- ¿Cuándo elegirías `LOAD DATA`, `bq load` o DTS?
> `LOAD DATA` para una carga declarativa dentro de SQL; `bq load` para scripts y automatización por CLI; DTS para movimientos administrados y recurrentes desde un conector compatible.

> [!question]- ¿Por qué Parquet o Avro suelen requerir menos definición manual que CSV?
> Son formatos autodescriptivos que almacenan información de esquema. En CSV hay que decidir explícitamente tipos, encabezados, delimitadores y tratamiento de nulos, salvo que se use autodetección.

> [!question]- Un equipo necesita consultar archivos esporádicamente sin copiarlos. ¿Nativo, externo o BigLake?
> Una tabla externa puede bastar. Si además necesita delegación de acceso, seguridad detallada o caché de metadatos, BigLake encaja mejor.

> [!question]- ¿Cuál es el intercambio al aumentar la obsolescencia máxima de la caché?
> Se reduce la frecuencia y el coste de actualización de metadatos, pero los cambios recientes en los objetos pueden tardar más en aparecer en las consultas.

> [!question]- ¿Por qué BigLake simplifica el acceso al almacenamiento?
> La cuenta de servicio de una conexión accede a los objetos; el usuario recibe permisos sobre la tabla y no necesita necesariamente permisos directos sobre el bucket.

## Fuentes oficiales

- [Carga de datos por lotes en BigQuery](https://cloud.google.com/bigquery/docs/batch-loading-data)
- [Sentencia `LOAD DATA`](https://cloud.google.com/bigquery/docs/reference/standard-sql/load-statements)
- [Herramienta de línea de comandos `bq`](https://cloud.google.com/bigquery/docs/bq-command-line-tool)
- [Introducción a BigQuery Data Transfer Service](https://cloud.google.com/bigquery/docs/dts-introduction)
- [Fuentes de datos externas de BigQuery](https://cloud.google.com/bigquery/docs/external-data-sources)
- [Introducción a BigLake](https://cloud.google.com/bigquery/docs/biglake-intro)
- [Caché de metadatos para tablas externas](https://cloud.google.com/bigquery/docs/metadata-caching-external-tables)

[[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/03 - Patrón de extracción y carga (EL)/00 - Índice|← Índice de la sección]] · [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/02 - Replicación y migración de datos/01 - Migración y replicación con Datastream|← Sección anterior]] · [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/04 - Patrón de extracción, carga y transformación (ELT)/00 - Índice|Siguiente sección: patrón ELT →]]
