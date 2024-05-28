## consistent hashing
How to assign a BLOB to one server out of n servers such that
- load is distributed
- removal or addition of a server only requires the BLOB in that server to be reassigned
### solution
- assign each server a position in the hash key space laid out in circle
- hash(BLOB_ID) gives a position in the hash space circle
- store the blob in the first server going clockwise around the circle
In this solution, if a server is removed, only BLOBS assigned to this server needs to be moved to the next server around the circle
### variant 1
assign the same physical server to multiple positions in the circle (each a virtual node)
when the server dies, its load gets evenly distributed to different servers around the circle.
### variant 2
assign the BLOB to the first k servers going clockwise around the circle.
at read time, we can avoid single server hotspot by randomly picking a server from the first k servers.
## rendezvous hashing
How to assign a BLOB to k servers out of n servers
variant 2 above is a solution to this
More generally we could
- given a BLOB, get a score for each server = hash(Server_ID, BLOB_ID) and pick k highest score server.
**This is not suitable for sharding database, but good for CDN sharding.**
In database, we make sure each shard has enough replicas that it never goes down. So we never run into the situation where a shard is removed and needs to redistribute keys quickly without affecting most other shards. Also, the cost of moving data as a consequence of resharding is high for databases compared to CDN.
https://shinglyu.com/web/2022/02/11/consistent-hashing-and-why-it-might-not-be-the-correct-answer-to-your-system-design-interview.html