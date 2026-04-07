# FlowExecution

`FlowExecution` is an [abstraction](#contract) of [flow executions](#implementations) (batch or streaming).

`FlowExecution` can be [executed asynchronously](#executeAsync).

## Contract

### TableIdentifier { #identifier }

```scala
identifier: TableIdentifier
```

### Destination { #destination }

```scala
destination: Output
```

[Output](Output.md)

See:

* [BatchTableWrite](BatchTableWrite.md#destination)
* [SinkWrite](SinkWrite.md#destination)
* [StreamingTableWrite](StreamingTableWrite.md#destination)

### QueryOrigin { #getOrigin }

```scala
getOrigin: QueryOrigin
```

### isStreaming { #isStreaming }

```scala
isStreaming: Boolean
```

See:

* [BatchTableWrite](BatchTableWrite.md#isStreaming)
* [StreamingFlowExecution](StreamingFlowExecution.md#isStreaming)

### PipelineUpdateContext { #updateContext }

```scala
updateContext: PipelineUpdateContext
```

[PipelineUpdateContext](PipelineUpdateContext.md) of this flow execution

### executeInternal { #executeInternal }

```scala
executeInternal(): Future[Unit]
```

See:

* [BatchTableWrite](BatchTableWrite.md#executeInternal)
* [StreamingFlowExecution](StreamingFlowExecution.md#executeInternal)

Used when:

* `FlowExecution` is requested to [execute asynchronously](#executeAsync)

## Implementations

* [BatchTableWrite](BatchTableWrite.md)
* [StreamingFlowExecution](StreamingFlowExecution.md)

## Execute Asynchronously { #executeAsync }

```scala
executeAsync(): Unit
```

`executeAsync` uses the internal [_future](#_future) to control whether this flow has been executed or not.

`executeAsync` [executeInternal](#executeInternal) (and stores the result of this execution in the internal [_future](#_future)).

??? note "Final Method"
    `executeAsync` is a Scala **final method** and may not be overridden in [subclasses](#implementations).

    Learn more in the [Scala Language Specification]({{ scala.spec }}/05-classes-and-objects.html#final).

---

`executeAsync` is used when:

* `GraphExecution` is requested to [planAndStartFlow](GraphExecution.md#planAndStartFlow)

## Human-Friendly Display Name { #displayName }

```scala
displayName: String
```

`displayName` is the `unquotedString` of this [TableIdentifier](#identifier).

??? note "Final Method"
    `displayName` is a Scala **final method** and may not be overridden in [subclasses](#implementations).

    Learn more in the [Scala Language Specification]({{ scala.spec }}/05-classes-and-objects.html#final).

---

`displayName` is used when:

* `SinkWrite` is requested to [startStream](SinkWrite.md#startStream) (used for a query name)
* `StreamingTableWrite` is requested to [startStream](StreamingTableWrite.md#startStream) (used for a query name)
* `GraphExecution` is requested to [stop an execution of a flow](GraphExecution.md#stopFlow) (used for error reporting)
* `FlowProgressEventLogger` is requested to [record a start of an execution of a flow](FlowProgressEventLogger.md#recordStart) (used for error reporting)
