## reliability
- continuing to work correctly even when things go wrong
- fault is when part of the system deviates from the spec and can result in failure
- hardware failures
    - redundant hardware components
    - cloud services use multiple machines, so it needs to be able to tolerate loss of entire machine
- software errors
    - cascading 
    - dependency error
    - process takes up shared resource
- human errors
    - configuration error is the leading cause of outage
## scalability
- if system grows in certain way, how do we cope with growth. How do we add compting resource to handle growth
- describe load with load parameter
    - pay attention to tail behavior of load parameters.
- decribe perf
    - throughput vs response time
    - P99.9 and response time measure from client are important
    - tail latency amplification: if we hit an upstream service multiple times to serve a request, the probability of any of the upstream calls fall into tail latency is higher than that of a single call. Since the latency of the request is the slowest of the upstream call, tail latency slows down the request.
- identify traffic characteristics
    - volume of read/write
    - volume of storage
    - complexity of data
    - response time
    - access pattern
## maintainability
- operability
- simplicity
    - accidental complexity
    - build good abstraction
- evolvability


    