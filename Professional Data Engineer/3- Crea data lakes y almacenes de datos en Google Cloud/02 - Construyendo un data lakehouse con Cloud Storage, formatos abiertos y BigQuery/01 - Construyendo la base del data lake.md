# 01 — Construyendo la base del data lake

[[Professional Data Engineer/3- Crea data lakes y almacenes de datos en Google Cloud/02 - Construyendo un data lakehouse con Cloud Storage, formatos abiertos y BigQuery/00 - Índice|Índice del sub-módulo]] · [[02 - El enfoque moderno - Data lakehouse|← Módulo 1]] 

> [!abstract] Resumen
> Para un negocio online (ej. **Cymbal**), los datos son un activo crítico: historial de compras, clics en el sitio, logística de supply chain y rendimiento de campañas. Una **arquitectura de datos robusta** es esencial para decidir con datos, personalizar experiencias y optimizar operaciones. La **base del data lake** de un lakehouse en Google Cloud se construye, principalmente, con **Cloud Storage**.

## Almacenamiento para lakehouses en Google Cloud

Un data lakehouse combina las características clave de **data lakes** y **data warehouses**:

- **Flexibilidad y escalabilidad de un data lake** → almacenar datos crudos, no estructurados o estructurados.
- **Schema enforcement y rendimiento de consultas de un data warehouse**.

Este enfoque híbrido permite manejar **todas las necesidades de datos en un solo sistema unificado**.

### Cloud Storage: el almacenamiento primario

> [!important] Cloud Storage
> Es el **almacenamiento primario** de un lakehouse en Google Cloud. Es un servicio de **almacenamiento de objetos**:
> - Masivamente **escalable**.
> - Altamente **duradero**.
> - **Costo-efectivo**.
> - Permite guardar **casi cualquier tipo de archivo**, sin importar tamaño o formato.
>
> 💡 **Multi-cloud:** no estás limitado a Cloud Storage — si tu estrategia es multi-cloud, podés dejar los datos en el almacenamiento de otro proveedor mayor.

## Almacenar varios tipos de datos (multimodal)

Una de las mayores ventajas de Cloud Storage es poder almacenar **datos multimodales**: varios formatos y estructuras en **un solo lugar**.

| Tipo | Definición | Ejemplo (Cymbal) | Procesamiento |
|---|---|---|---|
| **Estructurado** | Altamente organizado en un formato predefinido (filas/columnas) | Listas de clientes, catálogos de productos, transacciones de ventas | Modelo predecible, fácil de buscar/analizar |
| **Semiestructurado** | Mezcla de estructurado y no estructurado: sin modelo rígido, pero con **tags/markers** que crean jerarquía de registros y campos | Detalles de pedidos en **JSON** (estructura consistente, con variaciones por producto) | Requiere parseo parcial |
| **No estructurado** | Sin estructura organizacional predefinida | Texto de reseñas de clientes, logs de chat de soporte, imágenes de productos | Más complejo de procesar; **insights clave** con analítica moderna y AI |

> [!note] Los tabs interactivos
> En el HTML, los 3 tipos (structured / semi-structured / unstructured) aparecen como **tabs** que el HTML no renderiza; los completé con las capturas que me pasaste.

> [!important] Sin necesidad de definir estructura antes
> Cloud Storage permite almacenar **datos crudos sin procesar tal cual llegan**, sin definir su estructura de antemano. Es crucial para Cymbal, que recolecta nuevos tipos de datos y quiere **analizarlos después** sin transformaciones inmediatas.

> [!success] Puntos de examen
> 1. **Cloud Storage** = almacenamiento primario del lakehouse (objects, escalable, durable, costo-efectivo).
> 2. Lakehouse = **flexibilidad del data lake + schema/rendimiento del data warehouse**.
> 3. **Multimodal:** guarda **estructurado, semiestructurado y no estructurado** en un mismo lugar.
> 4. **Estructurado** = filas/columnas (clientes, catálogos, ventas). **Semiestructurado** = tags + jerarquía (JSON). **No estructurado** = reseñas, logs, imágenes.
> 5. Puede ser **multi-cloud** (datos en otro proveedor).
> 6. **Sin schema previo:** almacenás crudo y transformás después.

> [!warning] Trampa de examen
> Cloud Storage **no es una base de datos** — es almacenamiento de **objetos**. La diferencia clave del lakehouse es que podés guardar **cualquier tipo de dato** crudo ahí, y luego **consultarlo con el motor de BigQuery** (schema-on-read para el lake).

## Preguntas de repaso

> [!question]- 1. ¿Cuál es el almacenamiento primario de un lakehouse en GCP y por qué?
> **Cloud Storage**: almacenamiento de objetos masivamente escalable, durable y costo-efectivo; guarda casi cualquier tipo de archivo sin importar tamaño/formato.

> [!question]- 2. ¿Qué tipos de datos puede almacenar y cómo se diferencian?
> **Estructurado** (filas/columnas), **semiestructurado** (tags + jerarquía, ej. JSON) y **no estructurado** (reseñas, logs, imágenes). Cloud Storage guarda los tres (multimodal).

> [!question]- 3. ¿Qué permite el lakehouse que el data lake puro no?
> Sumar **schema enforcement y rendimiento de consultas** del data warehouse a la **flexibilidad y bajo costo** del data lake, en una **plataforma unificada**.

## Fuentes oficiales

- [Introducción a Cloud Storage](https://cloud.google.com/storage/docs/introduction)
- [Data lakehouse con BigLake](https://cloud.google.com/bigquery/docs/biglake-introduction)
