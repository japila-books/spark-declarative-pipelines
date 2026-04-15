---
title: Unity Catalog
hide:
  - navigation
---

# Demo: Spark Declarative Pipelines with Unity Catalog and Delta Lake

This demo walks through the steps to run a Spark Declarative Pipelines project with [Unity Catalog]({{ book.unity_catalog }}) and [Delta Lake]({{ book.delta_lake }}).

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
2026-04-15 09:42:07: Loading pipeline spec from /Users/jacek/sandbox/sdp-unity-catalog/spark-pipeline.yml...
2026-04-15 09:42:07: Creating Spark session...
...
2026-04-15 09:42:10: Creating dataflow graph...
2026-04-15 09:42:10: Registering graph elements...
2026-04-15 09:42:10: Loading definitions. Root directory: '/Users/jacek/sandbox/sdp-unity-catalog'.
2026-04-15 09:42:10: Found 2 files matching glob 'transformations/**/*'
2026-04-15 09:42:10: Importing /Users/jacek/sandbox/sdp-unity-catalog/transformations/example_python_materialized_view.py...
2026-04-15 09:42:10: Registering SQL file /Users/jacek/sandbox/sdp-unity-catalog/transformations/example_sql_materialized_view.sql...
2026-04-15 09:42:10: Starting run...
2026-04-15 07:42:10: Run is COMPLETED.
```

The pipelines project looks all good! 👍

## Set Up Pipelines Project

Edit [spark-pipeline.yml](../overview.md#pipeline-specification-file) to add extra `spark.remote` configuration.
With the configuration specified in the pipeline spec, you will not have to specify it on command line.

```yaml title="spark-pipeline.yml"
name: sdp-unity-catalog
storage: file:///Users/jacek/sandbox/sdp-unity-catalog/pipeline-storage
libraries:
  - glob:
      include: transformations/**
configuration:
  spark.remote: sc://localhost:15002
```

## Set Up Unity Catalog Server

### Build Unity Catalog

Build your own Unity Catalog as described in [this document]({{ book.unity_catalog }}/demo/spark-connector/#optional-build-spark-connector).

### Enable MANAGED table (experimental feature)

!!! warning "Experimental Feature"
    The MANAGED table feature in Unity Catalog is an experimental feature and is currently disabled.

    To enable it, set 'server.managed-table.enabled=true' in your Unity Catalog's `server.properties`.

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

```shell
./bin/uc catalog create --name demo --storage_root /tmp/demo_storage_root
```

### List Catalogs

```console
$ ./bin/uc catalog list --output jsonPretty
[ {
  "name" : "demo",
  "comment" : null,
  "properties" : { },
  "owner" : null,
  "created_at" : 1776113042202,
  "created_by" : null,
  "updated_at" : 1776113042202,
  "updated_by" : null,
  "id" : "a32767ec-127d-43f2-991a-8c6e4a509d52",
  "storage_root" : "file:///tmp/uc_storage_root",
  "storage_location" : "file:///tmp/uc_storage_root/__unitystorage/catalogs/a32767ec-127d-43f2-991a-8c6e4a509d52"
}, {
  "name" : "unity",
  "comment" : "Main catalog",
  "properties" : { },
  "owner" : null,
  "created_at" : 1721234405334,
  "created_by" : null,
  "updated_at" : 1776112841815,
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

Download and install [Apache Spark 4.1.1](https://spark.apache.org/downloads.html).

Open another terminal and go to the installation directory of your local Apache Spark.

Start a Spark Connect server with Delta Lake and Unity Catalog set up.

```shell
./sbin/start-connect-server.sh \
  --packages io.delta:delta-spark_2.13:4.1.0,io.unitycatalog:unitycatalog-spark_2.13:0.4.1,org.slf4j:slf4j-api:2.0.17 \
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

```shell
uvx --from "pyspark[pipelines]==4.1.1" spark-pipelines run
```

You should see some logs similar to the following:

```text
2026-04-15 10:27:14: Loading pipeline spec from /Users/jacek/sandbox/sdp-unity-catalog/spark-pipeline.yml...
2026-04-15 10:27:14: Creating Spark session...
2026-04-15 10:27:15: Creating dataflow graph...
2026-04-15 10:27:15: Registering graph elements...
2026-04-15 10:27:15: Loading definitions. Root directory: '/Users/jacek/sandbox/sdp-unity-catalog'.
2026-04-15 10:27:15: Found 2 files matching glob 'transformations/**/*'
2026-04-15 10:27:15: Importing /Users/jacek/sandbox/sdp-unity-catalog/transformations/example_python_materialized_view.py...
2026-04-15 10:27:15: Registering SQL file /Users/jacek/sandbox/sdp-unity-catalog/transformations/example_sql_materialized_view.sql...
2026-04-15 10:27:15: Starting run...
2026-04-15 08:27:15: Flow spark_catalog.default.example_python_materialized_view is QUEUED.
2026-04-15 08:27:15: Flow spark_catalog.default.example_sql_materialized_view is QUEUED.
2026-04-15 08:27:15: Flow spark_catalog.default.example_python_materialized_view is PLANNING.
2026-04-15 08:27:15: Flow spark_catalog.default.example_python_materialized_view is STARTING.
2026-04-15 08:27:15: Flow spark_catalog.default.example_python_materialized_view is RUNNING.
2026-04-15 08:27:16: Flow spark_catalog.default.example_python_materialized_view has COMPLETED.
2026-04-15 08:27:17: Flow spark_catalog.default.example_sql_materialized_view is PLANNING.
2026-04-15 08:27:17: Flow spark_catalog.default.example_sql_materialized_view is STARTING.
2026-04-15 08:27:17: Flow spark_catalog.default.example_sql_materialized_view is RUNNING.
2026-04-15 08:27:17: Flow spark_catalog.default.example_sql_materialized_view has COMPLETED.
2026-04-15 08:27:19: Run is COMPLETED.
```

Note that all the transformations (_tables_) of your pipelines projects are registered in the default `spark_catalog` catalog and `default` schema.

## Delete Existing Transformations

!!! warning "Mysteries All Around"
    For reasons I cannot explain whatever uses `spark_catalog` catalog runs into some issues:

    1. If `spark_catalog` catalog is not managed by Unity Catalog, there's an issue with a misconfiguration of Delta Lake.
    1. If `spark_catalog` catalog is managed by Unity Catalog, a table property `delta.feature.catalogManaged=supported` has to be set to all the tables.

With no explanation for the extra hurdles listed above, let's remove the existing transformations.

```shell
rm -rf transformations/*
```

## Define rates Delta Table in Unity Catalog

Define a streaming `rates` delta table that should be registered in Unity Catalog.

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

## Run Pipelines Project

```shell
uvx --from "pyspark[pipelines]==4.1.1" spark-pipelines run
```

You should see some logs similar to the following:

```text
2026-04-15 10:53:51: Loading pipeline spec from /Users/jacek/sandbox/sdp-unity-catalog/spark-pipeline.yml...
2026-04-15 10:53:51: Creating Spark session...
2026-04-15 10:53:51: Creating dataflow graph...
2026-04-15 10:53:51: Registering graph elements...
2026-04-15 10:53:51: Loading definitions. Root directory: '/Users/jacek/sandbox/sdp-unity-catalog'.
2026-04-15 10:53:51: Found 1 files matching glob 'transformations/**/*'
2026-04-15 10:53:51: Importing /Users/jacek/sandbox/sdp-unity-catalog/transformations/rates.py...
2026-04-15 10:53:51: Starting run...
2026-04-15 08:53:52: Flow demo.default.rates is QUEUED.
2026-04-15 08:53:52: Flow demo.default.rates is STARTING.
2026-04-15 08:53:52: Flow demo.default.rates is RUNNING.
2026-04-15 08:53:55: Flow demo.default.rates has COMPLETED.
2026-04-15 08:53:57: Run is COMPLETED.
```

## List Tables in Unity Catalog

```shell
./bin/uc table list --catalog demo --schema default
```

You should see the following output:

```text
[ {
  "name" : "rates",
  "catalog_name" : "demo",
  "schema_name" : "default",
  "table_type" : "MANAGED",
  "data_source_format" : "DELTA",
  "columns" : [ ],
  "storage_location" : "file:///tmp/demo_storage_root/__unitystorage/catalogs/bb33b4b8-c3fb-48f0-b47f-be2003fa8bae/tables/92cae939-e215-438c-936a-c8ac3538ec74",
  "comment" : "",
  "properties" : {
    "delta.checkpointPolicy" : "v2",
    "delta.enableRowTracking" : "true",
    "delta.minReaderVersion" : "3",
    "delta.feature.vacuumProtocolCheck" : "supported",
    "delta.minWriterVersion" : "7",
    "delta.enableInCommitTimestamps" : "true",
    "delta.rowTracking.materializedRowCommitVersionColumnName" : "_row-commit-version-col-9054d455-1349-45cc-8ad8-ec547a7d764a",
    "delta.feature.rowTracking" : "supported",
    "delta.lastUpdateVersion" : "0",
    "delta.feature.catalogManaged" : "supported",
    "delta.feature.v2Checkpoint" : "supported",
    "delta.feature.domainMetadata" : "supported",
    "delta.enableDeletionVectors" : "true",
    "delta.rowTracking.materializedRowIdColumnName" : "_row-id-col-071892f6-60e2-4e77-b489-02ddac73b924",
    "io.unitycatalog.tableId" : "92cae939-e215-438c-936a-c8ac3538ec74",
    "delta.feature.inCommitTimestamp" : "supported",
    "delta.feature.invariants" : "supported",
    "delta.feature.appendOnly" : "supported",
    "delta.feature.deletionVectors" : "supported",
    "delta.lastCommitTimestamp" : "1776243231330",
    "table_type" : "MANAGED"
  },
  "owner" : null,
  "created_at" : 1776243232178,
  "created_by" : null,
  "updated_at" : 1776243232178,
  "updated_by" : null,
  "table_id" : "92cae939-e215-438c-936a-c8ac3538ec74"
} ]
```

## PySpark Connect to Access Delta Table

In yet another terminal, run a [PySpark Connect]({{ book.spark_connect }}/pyspark/) client.

```shell
uvx --from "pyspark[pipelines]==4.1.1" pyspark --remote sc://localhost:15002
```

Show all the available tables.

```text
>>> sql("SHOW TABLES").show()
+---------+---------+-----------+
|namespace|tableName|isTemporary|
+---------+---------+-----------+
+---------+---------+-----------+
```

??? note "Spoiler alert: Why Empty?!"
    Without the catalog and schema Spark SQL uses `spark_catalog.default`.

    The above query is equivalent to `SHOW TABLES IN spark_catalog.default`,
    which you didn't use to register tables to in this demo.

```text
>>> sql("SHOW TABLES IN demo.default").show()
+---------+---------+-----------+
|namespace|tableName|isTemporary|
+---------+---------+-----------+
|  default|    rates|      false|
+---------+---------+-----------+
```

```text
>>> spark.table("demo.default.rates").show()
+-------------------+-----+
|          timestamp|value|
+-------------------+-----+
|1970-01-01 01:00:00|    4|
|1970-01-01 01:00:00|    6|
|1970-01-01 01:00:00|    7|
|1970-01-01 01:00:00|    1|
|1970-01-01 01:00:00|    3|
|1970-01-01 01:00:00|    8|
|1970-01-01 01:00:00|    2|
|1970-01-01 01:00:00|    5|
|1970-01-01 01:00:00|    9|
|1970-01-01 01:00:00|    0|
+-------------------+-----+
```
