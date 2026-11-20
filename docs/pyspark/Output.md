# Output

`Output` is a [base class](#contract) of [outputs](#implementations) in a pipeline dataflow graph.

`Output`s can be [registered](GraphElementRegistry.md#register_output) in [GraphElementRegistry](GraphElementRegistry.md).

## Contract

`Output` is a Python `dataclass` with the following properties:

* <span id="name"> Name
* <span id="comment"> Comment (optional)
* <span id="source_code_location"> `SourceCodeLocation`

## Implementations

* [Sink](Sink.md)
* [Table](Table.md)
* [TemporaryView](TemporaryView.md)
