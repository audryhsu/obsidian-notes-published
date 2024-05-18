---
tags:
  - "#designing-data-intensive-applications"
  - books
aliases: 
publish: true
---
# Chapter 5 Replication
Reasons for replicating data:
- keep data geographically close to users (reduce latency)
- availability if node goes down
- scale out number of machines that can serve read queries

## Asynchronous vs synchronous replication
- asynchronous means leader doesn't wait for follower's "OK" that replication is complete before responding to client
- In practice, a hybrid approach is used and usually make one follower synchronous to ensure at least one follower is always up to date
- use replication logs to revive failed replicas

Synchronous replication: you pay in performance (wait for followers to match) and availability (stop all writes if one goes down)
Asynchronous: ok with **eventual consistency**
changing of the png

![Pasted image 20240116084450.png](../images/Pasted%20image%2020240116084450.png)
![ddia-replication-leader-2024-01-12 09.35.07.excalidraw.excalidraw.svg](../images/ddia-replication-leader-2024-01-12%2009.35.07.excalidraw.excalidraw.svg.md)
%%[🖋 Edit in Excalidraw](../images/ddia-replication-leader-2024-01-12%2009.35.07.excalidraw.svg.md)%%
> [!NOTE] Challenge of replication: handling *changes* to replicated data
> 3 main algorithms for replicating changes between nodes: 
> - single leader (MySQL, PostgresQL, MongoDB)
> - multi-leader (CouchDB, GoogleDocs) 
> - leaderless (Cassandra, Voldemort).
### Failover and Replication Logs
If a leader or follower has failed, a replication log is used to bring the failed node up to date. 
Failover for a new leader is *very complex* and highly suspetible to errors
- new leader must be chosen through an election process.

#### Implementing a replication log
Statement- based replication
- forward every `INSERT`, `UPDATE`, or `DELETE`
- non-deterministic functions like `NOW()` will generate different results on replicas
- if auto-incrementing, statements must be executed *in the exact same order*, which can be difficult if concurrently executing transactions
- not preferred

⭐️ Logical (row-based) replication
- sequence of records *describing* writes to tables at the row-level granularity
	- log contains new values of all columns for an inserted row
	- log contains info to uniquely identify row that was deleted like primary key
- because log is not tied directly to SQL statements (i.e. the storage engine internals), decoupling of logical log and storage engine makes it easier to be backwards compatible and run different versions of software
Trigger-based replication
- application logic driven replication
- use case: flexibility to replicate subset of data
- trigger registers custom app code that is auto executed when a data change (write) occurs and logs the change to a separate table that can be read by external processes


## Algorithms for replicating data between nodes
### Single leader (master)
- write requests go to leader, which first writes new data to local storage
- potential for latency since all writes go to one server, regardless of data center
- followers get data change from replication log and updates its own local copy

🤔Automatic failover challenges:
	- is leader really dead
	- decide who is new leader (consensus problem)
	- reroute client requests to new leader
	- what if dead leader resurrects itself? (split brain problem 🤯)

### Multileader (master-master) - avoid if possible
- have a leader in each data center in multi-data center operation; leader in each data center asynchronously replicated to other data centers
	- 👍inter-datacenter network delay is hidden from users
	- 👍higher tolerance of data center outages
	- 👎👎write conflicts resolution if same data is being modified in two different data centers
		- no guarantee that each node receives write requests in same order 

#### 🤔 Converging toward a consistent state
Convergence: *database must resolve conflict in a way that all replicas arrive at the same final value when all changes have been replicated*
- best strategy is often conflict avoidance --> ensure writes for a particular record go to the same leader
- *last write wins* - attach timestamp to each write and apply the one with highest (latest) value
	- popular,  and prone to data loss
- merge values together e.g. order alphabetically and concatenate them
- if master-master fault, split brain again, add a third node as the arbiter! 
- clocks aren't reliable! 
- write custom code to resolve
	- on write -> background process that executes a conflict handler as soon as system detects conflict in replication log
	- on read -> all conflicts are stored, and multiple versions are returned to application on the next read to ask user to resolve
- record all conflicts and write app code that makes user resolve at later point in time (e.g. git)

Similar examples: collaborative editing tools, how to merge conflicts?
### Leaderless
- *reads and writes are to every available node*
- Examples: [DynamoDB](../DynamoDB.md), [Cassandra](../Cassandra.md), Voldemort
- if a node misses a write, uses versioning on next read to determine the most up to date data
- Read repair: if a client sees it got a stale value (older version) on a read request, it writes the newer value back to that replica
- Anti-entropy process: background processes looking for inconsistencies between replicas and copy missing data in no particular order 
- uses *quorum consistency*: $w + r > n$ 
	- where $r$ and $w$ are the minimum number of votes required for the read or write to be valid in a system with $n$ nodes
	- as long as writes are confirmed by w nodes and query at least r nodes for each read, the system will return an up to date value.


![Drawing_ddia-leaderless-replication-2024-01-24 09.00.29.excalidraw.svg](../images/Drawing_ddia-leaderless-replication-2024-01-24%2009.00.29.excalidraw.svg.md)
%%[🖋 Edit in Excalidraw](../images/Drawing_ddia-leaderless-replication-2024-01-24%2009.00.29.excalidraw.excalidraw.md)%%
Concurrent writes are still a problem even with quorum, need to eventually [converge towards a consistent state](DDIA%20Chapter%205%20Replication.md#🤔%20Converging%20toward%20a%20consistent%20state)
![Pasted image 20240124091818.png](../images/Pasted%20image%2020240124091818.png)