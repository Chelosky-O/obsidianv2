
## Cloud Storage

Los datos no estructurados suelen ser ideales para almacenarse en Cloud Storage.
En Cloud Storage, se accede a los objetos con solicitudes HTTP, incluidas solicitudes GET con alcance específico para recuperar porciones de los datos.

Los objetos tienen metadatos pero los objetos en sí mismos se manipulan como bytes sin estructura.

La escala del sistema permite entregar contenido estático de gran tamaño y aceptar contenido subido por los usuarios como videos, fotos y archivos.


Cada objeto puede tener un tamaño de hasta **5 terabytes**.

Cloud Storage se diseñó para tener disponibilidad y ser durable, escalable y coherente.
Es una solución ideal para alojar sitios web estáticos y almacenar imágenes videos, objetos y BLOB así como cualquier dato no estructurado.

Cloud Storage tiene cuatro clases de almacenamiento principales Standard Storage, Nearline Storage Coldline Storage y Archive Storage. Las clases se diferencian según el período esperado de acceso a los objetos.

## Opciones para almacenar data estructurada

![[Pasted image 20260902144358.png]]

No hay una opción única para todos y tu elección de soluciones de almacenamiento y bases de datos dependerá de la aplicación y la carga de trabajo.

## Cloud SQL 
Es el servicio de base de datos relacional administrado de Google Cloud.

## AlloyDB 
Es un servicio de base de datos de PostgreSQL de alto rendimiento y completamente administrado de Google Cloud.

## Spanner 
Es el servicio de base de datos relacional completamente administrado de Google Cloud que ofrece coherencia sólida y escalabilidad horizontal.

## Firestore
Es una base de datos de documentos NoSQL rápida completamente administrada y sin servidores que proporciona ajuste de escala automático alto rendimiento y facilidad para el desarrollo de aplicaciones.

## BigQuery 
Es un almacén de datos empresarial completamente administrado y sin servidores para el análisis de datos.

## Bigtable 
Es un servicio de base de datos NoSQL de alto rendimiento.
Bigtable se diseñó para buscar pares clave-valor rápidamente y admite una latencia coherente inferior a 10 milisegundos.



# Datalakes y Data warehouse

# Datalake
Un data lake es un repositorio amplio que permite almacenar datos sin procesar en diversos formatos, como no estructurados semiestructurados y estructurados.
Funciona como una solución de almacenamiento centralizado para diversos tipos de datos y admite casos de uso flexibles, como la ciencia de datos las aplicaciones y la toma de decisiones empresariales.

# Data warehouse
Un Data warehouse es un repositorio estructurado diseñado para almacenar datos agregados y procesados previamente que provienen de varias fuentes.

Se usa principalmente para el análisis empresarial a largo plazo y permite realizar con eficiencia consultas e informes para tomar decisiones bien fundamentadas.

A menudo, operan como sistemas independientes de otras soluciones de almacenamiento de datos.

---
BigQuery es un Data warehouse empresarial completamente administrado y sin servidores para el análisis de datos.
Tiene funciones integradas, como aprendizaje automático análisis geoespacial e inteligencia empresarial.

BigQuery puede analizar terabytes de datos en segundos y petabytes en minutos.

BigQuery es una excelente solución de procesamiento analítico en línea (OLAP) de cargas de trabajo para explorar y procesar macrodatos.

BigQuery también es apropiado para generar informes con herramientas de inteligencia empresarial.

BigQuery tiene muchas opciones fáciles de usar para acceder a los datos.

![[Pasted image 20260902144908.png]]

La primera es el editor de SQL de la consola de Google Cloud.
La segunda es la herramienta de línea de comandos de bq que es parte del SDK de Cloud.
La última opción es una API de REST sólida que admite llamadas en 7 lenguajes de programación.

BigQuery organiza tablas de datos en unidades llamadas conjuntos de datos.
Estos conjuntos de datos están en el alcance de tu proyecto de Google Cloud.

Cuando se hace  referencia a una tabla desde la línea de comandos en las consultas en SQL o en el código se hará referencia a ella con la construcción project.dataset.table.

![[Pasted image 20260902144954.png]]

El control de acceso es a través de IAM y se realiza al nivel del conjunto de datos, la tabla, la vista o la columna.

Para consultar los datos en una tabla o en una vista es necesario tener permisos de lectura en la tabla o la vista, al menos.

![[Pasted image 20260902145010.png]]

# Metadata
Los metadatos son un elemento clave para lograr que los datos sean más manejables y útiles para toda la organización.

# Dataplex

Dataplex es una solución integral de administración de datos que te permite descubrir administrar y supervisar de forma centralizada datos distribuidos en tu organización.

Con Dataplex, puedes desglosar los silos de datos centralizar la seguridad y la administración a la
 vez que habilitas la propiedad distribuida y buscar y descubrir con facilidad datos según contextos empresariales.

Dataplex también ofrece inteligencia de datos integrada compatibilidad con herramientas de código abierto y un ecosistema de sociossólido lo que te ayuda a confiar en tus datos y acelerar el tiempo de obtención de estadísticas.

Dataplex te permite estandarizar y unificar los metadatos, las políticas de seguridad la administración, la clasificación y la administración del ciclo de vida de los datos para estos datos distribuidos.

Otro caso de uso común es cuando a tus datos solo pueden acceder ingenieros de datos, y, después, los datos se perfeccionan y se ponen a disposición de científicos y analistas de datos.

En ese caso, puedes configurar un data lake para que tenga lo siguiente una zona sin procesar para los datos a la que acceden ingenieros y científicos de datos y una zona seleccionada para los datos a la que acceden todos los usuarios.

![[Pasted image 20260902145408.png]]

# Share Data
Compartir datos es desafiante especialmente fuera de tu organización.
Debes considerar la seguridad y las opciones de permisos de destino para las canalizaciones de datos la actualidad y exactitud de los datos y, por último, la supervisión del uso.

Analytics Hub se creó para abordar estos desafíos del uso compartido de datos.

Analytics Hub ayuda a las organizaciones a aprovechar el valor del uso compartido de datos lo que genera nuevas estadísticas y valor empresarial.

Con Analytics Hub, puedes crear un ecosistema de datos enriquecidos a través de la publicación de conjuntos de datos listos para el análisis y la suscripción a ellos.

Como los datos se comparten en el mismo lugar los proveedores de datos pueden controlar y supervisar la forma en que se usan sus datos.

Analytics Hub proporciona un método de autoservicio para acceder a recursos de datos valiosos y confiables incluidos datos proporcionados por Google.

Por último, Analytics Hub ofrece la oportunidad de monetizar los recursos de datos.

Analytics Hub elimina las tareas de crear la infraestructura necesaria para la monetización.
