# 01 — Los clásicos: Data lakes y data warehouses

[[Professional Data Engineer/3- Crea data lakes y almacenes de datos en Google Cloud/01 - Introducción a la Ingeniería de Datos Moderna/00 - Índice|Índice del módulo]] · [[02 - El enfoque moderno - Data lakehouse|Siguiente: Data lakehouse →]]

> [!abstract] Resumen
> Durante años, las organizaciones dependieron de dos métodos principales para el almacenamiento de datos a gran escala: **data lakes** y **data warehouses**. Sirven a **propósitos diferentes** y están **optimizados para distintos tipos de datos y workloads**.

## Data lakes

> [!quote] Idea central
> Piensa en un data lake como un **vasto embalse de datos**: almacena enormes cantidades de datos **crudos en su formato nativo**. Podés volcar **cualquier tipo de dato**: estructurado, semiestructurado y no estructurado.

### Qué puede guardar (ejemplo Cymbal)

| Tipo | Ejemplo |
|---|---|
| **Estructurado** | Tablas de transacciones de la base de ventas |
| **Semiestructurado** | Logs JSON de los servidores web |
| **No estructurado** | Imágenes de productos, videos y reseñas de texto de clientes |

### Principio: schema-on-read

> [!important] "Guarda todo ahora, fíjate cómo usarlo después"
> Los data lakes se crean bajo la premisa de **"store everything now, figure out how to use it later"**. Aplicás **estructura al leer** (schema-on-read), no al almacenar.
> Esto los hace ideales para **exploración de datos, machine learning y big data processing** (ej. data scientists de Cymbal usando clickstream original para un motor de recomendación).

### Ventajas

1. **Flexibilidad:** almacena todo tipo de datos.
2. **Agilidad:** ingesta rápida.
3. **Escalabilidad:** puede crecer a escala de exabytes.
4. **Costo-efectividad:** usa **almacenamiento de objetos de bajo costo**.
5. **Soporte para analítica avanzada:** ideal para **entrenamiento de modelos AI/ML**.

### Desventajas

1. **Riesgo de "data swamp":** sin gobernanza, se convierte en una colección desorganizada de datos inutilizables.
2. **Complejidad de gestión:** requiere overhead significativo de mantenimiento.
3. **Análisis que consume tiempo:** los datos suelen necesitar limpieza y wrangling antes de usarse.
4. **Riesgos de seguridad:** los formatos de datos crudos pueden aumentar los riesgos de seguridad y cumplimiento.

## Data warehouses

> [!quote] Idea central
> Piensa en un data warehouse como un **almacén altamente organizado y optimizado para datos estructurados y de negocio**. A diferencia del data lake, **estructura los datos antes de cargarlos** (schema-on-write) y está optimizado para **consultas analíticas rápidas (BI/OLAP)** sobre una **fuente única y confiable**.

### Características

- Diseñado para **datos estructurados, seleccionados y de misión crítica**.
- **Schema-on-write**: la estructura se aplica al **cargar** los datos, no al leerlos.
- **Fuente única de verdad** para analítica y BI.
- **Altamente optimizado para consultas** (rendimiento, agregaciones, reportes).

### Ejemplo (Cymbal)

> [!example]
> El **departamento de finanzas** de Cymbal usa un data warehouse (ej. **BigQuery**) para **BI de alta velocidad** sobre **datos estructurados de ventas** (dashboards, reportes).

### Ventajas

1. **Rendimiento de consultas:** optimizado para BI/analítica rápida.
2. **Calidad y gobernanza de datos:** datos curados y documentados.
3. **Seguridad y cumplimiento:** control centralizado.
4. **Consistencia (ACID) y confiabilidad:** transacciones y datos limpios.

### Desventajas

1. **Menos flexible** para datos crudos o nuevos (exige schema).
2. **Más costoso** (almacenamiento cómputo optimizado para consultas).
3. **Cambios de schema difíciles**.
4. **Limitado a datos estructurados**; no apto para formatos crudos o no estructurados.

> [!success] Puntos de examen
> 1. Data lake = **datos crudos en formato nativo** (estructurado, semiestructurado, no estructurado).
> 2. Premisa: **"store everything now, figure out how to use it later"** → **schema-on-read**.
> 3. Ideal para **exploración, ML y big data**.
> 4. Ventajas: **flexibilidad, agilidad, escalabilidad, costo (object storage), analítica avanzada**.
> 5. Desventajas: **data swamp, complejidad de gestión, tiempo de análisis, riesgos de seguridad**.
> 6. Datos crudos y sin estructurar = bajo costo; pero **sin gobernanza** se vuelven un "data swamp".
> 7. **Data warehouse = datos estructurados y curados**, **schema-on-write**, **fuente única de verdad** para BI.
> 8. Diferencia clave: lake = **schema-on-read** (agilidad/costo); warehouse = **schema-on-write** (rendimiento/gobierno).
> 9. Ejemplo warehouse: finanzas de Cymbal con **BigQuery** para BI sobre ventas.

> [!warning] Trampa de examen
> "Data lake" ≠ "base de datos". Es depósito de datos crudos con **schema-on-read**; la estructura se aplica al leer, no al guardar. La falta de gobernanza lo convierte en un **data swamp**.

## Preguntas de repaso

> [!question]- 1. ¿Qué premisa sigue un data lake?
> **"Store everything now, figure out how to use it later."** Guardás datos crudos y aplicás estructura al leer (**schema-on-read**).

> [!question]- 2. Menciona 2 ventajas y 2 desventajas de un data lake.
> Ventajas: flexibilidad (todos los tipos de datos) y costo-efectividad (object storage de bajo costo). Desventajas: riesgo de data swamp y tiempo que consume el análisis/limpieza.

> [!question]- 3. ¿Qué diferencia a un data warehouse de un data lake?
> El **data warehouse** usa **schema-on-write** (estructura al cargar) y está optimizado para **BI/consultas** sobre datos **estructurados** de negocio. El **data lake** usa **schema-on-read** (estructura al leer) y guarda **datos crudos** de bajo costo y alta agilidad.

## Fuentes oficiales

- [Introducción a Cloud Storage](https://cloud.google.com/storage/docs/introduction)
