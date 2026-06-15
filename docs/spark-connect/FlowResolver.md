# FlowResolver

## Creating Instance

`FlowResolver` takes the following to be created:

* <span id="rawGraph"> [DataflowGraph](DataflowGraph.md)

`FlowResolver` is created alongside a [CoreDataflowNodeProcessor](CoreDataflowNodeProcessor.md).

## attemptResolveFlow { #attemptResolveFlow }

```scala
attemptResolveFlow(
  flowToResolve: UnresolvedFlow,
  allInputs: Set[TableIdentifier],
  availableResolvedInputs: Map[TableIdentifier, Input]): ResolvedFlow
```

`attemptResolveFlow`...FIXME

---

`attemptResolveFlow` is used when:

* `CoreDataflowNodeProcessor` is requested to [processUnresolvedFlow](CoreDataflowNodeProcessor.md#processUnresolvedFlow)

### resolveFlow { #resolveFlow }

```scala
resolveFlow(
  flow: UnresolvedFlow,
  funcResult: FlowFunctionResult): ResolvedFlow
```

`resolveFlow` resolves the given [UnresolvedFlow](UnresolvedFlow.md) as follows:

* For [AutoCdcFlow](../auto-cdc/AutoCdcFlow.md), `resolveFlow` creates a [AutoCdcMergeFlow](../auto-cdc/AutoCdcMergeFlow.md).
* For [UntypedFlow](UntypedFlow.md), `resolveFlow` [transformUntypedFlowToResolvedFlow](#transformUntypedFlowToResolvedFlow).

### transformUntypedFlowToResolvedFlow { #transformUntypedFlowToResolvedFlow }

```scala
transformUntypedFlowToResolvedFlow(
  flow: UntypedFlow,
  funcResult: FlowFunctionResult): ResolvedFlow
```

`transformUntypedFlowToResolvedFlow` resolves the given [UntypedFlow](UntypedFlow.md) as follows (and in that order):

* [AppendOnceFlow](AppendOnceFlow.md) for a [once flow](UntypedFlow.md#once)
* [StreamingFlow](StreamingFlow.md) for the given [FlowFunctionResult](FlowFunctionResult.md) with a streaming `DataFrame`
* [CompleteFlow](CompleteFlow.md), otherwise
