# cassandra-notes

    docker run --rm cassandra:3.11
    docker exec -ti <container_name> bash
    csqlsh

Config in the docker is in /etc/cassandra; for e.g. logging there is a
file logback.xml there.

# CQL; the cassandra query language

https://cassandra.apache.org/doc/latest/cql/index.html

identifiers and keywords are case insensitive. (Can quote them in case
need case sensitivity, use double quotes).  String literals use single
quotes.

Prepared statements with bind_markers ('?' | ':' identifier).

Collection types are not meant to store large amounts of data.

TODO: TTL on UPDATE statement

TODO: frozen collections

TODO: for large amounts of data use a specific table with *clustering columns*

Operations on counters and lists are not idempotent; hence extra care
is needed.

counters and durations cannot be part of primary keys

User defined types are defined records.  <- have to be frozen in most cases.

Tuples are always frozen, and are fixed length and types tuple<int, text>

Custom types are complex, still there for backward compatibility, but
the use is discouraged.

# DS201: DataStax Enterprise Foundations of Apache Cassandra

datastax: company providing a commercial offering around cassandra
(with graph and analysis extensions).  Since official docs are under
construction, this course seems like a low bar entrace to
understanding the architecture.

https://academy.datastax.com/resources/ds201-foundations-apache-cassandra

transcript: https://academy.datastax.com/ds201-introduction

starting already above with a docker container.

CQL very similar to SQL, but e.g. no joins

select * from users;

keyspace: namespace/container that stores the replication
configuration for all tables in it

use <keyspace>

create table table1 (
  col1 text,
  col2 text,
  primary key (col1)
);

UUID and TIMEUUID (TIMEUUID starts with timestamp, so orders well with
time)

insert into users (user_id, first_name, last_name)
values (uuid(), 'Joe', 'Janssens');

in cqlsh can use

copy table (col1, col2, col3) from 'data.csv';
copy table (col1, col2, col3) from 'data.csv' with header=true;

also for copying table

TODO: DS 220 on data modeling

## Cassandra data model

partition: grouping of data, set up partitioner to assign group to data
first value in primary key is the partition key
partitions are assigned to nodes

primary key ((state), id)

id is a clustering column

when querying, always use the partition key; gives you data locality
within the single server, can query on clustering columns (using
equality (first) and range queries)
range search is binary search followed by linear read

## distributed architecture

bin/nodetool help
	     info       node settings and stats
	     status     node health and cluster info

3k/5k transactions per second
1/3 TB of data

every node can act as coordinator

ring: every nodes gets ranges of the partition values.

joining the cluster
states: joining, leaving, up, down

driver helps with this; it is aware of which token ranges are assigned
to which nodes.  Can more effecively send the data to the right node.

scaling is near linear

consistency level to determine how to deal with unreachable nodes; can
continue reading and writing when there is a network partition, though
then off course when the partition resolves it some data may get lost.

Vnodes
token assignment is not always an even spread
each nodes get several token ranges to manage (these ranges are the
vnodes)

gossip: cluster metadata for all nodes
heartbeat state
  generation
  version

Application state
  status
  dc
  rack
  schema
  load       disk pressure
  severity   IO pressure
  etc....

Different Snitch implementations.  Helping determine where your data
is.  For a node give a datacenter and a rack.