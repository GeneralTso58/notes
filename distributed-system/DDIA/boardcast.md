## broadcast
a mechanism to allow a message sent by a node in the group to get delivered to all nodes (including the node itself).
### reliable broadcast
guarantees delivery of messages with delays
#### implementation:
- eager reliable broadcast: when a node gets a message for the first time, it sends the message to all other nodes
- gossip: when a node gets a message for the first time, it sends the message to k other nodes
### FIFO broadcast
guarantees the messages sent by the same nodes being received by all nodes in the same order they were sent
#### implementation:
for each node, set up sendSeq := 0 and nextToDeliver := <0,0,0...> and buffer := {}
when send msg, send (nodeId, sendSeq, msg), and increment sendSeq
when receive msg
- add msg to buffer 
- check if buffer has msg with sendSeq and nodeId such that nextToDeliver[nodeId] == sendSeq
    - deliver msg to application then nextToDeliver[nodeId] ++
    - then check again to see if there are more to deliver
### causal broadcast
causal related messages must be delivered in causal order
#### implementation:
for each node, set up sendSeq := 0 and nextToDeliver := <0,0,0...> and buffer := {}
when send msg, send (nodeId, deps, msg), where deps:=nextToDeliver; deps[i]:=sendSeq; and increment sendSeq
when receive msg
- add msg to buffer 
- check if buffer has msg with deps such that deps <= nextToDeliver
    - deliver msg to application then nextToDeliver[i]++
    - then check again to see if there are more to deliver
the idea is that causal deps can be captured in a version vector indicating "what state of each process is the current event based off".
each message carries with it all causal dependencies and nextToDeliver contains versions of other process the current process's current state is based off.
### total broadcast
all messages must be delivered in the same order
#### implementation:
single leader
- all nodes send message to leader and leader use fifo broadcast to deliver all messages
- what if leader crashes
lamport
- process a message when we are sure no message of an earlier lamport time would get delivered later
- this is possible because the "next smallest lamport timestamp" is known 
neither of the two implementations are fault tolerant (a failure of one node would mean the system halts) 
### relationship
total order does not imply causal order
total + fifo => causal order => fifo order
## relation to replication
We can use broadcast protocols to replicate change logs across replicas to keep them consistent
- fifo total broadcast promises messages to be delivered to all replicas in the same order
- if all replicas receive and process the replication logs in the same order, they would end up in the same state
- if we use causal broad cast, then application needs to handle conflict or use CRDT
