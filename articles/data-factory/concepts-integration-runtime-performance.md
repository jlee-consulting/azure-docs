---
title: Integration Runtime Performance
titleSuffix: Azure Data Factory & Azure Synapse
description: Learn about how to optimize and improve performance of the Azure Integration Runtime in Azure Data Factory and Azure Synapse Analytics.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/09/2021
---

# Optimizing performance of the Azure Integration Runtime

Data flows run on Spark clusters that are spun up at run-time. The configuration for the cluster used is defined in the integration runtime (IR) of the activity. There are three performance considerations to make when defining your integration runtime: cluster type, cluster size, and time to live.

For more information how to create an Integration Runtime, see [Integration Runtime](concepts-integration-runtime.md).

## Cluster type

There are three available options for the type of Spark cluster spun up: general purpose, memory optimized, and compute optimized.

**General purpose** clusters are the default selection and will be ideal for most data flow workloads. These tend to be the best balance of performance and cost.

If your data flow has many joins and lookups, you may want to use a **memory optimized** cluster. Memory optimized clusters can store more data in memory and will minimize any out-of-memory errors you may get. Memory optimized have the highest price-point per core, but also tend to result in more successful pipelines. If you experience any out of memory errors when executing data flows, switch to a memory optimized Azure IR configuration. 

**Compute optimized** aren't ideal for ETL workflows and aren't recommended for most production workloads. For simpler, non-memory intensive data transformations such as filtering data or adding derived columns, compute-optimized clusters can be used at a cheaper price per core.

## Cluster size

Data flows distribute the data processing over different nodes in a Spark cluster to perform operations in parallel. A Spark cluster with more cores increases the number of nodes in the compute environment. More nodes increase the processing power of the data flow. Increasing the size of the cluster is often an easy way to reduce the processing time.

The default cluster size is four driver nodes and four worker nodes.  As you process more data, larger clusters are recommended. Below are the possible sizing options:

| Worker cores | Driver cores | Total cores | Notes |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Not available for compute optimized |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Data flows are priced at vcore-hrs meaning that both cluster size and execution-time factor into this. As you scale up, your cluster cost per minute will increase, but your overall time will decrease.

> [!TIP]
> There is a ceiling on how much the size of a cluster affects the performance of a data flow. Depending on the size of your data, there is a point where increasing the size of a cluster will stop improving performance. For example, If you have more nodes than partitions of data, adding additional nodes won't help. 
A best practice is to start small and scale up to meet your performance needs. 

## Time to live

By default, every data flow activity spins up a new Spark cluster based upon the Azure IR configuration. Cold cluster start-up time takes a few minutes and data processing can't start until it is complete. If your pipelines contain multiple **sequential** data flows, you can enable a time to live (TTL) value. Specifying a time to live value keeps a cluster alive for a certain period of time after its execution completes. If a new job starts using the IR during the TTL time, it will reuse the existing cluster and start up time will greatly reduced. After the second job completes, the cluster will again stay alive for the TTL time.

You can additionally minimize the startup time of warm clusters by setting the "Quick re-use" option in the Azure Integration runtime under Data Flow Properties. Setting this to true will tell the service to not teardown the existing cluster after each job and instead re-use the existing cluster, essentially keeping the compute environment you've set in your Azure IR alive for up to the period of time specified in your TTL. This option makes for the shortest start-up time of your data flow activities when executing from a pipeline.

However, if most of your data flows execute in parallel, it is not recommended that you enable TTL for the IR that you use for those activities. Only one job can run on a single cluster at a time. If there is an available cluster, but two data flows start, only one will use the live cluster. The second job will spin up its own isolated cluster.

> [!NOTE]
> Time to live is not available when using the auto-resolve integration runtime

## Next steps

See other Data Flow articles related to performance:

- [Data Flow performance](concepts-data-flow-performance.md)
- [Data Flow activity](control-flow-execute-data-flow-activity.md)
- [Monitor Data Flow performance](concepts-data-flow-monitoring.md)
