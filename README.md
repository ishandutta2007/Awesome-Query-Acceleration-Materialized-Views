# Awesome-Query-Acceleration-Materialized-Views

# Top Query Acceleration & Materialized Views Ecosystem

**Curated List of SaaS Products & Open-Source GitHub Projects**  
*Focused on Pre-Aggregation, Incremental View Maintenance & Self-Hosted OLAP Acceleration*  
**Last updated: November 2026**

This repository tracks notable **SaaS platforms** and **open-source projects** for **Query Acceleration & Materialized Views**. These tools enable organizations to precompute expensive aggregations and joins, then automatically rewrite queries to hit the precomputed results—turning second-scale dashboard queries into millisecond responses without changing application SQL.

**Examples** include Snowflake Materialized Views, ClickHouse Materialized Views, Materialize, StarRocks, Apache Pinot, Apache Druid, Hydrolix, Firebolt, Yellowbrick, and SingleStore (the category leaders).

**Open-source emphasis**: Query Acceleration is a domain where open-source tooling is **genuinely competitive but requires matching the engine to the workload shape**. **ClickHouse** leads for single-table extreme aggregation and log/time-series workloads, with materialized views acting as insert triggers into target tables and projections offering transparent alternative sort orders . **RisingWave** and **Materialize** provide true **incremental view maintenance (IVM)**—continuous, sub-second freshness with full SQL join support and no manual refresh commands . **Apache Pinot** and **Apache Druid** excel at real-time OLAP dashboards with high concurrency, though both require operational expertise and have limited join capabilities . **Apache Doris** and **StarRocks** offer MySQL-compatible real-time warehouses with strong lakehouse acceleration . The critical caveat: **Snowflake's materialized views have significant limitations**—no joins in MVs, non-additive metrics like `COUNT(DISTINCT)` and `AVG` cannot be re-aggregated, and queries with row-access policies on referenced columns fall back to base-table scans . **Rockset shut down on September 30, 2024**, and **CrateDB** positions itself as an open-source migration target .

Contributions welcome! Open a PR to add/update entries. Keep descriptions factual and link to official sites.

## Table of Contents
- [SaaS/Hosted Platforms](#saas-products)
- [Open-Source GitHub Projects](#open-source-github-projects)
- [How to Contribute](#how-to-contribute)
- [Disclaimer](#disclaimer)

## SaaS/Hosted Platforms

- **[Snowflake Materialized Views](https://docs.snowflake.com/en/user-guide/views-materialized)**  
  Precomputed aggregations maintained automatically by background processes; the query optimizer rewrites compatible queries to use the MV. **Critical limitations**: MVs cannot contain joins; non-additive metrics (`COUNT(DISTINCT)`, `AVG`, `MEDIAN`, `PERCENTILE_*`) cannot be re-aggregated across grains, so a fine-grained rollup only serves queries at exactly the stored grain . **Semantic view materializations** extend this to governed metrics with `MAX_STALENESS` freshness bounds and automatic re-aggregation of additive metrics (`SUM`, `COUNT`, `MIN`, `MAX`) . **Best for Snowflake-native BI acceleration**—but verify metric additivity before assuming broad query coverage.

- **[ClickHouse Materialized Views](https://clickhouse.com/docs/en/sql-reference/statements/create/view#materialized-view)**  
  Insert triggers that transform data block-by-block into explicit target tables; **refreshable MVs** periodically recompute full results for complex joins . **Projections** offer an alternative for same-table alternate sort orders with transparent optimizer selection and atomic consistency . **Production guidance**: always use `TO` clause with explicit target tables, never use `POPULATE` on large tables, batch inserts to at least 1,000 rows, and monitor with `system.query_views_log` . **Best for log, time-series, and single-table aggregation workloads** where ClickHouse's vectorized engine excels—but joins are weak and MVs do not react to `UPDATE`/`DELETE` .

- **[Materialize](https://materialize.com/)**  
  Cloud-only streaming SQL database with **continuous incremental view maintenance** via differential dataflow; sub-second freshness with strict serializable consistency . **Source-available (BSL)** licensing—not OSI-approved. **Best for strict-serializable streaming use cases** requiring consistent multi-table joins.

- **[StarRocks](https://www.starrocks.io/)**  
  Real-time OLAP database with MySQL protocol compatibility, strong lakehouse acceleration, and materialized views for pre-aggregation . **Best for lakehouse analytics with MySQL ecosystem familiarity**—but incremental MV refresh was on the roadmap rather than fully implemented as of recent updates .

- **[Apache Pinot](https://pinot.apache.org/)**  
  Real-time OLAP datastore from LinkedIn with strong multi-tenancy and segment assignment strategies for p99 latency control . **Best for user-facing real-time dashboards** with high concurrency—but requires operational expertise for cluster management.

- **[Apache Druid](https://druid.apache.org/)**  
  Real-time analytics database with segment-based immutable storage and pre-aggregation (rollup) at ingestion . **Best for time-series dashboards and ad-tech monitoring**—but operational complexity is high (four node types plus ZooKeeper) and joins are weak .

- **[Hydrolix](https://hydrolix.io/)**  
  Log-optimized platform on ClickHouse SQL engine with Kubernetes-native stateless architecture, separate scaling pools for ingest/query/merge, and default full indexing for high-cardinality fields . **Best for 100% log workloads** with extreme cardinality and nested JSON—but lacks ClickHouse's Netflix-scale production validation .

- **[Firebolt](https://www.firebolt.io/)**  
  Cloud data warehouse optimized for interactive BI performance with columnar serving and ingestion-to-query workflow . **Best for data engineering teams** building fast-serving data marts—but governance depends on pipeline instrumentation outside the engine .

- **[Yellowbrick](https://www.yellowbrick.com/)**  
  Data warehouse priced per vCPU with deployment flexibility (public cloud, private cloud, data center, edge) . **Best for regulated firms with residency requirements**—but no free plan and smaller community than larger vendors .

- **[SingleStore](https://www.singlestore.com/)**  
  Hybrid row/column store with MySQL compatibility for mixed transactional and analytical workloads . **Best for live dashboards inside applications** needing fresh data—but hourly pricing floors add up for batch BI use .

## Open-Source GitHub Projects

### Incremental View Maintenance Engines

- **[RisingWave](https://github.com/risingwavelabs/risingwave)**  
  The **leading open-source IVM engine** (Apache 2.0) with **continuous, incremental maintenance of materialized views**—no refresh commands, no schedule, full SQL join support . Features: **exactly-once processing** with barrier-based checkpointing; strong consistency across multi-table joins; cascading views where one MV references another; storage-compute decoupled architecture for single-digit-second scaling . **Integrates with Apache Iceberg** for lakehouse workflows . **Best for real-time analytics, event-driven apps, and AI agent context** requiring sub-second freshness with self-hosting—but cluster deployment currently requires Kubernetes .

- **[Materialize (Self-Hosted)](https://github.com/MaterializeInc/materialize)**  
  While Materialize's primary offering is cloud-only, the source code is available under BSL. **Differential dataflow**-based IVM with strict serializable consistency . **Best for teams wanting Materialize's consistency guarantees** with source-available licensing—but self-hosting is not the primary supported path.

### Columnar OLAP Engines with Pre-Aggregation

- **[ClickHouse](https://github.com/ClickHouse/ClickHouse)**  
  The **dominant open-source columnar OLAP engine** (Apache 2.0) for single-table extreme aggregation and log/time-series workloads . Features: **MergeTree engine family** with vectorized execution and SIMD optimization; **materialized views as insert triggers** into target tables; **projections** for transparent alternate sort orders; **AggregatingMergeTree** with state functions for complex aggregations; native S3/Parquet reading . **Best for single-table aggregation at scale**—but joins are weak, `UPDATE`/`DELETE` handling is limited, and MVs require explicit target table management .

- **[Apache Doris](https://github.com/apache/doris)**  
  MySQL-compatible real-time data warehouse (Apache 2.0) with **row/column hybrid storage**, inverted indexes, `VARIANT` type for semi-structured data, and full SQL join support . **Best for Rockset refugees** wanting an open-source migration target with real-time updates and deletions .

- **[Apache Pinot](https://github.com/apache/pinot)**  
  LinkedIn-origin real-time OLAP datastore (Apache 2.0) with mature segment assignment strategies for p99 latency . **Best for user-facing real-time analytics** with multi-tenant requirements—but requires operational expertise.

- **[Apache Druid](https://github.com/apache/druid)**  
  Real-time analytics database (Apache 2.0) with segment-based immutable storage and rollup at ingestion . **Best for high-concurrency time-series dashboards**—but operational complexity is high and joins are weak.

### Additional Strong Open-Source Options

- **CrateDB** — Open-source distributed SQL database (Apache 2.0) built on Lucene with real-time indexing of structured and semi-structured data; positioned as a **Rockset migration target** with PostgreSQL wire protocol compatibility and vector search . **Best for teams migrating off Rockset** or wanting real-time analytics with SQL familiarity.
- **PostgreSQL + pg_ivm** — PostgreSQL materialized views are batch-only without extensions; `pg_ivm` adds basic incremental maintenance but **is not yet production-ready and blocks writes during maintenance** . **Best for OLTP databases** with occasional analytics needs—not high-frequency IVM.

**Frameworks for building custom systems**: For **continuous IVM with full SQL joins and sub-second freshness**, RisingWave is the leading open-source option with Apache 2.0 licensing . For **single-table extreme aggregation and log workloads**, ClickHouse with materialized views or projections . For **MySQL-compatible real-time analytics**, Apache Doris . For **user-facing high-concurrency dashboards**, Pinot or Druid . **Snowflake's MV limitations** (no joins, non-additive metrics) make it unsuitable for complex query acceleration—plan for base-table scans or semantic view materializations with additive metrics only .

## How to Contribute

1. Fork the repo.
2. Add/edit entries in `README.md` (follow existing format).
3. Include: name, link, 1–2 sentence description, and whether it's SaaS or open-source.
4. Submit PR with a short explanation.

Star the repo if you find it useful!

## Disclaimer

- This is a **community-curated** list — not exhaustive and not an endorsement.
- Query acceleration and materialized view platforms handling production data must comply with organizational security policies; **materialized views store precomputed data that may not reflect row-level security policies**—verify access controls before exposing accelerated results.
- **Materialized view acceleration is highly workload-dependent**—Snowflake MVs cannot contain joins and cannot re-aggregate non-additive metrics (`COUNT(DISTINCT)`, `AVG`, percentiles), limiting their broad query coverage . ClickHouse MVs are insert triggers, not self-maintaining caches, and require explicit target table management . **RisingWave provides true continuous IVM** with full SQL join support and sub-second freshness, but requires Kubernetes deployment . **Rockset shut down in September 2024**; CrateDB and Apache Doris position themselves as migration targets . Choose based on workload shape (single-table vs. multi-table joins), freshness requirements (batch vs. continuous), and operational tolerance.
- Verify license terms for commercial use; RisingWave uses Apache 2.0 , ClickHouse uses Apache 2.0 , Apache Doris uses Apache 2.0 , and Materialize uses BSL (source-available) .

---

**Made for data platform engineers, analytics architects, and organizations building query acceleration infrastructure.**  
Let's make query acceleration more open, but with clear eyes about the workload-specific limitations that define which engine actually accelerates your queries—and which quietly falls back to full scans.
