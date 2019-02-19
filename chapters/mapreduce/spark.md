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

At a high level, all of this is accomplished via a cluster setup that can be
broken down into three pieces:

1. A **Driver** that tells the cluster what to do.
2. A **Cluster Manager** that processes the request and distributes it to workers.
3. **Workers** that process tasks as they are received.

(Ref: https://jaceklaskowski.gitbooks.io/mastering-apache-spark/spark-architecture.html)

The fundamental building block of a Spark cluster is the worker. As the name suggests,
these workers perform the actual tasks requested of the cluster whether it be reading
in data, manipulating data, and reporting results back to the user. Typically, a
computer will have one worker per logical core available to the operating system. For
example, a process with 8 physical cores and 16 logical cores will run 16 worker threads
in order to best utilize the resources available to it. Each worker will connect to a
Cluster Manager and inform it of its current status and will then wait to be assigned
work. When work arrives, the worker will execute the instructions given to it and then
report the result (success, failure, etc.) back to the cluster manager before returning
to an idle state.


## Installing Spark

### Windows

### Mac OS X

### Ubuntu

## Setting up Spark

### Masters

### Workers

## Using Spark

### Basic File I/O

### Cloud I/O

### Resilient Distributed Dataset

### RDDs as a Dataframe

### User Defined Functions

### Machine Learning
