# UntypedFlow

`UntypedFlow` is a [UnresolvedFlow](UnresolvedFlow.md).

## Creating Instance

`UntypedFlow` takes the following to be created:

* <span id="identifier"> `TableIdentifier`
* <span id="destinationIdentifier"> `TableIdentifier`
* <span id="func"> [FlowFunction](FlowFunction.md)
* <span id="queryContext"> `QueryContext`
* <span id="sqlConf"> SQL Config
* <span id="once"> `once` flag
* <span id="origin"> `QueryOrigin`

`UntypedFlow` is created when:

* `PipelinesHandler` is requested to [define a flow](PipelinesHandler.md#defineFlow)
* `SqlGraphRegistrationContext` is requested to [handle the following SQL queries](SqlGraphRegistrationContext.md#processSqlQuery):
    * [CreateFlowCommand](SqlGraphRegistrationContext.md#CreateFlowCommand)
    * [CreateMaterializedViewAsSelect](SqlGraphRegistrationContext.md#CreateMaterializedViewAsSelect)
    * [CreateView](SqlGraphRegistrationContext.md#CreateView)
    * [CreateStreamingTableAsSelect](SqlGraphRegistrationContext.md#CreateStreamingTableAsSelect)
    * [CreateViewCommand](SqlGraphRegistrationContext.md#CreateViewCommand)
