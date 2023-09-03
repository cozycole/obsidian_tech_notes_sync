# Big Picture

## Main Memory

A collection of bits where input and output from all peripheral devices flow. A CPU is just an operator on memory; it reads its instructions and data from the memory and writes data back out to the memory.

## The Kernel

Nearly everything the kernel does revolves around main memory. It's in charge of:
- Processes: which ones are allowed to use the CPU
- Memory: keep track of which processes have access to which spaces in memory
- Device drivers: operating the hardware along with any kernel jobs
- System calls : certain functions that a process can request the kernel to do

