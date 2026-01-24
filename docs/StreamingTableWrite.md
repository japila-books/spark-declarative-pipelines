---
title: StreamingTableWrite
---

# StreamingTableWrite Flow Execution

`StreamingTableWrite` is a [StreamingFlowExecution](StreamingFlowExecution.md) that represents a [StreamingFlow](StreamingFlow.md) at execution.

When [executed](#startStream), `StreamingTableWrite` starts a streaming query (a streaming `DataFrame` ([Spark SQL]({{ book.spark_sql }}/DataFrame))) to **append** new rows to a [Table](#destination) destination.

`StreamingTableWrite` uses `DataStreamWriter` ([Spark Structured Streaming]({{ book.structured_streaming }}/DataStreamWriter/)) for writing streaming data with the following properties:

`DataStreamWriter`'s Property | Value
-|-
 `queryName` | This [displayName](FlowExecution.md#displayName)
 `format` | The [format](Table.md#format) of this [output table](#destination) (if defined)
 `checkpointLocation` option | This [checkpoint path](StreamingFlowExecution.md#checkpointPath)
 `outputMode` | Always `Append` ([Spark Structured Streaming]({{ book.structured_streaming }}/OutputMode/#append))
 `trigger` | Always `AvailableNowTrigger` ([Spark Structured Streaming]({{ book.structured_streaming }}/Trigger/#AvailableNowTrigger))<br>(indirectly through this [streaming trigger](GraphExecution.md#streamTrigger))

## Creating Instance

`StreamingTableWrite` takes the following to be created:

* <span id="identifier"> [TableIdentifier](FlowExecution.md#identifier)
* <span id="flow"> [ResolvedFlow](StreamingFlowExecution.md#flow)
* <span id="graph"> [DataflowGraph](DataflowGraph.md)
* <span id="updateContext"> [PipelineUpdateContext](FlowExecution.md#updateContext)
* <span id="checkpointPath"> [Checkpoint Location](StreamingFlowExecution.md#checkpointPath)
* <span id="trigger"> [Streaming Trigger](StreamingFlowExecution.md#trigger)
* <span id="destination"> [Destination](FlowExecution.md#destination) ([Table](Table.md))
* <span id="sqlConf"> [SQL Configuration](StreamingFlowExecution.md#sqlConf)

`StreamingTableWrite` is created when:

* `FlowPlanner` is requested to [plan a StreamingFlow](FlowPlanner.md#plan)

## Execute Streaming Query { #startStream }

??? note "StreamingFlowExecution"

    ```scala
    startStream(): StreamingQuery
    ```

    `startStream` is part of the [StreamingFlowExecution](StreamingFlowExecution.md#startStream) abstraction.

`startStream` builds the logical query plan of this [flow](#flow)'s structured query (requesting the [DataflowGraph](#graph) to [reanalyze](DataflowGraph.md#reanalyzeFlow) this [flow](#flow)).

`startStream` creates a `DataStreamWriter` ([Spark Structured Streaming]({{ book.structured_streaming }}/DataStreamWriter/)) with the following:

`DataStreamWriter`'s Property | Value
-|-
 `queryName` | This [displayName](FlowExecution.md#displayName)
 `checkpointLocation` option | This [checkpoint path](#checkpointPath)
 `trigger` | This [streaming trigger](#trigger)
 `outputMode` | Always `Append` ([Spark Structured Streaming]({{ book.structured_streaming }}/OutputMode/#append))
 `format` | The [format](Table.md#format) of this [output table](#destination) (if defined)

In the end, `startStream` starts the streaming write query to this [output table](#destination).
