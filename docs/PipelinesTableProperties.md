# PipelinesTableProperties

`PipelinesTableProperties` defines the supported pipelines table properties.

Pipelines table properties start with `pipelines.` prefix.

## <span id="resetAllowed"> pipelines.reset.allowed { #pipelines.reset.allowed }

Controls whether or not a table should be reset when a Reset is triggered.

default: `true`

Used when:

* `DatasetManager` is requested to [constructFullRefreshSet](DatasetManager.md#constructFullRefreshSet)
* `State` is requested to [findElementsToReset](State.md#findElementsToReset)
* `GraphValidations` is requested to [validate that tables are allowed to be reset](GraphValidations.md#validateTablesAreResettable)
