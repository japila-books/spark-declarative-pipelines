# Spark Declarative Pipelines

**Spark Declarative Pipelines (SDP)** is a declarative framework for building data processing (ETL) pipelines on Apache Spark in [Python](python.md) and [SQL](sql.md) languages.

A Declarative Pipelines project is defined and configured in a [pipeline specification file](#pipeline-specification-file).

A Declarative Pipelines project can be executed with [spark-pipelines](#spark-pipelines) shell script.

Declarative Pipelines uses [Python decorators](python.md#python-decorators) to describe tables, views and flows, declaratively.

The definitions of tables, views and flows are registered in [DataflowGraphRegistry](DataflowGraphRegistry.md) (with [GraphRegistrationContext](GraphRegistrationContext.md)s by graph IDs). A `GraphRegistrationContext` is [converted into a DataflowGraph](GraphRegistrationContext.md#toDataflowGraph) when `PipelinesHandler` is requested to [start a pipeline run](PipelinesHandler.md#startRun) (when [spark-pipelines](#spark-pipelines) script is launched with `run` or `dry-run` command).

Streaming flows are backed by streaming sources, and batch flows are backed by batch sources.

[DataflowGraph](DataflowGraph.md) is the core graph structure in Declarative Pipelines.

Once described, a pipeline can be [started](PipelineExecution.md#runPipeline) (on a [PipelineExecution](PipelineExecution.md)).

## Configuration Properties { #spark.sql.pipelines }

[spark.sql.pipelines Configuration Properties](./configuration-properties.md)

## Pipeline Specification File

A Declarative Pipelines project is defined using a **pipeline specification file** (in YAML format).

Unless specified using spark-pipelines CLI's [--spec](SparkPipelines.md#run) option, Declarative Pipelines uses the following file names as the defaults:

* `spark-pipeline.yml`
* `spark-pipeline.yaml`

In the pipeline specification file, Declarative Pipelines developers specify files (`libraries`) with tables, views and flows (transformations) definitions in [Python](python.md) and [SQL](sql.md). A SDP project can use both languages simultaneously.

The following fields are supported:

| Field Name | Description |
|----------|----------|
| `name` (required) | &nbsp; |
| `storage` (required) | The root storage location of pipeline metadata (e.g., checkpoints for streaming flows).<br>[SPARK-53751 Explicit Checkpoint Location]({{ spark.jira }}/SPARK-53751) |
| `catalog` | The default catalog to register datasets into.<br>Unless specified, [PipelinesHandler](PipelinesHandler.md#createDataflowGraph) falls back to the current catalog. |
| `database` | The default database to register datasets into<br>Unless specified, [PipelinesHandler](PipelinesHandler.md#createDataflowGraph) falls back to the current database. |
| `schema` | Alias of `database`. Used unless `database` is defined |
| `configuration` | SparkSession configs<br>Spark Pipelines runtime uses the configs to build a new `SparkSession` when `run`.<br>[spark.sql.connect.serverStacktrace.enabled]({{ book.spark_connect }}/configuration-properties/#spark.sql.connect.serverStacktrace.enabled) is hardcoded to be always `false`. |
| `libraries` | `glob`s of `include`s with transformations in [SQL](#sql) and [Python](#python-decorators) |

??? info
    Pipeline spec is resolved in `pyspark/pipelines/cli.py::unpack_pipeline_spec`.

```yaml
name: hello-spark-pipelines
catalog: default_catalog
schema: default
storage: file:///absolute/path/to/storage/dir
configuration:
  spark.key1: value1
libraries:
  - glob:
      include: transformations/**
```

## Spark Pipelines CLI { #spark-pipelines }

`spark-pipelines` shell script is the **Spark Pipelines CLI** (that launches [org.apache.spark.deploy.SparkPipelines](SparkPipelines.md) behind the scenes).

## Dataset Types

Declarative Pipelines supports the following dataset types:

* [Append Flows](#append-flows)
* [Materialized Views](#materialized-views)
* [Streaming tables](#streaming-tables)
* **Table** that are published to a catalog.
* **Views** that are not published to a catalog.

### Append Flows

**Append Flows** can be created with the following:

* [@dp.append_flow](python.md#append_flow)
* `CREATE FLOW AS INSERT INTO BY NAME` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineInsertIntoFlow))

### Materialized Views

**Materialized Views** can be created with the following:

* [@dp.materialized_view](python.md#materialized_view)
* `CREATE MATERIALIZED VIEW AS` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset))

Materialized views are published to a catalog.

### Streaming Tables

**Streaming tables** are tables whose content is produced by one or more streaming flows.

Streaming tables can be created with the following:

* [@dp.create_streaming_table](python.md#create_streaming_table) or `CREATE STREAMING TABLE` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder//#visitCreatePipelineDataset)) (with no flows that can be defined later with [@dp.append_flow](python.md#append_flow) or `CREATE FLOW AS INSERT INTO BY NAME` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder//#visitCreatePipelineInsertIntoFlow)))
* `CREATE STREAMING TABLE AS` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset))

Streaming tables are published to a catalog.

## Spark Connect Only { #spark-connect }

Declarative Pipelines currently only supports [Spark Connect]({{ book.spark_connect }}).

```console
$ ./bin/spark-pipelines --conf spark.api.mode=xxx
...
25/08/03 12:33:57 INFO SparkPipelines: --spark.api.mode must be 'connect'. Declarative Pipelines currently only supports Spark Connect.
Exception in thread "main" org.apache.spark.SparkUserAppException: User application exited with 1
 at org.apache.spark.deploy.SparkPipelines$$anon$1.handle(SparkPipelines.scala:73)
 at org.apache.spark.launcher.SparkSubmitOptionParser.parse(SparkSubmitOptionParser.java:169)
 at org.apache.spark.deploy.SparkPipelines$$anon$1.<init>(SparkPipelines.scala:58)
 at org.apache.spark.deploy.SparkPipelines$.splitArgs(SparkPipelines.scala:57)
 at org.apache.spark.deploy.SparkPipelines$.constructSparkSubmitArgs(SparkPipelines.scala:43)
 at org.apache.spark.deploy.SparkPipelines$.main(SparkPipelines.scala:37)
 at org.apache.spark.deploy.SparkPipelines.main(SparkPipelines.scala)
```

## Learning Resources

* [Spark Declarative Pipelines Programming Guide]({{ spark.docs }}/declarative-pipelines-programming-guide.html)
