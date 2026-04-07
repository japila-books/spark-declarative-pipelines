# FlowSystemMetadata

`FlowSystemMetadata` is a [SystemMetadata](SystemMetadata.md) associated with a [Flow](#flow).

## Creating Instance

`FlowSystemMetadata` takes the following to be created:

* <span id="context"> [PipelineUpdateContext](PipelineUpdateContext.md)
* <span id="flow"> [Flow](Flow.md)
* <span id="graph"> [DataflowGraph](DataflowGraph.md)

`FlowSystemMetadata` is created when:

* `FlowPlanner` is requested to [plan a StreamingFlow for execution](FlowPlanner.md#plan)
* `State` is requested to [clear out the state of a flow](State.md#reset)

## Latest Checkpoint Location { #latestCheckpointLocation }

```scala
latestCheckpointLocation: String
```

`latestCheckpointLocation` determines the path of the [latest checkpoint directory](SystemMetadata.md#getLatestCheckpointDir) under the [checkpoints root directory](#flowCheckpointsDirOpt).

---

`latestCheckpointLocation` is used when:

* `FlowPlanner` is requested to [plan a StreamingFlow](FlowPlanner.md#plan)
* `State` is requested to [reset a flow](State.md#reset)

## <span id="flowCheckpointsDirOpt"> Checkpoint Directory { #flowCheckpointsDir }

```scala
flowCheckpointsDir(): Path
```

`flowCheckpointsDir` returns the checkpoint directory for this [Flow](#flow) (under the [storage root](PipelineUpdateContext.md#storageRoot)).

---

`flowCheckpointsDir` asserts that the [destination table](Flow.md#destinationIdentifier) of this [Flow](#flow) is either a [table](DataflowGraph.md#table) or [sink](DataflowGraph.md#sink).

`flowCheckpointsDir` builds a path that is the [storageRoot](PipelineUpdateContext.md#storageRoot) (of this [PipelineUpdateContext](#context)) followed by `_checkpoints` (hardcoded).

!!! note "Checkpoint Root Directory"
    **Checkpoint Root Directory** of a SDP project is always the [storageRoot](PipelineUpdateContext.md#storageRoot) followed by `_checkpoints`.

`flowCheckpointsDir` creates a path of the checkpoint directory based on the following:

1. A path for the table identifier (of the [destination table](Flow.md#destinationIdentifier)) with the dots replaced by path separators.
1. The "table" part of the [TableIdentifier](Flow.md#identifier) of this [Flow](#flow).

In the end, `flowCheckpointsDir` prints out the following INFO message to the logs:

```text
Flow [flowName] using checkpoint directory: [checkpointDir]
```

??? note "IllegalArgumentException"
    throws an `IllegalArgumentException` when the [destination table](Flow.md#destinationIdentifier) of this [Flow](#flow) is neither a [table](DataflowGraph.md#table) nor [sink](DataflowGraph.md#sink).

---

`flowCheckpointsDir` is used when:

* FIXME

### flowCheckpointsDirOpt

```scala
flowCheckpointsDirOpt(): Option[Path]
```

??? warning "SPARK-56325 Refactor FlowSystemMetadata.flowCheckpointsDirOpt"
    [It should soon be gone.](https://github.com/apache/spark/pull/55145)
