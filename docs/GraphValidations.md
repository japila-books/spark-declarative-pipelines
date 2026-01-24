# GraphValidations

`GraphValidations` is a set of validations on [DataflowGraph](DataflowGraph.md)s.

## validateTablesAreResettable { #validateTablesAreResettable }

```scala
validateTablesAreResettable(): Unit // (1)!
validateTablesAreResettable(
  tables: Seq[Table]): Unit
```

1. Validates all the [tables](DataflowGraph.md#tables) of a [DataflowGraph](DataflowGraph.md)

`validateTablesAreResettable`...FIXME

---

`validateTablesAreResettable` is used when:

* `DataflowGraph` is requested to [validate](DataflowGraph.md#validate)
