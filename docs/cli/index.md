# Spark Pipelines CLI

Spark Declarative Pipelines comes with [spark-pipelines](#spark-pipelines) shell script to launch a Spark Declarative Pipelines project.

```bash
$SPARK_HOME/bin/spark-pipelines
```

`spark-pipelines` prepares the runtime environment to run [SparkPipelines](SparkPipelines.md) (with the path to [cli.py](cli.md) Python script).

`cli.py` does two very critical steps in a SDP project's execution:

1. As a Python script, the `cli.py` imports all the Python transformation scripts written by a SDP developer (that are immediately executed per [Python import system](https://docs.python.org/3/reference/import.html)'s rules).
1. SQL libraries remain untouched and sent over the wire to a Spark Connect server ([PipelinesHandler](../PipelinesHandler.md)) for execution.

The Pipelines CLI supports the following commands:

* [dry-run](#dry-run)
* [init](#init)
* [run](#run)

=== "uv"

    ```console
    uvx --with "pyspark[pipelines]" spark-pipelines
    ```

## spark-pipelines Shell Script { #spark-pipelines }

## dry-run

Launch a run that just validates the graph and checks for errors

Option | Description | Default
-|-|-
 `--spec` | Path to the pipeline spec | (undefined)

## init

Generate a sample pipeline project, including a spec file and example definitions

Option | Description | Default | Required
-|-|-|:-:
 `--name` | Name of the project. A directory with this name will be created underneath the current directory | (undefined) | ✅

```console
$ ./bin/spark-pipelines init --name hello-pipelines
Pipeline project 'hello-pipelines' created successfully. To run your pipeline:
cd 'hello-pipelines'
spark-pipelines run
```

## run

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
    `CreateDataflowGraph` is handled by [PipelinesHandler](PipelinesHandler.md#createDataflowGraph) on the Spark Connect Server.

`run` prints out the following log message:

```text
Dataflow graph created (ID: [dataflow_graph_id]).
```

`run` prints out the following log message:

```text
Registering graph elements...
```

`run` creates a [SparkConnectGraphElementRegistry](SparkConnectGraphElementRegistry.md) and `register_definitions`.

`run` prints out the following log message:

```text
Starting run (dry=[dry], full_refresh=[full_refresh], full_refresh_all=[full_refresh_all], refresh=[refresh])...
```

`run` sends a `StartRun` command for execution in the Spark Connect Server.

!!! note "StartRun Command and PipelinesHandler"
    `StartRun` command is handled by [PipelinesHandler](PipelinesHandler.md#startRun) on the Spark Connect Server.

In the end, `run` keeps printing out pipeline events from the Spark Connect server.
