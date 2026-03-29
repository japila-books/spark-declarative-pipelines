---
title: Spark Declarative Pipelines with Delta Lake
hide:
  - navigation
---

# Demo: Spark Declarative Pipelines with Delta Lake

## Create SDP Project

Create a sample SDP project.

```shell
uvx --with "pyspark[pipelines]==4.1.1" spark-pipelines init --name sdp-delta
```

Switch to `sdp-delta` directory and execute `dry-run`.

```shell
uvx --with "pyspark[pipelines]==4.1.1" spark-pipelines dry-run
```

You should see some logs similar to the following:

```text
2026-03-29 18:48:02: Loading pipeline spec from /Users/jacek/sandbox/sdp-delta/spark-pipeline.yml...
2026-03-29 18:48:02: Creating Spark session...
...
2026-03-29 18:48:05: Creating dataflow graph...
2026-03-29 18:48:05: Registering graph elements...
2026-03-29 18:48:05: Loading definitions. Root directory: '/Users/jacek/sandbox/sdp-delta'.
2026-03-29 18:48:05: Found 2 files matching glob 'transformations/**/*'
2026-03-29 18:48:05: Importing /Users/jacek/sandbox/sdp-delta/transformations/example_python_materialized_view.py...
2026-03-29 18:48:05: Registering SQL file /Users/jacek/sandbox/sdp-delta/transformations/example_sql_materialized_view.sql...
2026-03-29 18:48:05: Starting run...
2026-03-29 16:48:05: Run is COMPLETED.
```

## Adjust SDP Project

### Remove Default Transformations

Let's remove the default transformations. They are not needed for the purpose of the demo.

```shell
rm -rf transformations/*
```

### Spark Remote Client and DeltaCatalog

Add configuration to `spark-pipeline.yml`.

```yaml title="spark-pipeline.yml"
name: sdp-delta
storage: file:///Users/jacek/sandbox/sdp-delta/pipeline-storage
libraries:
  - glob:
      include: transformations/**
configuration:
  spark.remote: sc://localhost:15002
```

## Start Spark Connect Server

In your Apache Spark installation directory, execute the following command:

```shell
./sbin/start-connect-server.sh \
  --packages io.delta:delta-spark_4.1_2.13:4.1.0,org.slf4j:slf4j-api:2.0.17 \
  --conf spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension \
  --conf spark.sql.catalog.spark_catalog=org.apache.spark.sql.delta.catalog.DeltaCatalog
```

## rates Delta Table

Define a streaming delta table.

Create `transformations/rates.py` transformation file with the following content.

```py title="transformations/rates.py"
from pyspark import pipelines as dp
from pyspark.sql import DataFrame, SparkSession

spark = SparkSession.active()

@dp.table(format="delta")           # defines a delta table
def rates() -> DataFrame:
    return (
        spark
        .readStream                 # defines a streaming table
        .format('rate-micro-batch') # from rate-micro-batch source
        .option('rowsPerBatch', 10) # 10 rows per batch
        .load()
    )
```

## Run Project

```shell
uvx --with "pyspark[pipelines]==4.1.1" spark-pipelines run
```

You should see some logs similar to the following:

```text
2026-03-29 21:27:22: Loading pipeline spec from /Users/jacek/sandbox/sdp-delta/spark-pipeline.yml...
2026-03-29 21:27:22: Creating Spark session...
2026-03-29 21:27:22: Creating dataflow graph...
2026-03-29 21:27:22: Registering graph elements...
2026-03-29 21:27:22: Loading definitions. Root directory: '/Users/jacek/sandbox/sdp-delta'.
2026-03-29 21:27:22: Found 1 files matching glob 'transformations/**/*'
2026-03-29 21:27:22: Importing /Users/jacek/sandbox/sdp-delta/transformations/rates.py...
2026-03-29 21:27:22: Starting run...
2026-03-29 19:27:22: Flow spark_catalog.default.rates is QUEUED.
2026-03-29 19:27:22: Flow spark_catalog.default.rates is STARTING.
2026-03-29 19:27:22: Flow spark_catalog.default.rates is RUNNING.
2026-03-29 19:27:23: Flow spark_catalog.default.rates has COMPLETED.
2026-03-29 19:27:24: Run is COMPLETED.
```

## PySpark to Access Delta Table

```shell
uvx --with "pyspark[pipelines]==4.1.1" pyspark --remote sc://localhost:15002
```

There should be our `rates` table available.

```text
>>> sql("show tables").show()
+---------+---------+-----------+
|namespace|tableName|isTemporary|
+---------+---------+-----------+
|  default|    rates|      false|
+---------+---------+-----------+
```

Depending on how many times you ran the SDP project, you can see different number of `timestamp` rows.
The number of records per `timestamp` will always be `10`. Can you explain why?

```text
>>> spark.table("rates").groupBy('timestamp').count().show()
+-------------------+-----+
|          timestamp|count|
+-------------------+-----+
|1970-01-01 01:00:01|   10|
|1970-01-01 01:00:02|   10|
|1970-01-01 01:00:03|   10|
+-------------------+-----+
```

??? note "10"
    The number of records per `timestamp` is `10` based on `rowsPerBatch` option of the source table (which is `rate-micro-batch`).
