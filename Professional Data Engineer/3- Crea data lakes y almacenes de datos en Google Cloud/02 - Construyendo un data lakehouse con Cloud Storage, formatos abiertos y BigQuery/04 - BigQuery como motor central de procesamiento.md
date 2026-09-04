# 04 — BigQuery como motor central de procesamiento

[[00 - Índice|Índice del sub-módulo]] · [[03 - Combinando datos operacionales en AlloyDB|← Anterior: AlloyDB]]

> [!abstract] Resumen
> **BigQuery** es el **motor central de analítica y consultas** del data lakehouse de Cymbal. Mientras Cloud Storage e Iceberg aportan la base flexible y **open-standard** para guardar grandes volúmenes de datos crudos y estructurados, **BigQuery es el motor de alto rendimiento que la activa**.

## Cómo se conecta

Al crear **Lakehouse tables**, Cymbal puede usar la **interfaz SQL familiar de BigQuery** para consultar **directa y seguramente** los datos formateados con **Iceberg** en su data lake de **Cloud Storage**.

```mermaid
flowchart LR
    GCS[Cloud Storage<br/>open-format] --> ICE[Apache Iceberg<br/>metadatos/estructura] --> BQ[BigQuery<br/>Lakehouse tables + SQL]
    BQ --> A[Analítica]
```

## El beneficio principal

> [!important] Sin duplicar datos ni ETL costoso
> Cymbal **no tiene que duplicar datos** ni ejecutar **procesos ETL costosos** para hacer analítica. Los analistas obtienen el **rendimiento y features de un data warehouse premium** consultando **directamente en su data lake open-format**.

## Almacenamiento nativo de BigQuery

BigQuery también ofrece su propio **almacenamiento nativo administrado y optimizado**. Cymbal puede usarlo para los datasets de acceso más frecuente ("**hot**") que requieren la máxima velocidad de consulta, como los **dashboards clave de rendimiento de marketing**.

> [!note] Plataforma unificada
> Esto da una **plataforma unificada** para analizar datos:
> - En el **data lake** (Cloud Storage + Iceberg), y/o
> - En el **almacenamiento nativo de BigQuery**,
>
> todo a través de una **única interfaz**.

> [!success] Puntos de examen
> 1. **BigQuery = motor central de analítica/consultas** del lakehouse.
> 2. **Lakehouse tables** permiten consultar con **SQL** los datos **Iceberg** en **Cloud Storage**.
> 3. **No duplica datos** ni requiere **ETL costoso** → analítica directa sobre el data lake open-format.
> 4. BigQuery da **rendimiento de data warehouse premium** sobre datos del data lake.
> 5. **Almacenamiento nativo de BigQuery** para datasets "**hot**" (máxima velocidad, ej. dashboards).
> 6. **Plataforma unificada:** data lake + native storage, una sola interfaz SQL.

> [!warning] Trampa de examen
> BigQuery **no mueve ni duplica** los datos del data lake: los consulta **en su lugar** (schema-on-read) vía Lakehouse tables. El almacenamiento nativo es **opcional** para datasets "hot".

## Preguntas de repaso

> [!question]- 1. ¿Qué rol cumple BigQuery en el lakehouse?
> Es el **motor central de analítica y consultas**: activa el data lake (Cloud Storage + Iceberg) con su **interfaz SQL** vía **Lakehouse tables**.

> [!question]- 2. ¿Qué evita este enfoque?
> **Duplicar datos** y ejecutar **procesos ETL costosos** para analítica → se consulta directo sobre el data lake open-format.

> [!question]- 3. ¿Para qué se usa el almacenamiento nativo de BigQuery?
> Para datasets de acceso frecuente ("**hot**") que requieren **máxima velocidad** de consulta (ej. dashboards de rendimiento).

## Fuentes oficiales

- [BigQuery](https://cloud.google.com/bigquery/docs)
- [BigLake](https://cloud.google.com/bigquery/docs/biglake-introduction)
