# AutoCdcFlow

`AutoCdcFlow` is an [UnresolvedFlow](../spark-connect/UnresolvedFlow.md).

`AutoCdcFlow` is resolved to [AutoCdcMergeFlow](AutoCdcMergeFlow.md) (when `FlowResolver` is requested to [resolve a flow](../spark-connect/FlowResolver.md#resolveFlow)).

## Creating Instance

`AutoCdcFlow` takes the following to be created:

* <span id="identifier"> `TableIdentifier`
* <span id="destinationIdentifier"> Destination (`TableIdentifier`)
* <span id="func"> [FlowFunction](../spark-connect/FlowFunction.md)
* <span id="queryContext"> `QueryContext`
* <span id="origin"> `QueryOrigin`
* <span id="changeArgs"> `ChangeArgs`
* <span id="sqlConf"> SQL Config (default: empty)

`AutoCdcFlow` is created when:

* `PipelinesHandler` is requested to [define an AutoCdcFlow](../spark-connect/PipelinesHandler.md#buildAutoCdcFlow)

## once Flag { #once }

??? note "Flow"

    ```scala
    once: Boolean
    ```

    `once` is part of the [Flow](../spark-connect/Flow.md#once) abstraction.

`once` is always disabled (`false`).
