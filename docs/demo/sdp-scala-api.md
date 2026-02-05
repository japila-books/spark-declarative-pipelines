---
hide:
  - navigation
---

# Demo: Scala API

## Register Dataflow Graph

[DataflowGraphRegistry](../DataflowGraphRegistry.md#createDataflowGraph)

```scala
import org.apache.spark.sql.connect.pipelines.DataflowGraphRegistry

val graphId = DataflowGraphRegistry.createDataflowGraph(
  defaultCatalog=spark.catalog.currentCatalog(),
  defaultDatabase=spark.catalog.currentDatabase,
  defaultSqlConf=Map.empty)
```

## Look Up Dataflow Graph

[DataflowGraphRegistry](../DataflowGraphRegistry.md#getDataflowGraphOrThrow)

```scala
import org.apache.spark.sql.pipelines.graph.GraphRegistrationContext

val graphCtx: GraphRegistrationContext =
  DataflowGraphRegistry.getDataflowGraphOrThrow(dataflowGraphId=graphId)
```

## Create DataflowGraph

[GraphRegistrationContext](../GraphRegistrationContext.md#toDataflowGraph)

```scala
import org.apache.spark.sql.pipelines.graph.DataflowGraph

val dp: DataflowGraph = graphCtx.toDataflowGraph
```

## Create Update Context

[PipelineUpdateContextImpl](../PipelineUpdateContextImpl.md)

```scala
import org.apache.spark.sql.pipelines.graph.{ PipelineUpdateContext, PipelineUpdateContextImpl }
import org.apache.spark.sql.pipelines.logging.PipelineEvent

val swallowEventsCallback: PipelineEvent => Unit = _ => ()

val updateCtx: PipelineUpdateContext =
  new PipelineUpdateContextImpl(unresolvedGraph=dp, eventCallback=swallowEventsCallback)
```

## Start Pipeline

[PipelineExecution](../PipelineExecution.md#runPipeline)

```scala
updateCtx.pipelineExecution.runPipeline()
```
