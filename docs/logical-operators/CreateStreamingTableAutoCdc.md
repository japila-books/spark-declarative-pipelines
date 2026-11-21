---
title: CreateStreamingTableAutoCdc
---

# CreateStreamingTableAutoCdc Binary Logical Command

`CreateStreamingTableAutoCdc` is a `BinaryCommand` ([Spark SQL]({{ book.spark_sql }}/logical-operators/Command/#BinaryCommand)) and [CreatePipelineDataset](CreatePipelineDataset.md) logical operator.

`CreateStreamingTableAutoCdc` represents `CREATE STREAMING TABLE...FLOW AUTO CDC` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset)) SQL statement in Spark Declarative Pipelines framework for [Auto CDC Flows](../auto-cdc/index.md).

`CreateStreamingTableAutoCdc` is handled by [SqlGraphRegistrationContext](../SqlGraphRegistrationContext.md#CreateStreamingTableAutoCdc).

## Creating Instance

`CreateStreamingTableAutoCdc` takes the following to be created:

* <span id="name"> Target Streaming Table's Name (`LogicalPlan`)
* <span id="columns"> User-specified columns of the target streaming table
* <span id="partitioning"> Column-based partitioning of the target streaming table
* <span id="tableSpec"> Table Specification
* <span id="ifNotExists"> `ifNotExists` flag
* <span id="source"> Change Events Source (`LogicalPlan`)
* <span id="keys"> Key Columns
* <span id="deleteCondition"> `DELETE` source row condition
* <span id="sequenceByExpr"> Order expression for CDC events for out-of-order arrivals
* <span id="includeColumns"> Source columns to include (optional)
* <span id="excludeColumns"> Source columns to exclude (optional)
* <span id="storedAsScdType"> SCD type of the target table
* <span id="trackHistoryColumns"> `trackHistoryColumns` (optional)
* <span id="trackHistoryExceptColumns"> `trackHistoryExceptColumns` (optional)

`CreateStreamingTableAutoCdc` is created when:

* `SparkSqlAstBuilder` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset)) is requested to parse `CREATE STREAMING TABLE...FLOW AUTO CDC` SQL statement
