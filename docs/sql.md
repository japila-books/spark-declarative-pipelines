# SQL

Spark Declarative Pipelines supports the following SQL statements to define data processing pipelines:

* `CREATE FLOW AS INSERT INTO BY NAME` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/ś#visitCreatePipelineInsertIntoFlow))
* `CREATE MATERIALIZED VIEW AS` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset))
* `CREATE STREAMING TABLE` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset))
* `CREATE STREAMING TABLE AS` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset))
* `CREATE VIEW` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreateView))
* `CREATE TEMPORARY VIEW` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreateView))
* `SET` ([Spark SQL]({{ book.spark_sql }}/logical-operators/SetCommand/))
* `SET CATALOG` ([Spark SQL]({{ book.spark_sql }}/logical-operators/SetCatalogCommand/))
* `USE NAMESPACE` ([Spark SQL]({{ book.spark_sql }}/logical-operators/SetNamespaceCommand/))

Pipelines elements are defined in files with `.sql` file extension.

The SQL files are included as `libraries` in a [pipelines specification file](overview.md#pipeline-specification-file).

[SqlGraphRegistrationContext](SqlGraphRegistrationContext.md) is used on [Spark Connect Server]({{ book.spark_connect }}/server) to handle SQL statements (from SQL definitions files and [Python decorators](python.md#python-decorators)).

A streaming table can be defined without a query, as streaming tables' data can be backed by standalone flows.
During a pipeline execution, it is validated that a streaming table has at least one standalone flow writing to the table, if no query is specified in the create statement itself.
