## concepts
### abstraction
- file is an array of bytes
- directory contains a list of (user-readable name, low-level ptr) pairs - directory entries, where the ptr recursively points to file or directory
### implementation
- inode: a on-disk structure to manage FS construct metadata: author, type, creation_date, data_block_ptrs, permissions, etc
- disk is block addressable. We divide disk into blocks, and allocate them into three regions: superblock, inodes, data
- given an inode number, we can compute the offset to inode block and access the inode data. Therefore, inode number is the low-level ptr. Once we find the inode, we can use data_block_ptr stored in inode to access content stored in data region. 
- inode allows us to implement files and directories
    - file inodes store file content in data (pointed to by its data_block_ptr)
    - dir stores (user-readable name, inode number) pairs in data
### details
- superblock: encodes info like FS type, locations of each region, etc.
- allocation structure: encodes free data blocks for allocation
- data_block_ptrs in inode need to support a potentially huge file: indirect pointers, instead of pointing to a block that contains user data, point to a block that contains more pointers
- FS operations potentially lots of disk IOs (traversal + allocation + inode write + data write + linking). OS solves this with write cache
## APIs
- file descriptor
    - integer private per process that points to a file struct
    - internally, it is an index to an array (owned by process) of file struct pointers
    - each ptr points to an entry in **open file table** in the kernel
    - each entry in open file table is a file struct that stores current offset (updated through reading/writing or lseek), ref_count, inode pointer
    - a few cases where an entry in open file table is shared (multiple fd points to the same file struct): dup and fork
- hard links
    - creates a dirent in the directory you are linking to
    - inode keeps track of link count and remove itself when it goes to 0 (no dirent points to the inode)
- symbolic links
    - symbolic link is a file (it has its own inode number), and its content (in data region) is a file path
    - basically a weak ptr so we have dangling reference problem
- mounting
    - take an existing dir as a target mount point and paste a new fs onto that directory tree
