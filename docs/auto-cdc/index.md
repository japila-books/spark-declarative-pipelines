# Auto CDC Flows

[dp.create_auto_cdc_flow](../pyspark/index.md#create_auto_cdc_flow) is used in PySpark to create an Auto CDC flow into the target table from the Change Data Capture (CDC) source.

Target table must have already been created using the [dp.create_streaming_table](../pyspark/index.md#create_streaming_table) function.

Auto CDC flows are represented as [AutoCdcFlow](./AutoCdcFlow.md)s.

`AutoCdcFlow` is resolved to [AutoCdcMergeFlow](AutoCdcMergeFlow.md) (when `FlowResolver` is requested to [resolve a flow](../spark-connect/FlowResolver.md#resolveFlow)).

`AutoCdcMergeFlow` applies a CDC event stream to a target table via MERGE.

`AutoCdcMergeFlow` can be one of the two [SCD Type](ChangeArgs.md#storedAsScdType)s (based on [ChangeArgs](#changeArgs)):

* SCD Type 1
* SCD Type 2

`AutoCdcMergeFlow` SCD Type 1 (with a [Table](../spark-connect/Table.md) output) is executed as a [Scd1MergeStreamingWrite](Scd1MergeStreamingWrite.md).
All the other variants fail at execution either with an `UnsupportedOperationException` or an `AnalysisException`.

`Scd1MergeStreamingWrite` is executed as a streaming query with `foreachBatch` ([Spark Structured Streaming]({{ book.structured_streaming }}/DataStreamWriter/#foreachBatch)) streaming operator and [Trigger.AvailableNow](../spark-connect/TriggeredGraphExecution.md#streamTrigger).
