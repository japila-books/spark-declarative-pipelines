# Python

## Python Import Alias Convention

As of this [commit 6ab0df9]({{ spark.commit }}/6ab0df9287c5a9ce49769612c2bb0a1daab83bee), the convention to alias the import of Declarative Pipelines in Python is `dp`.

```python
from pyspark import pipelines as dp
```

## pyspark.pipelines Python Module { #pyspark_pipelines }

`pyspark.pipelines` module (in `__init__.py`) imports `pyspark.pipelines.api` module to expose the following Python functions (incl. decorators) to wildcard imports:

* [append_flow](#append_flow)
* [create_sink](#create_sink)
* [create_streaming_table](#create_streaming_table)
* [materialized_view](#materialized_view)
* [table](#table)
* [temporary_view](#temporary_view)

Use the following import in your Python code:

```py
from pyspark import pipelines as dp
```

## Python Decorators

Declarative Pipelines uses [Python decorators](https://peps.python.org/pep-0318/) to define tables and views.

| Decorator | Purpose |
|-----------|---------|
| [@dp.append_flow](#append_flow) | [Append-only flows](#append-flows) |
| [@dp.materialized_view](#materialized_view) | Materialized views (with supporting flows) |
| [@dp.table](#table) | [Streaming](#streaming-tables) and batch tables (with supporting flows) |
| [@dp.temporary_view](#temporary_view) | Temporary views (with supporting flows) |

## @dp.append_flow { #append_flow }

```py
append_flow(
    *,
    target: str,
    name: Optional[str] = None,
    spark_conf: Optional[Dict[str, str]] = None,
) -> Callable[[QueryFunction], None] # (1)!
```

1. `QueryFunction = Callable[[], DataFrame]` is a Python function that takes no arguments and returns a PySpark `DataFrame`.

[Registers](GraphElementRegistry.md#register_flow) an append [Flow](Flow.md) (in the active [GraphElementRegistry](GraphElementRegistry.md))

`target` is the name of the dataset (_destination_) this flow writes to.

## dp.create_sink { #create_sink }

```py
create_sink(
    name: str,
    format: str,
    options: Optional[Dict[str, str]] = None,
) -> None
```

[Registers](GraphElementRegistry.md#register_output) a [Sink](Sink.md) output in the active [GraphElementRegistry](GraphElementRegistry.md).

??? warning "Not Python Decorator"
    Unlike the others, `create_sink` is not a [Python decorator](https://peps.python.org/pep-0318/) (`Callable`).

## dp.create_streaming_table { #create_streaming_table }

```py
create_streaming_table(
    name: str,
    *,
    comment: Optional[str] = None,
    table_properties: Optional[Dict[str, str]] = None,
    partition_cols: Optional[List[str]] = None,
    cluster_by: Optional[List[str]] = None,
    schema: Optional[Union[StructType, str]] = None,
    format: Optional[str] = None,
) -> None
```

??? warning "Not Python Decorator"
    Unlike the others, `create_streaming_table` is not a [Python decorator](https://peps.python.org/pep-0318/) (`Callable`).

[Registers](GraphElementRegistry.md#register_output) a `StreamingTable` dataset (in the active [GraphElementRegistry](GraphElementRegistry.md)) for [Append Flows](#append-flows).

## @dp.materialized_view { #materialized_view }

```py
materialized_view(
    query_function: Optional[QueryFunction] = None,
    *,
    name: Optional[str] = None,
    comment: Optional[str] = None,
    spark_conf: Optional[Dict[str, str]] = None,
    table_properties: Optional[Dict[str, str]] = None,
    partition_cols: Optional[List[str]] = None,
    cluster_by: Optional[List[str]] = None,
    schema: Optional[Union[StructType, str]] = None,
    format: Optional[str] = None,
) -> Union[Callable[[QueryFunction], None], None]
```

[Registers](GraphElementRegistry.md#register_output) a [MaterializedView](MaterializedView.md) dataset with an accompanying [Flow](GraphElementRegistry.md#register_flow) in the active [GraphElementRegistry](GraphElementRegistry.md).

## @dp.table { #table }

```py
table(
    query_function: Optional[QueryFunction] = None,
    *,
    name: Optional[str] = None,
    comment: Optional[str] = None,
    spark_conf: Optional[Dict[str, str]] = None,
    table_properties: Optional[Dict[str, str]] = None,
    partition_cols: Optional[List[str]] = None,
    cluster_by: Optional[List[str]] = None,
    schema: Optional[Union[StructType, str]] = None,
    format: Optional[str] = None,
) -> Union[Callable[[QueryFunction], None], None]
```

[Registers](GraphElementRegistry.md#register_output) a `StreamingTable` dataset with an accompanying [Flow](GraphElementRegistry.md#register_flow) in the active [GraphElementRegistry](GraphElementRegistry.md).

## @dp.temporary_view { #temporary_view }

```py
temporary_view(
    query_function: Optional[QueryFunction] = None,
    *,
    name: Optional[str] = None,
    comment: Optional[str] = None,
    spark_conf: Optional[Dict[str, str]] = None,
) -> Union[Callable[[QueryFunction], None], None]
```

[Registers](GraphElementRegistry.md#register_output) a `TemporaryView` dataset with an accompanying [Flow](GraphElementRegistry.md#register_flow) in the active [GraphElementRegistry](GraphElementRegistry.md).
