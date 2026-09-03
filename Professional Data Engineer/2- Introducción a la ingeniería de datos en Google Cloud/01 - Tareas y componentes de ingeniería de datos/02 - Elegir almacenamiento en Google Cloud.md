# 02 — Elegir almacenamiento en Google Cloud

[[00 - Índice|Índice de la sección]] · [[01 - Fundamentos de pipelines y tipos de datos|← Anterior: fundamentos]] · [[03 - Data lake, BigQuery y gobierno|Siguiente: data lake y gobierno →]]

> [!abstract] Resumen
> La elección de almacenamiento comienza por el **tipo de carga**, no por el nombre del producto:
> - **Cloud Storage** guarda objetos y datos no estructurados.
> - Para datos estructurados, primero distingue entre cargas **transaccionales** y **analíticas**.
> - Después decide si necesitas SQL o NoSQL, alcance regional o global, y qué patrón de acceso tendrá el dato.
> - En Cloud Storage, Standard, Nearline, Coldline y Archive representan distintas frecuencias de acceso. Rapid Storage es una opción zonal de alto rendimiento añadida en 2026 y se estudia por separado.

## En esta nota

- [[#Cloud Storage|Cloud Storage]]
- [[#Las cuatro clases clásicas|Las cuatro clases clásicas]]
- [[#Actualización 2026: Rapid Storage|Actualización 2026: Rapid Storage]]
- [[#Elegir un producto para datos estructurados|Elegir almacenamiento estructurado]]
- [[#Tabla de decisión|Tabla de decisión]]
- [[#Puntos de examen|Puntos de examen]]
- [[#Preguntas de repaso|Preguntas de repaso]]

---

## Cloud Storage

### Modelo de almacenamiento

**Cloud Storage** es un servicio administrado de almacenamiento de objetos. Guarda cada archivo como un **objeto dentro de un bucket**.

- Los objetos pueden ser imágenes, videos, documentos, archivos de audio, BLOB, copias de seguridad o contenido estático.
- Se accede a ellos mediante solicitudes HTTP.
- Una solicitud GET puede recuperar el objeto completo o solo un rango de bytes.
- Cada objeto tiene metadatos, pero su contenido se manipula como una secuencia de bytes sin estructura.
- Un objeto puede alcanzar hasta **5 TiB**.
- El servicio está diseñado para ser durable, escalable, disponible y coherente.
- Es apropiado para alojar contenido estático y recibir archivos generados por usuarios.

> [!important] Modelo mental
> Cloud Storage no es una base de datos relacional. Tampoco debes asumir que funciona como un sistema de archivos POSIX tradicional: la aplicación o la herramienta de procesamiento interpreta la estructura interna de cada objeto.

### Cuándo usarlo

| Necesidad | Por qué encaja Cloud Storage |
|---|---|
| Guardar imágenes o videos | El servicio trata cada archivo como un objeto |
| Conservar datos crudos de un data lake | Acepta formatos estructurados, semiestructurados y no estructurados |
| Publicar contenido estático | Los objetos se recuperan mediante HTTP |
| Mantener backups o archivos históricos | Las clases frías reducen el costo de almacenamiento |
| Intercambiar archivos con procesos de datos | Se integra con servicios analíticos y herramientas de transferencia |

> [!example] Ejemplo
> Una aplicación recibe fotografías de usuarios. Guarda el archivo original en Cloud Storage, conserva como metadatos el tipo de contenido y la fecha de creación, y almacena en una base de datos solo la información necesaria para buscarlo desde la aplicación.

---

## Las cuatro clases clásicas

La clase de almacenamiento es un metadato del objeto que afecta su precio, disponibilidad y modelo de recuperación. En el curso, la decisión se basa principalmente en la **frecuencia esperada de acceso**.

| Clase | Acceso esperado | Duración mínima | Cargo por recuperación | Uso típico |
|---|---:|---:|---:|---|
| **Standard** | Frecuente o datos de vida corta | Sin mínimo | No | Aplicaciones, contenido activo y analítica interactiva |
| **Nearline** | Una vez al mes o menos | 30 días | Sí | Backups mensuales y contenido poco consultado |
| **Coldline** | Una vez por trimestre o menos | 90 días | Sí | Recuperación ante desastres y backups fríos |
| **Archive** | Una vez al año o menos | 365 días | Sí | Archivo regulatorio y conservación a largo plazo |

### Cómo decidir

1. Estima cuántas veces se leerá o modificará el objeto.
2. Determina cuánto tiempo debe conservarse.
3. Considera los cargos de recuperación y operaciones.
4. Comprueba si la duración mínima es compatible con el ciclo de vida.
5. Evalúa ubicación, disponibilidad y costo de red.

> [!warning] Trampa de examen
> La clase con menor costo de almacenamiento no siempre produce el menor costo total. Si recuperas con frecuencia un objeto de Archive, los cargos de recuperación y la duración mínima pueden volver incorrecta la decisión.

### Ciclo de vida y Autoclass

- **Object Lifecycle Management** permite cambiar automáticamente la clase o eliminar objetos cuando se cumplen condiciones definidas.
- **Autoclass** administra automáticamente transiciones compatibles cuando el patrón de acceso es difícil de predecir.
- Cambiar la clase predeterminada de un bucket no cambia por sí solo la clase de los objetos que ya existen.

### Ejemplo de ciclo de vida

Una empresa conserva grabaciones de soporte:

1. Durante los primeros 30 días se reproducen con frecuencia → **Standard**.
2. Después solo se consultan en auditorías mensuales → **Nearline**.
3. Al cabo de varios meses quedan como respaldo → **Coldline**.
4. Tras cumplir el período operativo, se conservan únicamente por obligación legal → **Archive**.

> [!tip] Regla rápida
> **Standard = caliente · Nearline = mensual · Coldline = trimestral · Archive = anual.**

---

## Actualización 2026: Rapid Storage

> [!info] Estado actual
> Desde el **10 de marzo de 2026**, **Rapid Bucket** está disponible de forma general. Permite usar la clase **Rapid** en un bucket con ubicación zonal para colocar el almacenamiento cerca del cómputo y obtener menor latencia y mayor throughput.

Rapid Storage está orientado a cargas intensivas de E/S:

- Entrenamiento, checkpointing y serving de modelos.
- IA y aprendizaje automático.
- Analítica de datos de gran escala.
- Lecturas y escrituras que requieren rendimiento zonal.

No desplaza las cuatro clases clásicas:

| Clases clásicas | Rapid |
|---|---|
| Optimizan costo según frecuencia y retención | Optimiza rendimiento y colocación zonal |
| Admiten ubicaciones regionales, duales o multirregionales según la clase | Requiere un bucket zonal |
| Sirven al ciclo de vida general de objetos | Sirve a cargas especializadas de E/S |

> [!warning] Límite relevante
> La documentación actual enumera **BigQuery** entre los servicios incompatibles con Rapid Bucket. No lo elijas como almacenamiento externo de BigQuery solo porque la carga sea analítica.

Consulta [Rapid Bucket](https://cloud.google.com/storage/docs/rapid/rapid-bucket), las [clases de almacenamiento](https://cloud.google.com/storage/docs/storage-classes) y las [notas de versión de Cloud Storage](https://cloud.google.com/storage/docs/release-notes).

---

## Elegir un producto para datos estructurados

No existe un producto correcto para todos los datos estructurados. La elección depende de la carga, el modelo, la escala y la distribución geográfica.

### Árbol de decisión

![[Pasted image 20260902144358.png|1050]]

*Figura 1. Selección del curso para datos estructurados: carga transaccional o analítica, modelo SQL o NoSQL y alcance de escalabilidad.*

Lee la figura de izquierda a derecha:

1. **¿La carga es transaccional o analítica?**
2. **¿El modelo es SQL o NoSQL?**
3. Si es transaccional y SQL, **¿la escala es local/regional, PostgreSQL de alto rendimiento o global?**
4. Si es analítica, **¿necesitas un data warehouse SQL o acceso NoSQL de clave-valor?**

### Tabla de decisión

| Necesidad principal | Producto | Modelo | Razón |
|---|---|---|---|
| Transacciones relacionales locales o regionales | **Cloud SQL** | SQL | Base de datos relacional administrada |
| PostgreSQL de alto rendimiento y gran escala | **AlloyDB** | PostgreSQL compatible | Motor administrado optimizado para rendimiento |
| Transacciones globales con coherencia sólida | **Spanner** | SQL relacional distribuido | Escalabilidad horizontal y consistencia global |
| Aplicaciones basadas en documentos | **Firestore** | NoSQL documental | Serverless, escalado automático y desarrollo ágil |
| Analítica empresarial y BI | **BigQuery** | SQL analítico | Data warehouse administrado y serverless |
| Clave-valor o series temporales a gran escala | **Bigtable** | NoSQL wide-column | Alto throughput y latencia coherente inferior a 10 ms |

### Cloud SQL

Servicio administrado para bases de datos relacionales. Es la primera opción cuando una aplicación:

- Necesita transacciones SQL.
- Usa un motor relacional tradicional.
- Opera principalmente en una región.
- No requiere la escala global de Spanner.

> [!example]
> Un sistema regional de pedidos que necesita claves foráneas, transacciones y consultas SQL encaja en Cloud SQL.

### AlloyDB

Servicio de base de datos compatible con PostgreSQL, completamente administrado y orientado a cargas que necesitan mayor rendimiento y escala.

Elígelo cuando el requisito no sea simplemente “una base SQL”, sino específicamente una carga PostgreSQL exigente.

### Spanner

Base de datos distribuida y completamente administrada que combina:

- Esquema y consultas SQL.
- Transacciones con coherencia sólida.
- Escalabilidad horizontal.
- Distribución regional o multirregional.

> [!warning] Trampa de examen
> “Puede crecer en el futuro” no basta para justificar Spanner. Debe existir un requisito real de escala horizontal, alta disponibilidad o distribución global. Para una aplicación regional convencional, Cloud SQL suele ser más directo.

### Firestore

Base de datos documental NoSQL, serverless y con escalado automático. Encaja cuando la aplicación trabaja naturalmente con documentos y necesita:

- Estructura flexible.
- Desarrollo rápido.
- Acceso desde aplicaciones.
- Escalado sin administrar servidores.

### BigQuery

Data warehouse empresarial, completamente administrado y serverless. Está diseñado para:

- Análisis de grandes volúmenes.
- Consultas SQL analíticas.
- Informes e inteligencia empresarial.
- Cargas OLAP, no transacciones operacionales fila por fila.

### Bigtable

Base de datos NoSQL de alto rendimiento para búsquedas por clave. Encaja en cargas como:

- Series temporales.
- Telemetría.
- Métricas.
- Datos financieros de alta frecuencia.
- Grandes volúmenes con acceso predecible por clave.

> [!important] BigQuery frente a Bigtable
> **BigQuery** responde preguntas analíticas mediante SQL sobre grandes conjuntos de datos. **Bigtable** sirve lecturas y escrituras de baja latencia por clave. Que ambos puedan manejar grandes volúmenes no los vuelve intercambiables.

### Casos rápidos

| Caso | Elección | Motivo decisivo |
|---|---|---|
| ERP regional con transacciones ACID | Cloud SQL | SQL operacional regional |
| Aplicación PostgreSQL con alta exigencia de rendimiento | AlloyDB | PostgreSQL administrado optimizado |
| Plataforma financiera activa en varios continentes | Spanner | Transacciones globales coherentes |
| Catálogo flexible para una app móvil | Firestore | Documentos y escalado serverless |
| Informes históricos sobre petabytes | BigQuery | OLAP y BI |
| Telemetría indexada por dispositivo y tiempo | Bigtable | Clave-valor y series temporales |

---

## Puntos de examen

> [!success] Qué memorizar
> 1. **Datos no estructurados u objetos → Cloud Storage.**
> 2. En Cloud Storage, la frecuencia de acceso determina la clase clásica inicial.
> 3. Nearline, Coldline y Archive tienen duración mínima y cargos de recuperación.
> 4. Rapid responde a rendimiento zonal; no es una clase fría ni reemplaza el modelo del curso.
> 5. **Transaccional vs. analítico** es la primera bifurcación para datos estructurados.
> 6. Cloud SQL cubre SQL regional; Spanner cubre SQL distribuido y global.
> 7. Firestore es documental; Bigtable está optimizado para acceso por clave a gran escala.
> 8. BigQuery es OLAP y BI; no es la base operacional predeterminada de una aplicación.

> [!danger] Confusiones frecuentes
> - **Cloud Storage ≠ base de datos:** almacena objetos.
> - **Standard ≠ “sin costo”:** no cobra recuperación, pero almacenamiento, operaciones y red sí tienen costo.
> - **Cloud SQL ≠ BigQuery:** transacciones operacionales frente a análisis.
> - **Firestore ≠ Bigtable:** documentos de aplicación frente a acceso masivo por clave.
> - **Spanner ≠ opción predeterminada:** su complejidad se justifica por escala y distribución reales.

---

## Preguntas de repaso

> [!question]- 1. ¿Qué guarda Cloud Storage y cómo se accede al contenido?
> Guarda archivos como objetos dentro de buckets. Cada objeto combina datos y metadatos; se accede mediante HTTP y una solicitud GET puede recuperar todo el objeto o un rango de bytes.

> [!question]- 2. ¿Cómo eliges entre Standard, Nearline, Coldline y Archive?
> Usa Standard para acceso frecuente, Nearline para acceso mensual o menor, Coldline para acceso trimestral o menor y Archive para acceso anual o menor. Después valida duración mínima, cargos de recuperación, disponibilidad y costo de red.

> [!question]- 3. ¿Qué problema resuelve Rapid Storage y qué precaución debes recordar?
> Rapid Storage coloca un bucket en una zona para reducir latencia y aumentar throughput en cargas intensivas de E/S. No reemplaza las cuatro clases clásicas y Rapid Bucket no es compatible actualmente con BigQuery.

> [!question]- 4. ¿Cómo eliges entre Cloud SQL, AlloyDB, Spanner, Firestore, BigQuery y Bigtable?
> Cloud SQL para SQL transaccional regional; AlloyDB para PostgreSQL administrado de alto rendimiento; Spanner para transacciones distribuidas y coherentes; Firestore para documentos de aplicación; BigQuery para OLAP y BI; Bigtable para clave-valor o series temporales a gran escala.

---

## Fuentes oficiales

- [Descripción general de Cloud Storage](https://cloud.google.com/storage/docs/introduction)
- [Clases de almacenamiento](https://cloud.google.com/storage/docs/storage-classes)
- [Object Lifecycle Management](https://cloud.google.com/storage/docs/lifecycle)
- [Autoclass](https://cloud.google.com/storage/docs/autoclass)
- [Rapid Bucket](https://cloud.google.com/storage/docs/rapid/rapid-bucket)
- [Cloud SQL](https://cloud.google.com/sql/docs/introduction)
- [AlloyDB](https://cloud.google.com/alloydb/docs/overview)
- [Spanner](https://cloud.google.com/spanner/docs)
- [Firestore](https://cloud.google.com/firestore/docs/overview)
- [BigQuery](https://cloud.google.com/bigquery/docs/introduction)
- [Bigtable](https://cloud.google.com/bigtable/docs/overview)

---

[[00 - Índice|Índice de la sección]] · [[01 - Fundamentos de pipelines y tipos de datos|← Anterior: fundamentos]] · [[03 - Data lake, BigQuery y gobierno|Siguiente: data lake y gobierno →]]
