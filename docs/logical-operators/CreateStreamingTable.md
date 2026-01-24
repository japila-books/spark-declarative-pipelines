---
title: CreateStreamingTable
---

# CreateStreamingTable Unary Logical Command

`CreateStreamingTable` is a `UnaryCommand` ([Spark SQL]({{ book.spark_sql }}/logical-operators/Command/#UnaryCommand)) and a [CreatePipelineDataset](CreatePipelineDataset.md) that represents `CREATE STREAMING TABLE` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset)) SQL statement (with no `AS` clause) in Spark Declarative Pipelines framework.

`CreateStreamingTable` is handled by [SqlGraphRegistrationContext](../SqlGraphRegistrationContext.md#CreateStreamingTable).

??? note "CreateStreamingTableAsSelect for `CREATE STREAMING TABLE AS` SQL Statement"
    `CREATE STREAMING TABLE AS` SQL statement (with `AS` clause) gives a [CreateStreamingTableAsSelect](CreateStreamingTableAsSelect.md) binary logical command.

## Creating Instance

`CreateStreamingTable` takes the following to be created:

* <span id="name"> Name (`LogicalPlan` ([Spark SQL]({{ book.spark_sql }}/logical-operators/LogicalPlan)))
* <span id="columns"> Columns (`ColumnDefinition`s)
* <span id="partitioning"> Partitioning (`Transform`s ([Spark SQL]({{ book.spark_sql }}/connector/Transform)))
* <span id="tableSpec"> `TableSpecBase`
* <span id="ifNotExists"> `ifNotExists` flag

`CreateStreamingTable` is created when:

* `SparkSqlAstBuilder` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset)) is requested to parse `CREATE STREAMING TABLE` SQL statement
