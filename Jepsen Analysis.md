
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


