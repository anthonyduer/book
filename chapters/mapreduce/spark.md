# Spark

## What is Spark?

The Apache Software Foundation describes Spark as:
>a unified analytics engine for large-scale data processing.
(Ref: https://spark.apache.org/)

It's main goal is to help alleviate one of the most critical problems that face
most data scientists in the era of 'big data', which is that using lots of data
requires you to be able to manage the data lifecycle at scale. With gigabytes,
or even terabytes, of new arriving on a daily basis, the capabilities of a single
computer are quickly outstripped or would taken an incredibly long time to process.

To alleviate this issue, Spark was developed first as a research project and then
open-sourced into a standalone project in 2010 and later adopted by the Apache
Software Foundation in 2013. Since then, Spark has become one of the leading
technologies for processing data at scale utilizing commodity hardware. At its
core, Spark aims to accomplish three things:
(Ref: https://spark.apache.org/history.html)

1. Allow for the processing and analysis of large quantities of data quickly
in a distributed manner.
2. Provide fault tolerance in the event of a node's failure.
3. Utilize hetergenous, potentially virtual, servers and computers.

By not tying itself to any one piece of hardware or requiring 100% uptime to
process data successfully, Spark provides the user with the flexibility of using
as much or as little computing resources that's neccesary to accomplish a task,
allows for off-the-shelf components to be used in developing the cluster to reduce
costs, and provides users with a familiar dataset based approach to interacting
with data, but at a much larger scale.

## The Building Pieces of a Spark Cluster

At a high level, all of this is accomplished via a cluster setup that can be
broken down into three pieces:

1. A **Driver** that tells the cluster what to do.
2. A **Cluster Manager** that processes the request and distributes it to workers.
3. **Workers** that process tasks as they are received.

(Ref: https://jaceklaskowski.gitbooks.io/mastering-apache-spark/spark-architecture.html)

The fundamental building block of a Spark cluster is the worker. As the name suggests,
these workers perform the tasks requested of the cluster whether it be reading
in data, manipulating data, and reporting results back to the user. Typically, a
worker will have one executor per logical core available to the operating system. For
example, a processor with 8 physical cores and 16 logical cores would be considered 1
worker and have 16 executor threads in order to best utilize the resources available to
it. Each worker will connect to a Cluster Manager and inform it of its current status
and will then wait to be assigned work. When work arrives, the worker will execute the
instructions given to it and then report the result (success, failure, etc.) back to the
cluster manager before returning to an idle state.

The cluster manager oversees the work being done by workers/executors and tasks requested
by users. Its job is to distribute work (not neccesarily the data) to workers, monitor
their status, restart tasks that were given to failed workers, and to return the status
of the job to the driver. The cluster manager does not contain copies of the data itself,
but, rather, manages which workers and executors are responsible for which pieces of the
data. Apache Spark is agnostic in terms of what platform the cluster manager is running on
and supports the native manager bundled with Spark, Apache Mesos, or Kubernetes.
(Ref: https://spark.apache.org/docs/latest/cluster-overview.html)

The driver is the final piece of the Spark puzzle. When using a Spark cluster, the driver
is typically the user's computer that is running the program interacting with the cluster.
Its job is to issue commands, receive the results, and return them to the user. The driver
is responsible for managing the SparkContext and managing the communication between the host
program and the remote cluster.
(Ref: https://spark.apache.org/docs/latest/cluster-overview.html)

## How Spark Manages Data

Spark stores data in Resilient Distributed Datasets (RDDs). As the name suggests, they are
resilient against failure and distributed in nature. The resilience comes from how Spark
manages the data internally and the distributed nature comes from how the data is split
amongst various workers.

### Resiliencey

One of the key issues with any cluster is how to manage the failure of a worker node. If the
data only exists on the failed node and there is no other available copy of the data, then the
entire job fails, which is not an ideal situation when calculations may take a long period of
time to perform from start to finish. Spark alleviates this via two methods:

1. The provenance of each bit of data is tracked using a Directed Acyclic Graph.
2. Once an RDD is created, it is immutable and any changes made to it result in a new RDD, not
a modified one.

The first method utilizes a mathematical construct: a directed acyclic graph (DAG). Breaking it
down, the graph is directed - step A can lead to step B but step B cannot lead back to step A
- and acyclic - the steps in the graph do not contain any loops. When a task is running, Spark
keeps track of this DAG and can reconstruct data from a failed node by simply repeating the steps
on the DAG that lead up to the failed point and then continuing on from there. The second method
leads from the first: in order to avoid long recalculation chains, RDDs are immutable and cannot
be changed. Rather, new copies of the RDD are created when actions are performed.

Finally, with the above two methods in mind, it's important to note one tradeoff Spark made
when it came to performance and how it relates to the immutablility of RDDs. One of the common
complaints about Hadoop and MapReduce was that it wrote the entire dataset out to disk after
each step in the graph, which made things slow but allowed for large datasets to be active
as they mostly existed on disk where space is measured in terabytes. Spark, instead, keeps
RDDs resident in memory, which makes is much quicker. The tradeoff, then, is increased speed
at the cost of costlier storage (RAM instead of HDDS). 
(Ref: https://www.tutorialspoint.com/apache_spark/apache_spark_rdd.htm)

### Distributed

As mentioned above, RDDs are distributed in nature. When reading data, for example, each
worker is given a part of the file to read and process. Assuming no network congestion,
that means a Spark cluster can read data almost linearly faster than a single computer.
The distributed nature of the dataset does come at a cost: merging datasets or any action
that causes the data to need to look up values in other records (e.g. a SQL join) may
require a significant number of observations/records to be shuffled across the network. One
of the key optimizations in Spark is reducing actions that cause unneccesary shuffles or
providing hints to Spark to keep observations/records logically organized.

To take a real life example, imagine that a Spark cluster contains customer purchase data
that has been distributed amongst all of the workers such that a customer's data is
equally split across the entire cluster. If the cluster is asked how much the customer
has spent, that question can be answered without shuffling any data: each worker will sum
up the amount it has for that customer and report it to the cluster manager. The manager
will them sum the sums from the workers to arrive at the total. However, if we want to 
know the distinct list of items the customer has ordered, then shuffling the data is 
required as the list may not fit inside of the cluster manager's memory - so the workers
have to tell each of the other workers the distinct list it has and then collapse the
list down before returning it to the cluster manager as a new RDD. It's important to keep
track of commands that may cause a shuffle. A more exhaustive discussion can be found
in the documentation.
(Ref: https://spark.apache.org/docs/latest/rdd-programming-guide.html#shuffle-operations)

## Installing Spark

### Windows

### Mac OS X

### Ubuntu

## Using Spark

### Basic File I/O

### Cloud I/O

### Resilient Distributed Dataset

### RDDs as a Dataframe

### User Defined Functions

### Machine Learning
