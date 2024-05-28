# partition
- each piece of data belongs to exactly one partition. 
- Each partition is a database of its own.
- possible to have replicas for each partition
## partition key value data
skewed - some partition has more data than others
hotspot - a partition has disporortionately higher load
### key range partition
assign a continuous range of keys to each partition
within each partition, keep keys in sorted order to make range query easier
watch out for hotspot
### hash key partition
hash the key and partition the hash range. 
- cannot do efficient range query. keys once adjacent are now scattered across partitions
- compound primary key = parition key + sort key
    - parition key will be hashed to determine the partition
        - cannot perform range query on partition key
    - sort keys are concatenated index for sorting data in SSTable within a partition
        - can perform range query
If a single key is being written to in high volume, we still get hotspot.
- to relieve this, we can add random numbers to the key and produce a group of keys and scatter them across partitions to relieve load
- but read now needs to read across all partitions to combine the writes
### secondary index partition
- partition by document
    - each partition maintains its own secondary indexes, covering documents in that partition
    - read needs to query all partitions
- partition by term
    - construct a global index and partition it separately from primary index
    - write is complicated as it needs to perform a transaction across partitions
    - in practice it is async
### rebalancing
- For hash key partition, load is uniformly distributed. We can **fix the partition boundary** apriori, have lots of partitions, and only move partitions around the nodes to load balance.
- For key range partition, we cannot fix partition boundary apriori. **dynamic splitting** is unavoidable. When a partition exceeds x records, split it in half and move the half to new nodes. This can be applied to hash key partition as well, but splitting is complex.
- we can also fix the number of partitions per node. When a new node joins, we randomly choose a fixed number of existing partition to split.
### operations
be wary of automatic rebalancing. Usually partition needs to be rebalanced during high traffic and do balancing at that time adds dangerous load.

