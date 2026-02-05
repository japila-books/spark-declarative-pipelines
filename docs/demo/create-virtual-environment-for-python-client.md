---
hide:
  - navigation
---

# Demo: Create Virtual Environment for Python Client

This demo shows how to work with a development (unreleased) version of Spark Declarative Pipelines.

!!! note
    For released versions of Spark Declarative Pipelines, use `uvx` instead:

    ```shell
    uvx --with "pyspark[pipelines]" spark-pipelines
    ```

It is assumed that `SPARK_HOME` environment variable points at the sources of Apache Spark.

```shell
export SPARK_HOME=/Users/jacek/oss/spark
```

## Create SDP Project using uv

```shell
uv init hello-spark-pipelines && cd hello-spark-pipelines
```

```shell
uv add --editable $SPARK_HOME/python/packaging/client
```

```shell
uv tree --depth 2
```

=== "Output"

    ```text
    hello-spark-pipelines v0.1.0
    └── pyspark-client v4.2.0.dev0
        ├── googleapis-common-protos v1.72.0
        ├── grpcio v1.76.0
        ├── grpcio-status v1.76.0
        ├── numpy v2.4.2
        ├── pandas v3.0.0
        ├── pyarrow v23.0.0
        ├── pyyaml v6.0.3
        └── zstandard v0.25.0
    ```

```shell
uv pip list
```

=== "Output"

    ```text
    Package                  Version     Editable project location
    ------------------------ ----------- ----------------------------------------------
    googleapis-common-protos 1.72.0
    grpcio                   1.76.0
    grpcio-status            1.76.0
    numpy                    2.4.2
    pandas                   3.0.0
    protobuf                 6.33.5
    pyarrow                  23.0.0
    pyspark-client           4.2.0.dev0  /Users/jacek/oss/spark/python/packaging/client
    python-dateutil          2.9.0.post0
    pyyaml                   6.0.3
    six                      1.17.0
    typing-extensions        4.15.0
    zstandard                0.25.0
    ```

## Activate Virtual Environment

Activate (_source_) the virtual environment (that `uv` helped us create).

```shell
source .venv/bin/activate
```

This activation brings all the necessary Spark Declarative Pipelines Python dependencies (that are only available in the source format only) for non-`uv` tools and CLI, incl. [Spark Pipelines CLI](#spark-pipelines) itself.

## Use Spark Pipelines CLI

```shell
$SPARK_HOME/bin/spark-pipelines --help
```

=== "Output"

    ```text
    usage: cli.py [-h] {run,dry-run,init} ...

    Pipelines CLI

    positional arguments:
      {run,dry-run,init}
        run               Run a pipeline. If no refresh options specified, a
                          default incremental update is performed.
        dry-run           Launch a run that just validates the graph and checks
                          for errors.
        init              Generate a sample pipeline project, with a spec file and
                          example transformations.

    options:
      -h, --help          show this help message and exit
    ```

!!! note "macOS and PYSPARK_PYTHON"
    On macOS, you may want to define `PYSPARK_PYTHON` environment variable to point at Python >= 3.10.

    ```shell
    export PYSPARK_PYTHON=python3.14
    ```
