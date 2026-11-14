# PipelinesCatalogUtils

## resolveTableCatalog { #resolveTableCatalog }

```scala
resolveTableCatalog(
  spark: SparkSession,
  ident: TableIdentifier): (TableCatalog, Identifier)
```

`resolveTableCatalog`...FIXME

---

`resolveTableCatalog` is used when:

* `DatasetManager` is requested to [materialize datasets](./spark-connect/DatasetManager.md#materializeDatasets) (incl. [tables](./spark-connect/DatasetManager.md#materializeTable) and [auxiliary tables](./spark-connect/DatasetManager.md#materializeAuxiliaryTable))
