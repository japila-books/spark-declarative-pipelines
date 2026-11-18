# VirtualTableInput

`VirtualTableInput` is a [TableElement](TableElement.md) and an [Input](Input.md).

`VirtualTableInput` is a "virtual" counterpart of a table node in a [DataflowGraph](DataflowGraph.md) used for analysis when only the schema matters (and the "parent" table could not be available in a catalog).

## Creating Instance

`VirtualTableInput` takes the following to be created:

* <span id="identifier"> `TableIdentifier`
* <span id="specifiedSchema"> Optional User-Specified Schema
* <span id="incomingFlowIdentifiers"> Incoming Flow `TableIdentifier`s
* <span id="sessionCaseSensitive"> `sessionCaseSensitive` flag
* <span id="availableFlows"> Available [ResolvedFlow](ResolvedFlow.md)s (default: empty)

!!! note ""
    It's assumed that the [destinationIdentifier](../spark-connect/ResolutionCompletedFlow.md#destinationIdentifier) of all the [availableFlows](#availableFlows) is exactly this [identifier](#identifier).

`VirtualTableInput` is created when:

* `CoreDataflowNodeProcessor` is requested to [process a table node](../spark-connect/CoreDataflowNodeProcessor.md#processNode)

## Load Data { #load }

??? note "Input"

    ```scala
    load(
      asStreaming: Boolean): DataFrame
    ```

    `load` is part of the [Input](Input.md#load) abstraction.

`load` infers the schema from the incoming flows unless this [specifiedSchema](#specifiedSchema) is defined.

In the end, `load` creates an empty batch or streaming `DataFrame` (based on `asStreaming` flag).
