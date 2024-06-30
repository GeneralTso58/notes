## principle
Multiple programs need to reside in the memory for timesharing to be achieved efficiently. The virtual memory is responsible for providing to each program an illusion of a large sparse, private address space containing all program data, and which can be referenced by program via virtual address. The OS works with hardware to translate VM to physical addresses
- transparency: the program should not be aware the memory is virtualized
- efficiency: fast translation
- protection: each memory pieces should be isolated to prevent unintended accessing
## approach
Upon process creation, OS finds the phyiscal memory locations for the process's virtual memory and define the mapping in terms of bounds.
- each process has code, heap, stack segments, each with its own bounds (base & size)
Before running the process, OS loads the bounds into MMU registers of CPU. While the process runs, hardware uses MMU to translate virtual address to physical address.
- the first 2 bits of virtual address indicates which segment the address belongs to. The following bits indicate the offset (carefully select seg boundary in virtual addr such that bits are flags)
- e.g. code base at 4 KB. virtual memory 000003. physical addr = 4KB + 3.
When switching process, OS saves the MMU registers with the process struct and loads new registers
When process tries to access memory outside of bound, exception occurs