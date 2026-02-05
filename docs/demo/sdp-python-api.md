---
hide:
  - navigation
---

# Demo: SDP Python API

!!! warning "Activate Virtual Environment"
    Follow [Demo: Create Virtual Environment for Python Client](create-virtual-environment-for-python-client.md) before getting started with this demo.

In a terminal, start a Spark Connect Server.

```shell
./sbin/start-connect-server.sh
```

It will listen on port 15002.

??? note "Monitor Logs"

    ```shell
    tail -f logs/*org.apache.spark.sql.connect.service.SparkConnectServer*.out
    ```

## Start PySpark Shell

Start a Spark Connect-enabled PySpark shell.

```shell
$SPARK_HOME/bin/pyspark --remote sc://localhost:15002
```

```py
from pyspark.pipelines.spark_connect_pipeline import create_dataflow_graph
dataflow_graph_id = create_dataflow_graph(
  spark,
  default_catalog=None,
  default_database=None,
  sql_conf=None,
)

# >>> print(dataflow_graph_id)
# 3cb66d5a-0621-4f15-9920-e99020e30e48
```

```py
from pyspark.pipelines.spark_connect_graph_element_registry import SparkConnectGraphElementRegistry
registry = SparkConnectGraphElementRegistry(spark, dataflow_graph_id)
```

```py
from pyspark import pipelines as dp
```

```py
from pyspark.pipelines.graph_element_registry import graph_element_registration_context
with graph_element_registration_context(registry):
  dp.create_streaming_table("demo_streaming_table")
```

You should see the following INFO message in the logs of the Spark Connect Server:

```text
INFO PipelinesHandler: Define pipelines dataset cmd received: define_dataset {
  dataflow_graph_id: "3cb66d5a-0621-4f15-9920-e99020e30e48"
  dataset_name: "demo_streaming_table"
  dataset_type: TABLE
}
```
