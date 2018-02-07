---
layout: post
title: Thor and Roxie
---

As you might have already discovered, the HPCC Systems stack consists of two primary processing cluster components.

The **Thor** component is synonymous to Hadoop and Spark. It is used to process batch workloads on large-scale data and produce analytical outputs like aggregation views, scoring models etc. Since Thor has been built from the ground up to process batch data, its architecture is primed to perform tasks like reading sequential data from a file and processing it quickly.

Once Thor prepares the data, the data will be ready to be served to end users in the form of queries or services. Queries and services require QoS (Quality of Service) around real-time response, concurrent access, and high availability. For example, when you look up the weather for London today, you expect these same QoS - Sub-second response, always alive and highly concurrent access. This introduces us to **Roxie** the query component. Roxie is designed from the ground up to fulfill this QoS.

Both Thor and Roxie are designed to scale from a single execution process to thousands of execution processes in a single cluster. In the case of Thor, each process can run on its own dedicated physical (or virtual) server or multiple processes can share the same physical (or virtual) server. For Roxie, one Roxie process runs on each physical (or virtual) server. The type of workload, the amount of data and the capacity of each server being some of the attributes used to determine the size and configuration of the cluster.

A sample Thor configuration:

![](/assets/images/Slide6.PNG)

**Dali** is the HPCC Systems equivalent of the name node in the Hadoop architecture. The primary function of Dali is to store the metadata for the distributed file system. In addition, Dali stores the job execution statistics and data outputs. 

**DFU Server** is responsible for importing (Spraying) data from landing zones, partitioning it and distributing the partitions such that they are local to the Thor Slave processes. In addition, DFU server is responsible for exporting the data by assembling all the parts of a file and copying the file to the landing zone.  

**File Service** runs on every physical server (or virtual) that hosts a partition of a file. For example, a File Service is present on the landing zone server as well as every server that hosts the Thor slave. 

**Thor** is based on a **Master and Slave** architecture, where each Slave process is responsible to process a partition of a data file. The Master node is responsible for the overall execution of a job and coordination of the execution flow. In circumstances where data needs to be exchanged between the Slave nodes, the Slave nodes communicate among themselves. The architecture is optimized to maximize parallel processing on data that is stored locally (shared nothing architecture).


A sample Roxie configuration:

![](/assets/images/Slide7.PNG)


**Roxie** is based on a peer to peer architecture. Each Roxie process is allocated a data partition of a file. A Roxie process consists of two components. The **Worker** component is responsible for processing a local data partition.  The **Farmer** component has three major responsibilities:

1. Services queries from clients.
1. For storing the metadata of every file in the cluster and the cluster location of the data parts that make up each file. 
1. Communicate with the workers in the cluster to process the data and return the results

As discussed, Thor and Roxie are complementing components in the HPCC Systems stack. Large batch data manipulation - clean, normalize, transform, analyze - is Thor's strength. Roxie, on the other hand, is used to surfacing Thor's analyzed data as queries and services for real-time consumption.


