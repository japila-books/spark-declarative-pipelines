---
title: cli.py
subtitle: pyspark/pipelines/cli.py
---

# cli Python Module

`pyspark/pipelines/cli.py` Python module is at the heart of the [Spark Pipelines CLI](index.md).

## Launch Standalone Application { #main }

```py
main() -> None
```

`main`...FIXME

---

`main` is used when:

* `SparkPipelines` is [launched as a standalone application](SparkPipelines.md#main) (with the first argument being the path to this `pyspark/pipelines/cli.py` module)

## Run Pipeline { #run }

```py
run(
    spec_path: Path,
    full_refresh: Sequence[str],
    full_refresh_all: bool,
    refresh: Sequence[str],
    dry: bool,
) -> None
```

`run`...FIXME

---

`run` is used when:

* `cli.py` is [launched as a standalone application](#main) (with either `run` or `dry-run` options)

## load_pipeline_spec { #load_pipeline_spec }

```py
load_pipeline_spec(
    spec_path: Path,
) -> PipelineSpec
```

`load_pipeline_spec` [builds a PipelineSpec](#unpack_pipeline_spec) off of the YAML file at the given `spec_path`.

---

`load_pipeline_spec` is used when:

* `cli.py` is requested to [run the pipeline](#run)

## unpack_pipeline_spec { #unpack_pipeline_spec }

```py
unpack_pipeline_spec(
    spec_data: Mapping[str, Any],
) -> PipelineSpec
```

`unpack_pipeline_spec` creates a [PipelineSpec](PipelineSpec.md) from the given `spec_data` mapping.

`unpack_pipeline_spec` makes sure that only allowed fields are used (with two required):

* `name` (required)
* `storage` (required)
* `catalog`
* `database`
* `schema`
* `configuration`
* `libraries`

??? note "`database` takes precedence over `schema`"
    `database` and `schema` are synonyms, with the former taking precedence over the latter.

??? note "PySparkException"
    `unpack_pipeline_spec` raises a `PySparkException` when either there are unexpected fields or the required fields are missing.

---

`unpack_pipeline_spec` is used when:

* `cli.py` is requested to [load_pipeline_spec](#load_pipeline_spec)
