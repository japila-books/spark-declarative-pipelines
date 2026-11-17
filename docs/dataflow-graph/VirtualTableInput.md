# VirtualTableInput

`VirtualTableInput` is a [TableElement](TableElement.md) and an [Input](Input.md).

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

## load { #load }

??? note "Input"

    ```scala
    load(
      asStreaming: Boolean): DataFrame
    ```

    `load` is part of the [Input](Input.md#load) abstraction.

`load`...FIXME
