# Quiz — The Extract, Transform, and Load Data Pipeline Pattern

> [!success] Resultado del intento
> **80 % — aprobado**. Respondiste correctamente 4 de 5 preguntas, justo el mínimo requerido.

[[00 - Índice|← Índice de la sección]] · [[02 - Streaming con Pub Sub y Dataflow|← Streaming y Dataflow]] · [[Professional Data Engineer/2- Introducción a la ingeniería de datos en Google Cloud/06 - Técnicas de automatización/00 - Índice|Siguiente sección →]]

| Pregunta | Tema                       | Resultado |
| -------: | -------------------------- | :-------: |
|        1 | Data Fusion                |     ✅     |
|        2 | Spark serverless           |     ✅     |
|        3 | Dataprep                   |     ✅     |
|        4 | Dataflow templates         |     ✅     |
|        5 | BigQuery frente a Bigtable |     ✅     |

## 1. Pipelines visuales complejos

**¿Qué servicio de Google Cloud es el más adecuado para compilar canalizaciones de datos complejas con una interfaz visual de arrastrar y soltar?**

A. Dataproc  
B. Dataflow  
C. Dataprep  
D. Data Fusion

> [!success]- Respuesta y explicación
> **D. Data Fusion.**
>
> Data Fusion Studio ofrece una interfaz visual de arrastrar y soltar, conectores, transformaciones y múltiples sinks. Dataprep se enfoca en preparación mediante recetas; Dataproc y Dataflow son principalmente motores de procesamiento.

## 2. Desarrollo interactivo con Spark serverless

**¿Cuál de las siguientes características hace que Dataproc Serverless para Spark sea ideal para la exploración y el desarrollo interactivos?**

A. Procedimientos externos de BigQuery  
B. Integración en JupyterLab  
C. Plantillas de flujos de trabajo  
D. Contenedores personalizados

> [!success]- Respuesta y explicación
> **B. Integración en JupyterLab.**
>
> Las sesiones de notebook permiten escribir, ejecutar y explorar Spark interactivamente. Los contenedores personalizan el runtime y los Workflow Templates coordinan trabajos; no son el entorno de exploración.
>
> En la documentación actual, Dataproc Serverless aparece como **Managed Service for Apache Spark serverless**.

## 3. Preparación visual mediante recetas

**¿Qué servicio de Google Cloud está diseñado específicamente para la transformación de datos sin servidores ni código usando recetas?**

A. Data Fusion  
B. Dataprep  
C. Dataflow  
D. Dataproc

> [!success]- Respuesta y explicación
> **B. Dataprep.**
>
> Las pistas son **sin código** y **recetas**. Dataflow ejecuta pipelines de Apache Beam; Dataproc procesa con Spark/Hadoop; Data Fusion diseña pipelines completos mediante una interfaz visual.
>
> [!note] Terminología actual
> Dataprep es el nombre histórico esperado por este quiz. La experiencia nativa actual de preparación visual está integrada en BigQuery.

## 4. Plantillas de Dataflow

**¿Cuál es la principal ventaja de usar plantillas de Dataflow?**

A. Permite la integración directa con APIs externas.  
B. Reemplaza la necesidad de SQL en las transformaciones de datos.  
C. Permite reutilizar y parametrizar las canalizaciones.  
D. Automatiza la migración de datos desde bases de datos locales.

> [!success]- Respuesta y explicación
> **C. Permite reutilizar y parametrizar las canalizaciones.**
>
> Una plantilla empaqueta el pipeline para desplegarlo repetidamente y acepta parámetros para cambiar entradas, salidas u otras opciones. No añade integraciones externas automáticamente, no reemplaza SQL y no es una herramienta de migración por sí sola.

## 5. Streaming con latencia de milisegundos

**¿Qué servicio de Google Cloud se recomienda para controlar canalizaciones de datos de transmisión que requieran análisis con latencia de milisegundos?**

A. Dataproc  
B. BigQuery  
C. Dataflow  
D. Bigtable

> [!success]- Respuesta y explicación
> **D. Bigtable.**
>
> Bigtable ofrece lecturas y escrituras por clave con latencia de milisegundos y alto throughput. **Dataflow procesa** el flujo, pero **Bigtable sirve** los resultados con la latencia pedida. BigQuery está orientado a analítica SQL y Dataproc a cargas Spark/Hadoop.

## Clave rápida

> [!example]- Mostrar respuestas
> **1D · 2B · 3B · 4C · 5D**

## Recursos verificados

- [Cloud Data Fusion Studio](https://docs.cloud.google.com/data-fusion/docs/concepts/studio-overview?hl=es-419)
- [Managed Service for Apache Spark serverless](https://docs.cloud.google.com/managed-spark/docs/serverless-overview)
- [Dataflow templates](https://docs.cloud.google.com/dataflow/docs/concepts/dataflow-templates)
- [Bigtable](https://docs.cloud.google.com/bigtable/docs/overview)
