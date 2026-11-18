---
title: Spark Pipelines CLI
hide:
  - navigation
---

# Demo: Spark Pipelines CLI

## Display Pipelines Help

Run `spark-pipelines --help` to learn the options.

```shell
uvx --with "pyspark[pipelines]=={{ spark.version }}" spark-pipelines --help
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

Create `hello-spark-pipelines` pipelines project with a sample `spark-pipeline.yml` and sample transformations (in Python and in SQL).

```shell
uvx --with "pyspark[pipelines]=={{ spark.version }}" spark-pipelines init --name hello-spark-pipelines
```

```shell
cd hello-spark-pipelines
```

```shell
cat spark-pipeline.yml
```

=== "Output"

    ```yaml
    name: hello-spark-pipelines
    storage: file:///Users/jacek/demo/hello-spark-pipelines/pipeline-storage
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

## Dry Run Pipelines Project

!!! warning "Spark Connect Server should be down"
    `spark-pipelines dry-run` starts its own Spark Connect Server at 15002 port (unless started with `--remote` option).

    Shut down Spark Connect Server if you started it already.

    ```shell
    $SPARK_HOME/sbin/stop-connect-server.sh
    ```

!!! info "`--remote` option"
    Use `--remote` option to connect to a standalone Spark Connect Server.

    ```shell
    uvx --with "pyspark[pipelines]=={{ spark.version }}" spark-pipelines --remote sc://localhost dry-run
    ```

```shell
uvx --with "pyspark[pipelines]=={{ spark.version }}" spark-pipelines dry-run
```

=== "Output"

    ```text
    Creating Spark session...
    Creating dataflow graph...
    Registering graph elements...
    Loading definitions. Root directory: '/Users/jacek/demo/hello-spark-pipelines'.
    Found 2 files matching glob 'transformations/**/*'
    Importing /Users/jacek/demo/hello-spark-pipelines/transformations/example_python_materialized_view.py...
    Registering SQL file /Users/jacek/demo/hello-spark-pipelines/transformations/example_sql_materialized_view.sql...
    Starting run...
    Run is COMPLETED.
    ```

## Run Pipelines Project

Run the pipeline.

```shell
uvx --with "pyspark[pipelines]=={{ spark.version }}" spark-pipelines run
```

=== "Output"

    ```text
    Loading pipeline spec from /Users/jacek/demo/hello-spark-pipelines/spark-pipeline.yml...
    Creating Spark session...
    Creating dataflow graph...
    Registering graph elements...
    Loading definitions. Root directory: '/Users/jacek/demo/hello-spark-pipelines'.
    Found 2 files matching glob 'transformations/**/*'
    Importing /Users/jacek/demo/hello-spark-pipelines/transformations/example_python_materialized_view.py...
    Registering SQL file /Users/jacek/demo/hello-spark-pipelines/transformations/example_sql_materialized_view.sql...
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
    │   └── part-00000-a9f87452-fde4-4634-8549-c0c94ce95cab-c000.snappy.parquet
    └── example_sql_materialized_view
        ├── _SUCCESS
        └── part-00000-10c7320c-7acc-4929-aff7-d454f5bf84c0-c000.snappy.parquet

    3 directories, 4 files
    ```
