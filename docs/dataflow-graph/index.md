# Dataflow Graph

**Dataflow Graph** ([DataflowGraph](DataflowGraph.md)) is a graph of tables, sinks, views and flows in a Spark Declarative Pipelines project.

A Dataflow Graph is created every time [Spark Pipelines CLI](../cli/index.md) (`spark-pipelines`) is launched to [run a pipeline update](../cli/cli.md#run) (with either `run` or `dry-run` option).

Spark Pipelines CLI uses Spark Connect to talk to [PipelinesHandler](../spark-connect/PipelinesHandler.md) to [run a pipeline update](../spark-connect/PipelinesHandler.md#startRun).

`PipelinesHandler` prints out the following INFO message to the logs:

```text
PipelinesHandler: Start pipeline cmd received: start_run {
  dataflow_graph_id: "..."
  full_refresh_all: ...
  dry: ...
  storage: "..."
}
```

`PipelinesHandler` uses [GraphRegistrationContext](../spark-connect/GraphRegistrationContext.md) to register [GraphElement](GraphElement.md)s and, eventually, [build a DataflowGraph](../spark-connect/GraphRegistrationContext.md#toDataflowGraph).
