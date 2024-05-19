# hardware design philosophy
- latency-oriented
    - CPUs
    - limited number of threads/processors
    - handle sequential workloads in low latency (threads running fast without waiting for resources)
    - achieving above via lots of hardware caches
- throughput-oriented
    - GPUs
    - lots of threads going at once, high throughput
    - latency (accessing/loading up memory or performing arithmetic computation for each trhead) might be high
# how much can we speed up
- degree of parallelism of the program
- memory bandwidth
    - reduce the need to go to DRAM by using on GPU memory
# related interfaces
- MPI (message passing interface)
    - this API is conceived based on a parallelism model where computing instances do not share memory and information needs to be passed around as messages. great for defining the API in a cluster amongst nodes.
    - OpenCL. similar to CUDA
