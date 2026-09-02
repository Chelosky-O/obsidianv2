## BigQuery

- En [[BigQuery]] #bigquery #seguridad, las **etiquetas de política** permiten controlar el acceso a nivel de columna. Úsalas cuando solo ciertos usuarios deban ver columnas sensibles.

- En [[BigQuery]] #bigquery #rendimiento, si los datos son jerárquicos y normalmente se consultan juntos, conviene usar **campos anidados y repetidos** en vez de mantener muchas tablas normalizadas.

- En [[BigQuery]] #bigquery #rendimiento, una **vista normal** vuelve a ejecutar la consulta sobre los datos originales cada vez que se utiliza.

- En [[BigQuery]] #bigquery #rendimiento, una **vista materializada** puede mejorar el rendimiento cuando se ejecutan repetidamente las mismas consultas complejas, porque precalcula y almacena resultados.

- En [[BigQuery]] #bigquery #sql, si necesitas calcular algo sobre varias filas pero obtener **un resultado para cada fila**, utiliza una **función analítica con `OVER`**.

- En [[BigQuery]] #bigquery #rendimiento, `LIMIT` **no reduce necesariamente los datos procesados**, porque se aplica al final de la consulta.

- En [[BigQuery]] #bigquery #rendimiento, intenta **filtrar los datos lo antes posible** para reducir la cantidad de datos procesados durante el resto de la consulta.

- En [[BigQuery]] #bigquery #rendimiento, evita los **self JOIN** cuando puedas. Las funciones analíticas pueden ser una alternativa más eficiente.

- En [[BigQuery]] #bigquery #rendimiento, es mejor realizar **inserciones y actualizaciones por lotes** que modificar filas individualmente.

- En [[BigQuery]] #bigquery #particiones, las tablas particionadas permiten que una consulta lea solamente una parte de los datos, mejorando rendimiento y reduciendo datos procesados.

- En [[BigQuery]] #bigquery, para analítica normalmente conviene **desnormalizar los datos**, ya que realizar JOIN grandes de forma repetida aumenta el costo y el tiempo de las consultas.

- En [[BigQuery]] #bigquery #views, utiliza una **vista** cuando quieras simplificar una consulta compleja para otros usuarios sin duplicar los datos.

- En [[BigQuery]] #bigquery #federatedquery, las **consultas federadas** permiten consultar datos de [[Cloud SQL]] directamente desde BigQuery sin copiarlos primero. Son útiles cuando esos datos cambian frecuentemente.


## Cloud Storage

- En [[Cloud Storage]] #cloudstorage, **Standard** está pensado para datos que se consultan frecuentemente.

- En [[Cloud Storage]] #cloudstorage, **Nearline** es adecuado para datos a los que se accede aproximadamente una vez al mes.

- En [[Cloud Storage]] #cloudstorage, **Coldline** es adecuado para datos a los que se accede muy poco, por ejemplo una vez por trimestre.

- En [[Cloud Storage]] #cloudstorage, **Archive** es adecuado para datos que prácticamente no se utilizan, por ejemplo una vez al año o menos.

- En [[Cloud Storage]] #cloudstorage #costos, utiliza **políticas de ciclo de vida** para mover automáticamente objetos a clases de almacenamiento más económicas cuando dejan de utilizarse frecuentemente.

- En [[Cloud Storage]] #cloudstorage #retencion, utiliza una **política de retención** cuando los archivos deban permanecer inmutables durante un período determinado.

- En [[Cloud Storage]] #cloudstorage, el **versionado de objetos no equivale a una política de retención**. Mantiene versiones anteriores, pero puede aumentar los costos.

- Para [[Dataproc]] #dataproc, [[Cloud Storage]] es una buena opción de almacenamiento porque los datos permanecen independientes de los nodos y pueden utilizarse desde distintos clústeres.


## Cloud SQL y Spanner

- En [[Cloud SQL]] #cloudsql #database, úsalo cuando necesitas una **base de datos relacional transaccional administrada** y la aplicación opera principalmente en una región.

- En [[Spanner]] #spanner #database, piensa en él cuando necesitas una **base de datos transaccional distribuida globalmente**.


## Bigtable

- En [[Bigtable]] #bigtable #database, úsalo para datos **clave-valor a gran escala** que necesitan alta capacidad de procesamiento.

- En [[Bigtable]] #bigtable #database, es especialmente adecuado para **series temporales**, como datos financieros, telemetría o métricas.


## Pipelines

- En #batch, cuando recibes archivos externos periódicamente, una arquitectura común es:

  `Fuente → Cloud Storage → ETL/ELT → BigQuery`

- En una #pipeline, primero identifica la **fuente de los datos**, luego el **destino** y después las transformaciones necesarias.

- Para procesamiento #streaming en Google Cloud, una arquitectura recomendada es:

  `Pub/Sub → Dataflow → BigQuery`


## Pub/Sub

- [[Pub/Sub]] #pubsub #streaming se utiliza para **recibir y distribuir eventos o mensajes** dentro de arquitecturas de procesamiento en tiempo real.

- En una pipeline de streaming, [[Pub/Sub]] normalmente funciona como la **entrada de eventos**, [[Dataflow]] los procesa y [[BigQuery]] almacena los resultados para análisis.


## Dataflow

- [[Dataflow]] #dataflow permite procesar datos tanto **por lotes como en streaming**.

- En [[Dataflow]] #dataflow #streaming, las **ventanas** permiten dividir un flujo infinito de datos en intervalos que pueden ser procesados.

- En [[Dataflow]] #dataflow #streaming, utiliza **ventanas fijas** cuando quieras obtener resultados por períodos constantes que no se superponen, por ejemplo ventas por hora.

- En #streaming, las **marcas de agua** ayudan a determinar hasta qué punto se espera que hayan llegado los eventos correspondientes a cierto momento.

- En #streaming, los **activadores** permiten controlar cuándo se emiten resultados de una ventana.


## Cloud Data Fusion

- [[Cloud Data Fusion]] #datafusion es útil cuando quieres crear pipelines de integración y transformación de datos mediante una **interfaz gráfica**.

- [[Cloud Data Fusion]] #datafusion es una buena opción cuando los usuarios tienen pocos conocimientos de programación pero necesitan crear pipelines.


## Dataproc

- [[Dataproc]] #dataproc se utiliza para ejecutar cargas de trabajo basadas en tecnologías como **Apache Spark y Hadoop**.

- En [[Dataproc Serverless]] #dataproc, puedes ejecutar trabajos de Spark sin tener que aprovisionar ni administrar clústeres manualmente.


## Batch

- [[Batch]] #batch es adecuado para ejecutar **trabajos por lotes de larga duración**.

- [[Batch]] #batch administra automáticamente el aprovisionamiento de recursos necesarios para ejecutar los trabajos.

- Si un trabajo puede durar horas o días y no quieres administrar infraestructura, considera [[Batch]].


## Cloud Composer

- [[Cloud Composer]] #composer #pipelines se utiliza para **orquestar pipelines y tareas dependientes**.

- [[Cloud Composer]] #composer está basado en **Apache Airflow**.

- Usa [[Cloud Composer]] cuando tengas un flujo como:

  `Detectar archivo → Ejecutar Dataflow → Validar BigQuery → Eliminar archivo`

- [[Cloud Scheduler]] sirve para ejecutar algo a una hora determinada, pero **no reemplaza un orquestador de pipelines** como [[Cloud Composer]].


## Cloud Build

- [[Cloud Build]] #cloudbuild #cicd permite automatizar **compilación, pruebas e implementación** cuando cambia el código.

- En pipelines de datos, [[Cloud Build]] puede utilizarse para detectar cambios en un repositorio y desplegar automáticamente nuevas versiones.

- [[Terraform]] #terraform sirve para automatizar **infraestructura**, pero no es por sí solo una solución de CI/CD.


## IAM

- En [[IAM]] #iam #seguridad, aplica siempre el **principio de mínimo privilegio**: entrega solamente los permisos que un usuario necesita.

- En [[IAM]] #iam, evita entregar roles demasiado amplios si existen roles predefinidos que cumplen exactamente el requisito.

- En Google Cloud #iam, las políticas aplicadas a niveles superiores de la jerarquía pueden ser **heredadas por los recursos inferiores**.

- Si distintas regiones necesitan políticas diferentes, puedes organizar proyectos utilizando **carpetas por región** y aplicar las políticas en esas carpetas.


## Sensitive Data Protection

- [[Sensitive Data Protection]] #dlp #seguridad permite **detectar, clasificar y proteger información sensible**.

- [[Sensitive Data Protection]] #dlp utiliza **infotipos** para identificar información como números de tarjetas, teléfonos o correos electrónicos.

- Para detectar PII, utilizar infotipos suele ser más confiable que depender solamente del nombre de las columnas o crear expresiones regulares manualmente.

- [[Sensitive Data Protection]] puede utilizarse para **ocultar o enmascarar datos sensibles** antes de utilizarlos para análisis.


## KMS / HSM / EKM

- [[Cloud KMS]] #kms administra claves criptográficas dentro de Google Cloud.

- [[Cloud HSM]] #kms utiliza módulos de seguridad de hardware para almacenar claves dentro de Google Cloud.

- [[Cloud EKM]] #kms se utiliza cuando necesitas mantener tus **claves fuera de Google Cloud** pero administrarlas desde Google Cloud.


## Dataplex

- [[Dataplex]] #dataplex #governance permite organizar y descubrir datos distribuidos entre diferentes sistemas.

- [[Dataplex]] #dataplex ayuda a crear una **vista centralizada de los datos** para facilitar su descubrimiento y comprensión.

- En [[Dataplex]] #dataplex, un **lake representa un dominio de datos o unidad de negocio**, no necesariamente una tecnología específica de almacenamiento.

- Un lake de [[Dataplex]] puede contener datos tanto de [[Cloud Storage]] como de [[BigQuery]].

- En [[Dataplex]] #dataplex, utiliza una **Raw Zone** para datos sin procesar.

- En [[Dataplex]] #dataplex, utiliza una **Curated Zone** para datos que ya fueron procesados y preparados para su consumo.

- En [[Dataplex]] #dataplex #troubleshooting, si un archivo no aparece durante el descubrimiento, revisa si existe un **patrón de exclusión** que esté haciendo que Dataplex lo ignore.

- [[Dataplex]] puede descubrir y catalogar automáticamente determinados formatos de archivos.


## Data Catalog y metadatos

- En [[Data Catalog]] #datacatalog #metadata, las **etiquetas permiten agregar metadatos a los datos** sin modificar las tablas originales.

- Los metadatos permiten indicar información como **propietario, descripción o propósito de un conjunto de datos**.

- Agregar buenos metadatos mejora la **búsqueda, descubrimiento y comprensión de los datos**.

- En una arquitectura de datos grande, los metadatos evitan que los consumidores tengan que preguntar constantemente a los productores qué significa cada dato.


## Migraciones

- En #migration, antes de elegir cómo transferir datos considera:

  - Cantidad de datos.
  - Ancho de banda disponible.
  - Confiabilidad de la red.
  - Tiempo disponible para completar la migración.

- [[Transfer Appliance]] #migration es útil cuando tienes **cantidades enormes de datos, poco ancho de banda y un plazo limitado**.

- Para cientos de TB con una conexión lenta, puede ser más eficiente realizar una transferencia física mediante [[Transfer Appliance]] que intentar subir todos los datos por Internet.


## Machine Learning

- En [[BigQuery ML]] #bigqueryml #machinelearning, tener **más datos del mismo tipo no necesariamente mejora un modelo** si esos datos no representan correctamente el problema del negocio.

- En #machinelearning, la **ingeniería de atributos** consiste en seleccionar, transformar o combinar variables para representar mejor el problema que quieres modelar.

- Si un modelo no representa bien el objetivo empresarial, considera mejorar los **atributos de entrada** antes de simplemente aumentar el tiempo de entrenamiento.

- La ingeniería de atributos puede incluir **combinar columnas existentes para crear nuevas variables más útiles**.

# Analytics Hub

- [[Analytics Hub]] #analyticshub es una herramienta conveniente para compartir datos con socios. Los ingenieros de datos tendrán control sobre lo que las personas pueden hacer con sus datos. Analytics Hub también puede convertir un centro de costos en un centro de ganancias con la monetización de datos.