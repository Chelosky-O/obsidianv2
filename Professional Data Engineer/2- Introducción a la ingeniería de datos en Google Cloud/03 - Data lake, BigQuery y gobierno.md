# Data lake, BigQuery y gobierno

[[00 - Índice del módulo|← Índice del módulo]] · [[02 - Elegir almacenamiento en Google Cloud|← Anterior: elegir almacenamiento]] · [[04 - Migración y replicación con Datastream|Siguiente: migración y Datastream →]]

> [!summary] Idea clave
> Un **data lake** conserva datos diversos con flexibilidad; un **data warehouse** los prepara para análisis consistente. En Google Cloud, **BigQuery** cubre el análisis empresarial, mientras **Dataplex y Knowledge Catalog** aportan contexto y gobierno, y **BigQuery sharing** permite publicar datos sin crear una copia para cada consumidor.

## 1. Data lake frente a data warehouse

| Aspecto | Data lake | Data warehouse |
|---|---|---|
| **Estado del dato** | Crudo o con distintos niveles de procesamiento | Depurado, agregado y modelado |
| **Formatos** | Estructurados, semiestructurados y no estructurados | Principalmente estructurados |
| **Esquema** | Flexible; puede aplicarse al leer | Definido para ofrecer análisis consistente |
| **Consumidores** | Ingeniería, ciencia de datos, ML y aplicaciones | Analistas, BI y responsables de decisiones |
| **Fortaleza** | Exploración y reutilización flexible | Consultas, informes y métricas gobernadas |
| **Ejemplo en Google Cloud** | Cloud Storage gobernado con Dataplex | BigQuery |

Un **data lake** es un repositorio central para datos sin procesar en distintos formatos. Permite reutilizarlos en ciencia de datos, aplicaciones y análisis sin exigir que todo esté modelado de antemano.

Un **data warehouse** almacena datos estructurados, agregados o procesados previamente. Está orientado a consultas e informes empresariales eficientes y consistentes a largo plazo.

Aunque tradicionalmente podían funcionar como sistemas independientes, **BigLake** reduce esa separación: permite consultar con BigQuery datos que siguen almacenados en el lake.

> [!tip] Regla mental
> **Lake = conservar flexibilidad. Warehouse = servir análisis confiable.** Una arquitectura empresarial puede necesitar ambos.

> [!example] Ejemplo combinado
> Una empresa conserva archivos crudos, imágenes y eventos en Cloud Storage. Después publica en BigQuery tablas de ventas ya depuradas y modeladas. El lake mantiene el material reutilizable; el warehouse entrega métricas consistentes al área de BI.

## 2. BigQuery como data warehouse

BigQuery es un data warehouse empresarial, completamente administrado y *serverless*.

- Incluye capacidades de aprendizaje automático, análisis geoespacial e inteligencia empresarial.
- Está diseñado para analizar terabytes en segundos y petabytes en minutos, según la consulta y la organización de los datos.
- Es apropiado para cargas **OLAP**, exploración de grandes volúmenes e informes de BI.
- Separa la administración de infraestructura del trabajo analítico.

### Formas de acceso

![[Pasted image 20260902144908.png|1050]]

*Figura 1. Acceso a BigQuery mediante el editor SQL de la consola, la herramienta `bq` y la API REST.*

Las vías destacadas en el curso son:

1. El **editor SQL** de la consola de Google Cloud.
2. La herramienta de línea de comandos **`bq`**, incluida en Google Cloud CLI.
3. La **API REST** y las bibliotecas cliente; el material resume compatibilidad con siete lenguajes.

La consulta de la figura agrupa comentarios por usuario, ordena el resultado y limita las filas devueltas. La misma operación puede ejecutarse desde cualquiera de estas interfaces.

### Jerarquía de recursos

![[Pasted image 20260902144954.png|950]]

*Figura 2. BigQuery organiza sus recursos por proyecto y dataset; un dataset puede contener tablas, vistas, modelos de ML y rutinas.*

La referencia completa de una tabla sigue este formato:

```text
proyecto.dataset.tabla
```

```sql
SELECT *
FROM `mi-proyecto.ventas.pedidos`;
```

| Nivel | Función |
|---|---|
| **Proyecto** | Delimita la facturación y la administración general |
| **Dataset** | Agrupa recursos relacionados |
| **Recurso** | Puede ser una tabla, vista, modelo de ML o rutina |

### Seguridad y control de acceso

![[Pasted image 20260902145010.png|760]]

*Figura 3. IAM controla el acceso a datasets, tablas y vistas; BigQuery añade controles detallados por filas y columnas.*

El acceso se basa en IAM y puede limitarse a diferentes niveles:

| Nivel de control | Alcance |
|---|---|
| **Dataset** | Conjunto de recursos relacionados |
| **Tabla o vista** | Recurso analítico específico |
| **Columna** | Datos sensibles mediante seguridad a nivel de columna |
| **Fila** | Subconjunto de registros mediante políticas de acceso por fila |

Para consultar una tabla o vista se necesitan, como mínimo, permisos para leer sus datos y crear el trabajo de consulta correspondiente.

> [!warning] Trampa de examen
> No concedas un rol amplio de proyecto si basta con acceso a un dataset, una tabla, una vista o determinadas columnas. Aplica **mínimo privilegio** en el nivel más bajo que satisfaga el requisito.

## 3. Metadatos, Dataplex y Knowledge Catalog

Los **metadatos** hacen que los datos sean comprensibles y gobernables. Explican:

- Qué representa un recurso.
- Quién es su propietario.
- Dónde se encuentra.
- Cómo está clasificado.
- Qué políticas lo gobiernan.

Sin este contexto, los datos pueden existir técnicamente y aun así resultar difíciles de descubrir y utilizar.

### Qué aporta Dataplex

Dataplex permite descubrir, administrar y supervisar de forma centralizada datos distribuidos en una organización.

- Reduce silos de datos.
- Centraliza seguridad y gobierno sin eliminar la propiedad distribuida.
- Facilita la búsqueda mediante contexto empresarial.
- Unifica metadatos, políticas de seguridad, clasificación y ciclo de vida.
- Integra inteligencia de datos, herramientas de código abierto y un ecosistema de socios.
- Ayuda a que ingenieros, científicos y analistas trabajen con recursos confiables.

> [!info] Nombre actual
> El curso utiliza **Dataplex** y **Dataplex Universal Catalog**. La documentación actual llama **Knowledge Catalog** al catálogo y contexto de gobierno. Los conceptos de lake, zona y activo siguen siendo útiles para comprender la arquitectura.

### Zonas del lake

![[Pasted image 20260902145408.png|780]]

*Figura 4. Evolución del dato desde una etapa de aterrizaje hasta las zonas raw y curated, con acceso progresivamente más amplio.*

| Zona lógica | Contenido | Acceso habitual |
|---|---|---|
| **Landing** | Datos recién ingeridos | Procesos e ingenieros con acceso restringido |
| **Raw** | Datos preservados para procesamiento y trazabilidad | Ingeniería y ciencia de datos |
| **Curated** | Datos procesados y listos para consumo | Analistas y otros consumidores autorizados |

En Dataplex, los tipos formales principales son **raw** y **curated**; *landing* suele representar una etapa arquitectónica de ingreso. Un lake puede incluir activos de Cloud Storage y datasets de BigQuery.

> [!example] Flujo de gobierno
> Un archivo llega a Landing con acceso limitado, se valida y conserva en Raw para mantener trazabilidad, y finalmente se publica en Curated como fuente confiable para analistas.

## 4. Compartir datos con BigQuery sharing

Compartir datos, especialmente fuera de una organización, exige resolver:

- Seguridad y permisos del destino.
- Actualidad y exactitud de los datos.
- Construcción y mantenimiento de canalizaciones.
- Supervisión del uso.
- Posible monetización del activo.

**Analytics Hub**, llamado actualmente **BigQuery sharing**, aborda estos problemas con un modelo de publicación y suscripción.

| Rol o recurso | Función |
|---|---|
| **Publisher** | Publica un recurso listo para análisis y controla quién puede utilizarlo |
| **Data exchange** | Organiza *listings* y sus permisos |
| **Listing** | Describe y expone el recurso compartido |
| **Subscriber** | Se suscribe y recibe un dataset enlazado de solo lectura |
| **Usage metrics** | Permiten al proveedor observar el consumo |

### Beneficios para proveedores y consumidores

Los proveedores pueden:

- Publicar datasets preparados para análisis.
- Mantener control y visibilidad sobre su uso.
- Ofrecer acceso de autoservicio a datos confiables, incluidos datasets proporcionados por Google.
- Monetizar recursos mediante Google Cloud Marketplace o sus propios canales.
- Evitar infraestructura específica para distribuir copias.

Los consumidores pueden descubrir *listings*, suscribirse y consultar los datos desde su propio proyecto.

> [!important] Punto clave
> La suscripción crea un **linked dataset**, una referencia de solo lectura. Los datos se comparten en el lugar, sin replicar una copia para cada consumidor.

> [!example] Publicación sin copias
> Una empresa publica un dataset de indicadores para sus socios. Cada socio se suscribe desde su proyecto y consulta el dataset enlazado; el proveedor conserva el control del recurso original y observa su uso.

## 5. Mapa de decisión

| Necesidad | Componente principal | Motivo |
|---|---|---|
| Conservar datos diversos y crudos | **Data lake** | Mantiene formatos y usos flexibles |
| Entregar consultas e informes consistentes | **BigQuery** | Funciona como data warehouse analítico y serverless |
| Descubrir, clasificar y gobernar datos distribuidos | **Dataplex / Knowledge Catalog** | Aporta catálogo, contexto y políticas comunes |
| Publicar datos para otros consumidores | **BigQuery sharing** | Comparte mediante suscripción y datasets enlazados |

## Puntos de examen

- Un **data lake** prioriza flexibilidad; un **data warehouse**, análisis consistente.
- Lake y warehouse no son excluyentes: una arquitectura puede utilizar ambos.
- **BigQuery** está orientado a OLAP y BI, y no exige administrar servidores.
- Una tabla se identifica como `proyecto.dataset.tabla`.
- En BigQuery, aplica IAM con **mínimo privilegio** al nivel más específico que satisfaga el requisito.
- Los metadatos describen significado, propiedad, ubicación, clasificación y políticas.
- En Dataplex, **raw** conserva datos para procesamiento y trazabilidad; **curated** ofrece datos listos para consumo.
- *Landing* es una etapa arquitectónica de ingreso, no uno de los dos tipos formales principales de zona en Dataplex.
- **Analytics Hub** aparece en la documentación actual como **BigQuery sharing**.
- Un **linked dataset** es una referencia de solo lectura; compartir no implica copiar los datos para cada suscriptor.

## Repaso activo

> [!question]- 1. ¿Cuál es la diferencia esencial entre un data lake y un data warehouse?
> El lake conserva datos en distintos formatos y estados con máxima flexibilidad; el warehouse almacena datos preparados y estructurados para consultas e informes consistentes.

> [!question]- 2. ¿Cómo se referencia completamente una tabla de BigQuery y qué representa cada parte?
> Como `proyecto.dataset.tabla`: el proyecto delimita facturación y administración, el dataset agrupa recursos y la tabla contiene los datos consultables.

> [!question]- 3. ¿Qué principio debe guiar la asignación de acceso en BigQuery?
> El **mínimo privilegio**: conceder únicamente los permisos necesarios y en el nivel más específico posible, como dataset, tabla, vista, columna o fila.

> [!question]- 4. ¿Qué diferencia existe entre Landing, Raw y Curated?
> Landing representa el ingreso inicial; Raw preserva los datos para procesamiento y trazabilidad; Curated contiene datos procesados y listos para consumidores autorizados.

> [!question]- 5. ¿Cómo comparte datos BigQuery sharing sin crear una copia por consumidor?
> El publisher publica un listing y el subscriber obtiene un **linked dataset** de solo lectura que referencia los datos compartidos en el lugar.

## Fuentes

- [Ruta Professional Data Engineer de Google Cloud Skills Boost](https://partner.skills.google/paths/85?locale=es)
- [Introducción a BigQuery](https://cloud.google.com/bigquery/docs/introduction)
- [Control de acceso con IAM en BigQuery](https://cloud.google.com/bigquery/docs/access-control)
- [BigLake](https://cloud.google.com/bigquery/docs/biglake-intro)
- [Knowledge Catalog](https://cloud.google.com/dataplex/docs/introduction)
- [BigQuery sharing, antes Analytics Hub](https://cloud.google.com/bigquery/docs/analytics-hub-introduction)

---

[[00 - Índice del módulo|← Índice del módulo]] · [[02 - Elegir almacenamiento en Google Cloud|← Anterior: elegir almacenamiento]] · [[04 - Migración y replicación con Datastream|Siguiente: migración y Datastream →]]
