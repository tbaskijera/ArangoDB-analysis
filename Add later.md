
<https://www.arangodb.com/community-server/cluster/>

<https://www.arangodb.com/docs/stable/architecture-deployment-modes-cluster-architecture.html>

<https://www.arangodb.com/docs/stable/architecture-single-instance-vs-cluster.html>

<https://www.arangodb.com/docs/stable/architecture-single-instance-vs-cluster.html#performance>

<https://www.arangodb.com/docs/stable/deployment-cluster-using-the-starter.html>

--replication-factor: number of replicas for each shard in collections created by arangobench. This option is only meaningful for test cases that write into collections. The larger the replication factor is, the more expensive write operations will become.

Particioniranje u shardovima + replikacija

a cluster comprised of multiple nodes with synchronous replication and automatic failover for high availability and resilience

Synchronous replication works on a per-shard basis. Using the option replicationFactor, one configures for each collection how many copies of each shard are kept in the Cluster.

## Components

ArangoDB Components
The full ArangoDB package ships with a set of programs and tools that may simplify your workflow, such as:

ArangoDB Server or arangod, the core component of ArangoDB
ArangoDB Shell or arangosh, a client tool that you can use for administration of ArangoDB servers
ArangoDB Starter or arangodb, a tool that helps you deploy ArangoDB in an easy way
and many more.
