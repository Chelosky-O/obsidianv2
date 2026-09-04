# 05 — Caso de uso real: optimizando marketing y supply chain

[[00 - Índice|Índice del sub-módulo]] · [[04 - BigQuery como motor central de procesamiento|← Anterior: BigQuery]]

> [!abstract] Resumen
> Cymbal lanzó una gran campaña de marketing para su nueva colección outdoor **"Evergreen"**. Para medir el **retorno real de la inversión**, su equipo de datos usó el **data lakehouse de Google Cloud** con el objetivo de ir más allá de las métricas simples de ventas y entender el **customer journey completo**. Este caso **integra los 4 componentes**: Cloud Storage + Apache Iceberg + BigQuery + AlloyDB.

## Los 3 pasos

### Paso 1 · Unified data analysis (Combinando datos con BigQuery)

Los analistas corrieron **una sola consulta federada** con BigQuery para unir datos de múltiples fuentes:

| Fuente | Datos |
|---|---|
| **AlloyDB** | Transacciones de ventas + perfiles de clientes (**estructurados**) |
| **Cloud Storage (Iceberg tables)** | Petabytes de clickstream (**semiestructurados**): el journey de cada cliente desde ad click → página de producto → checkout o abandono |

### Paso 2 · Insight discovery (Por qué los clientes no convertían)

El análisis reveló un **insight regional**:

- Clientes del **Pacific Northwest** mostraban fuerte interés en una **chaqueta impermeable**.
- Pero las **tasas de conversión eran bajas**.
- Al **unir los datos de comportamiento** con el **texto de las reseñas** en las Iceberg tables, descubrieron que todas las reseñas mencionaban un **set de colores que faltaba** y que **sí aparecían en los ads**.

### Paso 3 · Data-driven action (Ajustando inventario y marketing)

Con este insight, Cymbal actuó rápido:

1. **Actualizó el inventario** con los colores deseados de la chaqueta.
2. **Alertó al supply chain** para aumentar stock en esa región.
3. **Lanzó una campaña de ads dirigida** a los clientes interesados.

> [!example] Resultado
> No solo **recuperó ventas perdidas**, sino que también **mejoró el ROI de marketing y la experiencia del cliente**.

## El poder del lakehouse (resumen)

> [!important] Arquitectura completa
> Un data lakehouse construido con:
> - **Cloud Storage** como base,
> - **formatos abiertos** (Apache Iceberg),
> - **BigQuery** como motor,
> - **AlloyDB** para datos operacionales críticos,
>
> proporciona una arquitectura **potente, flexible y a prueba de futuro**. Permite aprovechar **todos los activos de datos** (de multimedia cruda a transacciones en tiempo real) para tomar **decisiones inteligentes basadas en datos de alta calidad**.

> [!success] Puntos de examen
> 1. El lakehouse integra **Cloud Storage + Iceberg + BigQuery + AlloyDB**.
> 2. **Federated query** de BigQuery une datos de varias fuentes en **una sola consulta**.
> 3. Origen de datos: **AlloyDB** (estructurado, transacciones/ perfiles) y **Cloud Storage/Iceberg** (semiestructurado, clickstream).
> 4. **Insight** típico: cruzar datos de comportamiento con reseñas (texto) → descubrir el **porqué** de la baja conversión.
> 5. **Acción data-driven:** ajustar inventario, supply chain y campañas → mejorar **ROI y experiencia**.
> 6. Beneficio: **sin duplicar datos ni ETL costoso**, todo en una plataforma unificada.

> [!warning] Trampa de examen
> El valor del lakehouse no es solo guardar datos: es **combinarlos** (AlloyDB + Iceberg/Cloud Storage) con una **consulta federada** de BigQuery para generar **insights accionables**.

## Preguntas de repaso

> [!question]- 1. ¿Qué componentes se juntan en el caso real?
> **Cloud Storage** (base) + **Apache Iceberg** (open format) + **BigQuery** (motor/federated query) + **AlloyDB** (datos operacionales).

> [!question]- 2. ¿Qué es una *federated query* y qué permite?
> Una consulta de BigQuery que **une datos de múltiples fuentes** (AlloyDB + Cloud Storage/Iceberg) **en una sola consulta**, sin duplicarlos.

> [!question]- 3. ¿Qué insight descubrió Cymbal y qué hizo?
> Reseñas mencionaban **colores que faltaban** en la chaqueta (aparecían en ads) → baja conversión. Ajustó inventario, supply chain y lanzó una campaña dirigida → mejoró ROI y experiencia.

## Fuentes oficiales

- [BigQuery federated queries](https://cloud.google.com/bigquery/docs/federated-queries-intro)
- [AlloyDB](https://cloud.google.com/alloydb/docs)
- [Apache Iceberg](https://iceberg.apache.org/)
