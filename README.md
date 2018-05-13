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