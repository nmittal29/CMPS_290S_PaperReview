## Vector Clocks

<p align="justify">
Vector Clocks are used to determine whether pairs of events are causally related in in distributed systems. Timestamps are generated for each event in the system, and their causal relationship is determined by comparing those timestamps.
The timestamp for an event is vector of numbers, with each number corresponding to a process. Each process knows its position in the vector.
</p>

<p align="justify">
Each process assigns a timestamp to each event. The timestamp is composed of that process’ logical time and the last known time of every other process.
For process P2, the timestamp will be:
</p>

~~~~
(P1_Last_Known_Time, P2_Logical_Time, P3_Last_Known_Time)
~~~~

<p align="justify">
If the event is the sending of a message, the entire vector associated with that event is sent along with the message. When the message is received by a process, the receiving process does the following:
</p>

1. Increment the counter for the process' position in the vector
2. Perform an element-by-element comparison of the received vector with the process's timestamp vector. Set the process' timestamp vector to the higher of the values:

~~~~
for (i=0; i < num_elements; i++) 
	if (received[i] > system[i])
		system[i] = received[i];
~~~~

<p align="justify">
To determine if two events are concurrent, do an element-by-element comparison of the corresponding timestamps. If each element of timestamp V is less than or equal to the corresponding element of timestamp W then V causally precedes W and the events are not concurrent. If each element of timestamp V is greater than or equal to the corresponding element of timestamp W then W causally precedes V and the events are not concurrent. If, on the other hand, neither of those conditions apply and some elements in V are greater than while others are less than the corresponding element in W then the events are concurrent.
</p>

