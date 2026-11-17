# TableElement

`TableElement` is an [extension](#contract) of the [GraphElement](GraphElement.md) abstraction for [graph elements backed by a table](#implementations) (with an optional [user-specified schema](#specifiedSchema)).

## Contract

### User-Specified Schema { #specifiedSchema }

```scala
specifiedSchema: Option[StructType]
```

Used when:

* `CoreDataflowNodeProcessor` is requested to [process a table node](../spark-connect/CoreDataflowNodeProcessor.md#processNode)
* `DatasetManager` is requested to [materialize a table](../spark-connect/DatasetManager.md#materializeTable)
* `VirtualTableInput` is requested to [load data](VirtualTableInput.md#load)
* `GraphValidations` is requested to [validateUserSpecifiedSchemas](../spark-connect/GraphValidations.md#validateUserSpecifiedSchemas)

## Implementations

??? note "Sealed Trait"
    `TableElement` is a Scala **sealed trait** which means that all of the implementations are in the same compilation unit (a single file).

    Learn more in the [Scala Language Specification]({{ scala.spec }}/05-classes-and-objects.html#sealed).

* [Table](Table.md)
* [VirtualTableInput](VirtualTableInput.md)
