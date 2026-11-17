# Table

`Table` is a [TableElement](TableElement.md) and [Dataset](Dataset.md) output.

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

* `PipelinesHandler` is requested to [define an Output](../spark-connect/PipelinesHandler.md#defineOutput)
* `SqlGraphRegistrationContext` is requested to handle [CreateMaterializedViewAsSelect](../spark-connect/SqlGraphRegistrationContext.md#CreateMaterializedViewAsSelect), [CreateStreamingTableAsSelect](../spark-connect/SqlGraphRegistrationContext.md#CreateStreamingTableAsSelect), [CreateStreamingTable](../spark-connect/SqlGraphRegistrationContext.md#CreateStreamingTable), [CreateStreamingTableAutoCdc](../spark-connect/SqlGraphRegistrationContext.md#CreateStreamingTableAutoCdc) logical commands

## Storage Location { #path }

??? note "Dataset"

    ```scala
    path: String
    ```

    `path` is part of the [Dataset](Dataset.md#path) abstraction.

`path`...FIXME

## DatasetType { #datasetType }

```scala
datasetType: DatasetType
```

`datasetType` is `STREAMING_TABLE` for [isStreamingTable](#isStreamingTable). Otherwise, `datasetType` is `MATERIALIZED_VIEW`.
