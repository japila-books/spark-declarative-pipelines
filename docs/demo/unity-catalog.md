---
title: Unity Catalog
hide:
  - navigation
---

# Demo: Spark Declarative Pipelines with Unity Catalog and Delta Lake

This demo walks through the steps to run a Spark Declarative Pipelines project with [Unity Catalog]({{ book.unity_catalog }}) and [Delta Lake]({{ book.delta_lake }}).

## Before You Start Checklist

This checklist should help you start from scratch (and avoid any surprises during a demo session).

1. Remove `sdp-unity-catalog` demo project directory
1. `git restore etc/` in the git clone of Unity Catalog
1. Remove `spark-warehouse` directory in Apache Spark

## Create Pipelines Project

Create a new pipelines project using [Spark Pipelines CLI](../cli/index.md).

```shell
uvx --from "pyspark[pipelines]==4.1.1" spark-pipelines init --name sdp-unity-catalog
```

This command should create `sdp-unity-catalog` directory.
Change your working directory to be `sdp-unity-catalog`.

```shell
cd sdp-unity-catalog
```

Execute [dry-run](../cli/index.md#dry-run) command.

```shell
uvx --from "pyspark[pipelines]==4.1.1" spark-pipelines dry-run
```

You should see some logs similar to the following:

```text
2026-04-16 15:46:06: Loading pipeline spec from /Users/jacek/demo/sdp-unity-catalog/spark-pipeline.yml...
2026-04-16 15:46:06: Creating Spark session...
...
2026-04-16 15:46:09: Creating dataflow graph...
2026-04-16 15:46:09: Registering graph elements...
2026-04-16 15:46:09: Loading definitions. Root directory: '/Users/jacek/demo/sdp-unity-catalog'.
2026-04-16 15:46:09: Found 2 files matching glob 'transformations/**/*'
2026-04-16 15:46:09: Importing /Users/jacek/demo/sdp-unity-catalog/transformations/example_python_materialized_view.py...
2026-04-16 15:46:09: Registering SQL file /Users/jacek/demo/sdp-unity-catalog/transformations/example_sql_materialized_view.sql...
2026-04-16 15:46:09: Starting run...
2026-04-16 13:46:09: Run is COMPLETED.
```

The pipelines project looks all good! 👍

## Set Up Pipelines Project

While in the home directory of the pipelines project...

Edit [spark-pipeline.yml](../overview.md#pipeline-specification-file) to add extra `spark.remote` configuration.

```yaml title="spark-pipeline.yml"
name: sdp-unity-catalog
storage: file:///Users/jacek/demo/sdp-unity-catalog/pipeline-storage
libraries:
  - glob:
      include: transformations/**
configuration:
  spark.remote: sc://localhost:15002
```

With `spark.remote` configuration specified in the pipeline spec, you will not have to specify it on command line.

## Set Up Unity Catalog Server

Open a separate terminal.

### Build Unity Catalog

Build your own Unity Catalog as described in [this document]({{ book.unity_catalog }}/demo/spark-connector/#optional-build-spark-connector).

### Enable MANAGED table (experimental feature)

!!! warning "Experimental Feature"
    The MANAGED table feature in Unity Catalog is an experimental feature and is currently disabled.

    To enable it, set `server.managed-table.enabled=true` in `server.properties` of your local Unity Catalog installation.

Enable MANAGED table (experimental feature) in `etc/conf/server.properties`.

```shell
echo "server.managed-table.enabled=true" >> etc/conf/server.properties
```

### Start Unity Catalog

Start the Unity Catalog server.

```shell
./bin/start-uc-server
```

### Create Demo Catalog with Storage Root

Review the available catalogs in Unity Catalog.
There should be `unity` catalog only by default.

```shell
./bin/uc catalog list
```

??? note "Output"
    ```text
    ┌─────┬────────────┬──────────┬─────┬─────────────┬──────────┬──────────┬──────────┬────────────────────────────────────┬────────────┬────────────────┐
    │NAME │  COMMENT   │PROPERTIES│OWNER│ CREATED_AT  │CREATED_BY│UPDATED_AT│UPDATED_BY│                 ID                 │STORAGE_ROOT│STORAGE_LOCATION│
    ├─────┼────────────┼──────────┼─────┼─────────────┼──────────┼──────────┼──────────┼────────────────────────────────────┼────────────┼────────────────┤
    │unity│Main catalog│{}        │null │1721234405334│null      │null      │null      │f029b870-9468-4f10-badc-630b41e5690d│null        │null            │
    └─────┴────────────┴──────────┴─────┴─────────────┴──────────┴──────────┴──────────┴────────────────────────────────────┴────────────┴────────────────┘
    ```

```shell
./bin/uc catalog create --name demo --storage_root /tmp/demo_storage_root
```

You should have two catalogs now.

### List Catalogs

```shell
./bin/uc catalog list --output jsonPretty
```

??? note "Output"
    ```json
    [ {
      "name" : "demo",
      "comment" : null,
      "properties" : { },
      "owner" : null,
      "created_at" : 1776347905206,
      "created_by" : null,
      "updated_at" : 1776347905206,
      "updated_by" : null,
      "id" : "89dfb08f-8902-493e-b7d6-beeeb8e172cf",
      "storage_root" : "file:///tmp/demo_storage_root",
      "storage_location" : "file:///tmp/demo_storage_root/__unitystorage/catalogs/89dfb08f-8902-493e-b7d6-beeeb8e172cf"
    }, {
      "name" : "unity",
      "comment" : "Main catalog",
      "properties" : { },
      "owner" : null,
      "created_at" : 1721234405334,
      "created_by" : null,
      "updated_at" : null,
      "updated_by" : null,
      "id" : "f029b870-9468-4f10-badc-630b41e5690d",
      "storage_root" : null,
      "storage_location" : null
    } ]
    ```

### Create Default Schema in Demo Catalog

```shell
./bin/uc schema create --catalog demo --name default
```

### Create spark_catalog.default Schema in Unity Catalog

??? warning "`spark_catalog` should be `DeltaCatalog`, too?!"
    For reasons I cannot explain the default built-in `spark_catalog` catalog should be a `DeltaCatalog`, too.

    Otherwise, you'll run into `DeltaAnalysisException` for the tables that should not be in Unity Catalog.

    No idea why?!

```shell
./bin/uc catalog create --name spark_catalog
./bin/uc schema create --catalog spark_catalog --name default
```

## Start Spark Connect Server

Open another terminal.

Download and install [Apache Spark 4.1.1](https://spark.apache.org/downloads.html).

Go to the installation directory of your local Apache Spark.

Start a Spark Connect server with Delta Lake and Unity Catalog set up.

```shell
./sbin/start-connect-server.sh \
  --packages io.delta:delta-spark_2.13:4.2.0,io.unitycatalog:unitycatalog-spark_2.13:0.4.1,org.slf4j:slf4j-api:2.0.17 \
  --conf spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension \
  --conf spark.sql.catalog.spark_catalog=io.unitycatalog.spark.UCSingleCatalog \
  --conf spark.sql.catalog.spark_catalog.uri=http://localhost:8080 \
  --conf spark.sql.catalog.spark_catalog.type=static \
  --conf spark.sql.catalog.spark_catalog.token=some_token \
  --conf spark.sql.catalog.demo=io.unitycatalog.spark.UCSingleCatalog \
  --conf spark.sql.catalog.demo.uri=http://localhost:8080 \
  --conf spark.sql.catalog.demo.type=static \
  --conf spark.sql.catalog.demo.token=some_token
```

??? note "Optional: review logs of Spark Connect Server"
    Optionally, review the logs of the Spark Connect Server live.

    ```shell
    tail -f logs/*-org.apache.spark.sql.connect.service.SparkConnectServer-*.out
    ```

## Run Pipelines Project

Go back to the terminal with the pipelines project open.

```shell
uvx --from "pyspark[pipelines]==4.1.1" spark-pipelines run
```

This command will fail with the following exception:

```text
Managed table creation requires table property 'delta.feature.catalogManaged'='supported' to be set.
```

This is expected (yet annoying for sure 🤷‍♂️).

## Delete Existing Transformations

!!! warning "Mysteries All Around"
    For reasons I cannot explain whatever uses `spark_catalog` catalog runs into some issues:

    1. If `spark_catalog` catalog is not managed by Unity Catalog, there's an issue with a misconfiguration of Delta Lake.
    1. If `spark_catalog` catalog is managed by Unity Catalog, a table property `delta.feature.catalogManaged=supported` has to be set to all the tables.

Remove the existing transformations.

```shell
rm -rf transformations/*
```

## Define rates Delta Table

The infrastructure has been set up. Coding time! 👨‍💻

Define a streaming `rates` delta table.
The table will be registered in `demo` catalog in Unity Catalog.

In your pipelines project, create `transformations/rates.py` file with the following content:

```py title="transformations/rates.py"
from pyspark import pipelines as dp
from pyspark.sql import DataFrame, SparkSession

spark = SparkSession.active()

@dp.table(
  format="delta",             # defines a delta table
  name="demo.default.rates",  # in demo catalog in Unity Catalog
  table_properties={
    # Managed table creation requires table property 'delta.feature.catalogManaged'='supported' to be set.
    "delta.feature.catalogManaged": "supported",
  }
)
def rates() -> DataFrame:
    return (
        spark
        .readStream                 # defines a streaming table
        .format("rate-micro-batch") # from rate-micro-batch source
        .option("rowsPerBatch", 10) # 10 rows per batch
        .load()
    )
```

!!! note "delta.feature.catalogManaged Table Property"
    `delta.feature.catalogManaged` table property is required
    (and was missed in the other two transformations, and was the exact reason for the exception).

## Run Pipelines Project

```shell
uvx --from "pyspark[pipelines]==4.1.1" spark-pipelines run
```

You should see some logs similar to the following:

```text
2026-04-16 16:19:36: Loading pipeline spec from /Users/jacek/demo/sdp-unity-catalog/spark-pipeline.yml...
2026-04-16 16:19:36: Creating Spark session...
2026-04-16 16:19:36: Creating dataflow graph...
2026-04-16 16:19:36: Registering graph elements...
2026-04-16 16:19:36: Loading definitions. Root directory: '/Users/jacek/demo/sdp-unity-catalog'.
2026-04-16 16:19:36: Found 1 files matching glob 'transformations/**/*'
2026-04-16 16:19:36: Importing /Users/jacek/demo/sdp-unity-catalog/transformations/rates.py...
2026-04-16 16:19:36: Starting run...
2026-04-16 14:19:37: Flow demo.default.rates is QUEUED.
2026-04-16 14:19:37: Flow demo.default.rates is STARTING.
2026-04-16 14:19:37: Flow demo.default.rates is RUNNING.
2026-04-16 14:19:40: Flow demo.default.rates has COMPLETED.
2026-04-16 14:19:42: Run is COMPLETED.
```

??? note "Fun Fact"
    Observe the discrepancy in the timestamps!

## List Tables in Unity Catalog

Go to the directory of your local Unity Catalog installation.

```shell
./bin/uc table list --catalog demo --schema default --output jsonPretty
```

??? note "Output"
    ```json
    [ {
      "name" : "rates",
      "catalog_name" : "demo",
      "schema_name" : "default",
      "table_type" : "MANAGED",
      "data_source_format" : "DELTA",
      "columns" : [ {
        "name" : "timestamp",
        "type_text" : "timestamp",
        "type_json" : "\"timestamp\"",
        "type_name" : "TIMESTAMP",
        "type_precision" : null,
        "type_scale" : null,
        "type_interval_type" : null,
        "position" : 0,
        "comment" : null,
        "nullable" : true,
        "partition_index" : null
      }, {
        "name" : "value",
        "type_text" : "bigint",
        "type_json" : "\"long\"",
        "type_name" : "LONG",
        "type_precision" : null,
        "type_scale" : null,
        "type_interval_type" : null,
        "position" : 1,
        "comment" : null,
        "nullable" : true,
        "partition_index" : null
      } ],
      "storage_location" : "file:///tmp/demo_storage_root/__unitystorage/catalogs/89dfb08f-8902-493e-b7d6-beeeb8e172cf/tables/baf2dd2f-25e5-465d-9dec-ba6933d137e1",
      "comment" : "",
      "properties" : {
        "delta.checkpointPolicy" : "v2",
        "delta.enableRowTracking" : "true",
        "delta.minReaderVersion" : "3",
        "delta.feature.vacuumProtocolCheck" : "supported",
        "delta.minWriterVersion" : "7",
        "delta.enableInCommitTimestamps" : "true",
        "delta.rowTracking.materializedRowCommitVersionColumnName" : "_row-commit-version-col-7665bef6-1cb3-42fa-a291-171e5fda0017",
        "delta.feature.rowTracking" : "supported",
        "delta.lastUpdateVersion" : "0",
        "delta.feature.catalogManaged" : "supported",
        "delta.feature.v2Checkpoint" : "supported",
        "delta.feature.domainMetadata" : "supported",
        "delta.enableDeletionVectors" : "true",
        "delta.rowTracking.materializedRowIdColumnName" : "_row-id-col-10cb3598-492c-420c-8ef1-ae3cc1c152fc",
        "io.unitycatalog.tableId" : "baf2dd2f-25e5-465d-9dec-ba6933d137e1",
        "delta.feature.inCommitTimestamp" : "supported",
        "delta.feature.invariants" : "supported",
        "delta.feature.appendOnly" : "supported",
        "delta.feature.deletionVectors" : "supported",
        "delta.lastCommitTimestamp" : "1776349176803",
        "table_type" : "MANAGED"
      },
      "owner" : null,
      "created_at" : 1776349177644,
      "created_by" : null,
      "updated_at" : 1776349177644,
      "updated_by" : null,
      "table_id" : "baf2dd2f-25e5-465d-9dec-ba6933d137e1"
    } ]
    ```

## PySpark Connect to Access Delta Table

In yet another terminal, run a [PySpark Connect]({{ book.spark_connect }}/pyspark/) client.

```shell
uvx --from "pyspark[pipelines]==4.1.1" pyspark --remote sc://localhost:15002
```

Show all the available tables.

```py
sql("SHOW TABLES").show()
```

??? note "Spoiler alert: Why Empty?!"
    Without the catalog and schema Spark SQL uses `spark_catalog.default`.

    The above query is equivalent to `SHOW TABLES IN spark_catalog.default`,
    which you didn't use to register tables to in this demo.

```py
sql("SHOW TABLES IN demo.default").show()
```

```py
spark.table("demo.default.rates").show()
```
