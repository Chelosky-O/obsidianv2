# Soluciones de almacenamiento de Google Cloud

> [!abstract] Resumen
> Google Cloud separa el problema en cuatro decisiones: **qué tipo de datos tienes**, **qué patrón de acceso necesitas**, **cómo llevarás los datos a la nube** y **dónde los consultarás**.
> - **Cloud Storage** guarda objetos y datos no estructurados.
> - **Cloud SQL, AlloyDB, Spanner y Firestore** cubren cargas transaccionales.
> - **BigQuery y Bigtable** cubren cargas analíticas con modelos distintos.
> - **Dataplex**, **BigQuery sharing** y **BigLake** ayudan a gobernar, compartir y consultar datos distribuidos.
> - **gcloud storage, Storage Transfer Service, Transfer Appliance y Datastream** resuelven distintos patrones de migración y replicación.

[[00 - Ruta Professional Data Engineer|← Volver a la ruta]] · [[Fuentes de datos frente a receptores de datos|← Fuentes y receptores]] · [[Patrón de canalización de datos de extracción, carga y transformación|Siguiente: ELT y Dataform →]]

> [!tip] Pregunta guía
> Antes de elegir un producto, responde: **¿transaccional o analítico?, ¿SQL o NoSQL?, ¿datos estructurados o no estructurados?, ¿acceso frecuente o esporádico?, ¿carga única o replicación continua?**

## Índice

- [[#1. Cloud Storage para datos no estructurados|1. Cloud Storage]]
- [[#2. Cómo elegir almacenamiento para datos estructurados|2. Almacenamiento estructurado]]
- [[#3. Data lake frente a data warehouse|3. Data lake vs. data warehouse]]
- [[#4. BigQuery como data warehouse|4. BigQuery]]
- [[#5. Metadatos y Dataplex|5. Metadatos y Dataplex]]
- [[#6. Compartir datos con Analytics Hub|6. Compartir datos]]
- [[#7. Migración y replicación hacia Google Cloud|7. Migración y replicación]]
- [[#8. Datastream y captura de datos modificados|8. Datastream]]
- [[#9. Extracción y carga hacia BigQuery|9. Extracción y carga]]
- [[#10. BigLake y tablas externas|10. BigLake]]
- [[#11. Tabla maestra de decisión|11. Tabla maestra]]
- [[#12. Puntos de examen|12. Puntos de examen]]
- [[#13. Repaso activo|13. Repaso activo]]
- [[#Fuentes oficiales|Fuentes oficiales]]

---

## 1. Cloud Storage para datos no estructurados

### Idea central

**Cloud Storage** es almacenamiento de objetos. Los objetos pueden representar imágenes, videos, documentos, archivos, BLOB y contenido estático de gran tamaño.

- Se accede a los objetos mediante solicitudes HTTP.
- Una solicitud GET puede recuperar un objeto completo o un rango de bytes.
- Cada objeto tiene metadatos, pero su contenido se manipula como una secuencia de bytes sin estructura.
- Un objeto puede alcanzar hasta **5 TiB**.
- El servicio está diseñado para ser durable, escalable, disponible y coherente.
- Es apropiado para sitios web estáticos, contenido generado por usuarios y, en general, datos no estructurados.

> [!important] Modelo mental
> Cloud Storage no es una base de datos relacional ni un sistema de archivos POSIX tradicional: almacena **objetos dentro de buckets**. La estructura lógica del dato la interpreta la aplicación o la herramienta que lo procesa.

### Las cuatro clases clásicas del curso

La clase se elige principalmente según la **frecuencia esperada de acceso** y el tiempo durante el cual se conservarán los objetos.

| Clase | Patrón de acceso típico | Duración mínima | Cargo por recuperación | Uso común |
|---|---:|---:|---:|---|
| **Standard** | Frecuente o datos de vida corta | Sin mínimo | No | Contenido activo, analítica interactiva, aplicaciones |
| **Nearline** | Aproximadamente una vez al mes o menos | 30 días | Sí | Backups y datos mensuales |
| **Coldline** | Aproximadamente una vez por trimestre o menos | 90 días | Sí | Backups poco consultados y recuperación ante desastres |
| **Archive** | Aproximadamente una vez al año o menos | 365 días | Sí | Archivo regulatorio y conservación a largo plazo |

> [!warning] Trampa de examen
> Una clase más fría reduce el costo de almacenamiento, pero puede aumentar los costos de recuperación y exige una duración mínima. La elección correcta depende del **patrón total de uso**, no solo del precio por GiB almacenado.

> [!info] Actualización 2026 — Rapid Storage
> Las cuatro clases anteriores siguen siendo el marco clásico del curso. Desde el **10 de marzo de 2026**, Google también ofrece en disponibilidad general la clase **Rapid** mediante **Rapid Bucket**: almacenamiento zonal de alto rendimiento para cargas intensivas de E/S, especialmente IA, ML y analítica. No sustituye a Standard, Nearline, Coldline ni Archive, porque responde a una necesidad de rendimiento y colocación zonal, no a una jerarquía de datos fríos. Rapid Bucket no es compatible actualmente con BigQuery. Consulta [Rapid Bucket](https://cloud.google.com/storage/docs/rapid/rapid-bucket), las [clases de almacenamiento](https://cloud.google.com/storage/docs/storage-classes) y las [notas de versión](https://cloud.google.com/storage/docs/release-notes).

### Ejemplo de decisión

Una empresa conserva grabaciones de soporte:

1. Durante los primeros 30 días se reproducen con frecuencia → **Standard**.
2. Después se consultan solo en auditorías mensuales → **Nearline**.
3. Tras varios meses quedan como respaldo poco utilizado → **Coldline** o **Archive**, según la frecuencia y el período de retención.

Las transiciones se pueden automatizar con **Object Lifecycle Management**. Si el patrón es impredecible, **Autoclass** puede administrar transiciones compatibles automáticamente.

---

## 2. Cómo elegir almacenamiento para datos estructurados

No existe una opción única para todas las cargas. La decisión depende de la aplicación, el modelo de datos, la distribución geográfica, el tipo de consulta y la escala.

![[Pasted image 20260902144358.png|1050]]

*Figura 1. Árbol de decisión del curso para cargas estructuradas: transaccional o analítica, SQL o NoSQL y escala requerida.*

### Matriz de productos

| Necesidad | Producto | Modelo | Característica principal |
|---|---|---|---|
| Transacciones relacionales locales o regionales | **Cloud SQL** | SQL | Base de datos relacional administrada |
| PostgreSQL de alto rendimiento y gran escala | **AlloyDB** | PostgreSQL compatible | Servicio administrado optimizado para rendimiento |
| Transacciones globales con coherencia sólida | **Spanner** | SQL relacional distribuido | Escalabilidad horizontal y consistencia global |
| Aplicaciones con documentos y escalado automático | **Firestore** | NoSQL documental | Serverless, desarrollo ágil y alto rendimiento |
| Analítica empresarial | **BigQuery** | SQL analítico | Data warehouse serverless |
| Clave-valor o series temporales a gran escala | **Bigtable** | NoSQL wide-column | Alto throughput y latencia coherente inferior a 10 ms |

### Cloud SQL

Servicio administrado para bases de datos relacionales. Es una opción natural cuando una aplicación necesita transacciones SQL y opera principalmente en una región.

### AlloyDB

Servicio de base de datos compatible con PostgreSQL, completamente administrado y orientado a cargas que necesitan mayor rendimiento y escala.

### Spanner

Base de datos relacional distribuida, completamente administrada, con coherencia sólida y escalabilidad horizontal. Se justifica cuando el requisito global es real; para una aplicación regional sencilla, Cloud SQL suele ser una solución más directa.

### Firestore

Base de datos documental NoSQL, serverless, rápida y con escalado automático. Favorece el desarrollo de aplicaciones que trabajan naturalmente con documentos.

### BigQuery

Data warehouse empresarial, administrado y serverless, optimizado para análisis de grandes volúmenes de datos.

### Bigtable

Base de datos NoSQL de alto rendimiento. Está diseñada para búsquedas rápidas por clave y cargas masivas como telemetría, series temporales y métricas.

> [!example] Ejemplos rápidos
> - Sistema de pedidos regional con transacciones ACID → **Cloud SQL**.
> - Plataforma financiera activa en varios continentes → **Spanner**.
> - Catálogo flexible de productos para una aplicación → **Firestore**.
> - Informes históricos sobre petabytes → **BigQuery**.
> - Telemetría por dispositivo y timestamp → **Bigtable**.

---

## 3. Data lake frente a data warehouse

| Aspecto | Data lake | Data warehouse |
|---|---|---|
| Estado del dato | Crudo o con distintos niveles de procesamiento | Agregado, depurado y modelado |
| Formatos | Estructurados, semiestructurados y no estructurados | Principalmente estructurados |
| Esquema | Flexible; puede aplicarse al leer | Definido para análisis consistente |
| Consumidores | Ingeniería, ciencia de datos, ML y aplicaciones | Analistas, BI y toma de decisiones |
| Fortalezas | Exploración y reutilización flexible | Consultas, informes y métricas gobernadas |
| Ejemplo en Google Cloud | Cloud Storage gobernado con Dataplex | BigQuery |

Un **data lake** es un repositorio central para datos sin procesar en distintos formatos. Permite reutilizarlos en ciencia de datos, aplicaciones y análisis sin exigir que todo esté modelado de antemano.

Un **data warehouse** almacena datos estructurados, agregados o procesados previamente. Su objetivo principal es ejecutar consultas e informes empresariales de forma eficiente y consistente a largo plazo.

Tradicionalmente ambos podían operar como sistemas independientes. Servicios como **BigLake** permiten reducir esa separación al consultar desde BigQuery datos que permanecen en el lake.

> [!tip] Regla mental
> **Lake = conservar flexibilidad. Warehouse = servir análisis confiable.** Una arquitectura empresarial puede necesitar ambos.

---

## 4. BigQuery como data warehouse

BigQuery es un data warehouse empresarial, completamente administrado y serverless.

- Incluye capacidades de aprendizaje automático, análisis geoespacial e inteligencia empresarial.
- Está diseñado para analizar terabytes en segundos y petabytes en minutos, según la consulta y la organización de los datos.
- Es apropiado para cargas **OLAP**, exploración de macrodatos e informes de BI.
- Separa la administración de infraestructura del trabajo analítico.

### Formas de acceso

![[Pasted image 20260902144908.png|1050]]

*Figura 2. Acceso a BigQuery mediante el editor SQL de la consola, la herramienta de línea de comandos `bq` y la API REST.*

Las vías destacadas por el curso son:

1. **Editor SQL** de la consola de Google Cloud.
2. Herramienta de línea de comandos **`bq`**, incluida en Google Cloud CLI.
3. **API REST** y bibliotecas cliente; el material resume compatibilidad con siete lenguajes.

La consulta mostrada en la figura agrupa comentarios por usuario, ordena el resultado y limita las filas devueltas. Es un ejemplo de cómo la misma operación puede ejecutarse desde distintas interfaces.

### Jerarquía de recursos

![[Pasted image 20260902144954.png|950]]

*Figura 3. BigQuery organiza los recursos por proyecto y dataset; cada dataset puede contener tablas, vistas, modelos de ML y rutinas.*

La referencia completa de una tabla sigue este formato:

```text
proyecto.dataset.tabla
```

Ejemplo:

```sql
SELECT *
FROM `mi-proyecto.ventas.pedidos`;
```

- El **proyecto** delimita facturación y administración general.
- El **dataset** agrupa recursos relacionados.
- El dataset puede contener tablas, vistas, modelos de ML y rutinas.

### Seguridad y control de acceso

![[Pasted image 20260902145010.png|760]]

*Figura 4. IAM controla el acceso a datasets, tablas y vistas; BigQuery añade controles detallados por filas y columnas.*

El control de acceso se basa en IAM y puede aplicarse a distintos niveles:

- Dataset.
- Tabla o vista.
- Columna mediante seguridad a nivel de columna.
- Fila mediante políticas de acceso por fila.

Para consultar una tabla o vista se necesitan, como mínimo, permisos que permitan leer sus datos y crear el trabajo de consulta correspondiente.

> [!warning] Trampa de examen
> No concedas un rol amplio de proyecto si basta con acceso a un dataset, una tabla, una vista o determinadas columnas. Aplica **mínimo privilegio** en el nivel más bajo que satisfaga el requisito.

---

## 5. Metadatos y Dataplex

Los **metadatos** explican qué representa un recurso, quién lo posee, dónde se encuentra, cómo se clasifica y qué políticas lo gobiernan. Sin ellos, los datos pueden existir técnicamente pero seguir siendo difíciles de descubrir y utilizar.

### Qué aporta Dataplex

Dataplex permite descubrir, administrar y supervisar de forma centralizada datos distribuidos en una organización.

- Reduce silos de datos.
- Centraliza seguridad y gobierno sin eliminar la propiedad distribuida.
- Facilita la búsqueda según contexto empresarial.
- Unifica metadatos, políticas de seguridad, clasificación y ciclo de vida.
- Integra inteligencia de datos, herramientas de código abierto y un ecosistema de socios.
- Ayuda a que ingenieros, científicos y analistas trabajen sobre recursos confiables.

### Zonas del lake

![[Pasted image 20260902145408.png|780]]

*Figura 5. Evolución del dato desde una zona de aterrizaje hasta zonas raw y curated, con acceso progresivamente más amplio.*

| Zona lógica | Contenido | Acceso habitual |
|---|---|---|
| **Landing** | Datos recién ingeridos | Restringido a procesos e ingenieros |
| **Raw** | Datos preservados para procesamiento y trazabilidad | Ingeniería y ciencia de datos |
| **Curated** | Datos procesados y listos para consumo | Analistas y otros consumidores autorizados |

En Dataplex, los tipos formales principales son **raw** y **curated**; “landing” suele representar una etapa arquitectónica de ingreso. Un lake puede contener activos de Cloud Storage y datasets de BigQuery.

> [!info] Nombre actual
> El curso utiliza **Dataplex** y **Dataplex Universal Catalog**. La documentación actual denomina **Knowledge Catalog** al catálogo y contexto de gobierno; los conceptos de lake, zona y activo siguen siendo útiles para comprender la arquitectura. Consulta [Knowledge Catalog](https://cloud.google.com/dataplex/docs/introduction).

> [!example] Flujo de gobierno
> Un archivo llega a Landing con acceso limitado, se valida y conserva en Raw para trazabilidad, y finalmente se publica en Curated como fuente confiable para analistas.

---

## 6. Compartir datos con Analytics Hub

Compartir datos, sobre todo fuera de una organización, exige resolver:

- Seguridad y permisos del destino.
- Actualidad y exactitud de los datos.
- Construcción y mantenimiento de canalizaciones.
- Supervisión del uso.
- Posible monetización del activo.

**Analytics Hub**, llamado actualmente **BigQuery sharing**, aborda estos problemas mediante un modelo de publicación y suscripción.

| Rol o recurso | Función |
|---|---|
| **Publisher** | Publica un recurso listo para análisis y controla quién puede usarlo |
| **Data exchange** | Organiza listings y sus permisos |
| **Listing** | Describe y expone el recurso compartido |
| **Subscriber** | Se suscribe y recibe un dataset enlazado de solo lectura |
| **Usage metrics** | Permiten al proveedor observar el consumo |

Los proveedores pueden:

- Publicar datasets preparados para análisis.
- Mantener control y visibilidad sobre su uso.
- Ofrecer autoservicio sobre datos confiables, incluidos datasets de Google.
- Monetizar recursos mediante Google Cloud Marketplace o sus propios canales.
- Evitar construir infraestructura específica para distribuir copias.

Los consumidores pueden descubrir listings, suscribirse y consultar los datos desde su propio proyecto.

> [!important] Punto clave
> La suscripción crea un **linked dataset**: una referencia de solo lectura. Los datos se comparten en el lugar, sin replicar una copia para cada consumidor.

Consulta la documentación actual de [BigQuery sharing, antes Analytics Hub](https://cloud.google.com/bigquery/docs/analytics-hub-introduction).

---

## 7. Migración y replicación hacia Google Cloud

Esta etapa lleva datos desde sistemas internos, locales o multicloud a Google Cloud para almacenarlos, refinarlos o analizarlos.

### Posibles orígenes

- Sistemas de archivos.
- Almacenes de objetos.
- HDFS.
- Bases de datos relacionales.
- Otros servicios de nube.

Google Cloud permite transferencias únicas, replicaciones programadas y captura continua de cambios. Los destinos habituales son Cloud Storage, BigQuery, Cloud SQL o AlloyDB, según la carga.

### Selección de herramienta

| Situación | Herramienta principal | Tipo |
|---|---|---|
| Menos de 1 TB con buena conectividad | **`gcloud storage`** | Transferencia online bajo demanda |
| Más de 1 TB o transferencias administradas/recurrentes | **Storage Transfer Service** | Online, programable |
| Volumen masivo y conectividad deficiente o inexistente | **Transfer Appliance** | Transferencia física/offline |
| Replicación continua de bases de datos | **Datastream** | CDC casi en tiempo real |
| Migración de motores relacionales | **Database Migration Service** | Migración administrada de bases de datos |
| Formatos o transformaciones complejas | **Dataflow** y sus plantillas | ETL por lotes o streaming |

La guía oficial usa **1 TB como punto de partida**, no como una frontera absoluta. También importan el plazo, la confiabilidad de la red, el número y tamaño de archivos y el costo.

### Transferencias pequeñas con `gcloud storage`

![[Pasted image 20260902151713.png|820]]

*Figura 6. `gcloud storage` mueve bajo demanda volúmenes pequeños o medianos desde sistemas locales a Cloud Storage.*

Ejemplo:

```bash
gcloud storage cp "*.csv" gs://mi-bucket/landing/
```

Es apropiado para una operación táctica que no necesita una infraestructura administrada de transferencia.

### Transferencias grandes con Storage Transfer Service

![[Pasted image 20260902151742.png|820]]

*Figura 7. Storage Transfer Service mueve volúmenes medianos o grandes desde sistemas locales o multicloud y admite programación.*

Storage Transfer Service:

- Puede trabajar con sistemas de archivos, almacenes de objetos y HDFS.
- Admite fuentes como Amazon S3 y Azure Blob Storage.
- Centraliza programación, ejecución y supervisión.
- Es adecuado para transferencias recurrentes o de gran escala.

### Impacto del ancho de banda

El material compara aproximadamente la transferencia de 1 TB:

| Enlace | Tiempo orientativo del curso |
|---:|---:|
| 100 Gbit/s | Cerca de 2 minutos |
| 100 Mbit/s | Cerca de 30 horas |

La fórmula teórica es:

```text
tiempo ≈ tamaño_en_bits / ancho_de_banda_en_bits_por_segundo
```

En la práctica se agregan latencia, cifrado, comprobaciones, concurrencia, sobrecarga de protocolo y rendimiento real del origen.

> [!tip] Decisión práctica
> Si el tiempo calculado no cumple el plazo, mejora la conectividad, usa una transferencia administrada o elige **Transfer Appliance** para trasladar los datos físicamente.

### Migración de bases de datos y ETL

- **Database Migration Service** facilita migraciones de Oracle, MySQL, PostgreSQL y SQL Server.
- Para fuentes NoSQL, formatos especiales o transformaciones complejas, las plantillas de **Dataflow** ofrecen mayor flexibilidad.
- El destino puede ser Cloud SQL, AlloyDB o BigQuery según el patrón transaccional o analítico.

### Comparación visual

![[Pasted image 20260902152436.png|920]]

*Figura 8. Comparación del curso entre `gcloud storage`, Storage Transfer Service, Transfer Appliance y Datastream.*

| Herramienta | Online/offline | Volumen orientativo | Velocidad | Formato |
|---|---|---:|---|---|
| `gcloud storage` | Online | Menos de 1 TB recomendado | Batch | Cualquiera |
| Storage Transfer Service | Online | Más de 1 TB recomendado | Batch, mínimo horario según el material | Cualquiera |
| Transfer Appliance | Offline | Dispositivos de 7, 40 o 300 TB mostrados en el curso | Batch | Cualquiera |
| Datastream | Online | Hasta 10 000 tablas por stream según el material | Batch inicial + streaming | Estructurado |

Consulta la guía oficial de [opciones de transferencia](https://cloud.google.com/storage-transfer/docs/transfer-options).

---

## 8. Datastream y captura de datos modificados

**Datastream** es un servicio serverless de replicación y **change data capture (CDC)**. Replica con baja latencia cambios desde bases de datos operacionales hacia Google Cloud.

### Qué resuelve

- Replicación continua desde entornos locales o multicloud.
- Backfill de datos históricos.
- Captura posterior de cambios nuevos.
- Selección por esquema, tabla o columna.
- Conectividad configurable.
- Destinos como BigQuery o Cloud Storage.
- Integración con plantillas de Dataflow para procesamiento personalizado.

Las fuentes destacadas por el curso son Oracle, MySQL, PostgreSQL y SQL Server.

![[Pasted image 20260902152111.png|1050]]

*Figura 9. Casos de uso de Datastream: replicación hacia BigQuery, procesamiento con Dataflow y arquitecturas basadas en eventos.*

Los casos de uso principales son:

1. Replicar bases de datos directamente en BigQuery para análisis.
2. Procesar cambios con Dataflow antes de cargarlos.
3. Construir arquitecturas orientadas a eventos.
4. Combinar Datastream y plantillas de Dataflow para migración o sincronización.

### Cómo captura cambios

Datastream aprovecha el registro de la base de datos para detectar operaciones **INSERT**, **UPDATE** y **DELETE**.

![[Pasted image 20260902152146.png|840]]

*Figura 10. Flujo CDC: el motor registra cambios, Datastream procesa eventos y los entrega como Avro, JSON o tablas.*

| Fuente | Mecanismo de registro |
|---|---|
| Oracle | LogMiner |
| MySQL | Binary log |
| PostgreSQL | Decodificación lógica |
| SQL Server | Registros de transacciones |

Los eventos se transforman en una representación estructurada y pueden escribirse como archivos Avro o JSON en Cloud Storage, o aplicarse a tablas de BigQuery.

### Estructura de un evento

![[Pasted image 20260902152226.png|840]]

*Figura 11. Un evento de Datastream contiene metadatos genéricos y una carga útil con los valores modificados.*

Un mensaje tiene dos partes básicas:

- **Metadatos:** objeto o tabla de origen, marcas de tiempo, identificador, método de lectura y contexto de procedencia.
- **Payload:** pares clave-valor con nombres de columnas y valores.

Además, puede incluir metadatos específicos de la fuente: base de datos, esquema, tabla, tipo de cambio e identificadores del sistema. Esta información ayuda a reconstruir el linaje y el contexto.

Ejemplo conceptual:

```json
{
  "object": "ventas.pedidos",
  "read_timestamp": "2026-09-02T17:20:00Z",
  "source_timestamp": "2026-09-02T17:19:58Z",
  "change_type": "UPDATE",
  "payload": {
    "pedido_id": 4102,
    "estado": "enviado"
  }
}
```

> [!important] Semántica de entrega
> La entrega de eventos es **al menos una vez** y el orden global no está garantizado. Los metadatos proporcionan información para ordenar eventos y eliminar duplicados cuando el destino lo requiera.

### Tipos unificados

Datastream normaliza tipos equivalentes para que distintas bases de datos puedan alimentar un destino común.

| Origen | Tipo unificado | Destino posible |
|---|---|---|
| Oracle `NUMBER` | Decimal | Avro decimal |
| MySQL `DECIMAL` | Decimal | JSON number |
| PostgreSQL `NUMERIC` | Decimal | BigQuery `NUMERIC` |
| SQL Server `DECIMAL` | Decimal | Tipo numérico del destino |

Esto mejora la coherencia y compatibilidad durante la replicación.

> [!summary] En una frase
> `gcloud storage` mueve archivos pequeños, Storage Transfer Service administra transferencias grandes, Transfer Appliance mueve datos sin conexión y Datastream mantiene datos estructurados sincronizados mediante backfill + CDC.

Consulta [Datastream](https://cloud.google.com/datastream/docs/overview) y su [comportamiento de CDC](https://cloud.google.com/datastream/docs/behavior-overview).

---

## 9. Extracción y carga hacia BigQuery

El patrón **Extract and Load (EL)** lleva los datos a BigQuery sin transformarlos previamente. Después pueden transformarse dentro de BigQuery, como ocurre en un patrón ELT.

### Opciones de ingreso

- Interfaz de usuario de BigQuery.
- Sentencia SQL **`LOAD DATA`**.
- Comando **`bq load`**.
- BigQuery Data Transfer Service.
- Tablas externas o BigLake cuando no se quiere copiar el dato.

Este patrón simplifica la transferencia, admite programación y puede evitar copias cuando se utiliza acceso externo.

### Formatos

| Operación | Formatos destacados por el curso |
|---|---|
| Carga | Avro, Parquet, ORC, CSV, JSON y exportaciones de Firestore |
| Exportación de tablas o resultados | CSV, JSON, Avro y Parquet |

### Consola y `LOAD DATA`

La interfaz permite elegir el archivo, indicar el formato y autodetectar el esquema. `LOAD DATA` brinda más control y es más apropiado para automatización, anexado o sobrescritura.

```sql
LOAD DATA INTO `mi-proyecto.ventas.pedidos`
FROM FILES (
  format = 'CSV',
  uris = ['gs://mi-bucket/landing/pedidos-*.csv'],
  skip_leading_rows = 1
);
```

### Herramienta `bq`

La herramienta `bq`, incluida en Google Cloud CLI, permite administrar BigQuery de forma programática.

Crear un dataset:

```bash
bq mk --dataset mi-proyecto:ventas
```

Cargar varios CSV desde Cloud Storage:

```bash
bq load   --source_format=CSV   --skip_leading_rows=1   mi-proyecto:ventas.pedidos   "gs://mi-bucket/landing/pedidos-*.csv"   schema.json
```

Parámetros importantes:

- Formato de origen.
- Número de filas de encabezado que se omiten.
- Dataset y tabla de destino.
- Comodines para cargar varios archivos.
- Esquema explícito opcional.

Consulta [cargas batch](https://cloud.google.com/bigquery/docs/batch-loading-data) y la referencia de [`LOAD DATA`](https://cloud.google.com/bigquery/docs/reference/standard-sql/load-statements).

### BigQuery Data Transfer Service

BigQuery Data Transfer Service carga datos a BigQuery desde aplicaciones SaaS, almacenes de objetos, otros almacenes de datos y fuentes de Google.

- Es administrado y serverless.
- No exige escribir una canalización propia.
- Admite ejecuciones programadas, recurrentes o bajo demanda.
- Permite configurar fuente, parámetros y destino.
- También se utiliza para copias de datasets y consultas programadas.

> [!warning] Alcance
> BigQuery Data Transfer Service automatiza movimientos **hacia BigQuery**; no es un servicio para exportar datos fuera de BigQuery.

Consulta [BigQuery Data Transfer Service](https://cloud.google.com/bigquery/docs/dts-introduction).

---

## 10. BigLake y tablas externas

BigQuery puede analizar datos que no residen en su almacenamiento nativo.

### Tres estrategias

| Estrategia | Dónde están los datos | Ventaja | Trade-off |
|---|---|---|---|
| **Tabla nativa de BigQuery** | BigQuery | Rendimiento y funciones completas | Requiere cargar o copiar |
| **Tabla externa** | Fuente externa | Configuración directa, sin mover datos | Permisos separados y menor rendimiento potencial |
| **Tabla BigLake** | Cloud Storage u otro object store | Delegación de acceso, seguridad detallada y caché de metadatos | Algunas funciones nativas no están disponibles |

Las fuentes externas pueden incluir:

- Cloud Storage.
- Hojas de cálculo de Google.
- Bigtable.
- Almacenes de objetos de otros proveedores mediante BigLake/BigQuery Omni.

Una tabla externa sobre Google Sheets permite indicar la URL y el formato de la hoja, y después consultarla desde BigQuery como una tabla.

### Cuándo cargar y cuándo consultar en el lugar

- **Carga nativa:** adecuada para análisis frecuente y de alto rendimiento.
- **Tabla externa:** adecuada para acceso menos frecuente o cuando no se desea mover el dato.
- **BigLake:** útil cuando se necesita consultar el lake con GoogleSQL, mantener el dato fuera de BigQuery y aplicar controles más detallados.

Con tablas externas, la estimación previa de costos, la vista previa y el almacenamiento en caché pueden tener limitaciones frente a una tabla nativa.

### Interfaz unificada

BigLake permite usar consultas GoogleSQL habituales, incluidas instrucciones `SELECT` y uniones, sobre datos externos. El material también destaca Apache Arrow dentro del ecosistema de acceso eficiente a datos.

Los formatos relevantes incluyen Avro, CSV, JSON, ORC y Parquet. La documentación actual añade otros formatos según el tipo de tabla y la fuente.

### Caché de metadatos

La caché puede almacenar información como:

- Nombres y tamaños de archivos.
- Cantidad de filas.
- Particiones.
- Estadísticas por columna, como mínimos y máximos en Parquet.

Esto permite:

- Evitar enumerar todos los objetos en cada consulta.
- Podar archivos y particiones con mayor rapidez.
- Habilitar predicate pushdown.
- Compartir estadísticas aprovechables por el conector Spark-BigQuery.

La antigüedad máxima configurable de la caché va de **30 minutos a 7 días**, con actualización automática o manual. A cambio, una consulta puede no ver cambios externos hasta la siguiente actualización.

### Seguridad: externa frente a BigLake

| Tabla externa tradicional | Tabla BigLake |
|---|---|
| El usuario necesita permisos sobre la tabla y la fuente subyacente | Una conexión usa una cuenta de servicio para acceder a la fuente |
| Administración de acceso más compleja | Acceso desacoplado y simplificado |
| Controles más limitados | Seguridad por filas y columnas y, según la fuente, enmascaramiento |

> [!important] Punto de examen
> **BigLake no mueve los datos.** La cuenta de servicio de la conexión accede al almacenamiento y BigQuery aplica el control sobre la tabla. Esa delegación es la diferencia esencial frente a una tabla externa tradicional.

Las tablas BigLake permiten consultar datos externos con seguridad y flexibilidad superiores, aunque no todas las funciones de una tabla nativa están disponibles y las tablas externas BigLake son de solo lectura.

Consulta la [introducción a fuentes externas](https://cloud.google.com/bigquery/docs/external-data-sources) y la [introducción a tablas BigLake](https://cloud.google.com/bigquery/docs/biglake-intro).

---

## 11. Tabla maestra de decisión

| Si necesitas… | Elige primero… | Razón |
|---|---|---|
| Objetos o datos no estructurados | Cloud Storage | Almacenamiento de objetos escalable |
| SQL transaccional regional | Cloud SQL | Relacional administrado |
| PostgreSQL administrado de alto rendimiento | AlloyDB | Compatibilidad PostgreSQL y rendimiento |
| SQL transaccional global | Spanner | Coherencia sólida y escala horizontal |
| Documentos para aplicaciones | Firestore | NoSQL documental y serverless |
| Data warehouse y BI | BigQuery | OLAP serverless |
| Clave-valor o series temporales | Bigtable | Alto throughput y baja latencia |
| Descubrimiento y gobierno de datos distribuidos | Dataplex / Knowledge Catalog | Metadatos, políticas y contexto |
| Compartir datos sin replicarlos | BigQuery sharing / Analytics Hub | Listings y datasets enlazados |
| Transferir menos de 1 TB bajo demanda | `gcloud storage` | Operación directa |
| Transferir más de 1 TB o programar | Storage Transfer Service | Servicio administrado |
| Migrar mucho volumen con mala red | Transfer Appliance | Movimiento offline |
| Replicar cambios de bases de datos | Datastream | Backfill y CDC |
| Automatizar cargas hacia BigQuery | BigQuery Data Transfer Service | Transferencias administradas |
| Consultar datos externos con gobierno | BigLake | Acceso delegado y metadatos |

---

## 12. Puntos de examen

> [!success] Lo que conviene recordar
> 1. El tipo de carga —transaccional o analítica— filtra primero las opciones.
> 2. Standard, Nearline, Coldline y Archive se eligen por frecuencia, duración mínima y costo de recuperación.
> 3. `proyecto.dataset.tabla` es la jerarquía básica de BigQuery.
> 4. IAM debe aplicarse con mínimo privilegio en el nivel adecuado.
> 5. Data lake conserva flexibilidad; data warehouse optimiza consumo analítico.
> 6. Raw conserva datos para procesamiento; Curated publica datos listos para consumo.
> 7. `gcloud storage`, Storage Transfer Service y Transfer Appliance resuelven tamaños y conectividades diferentes.
> 8. Datastream no es una simple copia de archivos: realiza backfill y después CDC.
> 9. Un evento de Datastream incluye metadatos y payload; la entrega es al menos una vez.
> 10. BigQuery Data Transfer Service carga hacia BigQuery y puede programarse.
> 11. Una tabla externa requiere permisos sobre la tabla y la fuente; BigLake delega el acceso mediante una conexión.
> 12. Analytics Hub comparte mediante linked datasets sin replicar una copia por suscriptor.

> [!danger] Confusiones frecuentes
> - **BigQuery ≠ Bigtable:** SQL analítico frente a NoSQL clave-valor/wide-column.
> - **Storage Transfer Service ≠ BigQuery Data Transfer Service:** objetos/archivos frente a cargas administradas hacia BigQuery.
> - **Migración ≠ replicación continua:** mover un conjunto finito no equivale a capturar cada cambio.
> - **Alta disponibilidad ≠ backup:** disponibilidad mantiene servicio; backup permite recuperar datos.
> - **Tabla externa ≠ BigLake:** ambas consultan datos externos, pero BigLake añade delegación y gobierno detallado.

---

## 13. Repaso activo

> [!question]- 1. ¿Cómo eliges entre las cuatro clases clásicas de Cloud Storage?
> Primero estima la frecuencia de acceso y el tiempo de conservación. Usa Standard para acceso frecuente, Nearline para acceso mensual o menor, Coldline para acceso trimestral o menor y Archive para acceso anual o menor. Después compara duración mínima y cargos de recuperación.

> [!question]- 2. ¿Qué agrega Rapid Storage y por qué no reemplaza las cuatro clases del curso?
> Rapid usa buckets zonales para maximizar rendimiento y reducir latencia en cargas intensivas de E/S, IA, ML y analítica. Resuelve colocación y rendimiento; las cuatro clases clásicas siguen resolviendo el ciclo de vida según frecuencia y costo.

> [!question]- 3. ¿Qué producto elegirías para Cloud SQL, Spanner, BigQuery y Bigtable?
> Cloud SQL para transacciones SQL regionales; Spanner para transacciones SQL globales con coherencia sólida; BigQuery para OLAP y BI; Bigtable para clave-valor o series temporales de gran escala.

> [!question]- 4. Debes mover 500 GB con buena red y 80 TB con una conexión inestable. ¿Qué usarías?
> Para 500 GB, `gcloud storage` es un buen punto de partida. Para 80 TB con conectividad deficiente, evalúa Transfer Appliance; si la red permite una transferencia administrada, Storage Transfer Service también puede ser adecuado.

> [!question]- 5. ¿Qué ocurre en una canalización de Datastream?
> Datastream realiza un backfill histórico y después captura INSERT, UPDATE y DELETE desde el log de la base de datos. Convierte los cambios en eventos con metadatos y payload y los entrega a BigQuery o Cloud Storage, directamente o mediante Dataflow.

> [!question]- 6. ¿Cómo se organiza una tabla de BigQuery y dónde se aplican permisos?
> Se identifica como `proyecto.dataset.tabla`. IAM puede aplicarse al dataset, tabla o vista; BigQuery también admite controles por filas y columnas. Debe usarse el nivel más específico que cumpla el requisito.

> [!question]- 7. ¿Cuál es la diferencia esencial entre una tabla externa y una tabla BigLake?
> En una tabla externa tradicional, el usuario necesita permisos tanto sobre la tabla como sobre el almacenamiento. BigLake usa una conexión con cuenta de servicio para delegar el acceso y permite controles detallados sobre la tabla.

> [!question]- 8. ¿Qué problemas resuelven Dataplex y Analytics Hub?
> Dataplex organiza metadatos, descubrimiento, clasificación, políticas y zonas de preparación. Analytics Hub —hoy BigQuery sharing— publica y comparte recursos mediante listings y linked datasets sin crear una copia para cada consumidor.

---

## Fuentes oficiales

- [Ruta Professional Data Engineer en Google Cloud Skills Boost](https://partner.skills.google/paths/85?locale=es)
- [Clases de Cloud Storage](https://cloud.google.com/storage/docs/storage-classes)
- [Rapid Bucket](https://cloud.google.com/storage/docs/rapid/rapid-bucket)
- [Opciones de transferencia de datos](https://cloud.google.com/storage-transfer/docs/transfer-options)
- [Storage Transfer Service](https://cloud.google.com/storage-transfer-service)
- [Descripción general de Datastream](https://cloud.google.com/datastream/docs/overview)
- [Conceptos y comportamiento de Datastream](https://cloud.google.com/datastream/docs/behavior-overview)
- [Cargas batch en BigQuery](https://cloud.google.com/bigquery/docs/batch-loading-data)
- [Sentencia `LOAD DATA`](https://cloud.google.com/bigquery/docs/reference/standard-sql/load-statements)
- [BigQuery Data Transfer Service](https://cloud.google.com/bigquery/docs/dts-introduction)
- [Fuentes de datos externas en BigQuery](https://cloud.google.com/bigquery/docs/external-data-sources)
- [Tablas BigLake](https://cloud.google.com/bigquery/docs/biglake-intro)
- [BigQuery sharing, antes Analytics Hub](https://cloud.google.com/bigquery/docs/analytics-hub-introduction)
- [Knowledge Catalog, antes Dataplex Universal Catalog](https://cloud.google.com/dataplex/docs/introduction)
