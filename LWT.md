## Lightweight Transactions (LWT)

<p align="justify">
Applications like banking or airline reservations require the operations to perform in sequence without any interruptions. This is linearizable consistency which is one of the strongest single-object consistency model. Cassandra provides linearizability via lightweight transactions (LWTs).
</p>
<p align="justify">
Cassandra Query Language (CQL) provides IF syntax to deal with such cases:
</p>

~~~~
INSERT INTO account (transaction_date, customer_id, amount) 
VALUES (2016-11-02, 356, 125.00) 
IF NOT EXISTS;
~~~~

~~~~
UPDATE account SET amount = 230.00 
WHERE payment_date = 2016-11-02
AND customer_id = 123 
IF amount = 125.00
~~~~
