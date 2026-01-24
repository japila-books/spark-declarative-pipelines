---
title: CreateMaterializedViewAsSelect
---

# CreateMaterializedViewAsSelect Logical Operator

`CreateMaterializedViewAsSelect` is a [CreatePipelineDatasetAsSelect](CreatePipelineDatasetAsSelect.md) binary logical command that represents `CREATE MATERIALIZED VIEW ... AS` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset)) SQL statement in Spark Declarative Pipelines framework.

`CreateMaterializedViewAsSelect` is handled by [SqlGraphRegistrationContext](../SqlGraphRegistrationContext.md#CreateMaterializedViewAsSelect).

## Creating Instance

`CreateMaterializedViewAsSelect` takes the following to be created:

* <span id="name"> Name (`LogicalPlan` ([Spark SQL]({{ book.spark_sql }}/LogicalPlan)))
* <span id="columns"> Columns (`ColumnDefinition`s)
* <span id="partitioning"> Partitioning (`Transform` ([Spark SQL]({{ book.spark_sql }}/connector/Transform)))
* <span id="tableSpec"> `TableSpecBase`
* <span id="query"> Query (`LogicalPlan` ([Spark SQL]({{ book.spark_sql }}/LogicalPlan)))
* <span id="originalText"> SQL Text
* <span id="ifNotExists"> `ifNotExists` flag

`CreateMaterializedViewAsSelect` is created when:

* `SparkSqlAstBuilder` ([Spark SQL](../sql/SparkSqlAstBuilder.md#visitCreatePipelineDataset)) is requested to parse `CREATE MATERIALIZED VIEW ... AS` SQL statement
