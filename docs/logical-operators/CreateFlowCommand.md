---
title: CreateFlowCommand
---

# CreateFlowCommand Binary Logical Operator

`CreateFlowCommand` is a `BinaryCommand` logical operator that represents `CREATE FLOW ... AS INSERT INTO ... BY NAME` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineInsertIntoFlow)) SQL statement in Spark Declarative Pipelines.

`CreateFlowCommand` is handled by [SqlGraphRegistrationContext](../SqlGraphRegistrationContext.md#CreateFlowCommand).

`Pipelines` execution planning strategy is used to prevent direct execution of Spark Declarative Pipelines SQL stataments.

## Creating Instance

`CreateFlowCommand` takes the following to be created:

* <span id="name"> Name (`UnresolvedIdentifier` leaf logical operator)
* <span id="flowOperation"> Flow operation (`InsertIntoStatement` ([Spark SQL]({{ book.spark_sql }}/logical-operators/InsertIntoStatement)) unary logical operator)
* <span id="comment"> Comment (optional)

`CreateFlowCommand` is created when:

* `SparkSqlAstBuilder` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineInsertIntoFlow)) is requested to parse `CREATE FLOW AS INSERT INTO BY NAME` SQL statement
