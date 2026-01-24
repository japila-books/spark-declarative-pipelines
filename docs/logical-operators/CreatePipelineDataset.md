---
title: CreatePipelineDataset
---

# CreatePipelineDataset Logical Commands

`CreatePipelineDataset` is an [extension](#contract) of the `Command` ([Spark SQL]({{ book.spark_sql }}/logical-operators/Command)) abstraction for [CREATE pipeline commands](#implementations) in Spark Declarative Pipelines framework:

* `CREATE MATERIALIZED VIEW ... AS` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset))
* `CREATE STREAMING TABLE` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset))
* `CREATE STREAMING TABLE AS` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset))

## Contract (Subset)

### Name { #name }

```scala
name: LogicalPlan
```

## Implementations

* [CreatePipelineDatasetAsSelect](CreatePipelineDatasetAsSelect.md)
* [CreateStreamingTable](CreateStreamingTable.md)
