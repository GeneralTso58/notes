# CUDA C structure
- reflects the coexistence of host and device.
- kernels are device code written with parallelism
- CPU code launches kernel function to be executed by a collection of threads called grid
# Data transfer
- cudaMalloc
- cudaMemcpy
To make the data transfer worthwhile, we do more parallel compute (complex kernel) per data
# Kernel and threading
- a kernel function sepcifies the code to be executed by all threads in parallel
- blockIdx, threadIdx, blockDim are identifier accessible in kernel so threads executing the same function could distinguish themselves
## qualifiers
- __host__: only runs on host threads
- __global__: called from host and launches a grid of threads in device
- __device__: runs on kernel threads on device.
# invocation
funcName<<<block_num, num_threads_in_block>>>
