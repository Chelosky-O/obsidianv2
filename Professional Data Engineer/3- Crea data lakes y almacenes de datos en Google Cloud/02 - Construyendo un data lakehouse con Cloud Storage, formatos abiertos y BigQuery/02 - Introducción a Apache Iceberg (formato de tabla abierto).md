# 02 — Introduction to Apache Iceberg (open table format)

[[00 - Índice|Índice del sub-módulo]] · [[01 - Building a data lake foundation|← Anterior: Data lake foundation]]

> [!abstract] Resumen
> Cloud Storage es excelente para **guardar archivos crudos**, pero un data lakehouse necesita una forma de dar **estructura y rendimiento** a esos datos. Ahí entran los **open table formats** — y **Apache Iceberg** es un ejemplo líder. Agega una **capa de metadatos y estructura** sobre los archivos en Cloud Storage, actuando como **índice y catálogo** de los archivos de datos.
> 💡 **No mueve tus datos**: los organiza y gestiona.

## El problema que resuelve

> [!example] Ejemplo Cymbal
> Cymbal tiene millones de pedidos como archivos en Cloud Storage. Si quieren consultarlos eficientemente (ej. pedidos de la última semana de clientes de una región específica), las consultas basadas en archivos son **lentas**: tienen que **escanear todos los archivos** para encontrar la información relevante.

## Cómo lo resuelve Iceberg

Iceberg agrega una capa de **metadatos y estructura** sobre los archivos de Cloud Storage:

- **No mueve los datos** → los organiza y gestiona.
- Actúa como **índice y catálogo** de los archivos.

## ¿Qué proporciona?

| Feature | Qué hace |
|---|---|
| **Schema evolution** | Permite **evolucionar el esquema** (agregar/renombrar columnas) **sin interrumpir** datos ni aplicaciones. |
| **Hidden partitioning** | **AUTOMATIZA el particionado** para consultas eficientes; los analistas **no se preocupan** por la organización subyacente de archivos. |
| **Time travel** | Permite **consultar versiones pasadas** de los datos. Si un reporte se corrió el mes pasado, podés **reproducir el estado exacto** de ese momento → **auditoría y debugging**. |
| **Atomic transactions** | Habilita **operaciones concurrentes confiables**: múltiples procesos pueden **leer y escribir** las mismas tablas **sin corrupción de datos**. |

## El beneficio

> [!important] Data warehouse benefits sobre tu data lake
> Al usar **Apache Iceberg con Cloud Storage**, Cymbal puede tratar su colección de archivos como **tablas consultables y de alto rendimiento**, llevando los **beneficios de un data warehouse** a su **data lake flexible**.

> [!success] Puntos de examen
> 1. **Apache Iceberg** = open table format que agrega **capa de metadatos/estructura** sobre archivos en Cloud Storage.
> 2. **No mueve los datos**; actúa como **índice y catálogo**.
> 3. Resuelve el problema de **escanear todos los archivos** en consultas tipo archivo.
> 4. Features: **schema evolution**, **hidden partitioning**, **time travel**, **atomic transactions**.
> 5. **Hidden partitioning** = particionado automático (el analista no se preocupa por la organización de archivos).
> 6. **Time travel** = consultar versiones pasadas (auditoría/debugging).
> 7. **Atomic transactions** = lecturas/escrituras concurrentes sin corrupción.
> 8. Convierte un **data lake** en **tablas consultables** (beneficio de warehouse sin sacrificar flexibilidad).

> [!warning] Trampa de examen
> Iceberg **no es un motor de almacenamiento ni una base de datos** — es un **formato de tabla abierto** que añade **metadatos** sobre archivos del data lake para darles **estructura, particionado y transacciones**. Iceberg **no mueve** tus datos.

## Preguntas de repaso

> [!question]- 1. ¿Qué problema resuelve Apache Iceberg y cómo?
> Evita el escaneo completo de archivos al consultar. Agrega una **capa de metadatos/estructura** (índice/catálogo) sobre los archivos de Cloud Storage, **sin moverlos**.

> [!question]- 2. Menciona 3 features de Iceberg.
> **Schema evolution** (evolucionar esquema sin romper), **hidden partitioning** (particionado automático) y **time travel** (consultar versiones pasadas). *(También: atomic transactions).*

> [!question]- 3. ¿Qué significa "hidden partitioning" y por qué importa?
> Iceberg **particiona automáticamente** para consultas eficientes; el analista **no tiene que preocuparse** por la organización física de los archivos.

## Fuentes oficiales

- [Apache Iceberg](https://iceberg.apache.org/)
- [Introducción a BigLake](https://cloud.google.com/bigquery/docs/biglake-introduction)
