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

### Costos y cargas de trabajo

- En [[BigQuery]] #bigquery #costos, el modelo **a pedido** cobra según el consumo realizado.
- En [[BigQuery]] #bigquery #costos, si necesitas capacidad adicional solamente durante un **período breve de alta demanda**, el material del curso recomienda utilizar capacidad temporal en vez de asumir compromisos largos.
- En [[BigQuery]] #bigquery #costos, si la demanda de capacidad es estable y predecible durante períodos largos, tiene más sentido evaluar capacidad reservada o compromisos que pagar continuamente por picos de consumo.
- En [[BigQuery]] #bigquery #batch, las **consultas interactivas** buscan ejecutarse inmediatamente.
- En [[BigQuery]] #bigquery #batch, las **consultas por lotes** pueden esperar hasta que haya recursos disponibles.
- En [[BigQuery]] #bigquery #cuotas, si tienes miles de consultas de reportes ejecutándose al mismo tiempo y alcanzas el límite de consultas simultáneas, mueve las consultas que no son urgentes a **modo batch**.
- En [[BigQuery]] #bigquery #cuotas, aumentar o reservar capacidad no necesariamente soluciona un **límite de concurrencia de consultas**.
- En [[BigQuery]] #bigquery #views, ejecutar las consultas mediante una vista tampoco evita el límite de consultas simultáneas.


## Cloud Storage

- En [[Soluciones de Almacenamiento de Google]] #cloudstorage, **Standard** está pensado para datos que se consultan frecuentemente.

- En [[Soluciones de Almacenamiento de Google]] #cloudstorage, **Nearline** es adecuado para datos a los que se accede aproximadamente una vez al mes.

- En [[Soluciones de Almacenamiento de Google]] #cloudstorage, **Coldline** es adecuado para datos a los que se accede muy poco, por ejemplo una vez por trimestre.

- En [[Soluciones de Almacenamiento de Google]] #cloudstorage, **Archive** es adecuado para datos que prácticamente no se utilizan, por ejemplo una vez al año o menos.

- En [[Soluciones de Almacenamiento de Google]] #cloudstorage #costos, utiliza **políticas de ciclo de vida** para mover automáticamente objetos a clases de almacenamiento más económicas cuando dejan de utilizarse frecuentemente.

- En [[Soluciones de Almacenamiento de Google]] #cloudstorage #retencion, utiliza una **política de retención** cuando los archivos deban permanecer inmutables durante un período determinado.

- En [[Soluciones de Almacenamiento de Google]] #cloudstorage, el **versionado de objetos no equivale a una política de retención**. Mantiene versiones anteriores, pero puede aumentar los costos.

- Para [[Dataproc]] #dataproc, [[Soluciones de Almacenamiento de Google]] es una buena opción de almacenamiento porque los datos permanecen independientes de los nodos y pueden utilizarse desde distintos clústeres.


## Cloud SQL y Spanner

- En [[Cloud SQL]] #cloudsql #database, úsalo cuando necesitas una **base de datos relacional transaccional administrada** y la aplicación opera principalmente en una región.
- En [[Spanner]] #spanner #database, piensa en él cuando necesitas una **base de datos transaccional distribuida globalmente**.

### Cloud SQL - Alta disponibilidad

- En [[Cloud SQL]] #cloudsql #highavailability, configura **alta disponibilidad (HA)** cuando necesites minimizar el tiempo de inactividad de una base de datos transaccional.
- En una configuración HA, [[Cloud SQL]] mantiene una instancia principal y una secundaria.
- Si la instancia principal falla, Google Cloud puede realizar **failover automáticamente** hacia la secundaria.
- Una **réplica de lectura** ayuda a distribuir operaciones de lectura, pero no reemplaza una instancia HA cuando necesitas mantener disponible una base de datos completa de lectura y escritura.
- Los **backups** permiten recuperar datos perdidos o antiguos, pero restaurarlos toma tiempo, por lo que no proporcionan disponibilidad continua.
- Tener una mayor cantidad de backups **no aumenta por sí solo la disponibilidad** de la base de datos.


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

### Monitoring y troubleshooting

- En [[Dataflow]] #dataflow #monitoring, usa alertas de [[Cloud Monitoring]] para detectar problemas de forma **proactiva** en vez de revisar manualmente logs o dashboards.
- En [[Dataflow]] #dataflow #monitoring, una alerta basada en **system lag** puede avisarte cuando una pipeline comienza a retrasarse más de lo esperado.
- En [[Dataflow]] #dataflow #troubleshooting, un error `HOT_KEY` indica que una clave concentra demasiados datos y está generando un cuello de botella.
- En [[Dataflow]] #dataflow #troubleshooting, para resolver una **hot key**, intenta distribuir los datos de manera más uniforme entre las claves.
- En [[Dataflow]] #dataflow #troubleshooting, agregar más workers **no soluciona necesariamente una hot key**, porque el problema está en cómo se distribuyen los datos.
- En [[Dataflow]] #dataflow, **Dataflow Shuffle** permite particionar y agrupar datos por clave de forma escalable y tolerante a errores.

### Disaster Recovery

- En [[Dataflow]] #dataflow #disasterrecovery, las **snapshots** permiten guardar el estado actual de una pipeline de streaming.
- Una snapshot permite iniciar posteriormente un nuevo trabajo de [[Dataflow]] utilizando el **estado guardado**.
- Una plantilla de Dataflow facilita recrear una pipeline, pero **no conserva su estado**, por lo que no reemplaza una snapshot para recuperación ante desastres.
- Para diseñar recuperación ante desastres considera [[RPO y RTO]] además del costo.

### Datos erróneos

- En [[Dataflow]] #dataflow #dataquality, si una transformación recibe datos válidos y erróneos, puedes utilizar una **salida complementaria** para capturar los registros erróneos.
- Evita volver a leer toda la entrada únicamente para procesar los datos erróneos.
- Evita crear dos pipelines completas que lean los mismos datos solamente para separar registros válidos y erróneos.


## Cloud Data Fusion

- [[Cloud Data Fusion]] #datafusion es útil cuando quieres crear pipelines de integración y transformación de datos mediante una **interfaz gráfica**.

- [[Cloud Data Fusion]] #datafusion es una buena opción cuando los usuarios tienen pocos conocimientos de programación pero necesitan crear pipelines.


## Dataproc

- [[Dataproc]] #dataproc se utiliza para ejecutar cargas de trabajo basadas en tecnologías como **Apache Spark y Hadoop**.
- En [[Dataproc Serverless]] #dataproc, puedes ejecutar trabajos de Spark sin tener que aprovisionar ni administrar clústeres manualmente.

### Optimización de recursos

- En [[Dataproc]] #dataproc #costos, los **clústeres persistentes** garantizan capacidad disponible, pero generan costos aunque los recursos no se estén utilizando.
- En [[Dataproc]] #dataproc #costos, los **clústeres basados en trabajos o efímeros** se crean cuando son necesarios y pueden eliminarse después de finalizar el trabajo.
- En [[Dataproc]] #dataproc #costos, utiliza **clústeres efímeros** cuando tengas varios trabajos pequeños e independientes que puedan ejecutarse en paralelo.
- Los clústeres efímeros también ayudan a evitar que las configuraciones o recursos de distintos trabajos **interfieran entre sí**.
- En [[Dataproc]] #dataproc #autoscaling, el escalado automático es especialmente adecuado para **clústeres de un solo trabajo**.
- En [[Dataproc]] #dataproc #autoscaling, ejecutar trabajos de tamaños muy diferentes en el mismo clúster puede provocar interferencias en el escalamiento.
- Si un clúster de [[Dataproc]] está inactivo, puede ser más rentable **eliminarlo** que mantenerlo funcionando reducido al mínimo.


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

### DAGs y repetibilidad

- En [[Cloud Composer]] #composer #airflow, los workflows pueden definirse como **DAGs (Directed Acyclic Graphs)** utilizando Python.
- En un DAG, diseña cada tarea para que tenga **una sola responsabilidad**.
- Una pipeline compleja debería construirse combinando varias **tareas pequeñas y atómicas**.
- Diseña las tareas para que sean **idempotentes**: ejecutarlas nuevamente debería producir un resultado coherente sin generar efectos secundarios inesperados.
- Para procesos repetibles, evita utilizar `now()` como referencia para determinar los datos que se procesarán, porque cada ejecución podría producir resultados distintos.
- En tareas que pueden volver a ejecutarse, evita utilizar `INSERT` si esto puede duplicar registros. El material recomienda utilizar **UPSERT** cuando corresponda.

## Cloud Build

- [[Cloud Build]] #cloudbuild #cicd permite automatizar **compilación, pruebas e implementación** cuando cambia el código.

- En pipelines de datos, [[Cloud Build]] puede utilizarse para detectar cambios en un repositorio y desplegar automáticamente nuevas versiones.

- [[Terraform]] #terraform sirve para automatizar **infraestructura**, pero no es por sí solo una solución de CI/CD.

## Cloud Monitoring

- [[Cloud Monitoring]] #monitoring #observability permite supervisar **rendimiento, disponibilidad y estado** de aplicaciones e infraestructura.
- [[Cloud Monitoring]] proporciona métricas y dashboards para observar los recursos de Google Cloud.
- Utiliza **alertas** para recibir notificaciones automáticamente cuando una métrica alcanza un umbral determinado.
- Para sistemas en producción, es mejor configurar alertas que depender de revisar manualmente dashboards o registros.
- [[Cloud Monitoring]] puede utilizarse para supervisar pipelines de [[Dataflow]], por ejemplo detectando aumentos en el **system lag**.

## Cloud Logging

- [[Cloud Logging]] #logging #observability permite consultar y analizar registros generados por aplicaciones y servicios de Google Cloud.
- Utiliza [[Cloud Logging]] para investigar qué ocurrió después de detectar un problema.
- En general:
  - **Monitoring → métricas, estado y alertas.**
  - **Logging → registros y análisis de eventos.**
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

- Un lake de [[Dataplex]] puede contener datos tanto de [[Soluciones de Almacenamiento de Google]] como de [[BigQuery]].

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

## Disaster Recovery y alta disponibilidad

- #disasterrecovery, diseña los sistemas considerando fallas desde el principio y no solamente después de que ocurran.
- Para recuperación ante desastres considera:
  - **RPO (Recovery Point Objective):** cuánto dato puedes permitirte perder.
  - **RTO (Recovery Time Objective):** cuánto tiempo puedes permitirte estar fuera de servicio.
  - **Costo** de la estrategia de recuperación.
- Ejecutar o almacenar datos en múltiples zonas o regiones puede mejorar la tolerancia a fallos.
- **Alta disponibilidad y backup no son lo mismo**:
  - HA → busca mantener el servicio disponible ante una falla.
  - Backup → permite recuperar datos después de una pérdida o corrupción.
- En [[Dataflow]], las **snapshots** ayudan a recuperar una pipeline desde un estado guardado.
- En [[Cloud SQL]], la **alta disponibilidad** permite failover automático hacia una instancia secundaria.

## Machine Learning

- En [[BigQuery ML]] #bigqueryml #machinelearning, tener **más datos del mismo tipo no necesariamente mejora un modelo** si esos datos no representan correctamente el problema del negocio.

- En #machinelearning, la **ingeniería de atributos** consiste en seleccionar, transformar o combinar variables para representar mejor el problema que quieres modelar.

- Si un modelo no representa bien el objetivo empresarial, considera mejorar los **atributos de entrada** antes de simplemente aumentar el tiempo de entrenamiento.

- La ingeniería de atributos puede incluir **combinar columnas existentes para crear nuevas variables más útiles**.

# Analytics Hub

- [[Analytics Hub]] #analyticshub es una herramienta conveniente para compartir datos con socios de manera segura y eficiente. Los ingenieros de datos tendrán control sobre lo que las personas pueden hacer con sus datos. Analytics Hub también puede convertir un centro de costos en un centro de ganancias con la monetización de datos.