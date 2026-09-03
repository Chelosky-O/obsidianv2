# Migración y replicación con Datastream

[[00 - Índice|← Índice de la sección]] · [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/01 - Tareas y componentes de ingeniería de datos/03 - Data lake, BigQuery y gobierno|← Sección anterior]] · [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/03 - Patrón de extracción y carga (EL)/00 - Índice|Siguiente sección: patrón EL →]]

> [!summary] Resumen
> Migrar mueve datos hacia Google Cloud; replicar mantiene una copia sincronizada. La herramienta depende sobre todo del **volumen**, el **ancho de banda**, la **frecuencia**, el **tipo de origen** y de si hace falta **transformar**. Para bases de datos operativas, Datastream realiza una carga histórica inicial y después captura cambios mediante **CDC** con baja latencia.

## 1. Elegir el patrón de movimiento

Los datos pueden proceder de sistemas de archivos, almacenes de objetos, HDFS, bases de datos relacionales u otras nubes. El movimiento puede ser:

- **Único:** una migración o carga puntual.
- **Programado:** copias recurrentes por lotes.
- **Continuo:** replicación de cambios casi en tiempo real mediante CDC.

El destino también orienta la decisión: Cloud Storage para aterrizaje y conservación de objetos, BigQuery para analítica, o una base administrada como Cloud SQL o AlloyDB para cargas transaccionales.

### Mapa de herramientas

| Necesidad principal | Herramienta | Modalidad | Idea para recordarla |
|---|---|---|---|
| Copia puntual, normalmente menor de 1 TB y con buena conexión | `gcloud storage` | En línea, bajo demanda | Copia directa desde la CLI |
| Transferencia grande, administrada o recurrente | Storage Transfer Service | En línea, puntual o programada | Orquesta, ejecuta y supervisa transferencias |
| Mucho volumen y conectividad escasa o inexistente | Transfer Appliance | Física, sin conexión | Los datos viajan en un dispositivo |
| Migrar una base relacional a un motor administrado | Database Migration Service (DMS) | Servicio de migración administrado | Migra el motor y facilita el cambio al destino |
| Replicar continuamente cambios de una base | Datastream | Carga inicial + streaming CDC | Convierte los cambios del log en eventos |
| Transformar, enrutar o adaptar formatos durante el movimiento | Dataflow y sus plantillas | Lotes o streaming | Pipeline de procesamiento, no sólo copia |

> [!important] El umbral de 1 TB es una orientación
> No es un límite del producto. También importan el plazo, la fiabilidad y el coste de la red, el número y tamaño de los archivos, la concurrencia y el rendimiento del origen.

### `gcloud storage`: copia directa

Es una opción sencilla para una transferencia puntual pequeña o mediana hacia Cloud Storage.

```bash
gcloud storage cp "*.csv" gs://mi-bucket/landing/
```

![[Pasted image 20260902151713.png|820]]

*Copia bajo demanda desde un entorno local hacia Cloud Storage.*

### Storage Transfer Service: transferencia administrada

Resulta apropiado cuando hay gran escala, recurrencia o necesidad de seguimiento centralizado. Puede trabajar con sistemas de archivos, almacenes de objetos y HDFS, además de orígenes como Amazon S3 o Azure Blob Storage. El servicio permite programar, ejecutar y monitorizar las transferencias.

![[Pasted image 20260902151742.png|820]]

*Transferencias administradas desde entornos locales y otras nubes.*

### El ancho de banda puede decidir la estrategia

Una estimación inicial es:

```text
tiempo ≈ tamaño_en_bits / ancho_de_banda_en_bits_por_segundo
```

El material del curso compara la transferencia de 1 TB:

| Enlace | Tiempo aproximado presentado en el curso |
|---|---:|
| 100 Gbit/s | ~2 minutos |
| 100 Mbit/s | ~30 horas |

La fórmula da un mínimo teórico. En la práctica influyen la latencia, el cifrado, las verificaciones, el protocolo, la concurrencia y la velocidad de lectura del origen. Si la estimación no cumple el plazo, se puede mejorar la conectividad, usar una transferencia administrada o recurrir a Transfer Appliance.

### DMS frente a Dataflow

| Situación | Elección razonable |
|---|---|
| Migrar una base relacional conservando el comportamiento de base de datos | **Database Migration Service** |
| Transformar documentos NoSQL, adaptar formatos o aplicar lógica compleja | **Dataflow** o una plantilla proporcionada |
| Alimentar analítica con cambios continuos de una base operativa | **Datastream**, directamente o junto con Dataflow |

DMS dispone de escenarios para motores como Oracle, MySQL, PostgreSQL y SQL Server, pero los pares origen-destino compatibles varían: antes de diseñar una migración se debe revisar la matriz vigente. El destino —por ejemplo, Cloud SQL, AlloyDB o BigQuery— depende de si la carga seguirá siendo transaccional o pasará a ser analítica.

![[Pasted image 20260902152436.png|920]]

> [!note] Cifras del material del curso
> La comparación visual usa referencias como “menos/más de 1 TB”, dispositivos de 7, 40 y 300 TB para Transfer Appliance y hasta 10 000 tablas por stream. Sirven para razonar sobre la solución, pero las cuotas y capacidades exactas deben comprobarse en la documentación vigente.

## 2. Datastream y la captura de datos modificados

Datastream es un servicio sin servidores para replicar cambios de bases operativas con baja latencia. Permite seleccionar esquemas, tablas y columnas, configurar la conectividad y enviar los datos a BigQuery o Cloud Storage. También puede alimentar una plantilla de Dataflow cuando hace falta procesar o enrutar los eventos.

```text
Base de datos → backfill histórico → lectura continua del log → eventos → destino
```

![[Pasted image 20260902152111.png|1050]]

### Casos de uso

1. Replicar directamente una base operativa en BigQuery para analítica.
2. Procesar los cambios con Dataflow antes de cargarlos.
3. Publicar cambios para arquitecturas controladas por eventos.
4. Combinar Datastream y Dataflow en una migración o sincronización personalizada.

### Backfill + CDC

- El **backfill** copia el estado histórico de los objetos seleccionados.
- **CDC** (*Change Data Capture*) lee el registro de transacciones para capturar cambios confirmados posteriores.
- Los cambios relevantes suelen ser `INSERT`, `UPDATE` y `DELETE`.
- Tras el backfill, la lectura continua evita tener que volver a copiar la tabla completa.

![[Pasted image 20260902152146.png|840]]

### ¿Qué registro lee CDC?

| Motor de origen | Mecanismo de registro |
|---|---|
| Oracle | LogMiner / registros de rehacer |
| MySQL | Binary log (`binlog`) |
| PostgreSQL | Decodificación lógica |
| SQL Server | Registro de transacciones |

La compatibilidad concreta de orígenes, versiones y métodos cambia con el servicio; esta tabla recoge el modelo mental del curso.

## 3. Eventos de cambio

Datastream transforma cada cambio en un evento estructurado. En Cloud Storage puede escribir archivos Avro o JSON; con BigQuery, los cambios se aplican en tablas de destino.

![[Pasted image 20260902152226.png|840]]

Un evento contiene dos grupos de información:

| Grupo | Ejemplos |
|---|---|
| Metadatos | Objeto o tabla, identificador, marcas de tiempo, método de lectura y procedencia |
| Payload | Pares columna-valor correspondientes al registro modificado |
| Contexto del origen | Base, esquema, tabla, tipo de cambio e identificadores del sistema |

### Ejemplo conceptual

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

> [!warning] Semántica de entrega
> Datastream entrega eventos **al menos una vez** y no garantiza un orden global. Por ello un consumidor personalizado debe poder deduplicar, ordenar mediante los metadatos y procesar de forma idempotente. El destino directo de BigQuery gestiona los eventos fuera de orden; si se escriben en Cloud Storage, esa responsabilidad recae en el procesamiento posterior.

### Tipos unificados

Datastream normaliza los tipos del origen en un esquema unificado basado en Avro. Así mantiene una representación común y sin pérdida siempre que sea posible, aunque cada destino pueda materializarla de forma distinta.

| Origen | Tipo original | Concepto unificado | Materialización ilustrativa |
|---|---|---|---|
| Oracle | `NUMBER` | Decimal | Decimal lógico de Avro |
| MySQL | `DECIMAL` | Decimal | Número en JSON |
| PostgreSQL | `NUMERIC` | Decimal | `NUMERIC` en BigQuery |
| SQL Server | `DECIMAL` | Decimal | Tipo numérico del destino |

La tabla ilustra la normalización; no sustituye las reglas de conversión oficiales para cada origen y destino.

## Puntos de examen

> [!tip] Qué distinguir
> - `gcloud storage`: copia directa y puntual.
> - Storage Transfer Service: transferencia grande, recurrente o administrada.
> - Transfer Appliance: el enlace de red no permite cumplir el plazo.
> - DMS: migración de una base relacional hacia un motor administrado.
> - Datastream: backfill y replicación continua mediante CDC.
> - Dataflow: transformación o enrutamiento durante el movimiento.
> - “Al menos una vez” implica diseñar para duplicados; “sin orden global” implica usar metadatos para reconstruir el orden necesario.

## Repaso activo

> [!question]- Una empresa debe copiar 600 GB una sola vez y dispone de una conexión fiable. ¿Qué opción probarías primero?
> `gcloud storage`, porque es una copia puntual, el volumen está por debajo de la referencia inicial de 1 TB y la conectividad es adecuada. El plazo real sigue siendo parte de la decisión.

> [!question]- Hay 80 TB en un centro de datos con un enlace inestable y una ventana de migración corta. ¿Qué cambia la decisión?
> El tiempo de red probablemente incumple la ventana. Transfer Appliance permite trasladar físicamente el conjunto; se debe comparar su logística con una mejora temporal de conectividad o Storage Transfer Service.

> [!question]- ¿Cómo se diferencian DMS, Datastream y Dataflow?
> DMS migra una base hacia un servicio de base de datos; Datastream replica sus cambios; Dataflow ejecuta lógica de procesamiento, transformación o enrutamiento.

> [!question]- ¿Por qué Datastream combina backfill y CDC?
> El backfill crea la copia histórica inicial y CDC incorpora después los cambios confirmados del log. Así el destino alcanza el estado presente y continúa sincronizado.

> [!question]- ¿Qué consecuencias tienen la entrega “al menos una vez” y la falta de orden global?
> Pueden aparecer duplicados y los eventos pueden llegar desordenados. Los consumidores personalizados deben ser idempotentes y aprovechar identificadores y marcas de tiempo para deduplicar u ordenar.

> [!question]- ¿Para qué sirve el esquema unificado de tipos?
> Separa la representación lógica de un dato de la sintaxis propia de cada motor y facilita que eventos de orígenes diferentes se consuman de forma coherente.

## Fuentes oficiales

- [Opciones de transferencia a Cloud Storage](https://cloud.google.com/storage-transfer/docs/transfer-options)
- [Descripción general de Database Migration Service](https://cloud.google.com/database-migration/docs/overview) y [bases compatibles](https://cloud.google.com/database-migration/docs/supported-databases)
- [Plantillas proporcionadas de Dataflow](https://cloud.google.com/dataflow/docs/guides/templates/provided-templates)
- [Descripción general de Datastream](https://cloud.google.com/datastream/docs/overview)
- [Comportamiento de Datastream](https://cloud.google.com/datastream/docs/behavior-overview) y [eventos y streams](https://cloud.google.com/datastream/docs/events-and-streams)
- [Plantilla Datastream to BigQuery de Dataflow](https://cloud.google.com/dataflow/docs/guides/templates/provided/datastream-to-bigquery)

[[00 - Índice|← Índice de la sección]] · [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/01 - Tareas y componentes de ingeniería de datos/03 - Data lake, BigQuery y gobierno|← Sección anterior]] · [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/03 - Patrón de extracción y carga (EL)/00 - Índice|Siguiente sección: patrón EL →]]
