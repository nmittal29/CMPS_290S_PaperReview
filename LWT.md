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
IF NOT EXISTS
~~~~

~~~~
UPDATE account SET amount = 230.00 
WHERE payment_date = 2016-11-02
AND customer_id = 356 
IF amount = 125.00
~~~~

<p align="justify">
To synchronize replicas for LWTs, Cassandra uses Paxos protocol for consensus. There are four phases to Paxos: <b>prepare/promise</b>, <b>read/results</b>, <b>propose/accept</b> and <b>commit/ack</b>. Thus, Cassandra makes four round trips between a node proposing a lightweight transaction and any needed replicas in the cluster to ensure proper execution, so performance is affected. 
</p>

<p align="center">
<img src="lwt.png"></img>
</p>

<p align="justify">
Prepare/promise is the core of the algorithm. The leader (node which proposes the value) picks a proposal number and sends it to the participating replicas. If the proposal number is the highest a replica has seen, it promises to not accept any proposals associated with any earlier proposal number. Along with that promise, it includes the most recent proposal it has already received.
</p>

<p align="justify">
If a majority of the nodes promise to accept the leader's proposal, it may proceed to the actual proposal, but with the wrinkle that if a majority of replicas included an earlier proposal with their promise, then that is the value the leader must propose.
</p>

<p align="justify">
Conceptually, if a leader interrupts an earlier leader, it must first finish that leader's proposal before proceeding with its own, thus giving the desired linearizable behavior.
After the commit phase, the value written by LWT is visible to non-LWTs. Following is an exmaple of paxos trace in Cassandra: 
</p>

~~~~
Parsing insert into users (username, password, email ) values ( ‘mick’, ’mick’, ’mick@gmail.com' ) if
not exists; [SharedPool-Worker-1] | 2016-08-22 12:38:44.132000 | 127.0.0.1 | 1125
Sending PAXOS_PREPARE message to /127.0.0.3 [MessagingService-Outgoing-/127.0.0.3] | 2016-08-22 12:38:44.141000
| 127.0.0.1 | 10414
Sending PAXOS_PREPARE message to /127.0.0.2 [MessagingService-Outgoing-/127.0.0.2] | 2016-08-22 12:38:44.142000
| 127.0.0.1 | 10908
Promising ballot fb282190-685c-11e6-71a2-e0d2d098d5d6 [SharedPool-Worker-1] | 2016-08-22 12:38:44.147000 |
127.0.0.3 | 4325
Promising ballot fb282190-685c-11e6-71a2-e0d2d098d5d6 [SharedPool-Worker-1] | 2016-08-22 12:38:44.147000 |
127.0.0.3 | 4325
Promising ballot fb282190-685c-11e6-71a2-e0d2d098d5d6 [SharedPool-Worker-3] | 2016-08-22 12:38:44.166000 |
127.0.0.1 | 35282
Accepting proposal Commit(fb282190-685c-11e6-71a2-e0d2d098d5d6, [lwts.users] key=mick columns=[[] | [email
password]]\n Row: EMPTY | email=mick@gmail.com, password=mick) [SharedPool-Worker-2] |
2016-08-22 12:38:44.199000 | 127.0.0.1 | 67804
~~~~

<p align="justify">
There are two consistency levels associated with LWTs:
</p>

1.	SERIAL : where paxos consensus protocol will involve all the nodes across multiple datacenters.
2.	LOCAL_SERIAL : where paxos consensus protocol will run on local datacenter.

<p align="justify">
Serial reads allows reading the current (and possibly uncommitted) data. If a SERIAL read finds an uncommitted LWT in progress, Cassandra performs a read repair as part of the commit.
</p>





