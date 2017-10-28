As you might have already discovered, the HPCC Systems stack consists of two primary processing cluster components.

The **Thor** component is synonymous to Hadoop and Spark. It is used to process batch workloads on large-scale data and produce analytical outputs like aggregation views, scoring models etc. Since Thor has been built from the ground up to process batch data, its architecture is primed to perform some tasks like reading sequential data from a file and processing it.

Once Thor prepares the data, the data will be ready to be served to end users in the form of queries or services. Queries and services require QoS (Quality of Service) around real-time response, concurrent access, and high availability. When you look up the weather for London today, you expect these same QoS - Sub-second response, always alive and highly concurrent access. This introduces us to **Roxie** the query component. Roxie is designed from the ground up to fulfill this QoS.

Both Thor and Roxie are designed to scale from a single execution node to thousands of execution nodes in a single cluster. The type of workload and the size of data being some of the attributes used to determine the size of the cluster.

A sample Thor configuration:

![](/assets/images/Architecture.002.jpeg)

**Dali** is the HPCC Systems equivalent of the name node in the Hadoop architecture. The primary function of Dali is to store the metadata for the distributed file system. In addition, Dali stores the job execution statistics and data outputs. 

**Thor** is based on a **Master and Slave** architecture, where each Slave node hosts a partition of a data file and is responsible for code that processes that data partition. The Master node is responsible for the overall execution of a job and coordination of the execution flow. In circumstances where data needs to be exchanged between the Slave nodes, the Slave nodes communicate among themselves. 

A sample Roxie configuration:

![](/assets/images/Architecture.003.jpeg)


**Roxie** is based on a peer to peer architecture. Each Roxie node contains a data partition of a file and two execution components. The **Worker** component is responsible for processing a local data partition.  The **Farmer** component has three major responsibilities:

1. Services queries from clients.
1. For storing the metadata of every file in the cluster and the cluster location of the data parts that make up each file. 
1. Communicate with the workers in the cluster to process the data and return the results

As discussed, Thor and Roxie are complementing components in the HPCC Systems stack. Large batch data manipulation - clean, normalize, transform, analyze - is Thor's strength. Roxie, on the other hand, is used to surfacing Thor's analyzed data as queries and services for real-time consumption.


