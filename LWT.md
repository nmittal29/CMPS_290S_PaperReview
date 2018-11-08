## Lightweight Transactions (LWT)

<p align="justify">
Applications like banking or airline reservations require the operations to perform in sequence without any interruptions. This is linearizable consistency which is one of the strongest single-object consistency model. Cassandra provides linearizability via lightweight transactions (LWTs).
</p>
<p align="justify">
Cassandra Query Language (CQL) provides IF syntax to deal with such cases.
</p>
<p>
`INSERT INTO payments (payment_time, customer_id, amount) 
VALUES (2016-11-02 12:23:34Z, 123, 12.00) 
IF NOT EXISTS;`
</p>
