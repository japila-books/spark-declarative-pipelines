# DatasetManager

`DatasetManager` is a global manager of [materialize datasets](#materializeDatasets) (tables and persistent views) right after a [pipeline update](PipelineExecution.md#startPipeline).

![DatasetManager](./images/DatasetManager.png)

!!! note "Materialization"
    **Materialization** is a process of publishing tables and persistent views to session `TableCatalog` ([Spark SQL]({{ book.spark_sql }}/connector/catalog/TableCatalog)) and `SessionCatalog` ([Spark SQL]({{ book.spark_sql }}/SessionCatalog)), for tables and persistent views, respectively.

??? note "Scala object"
    `DatasetManager` is an `object` in Scala which means it is a class that has exactly one instance (itself).
    A Scala `object` is created lazily when it is referenced for the first time.

    Learn more in [Tour of Scala](https://docs.scala-lang.org/tour/singleton-objects.html).

## Materialize Datasets { #materializeDatasets }

```scala
materializeDatasets(
  resolvedDataflowGraph: DataflowGraph,
  context: PipelineUpdateContext): DataflowGraph
```

`materializeDatasets` [finds the tables to be refreshed](#constructFullRefreshSet) among the [tables](DataflowGraph.md#tables) of the given resolved [DataflowGraph](DataflowGraph.md).

??? warning "All the tables to be refreshed ignored"
    The first collection of tables to be refreshed is completely ignored from the [tables to be refreshed](#constructFullRefreshSet).

`materializeDatasets` [materializes](#materializeTable) every table found to be refreshed or fully refreshed (that are referred to as _materialized_ in the code).

In the end, `materializeDatasets` [materializeViews](#materializeViews).

---

`materializeDatasets` is used when:

* `PipelineExecution` is requested to [initialize the dataflow graph](PipelineExecution.md#initializeGraph)

### Materialize Table { #materializeTable }

```scala
materializeTable(
  resolvedDataflowGraph: DataflowGraph,
  table: Table,
  isFullRefresh: Boolean,
  context: PipelineUpdateContext): Table
```

`materializeTable` uses `TableCatalog` ([Spark SQL]({{ book.spark_sql }}/connector/catalog/TableCatalog)) for the given [Table](Table.md) to make necessary changes:

* Loading and altering the table (even truncating it for a full refresh), if available
* Creating the table, otherwise

In the end, `materializeTable` returns a [Table](Table.md) that may have the [normalized table storage path](Table.md#normalizedPath) property set to be the `location` property of this table.

---

`materializeTable` prints out the following INFO message to the logs:

```text
Materializing metadata for table [identifier].
```

`materializeTable` uses the given [PipelineUpdateContext](PipelineUpdateContext.md) to find the `CatalogManager` ([Spark SQL]({{ book.spark_sql }}/connector/catalog/CatalogManager)) (in the [SparkSession](PipelineUpdateContext.md#spark)).

`materializeTable` finds the `TableCatalog` ([Spark SQL]({{ book.spark_sql }}/connector/catalog/TableCatalog)) for the table.

??? note "TableCatalog"
    `materializeTable` uses the catalog part of the [table identifier](Table.md#identifier), if defined, to find the table's catalog ([Spark SQL]({{ book.spark_sql }}/connector/catalog/CatalogPlugin)).
    Otherwise, `materializeTable` defaults to the current catalog ([Spark SQL]({{ book.spark_sql }}/connector/catalog/CatalogManager/#currentCatalog)).

`materializeTable` requests the `TableCatalog` ([Spark SQL]({{ book.spark_sql }}/connector/catalog/TableCatalog/#loadTable)) to load the table, if exists ([Spark SQL]({{ book.spark_sql }}/connector/catalog/TableCatalog/#tableExists)) already.

For an existing table, `materializeTable` wipes data out (`TRUNCATE TABLE`) if it is `isFullRefresh` or the table is not [streaming](Table.md#isStreamingTable).

For an existing table, `materializeTable` requests the `TableCatalog` ([Spark SQL]({{ book.spark_sql }}/connector/catalog/TableCatalog/#alterTable)) to alter the table if there are any changes in the schema or table properties.

Unless created already, `materializeTable` requests the `TableCatalog` ([Spark SQL]({{ book.spark_sql }}/connector/catalog/TableCatalog/#createTable)) to create the table.

In the end, `materializeTable` requests the `TableCatalog` ([Spark SQL]({{ book.spark_sql }}/connector/catalog/TableCatalog/#loadTable)) to load the materialized table and returns the given [Table](Table.md) back (with the [normalized table storage path](Table.md#normalizedPath) updated to the `location` property of the materialized table).

### Materialize Views { #materializeViews }

```scala
materializeViews(
  virtualizedConnectedGraphWithTables: DataflowGraph,
  context: PipelineUpdateContext): Unit
```

`materializeViews` requests the given [DataflowGraph](DataflowGraph.md) for the [persisted views](DataflowGraph.md#persistedViews) to materialize (_publish or refresh_).

!!! note "Publish (Materialize) Views"
    To publish a view, it is required that all the input sources must exist in the metastore.
    If a Persisted View target reads another Persisted View source, the source must be published first.

`materializeViews`...FIXME

For each view to be persisted (with no pending inputs), `materializeViews` [materialize the view](#materializeView).

#### Materialize View { #materializeView }

```scala
materializeView(
  view: View,
  flow: ResolvedFlow,
  spark: SparkSession): Unit
```

`materializeView` executes a `CreateViewCommand` ([Spark SQL]({{ book.spark_sql }}/logical-operators/CreateViewCommand/)) logical command (with `PersistedView` view type).

---

`materializeView` creates a `CreateViewCommand` ([Spark SQL]({{ book.spark_sql }}/logical-operators/CreateViewCommand/)) logical command (as a `PersistedView` with `allowExisting` and `replace` flags enabled).

`materializeView` requests the given [ResolvedFlow](ResolvedFlow.md) for the [QueryContext](ResolutionCompletedFlow.md#queryContext) to set the current catalog and current database, if defined, in the session `CatalogManager` ([Spark SQL]({{ book.spark_sql }}/connector/catalog/CatalogManager)).

In the end, `materializeView` executes the `CreateViewCommand` ([Spark SQL]({{ book.spark_sql }}/logical-operators/CreateViewCommand/#run)).

## Find Tables to Refresh (incl. Full Refresh) { #constructFullRefreshSet }

```scala
constructFullRefreshSet(
  graphTables: Seq[Table],
  context: PipelineUpdateContext): (Seq[Table], Seq[TableIdentifier], Seq[TableIdentifier])
```

`constructFullRefreshSet` determines the following table (identifiers) collections:

1. [Table](Table.md)s to be refreshed (incl. a full refresh)
1. `TableIdentifier`s of the tables to be refreshed (excl. fully refreshed)
1. `TableIdentifier`s of the tables to be fully refreshed only

??? note "First return value ignored by `materializeDatasets`"
    It appears that the first collection of tables to be refreshed is completely ignored while [materializing datasets](#materializeDatasets)
    (the only place in the codebase that uses it).

If there are tables to be fully refreshed yet not allowed for a full refresh (per [pipelines.reset.allowed](PipelinesTableProperties.md#resetAllowed) table property), `constructFullRefreshSet` prints out the following INFO message to the logs:

```text
Skipping full refresh on some tables
because pipelines.reset.allowed was set to false.
Tables: [fullRefreshNotAllowed]
```

---

`constructFullRefreshSet` is used when:

* `DatasetManager` is requested to [materialize datasets](#materializeDatasets)
