# Table

`Table` is a [TableInput](TableInput.md) and a [Dataset](Dataset.md) output.

## Creating Instance

`Table` takes the following to be created:

* <span id="identifier"> `TableIdentifier`
* <span id="specifiedSchema"> `specifiedSchema` (optional)
* <span id="partitionCols"> `partitionCols` (optional)
* <span id="clusterCols"> `clusterCols` (optional)
* <span id="normalizedPath"> `normalizedPath` (optional)
* <span id="properties"> Properties
* <span id="comment"> Comment (optional)
* <span id="origin"> `QueryOrigin`
* <span id="isStreamingTable"> `isStreamingTable` flag
* <span id="format"> Format (optional)

`Table` is created when:

* `PipelinesHandler` is requested to [define an Output](PipelinesHandler.md#defineOutput)
* `SqlGraphRegistrationContext` is requested to handle [CreateMaterializedViewAsSelect](SqlGraphRegistrationContext.md#CreateMaterializedViewAsSelect), [CreateStreamingTableAsSelect](SqlGraphRegistrationContext.md#CreateStreamingTableAsSelect), [CreateStreamingTable](SqlGraphRegistrationContext.md#CreateStreamingTable) logical commands

## Load Data { #load }

??? note "Input"

    ```scala
    load(
      readOptions: InputReadOptions): DataFrame
    ```

    `load` is part of the [Input](Input.md#load) abstraction.

`load` is a "shortcut" to create a batch or a streaming `DataFrame` (based on the type of the given [InputReadOptions](InputReadOptions.md)).

For [StreamingReadOptions](InputReadOptions.md#StreamingReadOptions), `load` creates a `DataStreamReader` ([Spark Structured Streaming]({{ book.structured_streaming }}/DataStreamReader/)) to load a table (using `DataStreamReader.table` operator) with the given `StreamingReadOptions`.

For [BatchReadOptions](InputReadOptions.md#BatchReadOptions), `load` creates a [DataFrameReader](../DataFrameReader.md) to load a table (using `DataFrameReader.table` operator).
