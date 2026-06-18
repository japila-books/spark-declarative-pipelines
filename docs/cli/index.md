# Spark Pipelines CLI

Spark Declarative Pipelines comes with [spark-pipelines](#spark-pipelines) shell script to manage declarative pipelines projects.

=== "uv"

    ```shell
    uvx --with "pyspark[pipelines]==4.1.1" spark-pipelines
    ```

=== "Local installation"

    ```shell
    $SPARK_HOME/bin/spark-pipelines
    ```

Internally, `spark-pipelines` prepares the runtime environment to run [SparkPipelines](SparkPipelines.md) standalone application (with the path to [pyspark/pipelines/cli.py](cli.md) Python script).

`cli.py` does two very important steps with regards to the transformation scripts in a SDP project's execution:

1. As a Python script, the `cli.py` imports all the transformation scripts written in [Python](../pyspark/index.md) by a SDP developer (that are immediately executed per [Python import system](https://docs.python.org/3/reference/import.html)'s rules).
2. SQL libraries remain untouched and sent over the wire to a Spark Connect server ([PipelinesHandler](../spark-connect//PipelinesHandler.md)) for execution.

## spark-pipelines Shell Script { #spark-pipelines }

The Pipelines CLI supports the following commands:

* [dry-run](#dry-run)
* [init](#init)
* [run](#run)

```console
$ uvx --with "pyspark[pipelines]==4.2.0.dev5" \
    --prerelease=allow \
    spark-pipelines --help
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

### dry-run

Launch a run that just validates the graph and checks for errors

Option | Description | Default
-|-|-
 `--spec` | Path to the pipeline spec | (undefined)

```console
$ uvx --from "pyspark[pipelines]" spark-pipelines dry-run
Loading pipeline spec from /Users/jacek/demo/hello-pipelines/spark-pipeline.yml...
Creating Spark session...
Creating dataflow graph...
Registering graph elements...
Loading definitions. Root directory: '/Users/jacek/demo/hello-pipelines'.
Found 2 files matching glob 'transformations/**/*'
Importing /Users/jacek/demo/hello-pipelines/transformations/example_python_materialized_view.py...
Registering SQL file /Users/jacek/demo/hello-pipelines/transformations/example_sql_materialized_view.sql...
Starting run...
Run is COMPLETED.
```

### init

Generate a sample pipeline project, including a spec file and example definitions

Option | Description | Default | Required
-|-|-|:-:
 `--name` | Name of the project. A directory with this name will be created underneath the current directory | (undefined) | ✅

```console
$ uvx --from "pyspark[pipelines]" spark-pipelines init --name hello-pipelines
Pipeline project 'hello-pipelines' created successfully. To run your pipeline:
cd 'hello-pipelines'
spark-pipelines run

$ cd 'hello-pipelines'

$ tree .
.
├── pipeline-storage
├── spark-pipeline.yml
└── transformations
    ├── example_python_materialized_view.py
    └── example_sql_materialized_view.sql

3 directories, 3 files
```

### run

Run a pipeline. If no `--refresh` option specified, a default incremental update is performed.

Option | Description | Default
-|-|-
 `--spec` | Path to the pipeline spec | (undefined)
 `--full-refresh` | List of datasets to reset and recompute (comma-separated) | (empty)
 `--full-refresh-all` | Perform a full graph reset and recompute | (undefined)
 `--refresh` | List of datasets to update (comma-separated) | (empty)

When executed, `run` prints out the following log message:

```text
Loading pipeline spec from [spec_path]...
```

`run` loads a pipeline spec.

`run` prints out the following log message:

```text
Creating Spark session...
```

`run` creates a Spark session with the configurations from the pipeline spec.

`run` prints out the following log message:

```text
Creating dataflow graph...
```

`run` sends a `CreateDataflowGraph` command for execution in the Spark Connect server.

!!! note "Spark Connect Server and Command Execution"
    `CreateDataflowGraph` is handled by [PipelinesHandler](../spark-connect/PipelinesHandler.md#CREATE_DATAFLOW_GRAPH) on the Spark Connect Server.

`run` prints out the following log message:

```text
Dataflow graph created (ID: [dataflow_graph_id]).
```

`run` prints out the following log message:

```text
Registering graph elements...
```

`run` creates a [SparkConnectGraphElementRegistry](../pyspark/SparkConnectGraphElementRegistry.md) and `register_definitions`.

`run` prints out the following log message:

```text
Starting run (dry=[dry], full_refresh=[full_refresh], full_refresh_all=[full_refresh_all], refresh=[refresh])...
```

`run` sends a `StartRun` command for execution in the Spark Connect Server.

!!! note "StartRun Command and PipelinesHandler"
    `StartRun` command is handled by [PipelinesHandler](../spark-connect/PipelinesHandler.md#startRun) on the Spark Connect Server.

In the end, `run` keeps printing out pipeline events from the Spark Connect server.

```console
❯ uvx --from "pyspark[pipelines]" spark-pipelines run
Loading pipeline spec from /Users/jacek/demo/hello-pipelines/spark-pipeline.yml...
Creating Spark session...
Creating dataflow graph...
Registering graph elements...
Loading definitions. Root directory: '/Users/jacek/demo/hello-pipelines'.
Found 2 files matching glob 'transformations/**/*'
Importing /Users/jacek/demo/hello-pipelines/transformations/example_python_materialized_view.py...
Registering SQL file /Users/jacek/demo/hello-pipelines/transformations/example_sql_materialized_view.sql...
Starting run...
Flow spark_catalog.default.example_python_materialized_view is QUEUED.
Flow spark_catalog.default.example_sql_materialized_view is QUEUED.
Flow spark_catalog.default.example_python_materialized_view is PLANNING.
Flow spark_catalog.default.example_python_materialized_view is STARTING.
Flow spark_catalog.default.example_python_materialized_view is RUNNING.
Flow spark_catalog.default.example_python_materialized_view has COMPLETED.
Flow spark_catalog.default.example_sql_materialized_view is PLANNING.
Flow spark_catalog.default.example_sql_materialized_view is STARTING.
Flow spark_catalog.default.example_sql_materialized_view is RUNNING.
Flow spark_catalog.default.example_sql_materialized_view has COMPLETED.
Run is COMPLETED.
```
