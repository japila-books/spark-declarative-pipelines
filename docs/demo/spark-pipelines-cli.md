---
hide:
  - navigation
---

# Demo: spark-pipelines CLI

!!! warning "Activate Virtual Environment"
    Follow [Demo: Create Virtual Environment for Python Client](create-virtual-environment-for-python-client.md) before getting started with this demo.

## Display Pipelines Help

Run `spark-pipelines --help` to learn the options.

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
        init              Generate a sample pipeline project, including a spec
                          file and example transformations.

    options:
      -h, --help          show this help message and exit
    ```

## Create Pipelines Demo Project

You've only created an empty Python project so far (using `uv`).

Create a demo double `hello-spark-pipelines` pipelines project with a sample `spark-pipeline.yml` and sample transformations (in Python and in SQL).

```shell
$SPARK_HOME/bin/spark-pipelines init --name hello-spark-pipelines && \
mv hello-spark-pipelines/* . && \
rm -rf hello-spark-pipelines
```

```shell
cat spark-pipeline.yml
```

=== "Output"

    ```yaml

    name: hello-spark-pipelines
    storage: file:///Users/jacek/sandbox/hello-spark-pipelines/hello-spark-pipelines/pipeline-storage
    libraries:
      - glob:
          include: transformations/**
    ```

```shell
tree transformations
```

=== "Output"

    ```text
    transformations
    ├── example_python_materialized_view.py
    └── example_sql_materialized_view.sql

    1 directory, 2 files
    ```

!!! warning "Spark Connect Server should be down"
    `spark-pipelines dry-run` starts its own Spark Connect Server at 15002 port (unless started with `--remote` option).

    Shut down Spark Connect Server if you started it already.

    ```shell
    $SPARK_HOME/sbin/stop-connect-server.sh
    ```

!!! info "`--remote` option"
    Use `--remote` option to connect to a standalone Spark Connect Server.

    ```shell
    $SPARK_HOME/bin/spark-pipelines --remote sc://localhost dry-run
    ```

## Dry Run Pipelines Project

```shell
$SPARK_HOME/bin/spark-pipelines dry-run
```

=== "Output"

    ```text
    Loading pipeline spec from /Users/jacek/sandbox/hello-spark-pipelines/spark-pipeline.yml...
    Creating Spark session...
    Creating dataflow graph...
    Registering graph elements...
    Loading definitions. Root directory: '/Users/jacek/sandbox/hello-spark-pipelines'.
    Found 2 files matching glob 'transformations/**/*'
    Importing /Users/jacek/sandbox/hello-spark-pipelines/transformations/example_python_materialized_view.py...
    Registering SQL file /Users/jacek/sandbox/hello-spark-pipelines/transformations/example_sql_materialized_view.sql...
    Starting run...
    Run is COMPLETED.
    ```

## Run Pipelines Project

Run the pipeline.

```shell
$SPARK_HOME/bin/spark-pipelines run
```

=== "Output"

    ```text
    Loading pipeline spec from /Users/jacek/sandbox/hello-spark-pipelines/spark-pipeline.yml...
    Creating Spark session...
    Creating dataflow graph...
    Registering graph elements...
    Loading definitions. Root directory: '/Users/jacek/sandbox/hello-spark-pipelines'.
    Found 2 files matching glob 'transformations/**/*'
    Importing /Users/jacek/sandbox/hello-spark-pipelines/transformations/example_python_materialized_view.py...
    Registering SQL file /Users/jacek/sandbox/hello-spark-pipelines/transformations/example_sql_materialized_view.sql...
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

```shell
tree spark-warehouse
```

=== "Output"

    ```text
    spark-warehouse
    ├── example_python_materialized_view
    │   ├── _SUCCESS
    │   └── part-00000-284bc03a-3405-4e8e-bbd7-f6f17d79c282-c000.snappy.parquet
    └── example_sql_materialized_view
        ├── _SUCCESS
        └── part-00000-8316b6c6-7532-4f7a-92f6-2ec024e069f4-c000.snappy.parquet

    3 directories, 4 files
    ```
