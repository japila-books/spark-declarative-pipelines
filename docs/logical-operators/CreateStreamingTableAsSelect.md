---
title: CreateStreamingTableAsSelect
---

# CreateStreamingTableAsSelect Binary Logical Command

`CreateStreamingTableAsSelect` is a [CreatePipelineDatasetAsSelect](CreatePipelineDatasetAsSelect.md) binary logical command that represents `CREATE STREAMING TABLE AS` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset)) SQL statement in Spark Declarative Pipelines framework.

`CreateStreamingTableAsSelect` is handled by [SqlGraphRegistrationContext](../SqlGraphRegistrationContext.md#CreateStreamingTableAsSelect).

??? note "CreateStreamingTable for `CREATE STREAMING TABLE` SQL Statement"
    `CREATE STREAMING TABLE` SQL statement (with no `AS` clause) gives a [CreateStreamingTable](CreateStreamingTable.md) unary logical command.

## Creating Instance

`CreateStreamingTableAsSelect` takes the following to be created:

* <span id="name"> Name (`LogicalPlan` ([Spark SQL]({{ book.spark_sql }}/logical-operators/LogicalPlan)))
* <span id="columns"> Colums (`ColumnDefinition`s)
* <span id="partitioning"> Partitioning (`Transform`s ([Spark SQL]({{ book.spark_sql }}/connector/Transform)))
* <span id="tableSpec"> `TableSpecBase`
* <span id="query"> `CREATE` query (`LogicalPlan` ([Spark SQL]({{ book.spark_sql }}/logical-operators/LogicalPlan)))
* <span id="originalText"> Original SQL Text
* <span id="ifNotExists"> `ifNotExists` flag

`CreateStreamingTableAsSelect` is created when:

* `SparkSqlAstBuilder` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset)) is requested to parse `CREATE STREAMING TABLE AS` SQL statement
