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
