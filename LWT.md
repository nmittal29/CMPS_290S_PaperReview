## Lightweight Transactions (LWT)

<p align="justify">
Applications like banking or airline reservations require the operations to perform in sequence without any interruptions. This is linearizable consistency which is one of the strongest single-object consistency model. Cassandra provides linearizability via lightweight transactions (LWTs).
Cassandra Query Language (CQL) provides IF syntax to deal with such cases.
</p>
