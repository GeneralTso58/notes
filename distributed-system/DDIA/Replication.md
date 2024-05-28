# scaling
## vertical scaling
- shared memory arch
    - many CPUs. many RAMs. one OS
    - cost grows faster than linearly
    - performance bottle neck. machine twice the size cannot handle twice the traffic
- shared disk arch
    - several machines. one disk. connected via a fast network
    - contention overhead
- share nothing
    - coordinate machines running database application (nodes) at application level
    - be aware of the constraints and tradeoff
# Replication
keep a copy of the same data on multiple machines that are connected via a network
- latency
- fault tolerant
- increase read throughput
**all of the difficulty in replication lies in handling changes to replicated data**
## leader follower
leader determines the order by which writes should be processed. follower applies leader write in the same order
### write to leader
- synchronous
    - wait for writing to all followers
    - slow
    - if 1 follower fails, leader needs to block all writes to wait (for strong consistency)
- semi-sync
    - one of the followers is sync
    - we have a backup in case leader has issue
- async
    - data might be lost if leader goes down
### read from any replica
read can access both leader and follower
### chain replication
- chain replicas in a chain and propogate writes from head to tail. ack from tail back to head
- write to head. read from tail
- acked data is stored in every node. high durability.
- all writes are experienced by nodes in the same order. strong consistency guaranteed (?)
### set up new follower
- take a snapshot of leader and copy that to the new follower
- identify the snapshot time in leader's replication log, and apply to follower those replication logs since snapshot was taken
- once the new follower processes the backlog, it is caught up
### node failure
- if follower reboots, it catches up by processing all replication logs since it went offline.
- if leader fails, following needs to happen
    - determine the leader failed
        - whats the right heartbeat timeout
    - choosing a new leader
    - configure the system to use the new leader
when the new leader does not have all writes, losing these writes is dangerous
split brain failure
## replication logs
this is about finding the correct data representation at the right abstraction level
- replicate write requets (the SQL statement)
    - too high level.
    - cannot handle non deterministic statement
    - execution order matters a lot 
- replicate write ahead log
    - too low level
    - WAL is coupled with storage engine and depends on software versions
    - making database upgrade impossible
- replicate logical (row-based) log
    - decouples replication logs from storage engine internal
    - defines row level operation
    - higher level than WAL, so can stay consistent across software upgrade and even across DB
    - also known as CDC
## consistency issue with replication
If you want to scale read with the number of followers, synchronuous writes would not be possible at some point.
With asynchronuous write, eventual consistency is unavoidable due to replication lag
### read-your-writes consistency
For a given user, it will always read the latest data after writes. no guarantee about writes from other users.
solution
- load data user can edit from leader
- for 1 min after last update, direct all read of the value to leader. at the same time make sure all follower's replication lag is below 1 min.
- client record write timestamp, and make sure data in read replica no staler than the timestamp (won't work if user can sign in through multiple device unless we centralize the timestamp)
### monotonic read
since replicas update at different pace, reading a more up to date one then a more stale one appears the reads are going back in time.
### consistent prefix read
if a sequence of writes happen in a certain order, then anyone reading those writes will see them appear in the same order.
## multi-leader replication
use cases include
- multi-dc operation
- clients with offline ops
- collaborative software
problem: the same data may be concurrently modified in two different dcs and those write conflicts must be resolved. there is no single leader to produce concensus on the order of concurrent writes.
Solutions:
- synchronuous conflict detection: need to replicate changes to all replicas, might as well just use single leader replication
- avoid conflict by routing all writes for a record to the same leader
- converging the value in conflict by using UUID/replicaID/keeping all branches of values
- leave conflict resolution to application logic
    - on write: ask user to define a handler
    - on read: surface all branches to user
all-to-all replication
- append replication log with node id to prevent the same log being processed by the same node twice
- we can run into causal consistency issue
## leaderless replication
### quorum reads/writes
there are n nodes
write - client sends n requests to all nodes in parallel. If w writes succeed, write succeeds.
read - client sends n requests to all nodes in parallel. If r reads succeed, read succeeds and returns the most up-to-date value amongs the r reads
**As long as w+r > n, we can guarantee that at least one read contains the up-to-date value (node set that has read and write overlaps)**
only after the number of reachable replicas fall below w/r, that read or write become unavailable
### limitations
- concurrent writes, who wins? same as multi-leader replication
- concurrent read/write, which value would read return? depending on write replication
- when a write fails to reach quorum, it needs to rollback
- node carrying new value may fail and restore from a node carrying old value. this break quorum w
### sloppy quorum
If a write fails to write to w nodes in n, we write to some backup outside of n. When network is repaired, the backup handoff the data back to the home nodes in n.
There is no guarantee a read of r nodes will see the updated value until hinted handoff is completed
