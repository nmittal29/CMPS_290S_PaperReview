
## Jepsen

<p align="justify">
Jepsen is an open source ‘clojure’ library, written by Kyle Kingsbury, designed to test the partition tolerance of distributed systems by fuzzing the systems with random operations. The results of these tests are analyzed to expose failure modes and to verify if the system violates any of the consistency properties it claims to have.
</p>

<p align="justify">
Jepsen test has three key properties:
</p>

1. <b>Generative</b>: relies on randomized testing to explore the state space of distributed systems
2. <b>Blackbox</b>: observes the system at client boundaries (does not need any tracing framework or apply some code patch in the distributed system to run the test)
3. <b>Invariance</b>: checks invariance from recorded history of operations rather than runtime

<p align="justify">Jepsen Test Data Structure: </p>

~~~~
{:name                    ...| name of the results
 :os                      ...| prepares the operating system
 :db                      ...| configures/starts/stops the database being tested
 :client                  ...| client protocol to interact with database
 :generator               ...| instructs on how to interact
 :conductors{:nemesis  ...}  | interacts with the environment
 :checker              ...}  | looks at and assesses the test run
~~~~

### How a test runs?

<p align="center">
<img src="lein_test1.png" width="500px;" />
</p>

1.	Orchestration node has one thread for each client and a thread for nemesis conductor
2.	A series of generated data comprising of read/write operations for client threads and crash/corrupt/partition operations for nemesis thread.
3.	N nodes on which Cassandra cluster is running.

<p align="center">
<img src="lein_test2.png" width="500px;" />
</p>

4. A concurrent recorded history that explains the chronological behavior of the test. 
5.	Operations in the history are expressed as windows which marks the beginning and ending.
6.	After running the tests, the attached checker is executed, which produces judgement on the validity of the test or produces some artifacts to explain the result of the tests.



