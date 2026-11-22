# Auto CDC Flows

[dp.create_auto_cdc_flow](../pyspark/index.md#create_auto_cdc_flow) or `CREATE FLOW AS AUTO CDC` creates an Auto CDC flow into a target streaming table from a Change Data Feed source.

??? note "AutoCDC Target Streaming Table"
    Target table should be created using the [dp.create_streaming_table](../pyspark/index.md#create_streaming_table) function.

```py
# Neither Delta Lake nor Iceberg work
# Used as formats for demo purposes only

from pyspark import pipelines as dp
from pyspark.sql import DataFrame


@dp.table
def cdc_events() -> DataFrame:
    return spark.readStream.format("delta").table("cdc_source")

dp.create_streaming_table(
    name="customers",
    format="iceberg",
)

dp.create_auto_cdc_flow(
    target="customers",
    source="cdc_events",
    keys=["id"],
    sequence_by="event_ts",
    apply_as_deletes="op = 'DELETE'",
    except_column_list=["op", "event_ts"],
    stored_as_scd_type=2,
    track_history_column_list=["address"],
)
```

Auto CDC flows are represented as [AutoCdcFlow](./AutoCdcFlow.md)s.

`AutoCdcFlow` is resolved to [AutoCdcMergeFlow](AutoCdcMergeFlow.md) (when `FlowResolver` is requested to [resolve a flow](../spark-connect/FlowResolver.md#resolveFlow)).

`AutoCdcMergeFlow` applies a CDC event stream to a target table using MERGE operation.

`AutoCdcMergeFlow` can be one of the two [SCD Type](ChangeArgs.md#storedAsScdType)s (based on [ChangeArgs](#changeArgs)):

* SCD Type 1
* SCD Type 2

`AutoCdcMergeFlow` SCD Type 1 (with a [Table](../spark-connect/Table.md) output) is executed as a [Scd1MergeStreamingWrite](Scd1MergeStreamingWrite.md).
All the other variants fail at execution either with an `UnsupportedOperationException` or an `AnalysisException`.

`Scd1MergeStreamingWrite` is executed as a streaming query with `foreachBatch` ([Spark Structured Streaming]({{ book.structured_streaming }}/DataStreamWriter/#foreachBatch)) streaming operator and [Trigger.AvailableNow](../spark-connect/TriggeredGraphExecution.md#streamTrigger).

## SupportsRowLevelOperations

Spark Declarative Pipelines [asserts](../DatasetManager.md#requireAutoCdcTargetSupportsRowLevelOps) that the AutoCDC target streaming table supports row-level data mutations that require rewriting data.

Auto CDC flows (with MERGE writes) require a target table to use a Spark SQL connector with `SupportsRowLevelOperations` ([Spark SQL]({{ book.spark_sql }}/connector/SupportsRowLevelOperations/)) support.
