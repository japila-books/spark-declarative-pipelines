# Spark Connect

Spark Declarative Pipelines supports [Spark Connect]({{ book.spark_connect }}) only.

When executed with `spark.api.mode` configuration property other than `connect`, [spark-pipelines](../cli/index.md) fails with the following exception:

```console
$ ./bin/spark-pipelines --conf spark.api.mode=xxx
...
26/04/07 19:41:25 ERROR SparkPipelines: spark.api.mode must be 'connect' (was 'xxx'). Declarative Pipelines currently only supports Spark Connect.
Exception in thread "main" org.apache.spark.SparkUserAppException: User application exited with 1
	at org.apache.spark.deploy.SparkPipelines$$anon$1.handle(SparkPipelines.scala:77)
	at org.apache.spark.launcher.SparkSubmitOptionParser.parse(SparkSubmitOptionParser.java:171)
	at org.apache.spark.deploy.SparkPipelines$$anon$1.<init>(SparkPipelines.scala:62)
	at org.apache.spark.deploy.SparkPipelines$.splitArgs(SparkPipelines.scala:61)
	at org.apache.spark.deploy.SparkPipelines$.constructSparkSubmitArgs(SparkPipelines.scala:48)
	at org.apache.spark.deploy.SparkPipelines$.main(SparkPipelines.scala:42)
	at org.apache.spark.deploy.SparkPipelines.main(SparkPipelines.scala)
```
