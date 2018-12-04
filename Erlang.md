---
title: Erlang
author: Natasha Mittal
layout: single
classes: wide
---

by Natasha Mittal ⋅ edited by Abhishek Alfred Singh and Lindsey Kuper

## Introduction
In 1981, the Ericsson [Computer Science Laboratory(CSLab)](http://www.cs-lab.org/) had been experimenting with ways to program telephony features in Prolog, a declarative language. Telecom applications in general are parallel systems with a large number of concurrent actions taking place. The downside to Prolog was that such declarative languages did not possess error-handling facilities and also lacked the means for concurrency control across multiple systems. Thus began a series of collaborations which led to the development of Erlang.

Erlang is said to be the result of [Joe Armstrong](https://joearms.github.io/)'s [thesis]
(http://erlang.org/download/armstrong_thesis_2003.pdf) on "Making reliable distributed systems in the presence of software errors". In it, he described the approaches towards programming telecom applications and argued how Erlang fulfills the requisites to build fault tolerant systems. Since its inception in 1986, Erlang has grown popular for building reliable telecom applications. It has been used in Web Prioritizer and [Mail Robustifier](https://dl.acm.org/citation.cfm?id=338532), two Erlang products developed by [Bluetail](https://www.walerud.com/blog/bluetail-spinning-out-of-ericsson-and-selling-for-152m-in-18-months), a company founded by Joe Armstrong. [Ericsson's AXD301](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.33.5674&rep=rep1&type=pdf), a scalable ATM switching system developed using Erlang middleware, was one of the company's most successful new products in the years 1998 to mid 2000s. 

Erlang is a [concurrent programming language designed for programming large-scale distributed soft real-time control applications](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.34.5602&rep=rep1&type=pdf). It is used in conjunction with libraries called [OTP (Open Telecom Platform)](http://erlang.org/doc/system_architecture_intro/sys_arch_intro.html) which use "supervision trees" to provide descriptions of error recovery actions to take, for a given error. Erlang is process-based, in the sense that individual processes do not share memory and communicate via asynchronous message passing, hence maintaining strong isolation between concurrent processes. Since the resource threads are not shared, Erlang's programming model is able to use fail-fast processes. Consistency is provided by the language and not the underlying operating system. 

The basic idea is to perform the application logic at one layer and have another layer, say the [error trapping layer](http://delivery.acm.org/10.1145/1820000/1810910/p68-armstrong.pdf?ip=76.102.6.80&id=1810910&acc=OPEN&key=4D4702B0C3E38B35%2E4D4702B0C3E38B35%2E4D4702B0C3E38B35%2E6D218144511F3437&__acm__=1543946542_df2e5d8166b0c84f74f5000a5d4ce297) to ensure the system state is restored properly and full recovery is possible after the occurrence of any error. 

Erlang also offers support for dynamic code replacement, which aids in code updating and maintenance without stopping the system. This is essential since telecom applications are very long-lived or more often than not, aren't shut down ever.

## Concurrency Oriented Programming

COPL stands for "Concurrency Oriented Programming Languages", this term was coined by Joe Armstrong in his [thesis](http://erlang.org/download/armstrong_thesis_2003.pdf), where he argued that Erlang falls into this category of languages. The main advantage to using COPLs is the way they can easily model real-world concurrent activities and simultaneously ensure that their mapping is done 1:1  to avoid degeneration of the program. This is in contrast to the approach in non-CO languages where program is not isomorphic to the problem, and so they are subject to severe interference errors.

As described in his [thesis](http://erlang.org/download/armstrong_thesis_2003.pdf), there are three major properties of Erlang which makes it satisfy the requirements for being considered a concurrent programming language. 
These are:

1. It supports lightweight processes, as in the computation required to generate and destroy processes are very little.
2. It supports isolation of processes.
3. Every process is identified uniquely by a Pid.
4. There are no shared states between processes.
5. Message passing does not guarantee delivery, and is pure (no dangling pointers or data references).
6. Processes can detect the occurrence of and also the reason of failures in other processes.

A critical requirement in COPLs is that of isolation. There must be strong isolation between the multiple processes running on a single machine. Unless programmed, no faults in any process should affect any of the other processes on the machine. To enable isolation, all processes have "share nothing" semantics and message passing between processes is asynchronous to prevent a sender of the message from getting indefinitely blocked in case software errors occur in the receiver. Data is also immutable within individual processes.

Another requirement is unforgeability of process names so that it is impossible to guess their names. Each process can only know its own name and the name if the child processes it has created. The act of revealing names to other processes is termed as the [name distribution problem](http://erlang.org/download/armstrong_thesis_2003.pdf). Such revelations have to be limited to trusted processes only to maintain system security. 

Thirdly, message passing has "send and pray" semantics. Every message is assumed to be received in its entirety or not received at all. Messages can be sent to and received via mailboxes, which every process has. To aid the isolation agenda, there can be no pointers or references to data structures residing on other machines, once a message has been passed. Additionally, message passing is done in order, i.e messages are received in the exact order they had been sent in. The key advantage to using message passing in relevance to today's world is the scalability. Such systems are very easily scalable and can be replicated over multiple isolated machines, thereby enabling fault tolerance as well. Even though individual components may fail, the probability of all of them failing at the same time would be minimal if replication is done sufficiently. 

In addition to these features, Erlang has a safe-type system, which ensures no corrupt data structures can be written by allowing them to be typed dynamically only. Another unique feature of Erlang is its principle of fault-tolerance - let processes fail and issue other processes to detect these failures and fix them. So an individual process getting destroyed has no adverse consequences on the system.

## Fault-tolerance in Erlang

According to [Joe](http://erlang.org/download/armstrong_thesis_2003.pdf), the main strategy for implementing fault-tolerance is to try to perform a task, and if unsuccessful, try to perform a simpler task. In this manner, a hierarchy of tasks is established. This strategy helps avoid unnecessary complexity that might result in the system becoming less reliable. 

This hierarchical organization of tasks is better known as a set of supervision hierarchies. In this level-based organization, the highest level task is to run an application according to specific parameters, and if not possible, to run simpler lower level tasks. A system failure occurs if the lowest level task cannot be performed successfully. As we go down to simpler tasks, failure to perform becomes more unlikely. It is also interesting to note that on encountering more an more failures at different levels, the emphasis becomes less towards providing complete service and more towards protecting the system. Hence it becomes important to have some mechanism to log all failures and their particular reasons.

### Supervision Hierarchy
The supervision hierarchy detects and as attempts to stop errors from propagating upwards in the system. Every task is associated with a supervisor process which assigns it to a worker for achieving the goals necessary to complete the given task. 

As described in [thesis](http://erlang.org/download/armstrong_thesis_2003.pdf), supervision trees are hierarchical trees of supervisors. Each node in the tree is responsible for monitoring errors in its child nodes: 

1. Supervision trees are trees of Supervisors.
2. Supervisors monitor Workers and Supervisors.
3. Workers are instances of Behaviors.
4. Behaviors are parameterized by Well-behaved functions.
5. Well-behaved functions raise exceptions when errors occur.

A supervisor needs to have the information about how to start, stop or restart every worker under it. This data is stored in an SSRS (Start Stop and Restart Specification). In a linear hierarchy, the rule is - stop all children processes if a parent asks to stop the supervisor; and to restart a child in case it dies. 

### AND/OR Supervision Hierarchies
An AND hierarchy is used where processes are intended to be coordinated with each other, whereas the OR hierarchy is used when processes are independent. 
The supervisor acts according to the following rules:

1. If my parent stops me then I should stop all my children.
2. If any child dies and I am an AND supervisor stop all my children and restart all my children.
3. If any child dies and I am an OR supervisor restart the child that died.
 
By implementing supervisors in the OTP system in this manner, it is in effect implementing a supervision tree that can be used to monitor the behavior of the system.

It is also important to exactly define what an error means for this system. Errors are not always corresponding to exceptions. [Joe](http://erlang.org/download/armstrong_thesis_2003.pdf) defines an error as "a deviation between the observed behavior of a system and the desired behavior of a system."

## Programming Model

