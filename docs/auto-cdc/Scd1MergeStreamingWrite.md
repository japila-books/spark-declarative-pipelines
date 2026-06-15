# Scd1MergeStreamingWrite

`Scd1MergeStreamingWrite` is a [StreamingFlowExecution](../spark-connect/StreamingFlowExecution.md) with [AutoCdcMergeWriteBase](AutoCdcMergeWriteBase.md) for [AutoCdcMergeFlow](AutoCdcMergeFlow.md)s (of `Type1` with [Table](../spark-connect/Table.md) output).

## Creating Instance

`Scd1MergeStreamingWrite` takes the following to be created:

* <span id="identifier"> `TableIdentifier`
* <span id="flow"> [AutoCdcMergeFlow](AutoCdcMergeFlow.md)
* <span id="graph"> [DataflowGraph](../spark-connect/DataflowGraph.md)
* <span id="updateContext"> [PipelineUpdateContext](../spark-connect/PipelineUpdateContext.md)
* <span id="checkpointPath"> Checkpoint Path
* <span id="trigger"> `Trigger`
* <span id="destination"> [Table](../spark-connect/Table.md)
* <span id="sqlConf"> SQL Configuration

`Scd1MergeStreamingWrite` is created when:

* `FlowPlanner` is requested to [plan an AutoCdcMergeFlow](../spark-connect/FlowPlanner.md#plan) (of `Type1` with [Table](../spark-connect/Table.md) output)

## Start Streaming Query { #startStream }

??? note "StreamingFlowExecution"

    ```scala
    startStream(): StreamingQuery
    ```

    `startStream` is part of the [StreamingFlowExecution](../spark-connect/StreamingFlowExecution.md#startStream) abstraction.

`startStream` gets the source's Change Data Feed. `startStream` requests this [DataflowGraph](#graph) to [resolve](../spark-connect/DataflowGraph.md#reanalyzeFlow) this [AutoCdcMergeFlow](#flow) to take the [DataFrame](../spark-connect/ResolvedFlow.md#df).

`startStream` [createAuxiliaryTableIfNotExists](#createAuxiliaryTableIfNotExists).

`startStream` creates a [Scd1ForeachBatchHandler](Scd1ForeachBatchHandler.md) with a [Scd1BatchProcessor](Scd1BatchProcessor.md).

In the end, `startStream` starts a streaming query with `foreachBatch` ([Spark Structured Streaming]({{ book.structured_streaming }}/DataStreamWriter/#foreachBatch)) streaming operator.
`foreachBatch` operator executes the [Scd1ForeachBatchHandler](Scd1ForeachBatchHandler.md#execute) for every streaming batch.
