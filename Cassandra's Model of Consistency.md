## Cassandra's Model of Consistency

<p align="justify">
Let's establish a few definitions before getting started:
<p>

* RF (Replication Factor): the number of copies of each data item
* R: the number of replicas that are contacted when a data object is accessed through a read operation
* W: the number of replicas that need to acknowledge the receipt of the update before the update completes
* QUORUM: sum_of_replication_factors/2 + 1, where sum_of_replication_factors = sum of all the replication factor settings for each data center

<p align="justify">  
R + W > RF is a strong consistency model, where the write set and the read set always overlap.
</p>

<p align="justify">
But, configuring RF, R and W in this model, depends on the application for which the storage system is being used. In write-intensive application, setting W=1 and R=RF can affect durability in presence of failures as there is a possibility of conflicting writes. In read-intensive applications, setting W=RF and R=1 can affect the probability of the write succeeding.
</p>
<p align="justify">
So, to provide strong consistency and fault tolerance for balanced read-write requests, these two properties are appropriate:
</p>

* R + W > RF 
* R = W = QUORUM

<p align="justify">
For example, a system with configuration RF=3, W=2 and R=2.
</p>

<p align="justify">
R + W <= RF is a weak/eventual consistency model, where there is a possibility that the read and write set will not overlap and system is vulnerable to reading from nodes that have not yet received the updates.
</p>
  
### Read Request in Cassandra
<p align="justify">
Cassandra can send three types of read requests to a replica:
</p>

1. direct read request
2. digest request
3. background read repair request

<p align="justify">
The coordinator node sends one replica node with a direct read request and a digest request to a number of replicas determined by the consistency level specified by the client. These contacted nodes return the requested data and the coordinator compare the rows from each replica to ensure consistency. If all replicas are not in sync, the coordinator uses the replica that has the most recent data (based on timestamp) to forward the result back to the client. Meanwhile, a background read repair request is sent to out-of-date replicas to ensure that the requested data is made consistent on all replicas.
</p>

#### Examples of Read Consistency Levels

1. A single data center cluster with a consistency level of QUORUM 
<p align="center">
  <img src="case1_readcase.png" alt="Read Example 1" width="400px;" style="background:none; border:none; box-shadow:none;"/>
</p>
2. A single data center cluster with a consistency level of ONE
<p align="center">
  <img src="case2_read.png" alt="Read Example 2" width="400px;" style="background:none; border:none; box-shadow:none;"/>
</p>
3. A two data center cluster with a consistency level of QUORUM
<p align="center">
  <img src="case3_read.png" alt="Read Example 3" width="400px;" style="background:none; border:none; box-shadow:none;"/>
</p>
4. A two data center cluster with a consistency level of LOCAL_QUORUM
<p align="center">
  <img src="case4_read.png" alt="Read Example 4" width="400px;" style="background:none; border:none; box-shadow:none;"/>
</p>
5. A two data center cluster with a consistency level of LOCAL_ONE 
<p align="center">
  <img src="case5_read.png" alt="Read Example 5" width="400px;" style="background:none; border:none; box-shadow:none;"/>
</p>

<p align="center">
  <img src="table1_read.png" alt="Read_Table" width="800px;" height="200px;" style="background:none; border:none; box-shadow:none;"/>
</p>
<p align="center">Table 1: Read Consistency Levels</p>

### Write Request in Cassandra

<p align="justify">
The coordinator sends a write request to all replicas that own the row being written. As long as all replica nodes are available, they will get the write request regardless of the consistency level specified by the client. The write consistency level determines how many replica nodes must respond with a success acknowledgment in order for the write to be considered successful.
</p>
<p align="center">
  <img src="write_case_multiple.png" alt="Read_Table" width="400px;" style="background:none; border:none; box-shadow:none;"/>
</p>
<p align="center">
  <img src="table2_write.png" alt="Write_Table" width="800px;" height="200px;" style="background:none; border:none; box-shadow:none;"/>
</p>
<p align="center">Table 2: Write Consistency Levels</p>



