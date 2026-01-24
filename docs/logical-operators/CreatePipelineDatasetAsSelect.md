---
title: CreatePipelineDatasetAsSelect
---

# CreatePipelineDatasetAsSelect Binary Logical Commands

`CreatePipelineDatasetAsSelect` is an [extension](#contract) of `BinaryCommand` ([Spark SQL]({{ book.spark_sql }}/logical-operators/Command/#BinaryCommand)) and [CreatePipelineDataset](CreatePipelineDataset.md) abstractions for [CTAS-like CREATE statements](#implementations):

* `CREATE MATERIALIZED VIEW AS` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset))
* `CREATE STREAMING TABLE AS` ([Spark SQL]({{ book.spark_sql }}/sql/SparkSqlAstBuilder/#visitCreatePipelineDataset))

`CreatePipelineDatasetAsSelect` is a `CTEInChildren`.

## Contract

### Query { #query }

```scala
query: LogicalPlan
```

### Original SQL Text { #originalText }

```scala
originalText: String
```

## Implementations

* [CreateMaterializedViewAsSelect](CreateMaterializedViewAsSelect.md)
* [CreateStreamingTableAsSelect](CreateStreamingTableAsSelect.md)
