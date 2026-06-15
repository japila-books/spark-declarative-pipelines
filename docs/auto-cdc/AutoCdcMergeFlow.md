# AutoCdcMergeFlow

`AutoCdcMergeFlow` is a [ResolvedFlow](../spark-connect/ResolvedFlow.md) that applies a CDC event stream to a target table via MERGE.

`AutoCdcMergeFlow` represents an [AutoCdcFlow](#flow) at execution time (when `FlowResolver` is requested to [resolve a flow](../spark-connect/FlowResolver.md#resolveFlow)).

`AutoCdcMergeFlow` can be one of the two [SCD Type](ChangeArgs.md#storedAsScdType)s (based on [ChangeArgs](#changeArgs)):

* `Type1`
* `Type2`

`AutoCdcMergeFlow` type 1 (with a [Table](../spark-connect/Table.md) output) is executed as a [Scd1MergeStreamingWrite](Scd1MergeStreamingWrite.md). All the other variants fail at execution either with an `UnsupportedOperationException` or an `AnalysisException`.

## Creating Instance

`AutoCdcMergeFlow` takes the following to be created:

* <span id="flow"> [AutoCdcFlow](AutoCdcFlow.md)
* <span id="funcResult"> [FlowFunctionResult](../spark-connect/FlowFunctionResult.md)

`AutoCdcMergeFlow` is created when:

* `FlowResolver` is requested to [resolve a flow](../spark-connect/FlowResolver.md#resolveFlow) (that happens to be an [AutoCdcFlow](AutoCdcFlow.md))
