# Apuntes nuevos del path

> [!important] Tu única bandeja de entrada
> Pega aquí todo lo nuevo de los videos: frases sueltas, transcripciones, timestamps, dudas y capturas. **No necesitas ordenarlo ni redactarlo bien.**

[[00 - Ruta Professional Data Engineer|← Ver ruta y progreso]] · [[00 - Índice del módulo|Abrir el módulo actual →]]

## Cómo usar esta nota

1. Escribe siempre debajo de **Por ordenar**.
2. Si cambias de video, añade un encabezado con su nombre.
3. Pega las capturas donde tengan sentido; Obsidian las guardará en `Img`.
4. Cuando quieras procesarlo, dime: **“ordena mis apuntes nuevos”**.

Yo moveré cada concepto a la nota correcta, corregiré la redacción, conservaré las imágenes y añadiré solo los ejemplos o aclaraciones que ayuden a estudiar.

---

## Por ordenar

### Nueva sesión

**Actividad o video:** 

# Dataform
Dataform es un framework sin servidores que simplifica el desarrollo y la administración de canalizaciones de ETL con SQL.

00:08Con Dataform, se transforman los datos en BigQuery asegurando su calidad y proporcionando documentación.

00:15Con este enfoque, se simplifica el proceso de mover datos de sistemas de origen a tablas de producción en BigQuery para operaciones más eficientes y manejables.

00:24Dataform simplifica las operaciones de datos en BigQuery unificando la transformación la aserción y la automatización.

00:31Sin Dataform, tareas como definir tablas, administrar código, probar la calidad de los datos y programar canalizaciones tomarían mucho tiempo y habría más errores.

00:40También se necesitarían varias herramientas y procesos manuales.

00:44Dataform simplifica estas tareas en BigQuery mejorando la eficiencia y la confiabilidad de los datos.

00:51Dataform y BigQuery funcionan juntos para administrar flujos de trabajo de SQL.

00:55Con Dataform, los desarrolladores crean y compilan flujos de trabajo de SQL con SQL y JavaScript.

01:01Luego, Dataform compila en tiempo real lo que incluye verificaciones de dependencias y manejo de errores.

01:06Luego los flujos de trabajo de SQL compilados se ejecutan en BigQuery, lo que habilita transformaciones y la materialización de SQL según demanda o ejecuciones programadas.

01:18En el desarrollo con Dataform, se usan espacios de trabajo con archivos y carpetas predeterminados.

01:23Hay carpetas como definiciones para .

01:25sqlx e includes para JavaScript.

01:28El archivo .

01:29gitignore se usa para administrar confirmaciones de Git.

01:32Los desarrolladores también pueden usar package.json y package-lock.json para controlar dependencias de JavaScript.

01:39El archivo workflow settings.yaml almacena la configuración de compilación del proyecto y también se pueden agregar archivos personalizados como README.md.

01:49La estructura del archivo sqlx brinda un framework para organizar código de SQL y las tareas asociadas.

01:55Comienza con un bloque de configuración para metadatos y pruebas de calidad de datos y, luego, emplea un bloque de js para definir funciones de JavaScript reutilizables.

02:04El bloque pre_operations controla instrucciones de SQL que se ejecutan antes del cuerpo de SQL, lo que define la lógica principal de SQL.

02:12El bloque post_operations tiene instrucciones de SQL que se ejecutarán después de la ejecución principal, lo que garantiza un flujo de trabajo eficiente y estructurado.

02:20El desarrollo de sqlx simplifica el código de SQL reemplazando patrones repetitivos con definiciones concisas.

02:26En este ejemplo de código se ve cómo reemplazar una instrucción CASE compleja que categoriza países por una llamada a función $(mapping.region("country")).

![[Pasted image 20260903093715.png]]

02:35Con este enfoque, se mejora la legibilidad y el mantenimiento del código reduciendo el código estándar y fomentando la reutilización.

02:42Con Dataform, las definiciones de vistas y tablas se crean de una manera específica para que se puedan compilar en instrucciones de SQL.
![[Pasted image 20260903093734.png]]
02:49Los parámetros clave son declaration (para hacer referencia a tablas de BigQuery), table (para crear o reemplazar tablas con una instrucción

02:59SELECT), incremental (para crear tablas y actualizarlas con datos nuevos) y view (para crear o reemplazar vistas que se pueden materializar opcionalmente).

03:09Dataform ofrece aserciones para definir pruebas de calidad de los datos lo que garantiza la coherencia y exactitud de estos.

![[Pasted image 20260903093833.png]]

03:15Este parámetro se puede escribir en SQL o JavaScript lo que brinda la flexibilidad necesaria para verificaciones complejas.

03:21El parámetro operation te permite ejecutar instrucciones de SQL antes, después o durante la ejecución de las canalizaciones.

03:27Estas opciones permiten transformaciones de datos, verificaciones de calidad y otras tareas en tus flujos de trabajo.

03:34Al combinar assertions y operations, Dataform permite crear canalizaciones de datos sólidas y confiables en BigQuery.

03:44Dataform tiene dos métodos para administrar dependencias la declaración implícita y la explícita.
![[Pasted image 20260903094045.png]]

03:49La primera consiste en hacer referencia a tablas o vistas en SQL con la función ref().

03:55Con la segunda, se enumeran las dependencias de un bloque de configuración con el array de dependencies.

04:01También puedes usar la función resolve() para referenciar sin crear una dependencia.

04:07Dataform te permite compilar definiciones de tabla creadas por usuarios en secuencia de comandos de SQL ejecutables.

![[Pasted image 20260903094141.png]]

04:13En el código de muestra, se ve la tabla customer_details que se crea o reemplaza según la tabla customer_source con una instrucción SELECT.

04:20Dataform administra las dependencias que hay entre estas tablas y orquesta su ejecución en un flujo de trabajo.

04:27Este proceso simplifica la transformación de los datos y garantiza una administración eficiente de la canalización de datos.

04:32Los flujos de SQL de Dataform se visualizan mejor en formato de gráfico.

04:36En el ejemplo, se empieza con una declaración de customer_source, seguida de una tabla customer_intermediate

04:42que tal vez se derivó de un sistema de origen como una fuente de datos preprocesada.

04:46Luego, customer_rowConsistency aplica aserciones para verificaciones de calidad.

04:50El gráfico se divide en dos rutas.

04:52En una de ellas, se invoca la operación customer_ml_training.

04:57Realiza operaciones en los datos validados.

05:00En la otra ruta, se crea la vista customer_prod_view.

05:04Hay varios mecanismos de programación y ejecución para flujos de SQL de Dataform.

05:09Uno de ellos consta de activadores internos.

05:12Incluye la ejecución manual en la IU de Dataform y configuraciones programadas directamente en Dataform.

05:18El otro consta de activadores externos.

05:21Incluye herramientas como Cloud Scheduler y Cloud Composer.

05:25Luego, los flujos de trabajo se ejecutan en BigQuery lo que destaca su papel fundamental en este proceso.




