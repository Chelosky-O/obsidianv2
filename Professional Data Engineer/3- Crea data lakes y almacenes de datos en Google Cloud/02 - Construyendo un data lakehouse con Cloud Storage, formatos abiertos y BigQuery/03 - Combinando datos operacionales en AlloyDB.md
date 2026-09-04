# 03 — Combinando datos operacionales en AlloyDB

[[00 - Índice|Índice del sub-módulo]] · [[02 - Introducción a Apache Iceberg (formato de tabla abierto)|← Anterior: Apache Iceberg]] · [[04 - BigQuery como motor central de procesamiento|Siguiente: BigQuery →]]

> [!abstract] Resumen
> Mientras Cloud Storage y los **open table formats** (como Iceberg) son ideales para datos analíticos a gran escala, los **datos operacionales** que impulsan las operaciones diarias de Cymbal requieren una **solución distinta**. Ahí **AlloyDB for PostgreSQL** es una excelente opción.

> [!quote] ¿Qué son los datos operacionales?
> Los datos operacionales son la información **en tiempo real, en constante cambio** que impulsa los **procesos de negocio inmediatos**.

## ¿Qué incluyen? (ejemplos Cymbal)

| Caso | Qué necesita |
|---|---|
| **Procesamiento de pedidos** | Cuando el cliente hace clic en "comprar", la transacción debe registrarse **al instante y con precisión**. |
| **Gestión de inventario** | **Actualizaciones en tiempo real** de niveles de stock en distintos almacenes. |
| **Información de login de usuarios** | Asegurar **acceso fluido y seguro** de los clientes a sus cuentas online. |

## Requisitos de estos casos de uso

> [!important] Características que exigen
> - Manejar **volúmenes de transacción muy altos**.
> - **Latencia extremadamente baja** en lecturas y escrituras.
> - **Consistencia de datos fuerte**.
>
> ⚠️ Las **bases de datos analíticas** **no están diseñadas** para estas demandas operacionales en tiempo real.

## AlloyDB for PostgreSQL

> [!note] Qué es
> **AlloyDB** es un servicio de base de datos **fully managed, compatible con PostgreSQL**, construido para **cargas de trabajo empresariales exigentes**. Combina la familiaridad y flexibilidad de PostgreSQL con el **rendimiento, disponibilidad y escalabilidad** de la nube para aplicaciones operacionales críticas.

Beneficios para Cymbal:

| Beneficio | Explicación |
|---|---|
| **Alto rendimiento** | Significativamente más rápido que PostgreSQL estándar → ideal para workloads de alta transacción (millones de pedidos online). |
| **Alta disponibilidad** | Construido para resiliencia → las operaciones core están **siempre online**, incluso ante apagones. |
| **Compatibilidad PostgreSQL** | **Aprovechan sus skills y herramientas** de PostgreSQL existentes → **migración más fácil** y menor curva de aprendizaje. |

> [!success] Puntos de examen
> 1. Los **datos operacionales** son **en tiempo real** y de **cambio constante**; requieren **alta transacción, baja latencia y consistencia fuerte**.
> 2. Las **BD analíticas** **no** están diseñadas para demandas operacionales en tiempo real.
> 3. **AlloyDB** = servicio **fully managed, compatible con PostgreSQL**, para **cargas empresariales exigentes**.
> 4. Beneficios: **alto rendimiento, alta disponibilidad, compatibilidad PostgreSQL**.
> 5. Ejemplos de datos operacionales: **procesamiento de pedidos, inventario, login de usuarios**.

> [!warning] Trampa de examen
> **Analítica ≠ operacional.** BigQuery es analítico (OLAP); AlloyDB es operacional (OLTP/alta transacción). Elige AlloyDB para **transacciones en tiempo real** con **consistencia fuerte** y **baja latencia**.

## Preguntas de repaso

> [!question]- 1. ¿Qué tipo de datos maneja AlloyDB y qué requisitos exigen?
> Datos **operacionales en tiempo real** (pedidos, inventario, login). Exigen **alto volumen de transacciones, baja latencia y consistencia fuerte**.

> [!question]- 2. ¿Por qué AlloyDB es buena opción vs una BD analítica?
> Porque es **compatible con PostgreSQL**, **fully managed**, con **alto rendimiento, alta disponibilidad y escalabilidad** — diseñada para cargas operacionales, no analíticas.

> [!question]- 3. Menciona 3 beneficios de AlloyDB.
> Alto rendimiento, alta disponibilidad y compatibilidad PostgreSQL.

## Fuentes oficiales

- [AlloyDB for PostgreSQL](https://cloud.google.com/alloydb/docs)
