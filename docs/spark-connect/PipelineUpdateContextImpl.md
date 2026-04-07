# PipelineUpdateContextImpl

`PipelineUpdateContextImpl` is a [PipelineUpdateContext](PipelineUpdateContext.md).

## Creating Instance

`PipelineUpdateContextImpl` takes the following to be created:

* <span id="unresolvedGraph"> [DataflowGraph](PipelineUpdateContext.md#unresolvedGraph)
* <span id="eventCallback"> `PipelineEvent` Callback (`PipelineEvent => Unit`)
* <span id="refreshTables"> `TableFilter` of the tables to be refreshed (default: `AllTables`)
* <span id="fullRefreshTables"> `TableFilter` of the tables to be refreshed (default: `NoTables`)
* [Pipeline Storage Location](#storageRoot)

While being created, `PipelineUpdateContextImpl` [validates the storage root](#validateStorageRoot).

`PipelineUpdateContextImpl` is created when:

* `PipelinesHandler` is requested to [run a pipeline](PipelinesHandler.md#startRun)

### Pipeline Storage Location { #storageRoot }

!!! note "Storage Root"
    **Storage Location** is also known as **Storage Root**.

`PipelineUpdateContextImpl` is given a [storage location](PipelineUpdateContext.md#storageRoot) when [created](#creating-instance).

The storage root is the `storage` of the `StartRun` pipeline command (when `PipelinesHandler` is requested to [run a pipeline update](PipelinesHandler.md#startRun)).

This storage root is immediately [validated](#validateStorageRoot).

### Validate Storage Root { #validateStorageRoot }

```scala
validateStorageRoot(
  storageRoot: String): Unit
```

`validateStorageRoot` asserts that the given `storageRoot` meets the following requirements:

1. It is an absolute path
1. The URI schema is defined

Otherwise, `validateStorageRoot` reports a `SparkException`:

```text
Pipeline storage root must be an absolute path with a URI scheme (e.g., file://, s3a://, hdfs://).
Got: `[storage_root]`.
```
