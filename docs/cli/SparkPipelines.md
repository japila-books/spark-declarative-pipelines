# SparkPipelines

`SparkPipelines` is a [standalone application](#main) that [spark-pipelines](./index.md) shell script uses to run [pyspark/pipelines/cli.py](cli.md) Python script.

This somewhat convoluted way of executing [pyspark/pipelines/cli.py](cli.md) Python script lets Spark Declarative Pipelines use the full execution power of `spark-submit` ([Apache Spark]({{ book.spark_core }}/tools/spark-submit/)) (with the built-in support for [Spark Connect]({{ book.spark_connect }}) among other features) with extra pipelines-specific command-line arguments and options.

[SparkPipelines](#main) behaves similarly to executing`spark-submit` explicitly as follows:

=== "Local installation"

    ```text
    spark-submit \
        [sparkSubmitArgs] \
        /absolute/path/to/pyspark/pipelines/cli.py \
        [pipelinesArgs]
    ```

=== "uv"

    ```text
    uvx --from "pyspark[pipelines]==4.1.1" \
        spark-submit \
            [sparkSubmitArgs] \
            /absolute/path/to/pyspark/pipelines/cli.py \
            [pipelinesArgs]
    ```

## Launch SparkPipelines { #main }

```scala
main(
  args: Array[String]): Unit
```

`main` expects the first command-line argument to be the absolute path of the [pyspark/pipelines/cli.py](cli.md) Python script.

`main` runs `SparkSubmit` ([Apache Spark]({{ book.spark_core }}/tools/spark-submit/SparkSubmit/)) with the [arguments properly ordered](#constructSparkSubmitArgs).

## constructSparkSubmitArgs { #constructSparkSubmitArgs }

```scala
constructSparkSubmitArgs(
  pipelinesCliFile: String,
  args: Array[String]): Seq[String]
```

`constructSparkSubmitArgs` [splits](#splitArgs) the given `args` into `spark-submit`- and pipelines-specific ones.

`constructSparkSubmitArgs` gives a sequence of the `spark-submit`-specific arguments followed by the given `pipelinesCliFile` and the pipelines-specific arguments.

## splitArgs { #splitArgs }

```scala
splitArgs(
  args: Array[String]): (Seq[String], Seq[String])
```

`splitArgs` parses the given `args` (using a custom `SparkSubmitArgumentsParser` ([Apache Spark]({{ book.spark_core }}/tools/spark-submit/))) and returns a pair of `spark-submit`- and pipelines-specific arguments.

`splitArgs` forces `spark.api.mode` configuration property to be `connect`.

??? note "SparkUserAppException"
    `splitArgs` reports a `SparkUserAppException` when `spark.api.mode` configuration property is specified explicitly on command line
    and is not `connect`.

    [Declarative Pipelines currently only supports Spark Connect](../overview.md/#spark-connect).

`splitArgs` uses `local` as the default value of `--remote` command-line option.

---

`splitArgs` creates a custom `SparkSubmitArgumentsParser` to parse the given `args`.

All known arguments are considered `spark-submit`-specific except the following:

* `--name`
* `-h`
* `--help`

Unknown and extra arguments are pipelines-specific.
