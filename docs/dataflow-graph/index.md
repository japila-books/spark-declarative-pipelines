# Dataflow Graph

**Dataflow Graph** ([DataflowGraph](DataflowGraph.md)) is a graph of tables, sinks, views, and flows in a Spark Declarative Pipelines project.

A Dataflow Graph is created every time the [Spark Pipelines CLI](../cli/index.md) (`spark-pipelines`) [runs a pipeline update](../cli/cli.md#run), whether with the `run` or the `dry-run` option.

The CLI uses Spark Connect to talk to [PipelinesHandler](../spark-connect/PipelinesHandler.md), which [runs the pipeline update](../spark-connect/PipelinesHandler.md#startRun).

```mermaid
sequenceDiagram
    participant CLI as spark-pipelines (CLI)
    participant PH as PipelinesHandler
    participant GRC as GraphRegistrationContext
    participant DG as DataflowGraph

    CLI->>PH: start_run (Spark Connect)
    PH->>GRC: register GraphElements
    GRC->>DG: toDataflowGraph
    DG-->>PH: DataflowGraph
    PH-->>CLI: pipeline update events
```

`PipelinesHandler` logs the following INFO message:

```text
PipelinesHandler: Start pipeline cmd received: start_run {
  dataflow_graph_id: "..."
  full_refresh_all: ...
  dry: ...
  storage: "..."
}
```

`PipelinesHandler` uses [GraphRegistrationContext](../spark-connect/GraphRegistrationContext.md) to register [GraphElement](GraphElement.md)s and, finally, to [build the DataflowGraph](../spark-connect/GraphRegistrationContext.md#toDataflowGraph).
